---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Julia
  language: julia
  name: julia-1.10
---

<!-- Run at top level of repo. -->
```{code-cell}
:tags: ["remove-cell"]
cd("..")
```

Case Studies
============

This chapter describes some of the use cases we've found for Julia, as well as
benchmarks we've tried.


Gaussian Mixture Modeling
-------------------------

The following code implements an [Expectation-Maximization (EM) algorithm][em] to find the maximum likelihood estimate of the mixing parameter of a two-component [Gaussian mixture model][mixmod] (a form of k-means clustering which assumes that each cluster has a Gaussian distribution). This "algorithm" [it's really a meta-algorithm framework, not a specific algorithm] is a very frequently-used approach for finding maximum likelihood estimates, particularly for problems that can be interpreted as involving [latent variables][lvs] or other forms of [incomplete data][id].

[mixmod]: https://scikit-learn.org/stable/modules/mixture.html
[em]: https://en.wikipedia.org/wiki/Expectation%E2%80%93maximization_algorithm
[lvs]: https://en.wikipedia.org/wiki/Latent_and_observable_variables
[id]: https://en.wikipedia.org/wiki/Missing_data

### Analysis objective

Let $Y$ denote the observed univariate outcome. To simplify this coding example, let's assume that we already know that:

- cluster one (denoted by $Z=1$) has a Gaussian distribution with
    - mean $\mu_1 = \text{E}[Y|Z=1] = 0$
    - standard deviation $\sigma_1 = \sqrt{\text{Var}(Y|Z=1)} = 1$
- cluster two ($Z=2$) has a Gaussian distribution with
    - mean $\mu_2 = \text{E}[Y|Z=2] = 2$
    - standard deviation $\sigma_2 = \sqrt{\text{Var}(Y|Z=2)} = 1$. 
    
We only need to estimate the mixing probability (the relative frequency of the clusters), $\pi = P(Z=1)$.

### Data-generating simulation

First, let's write a function to generate data that our EM algorithm can analyze.

```{code-cell}
using DataFrames, Distributions, DataFramesMeta

function gen_data(
  ; # no positional arguments
  
  # four keyword arguments:
  n = 500000, 
  mu = [0, 2], 
  sigma = 1, 
  pi = 0.8 
)
    data = DataFrame(
        Obs_ID = 1:n,
        Z = (rand(Uniform(0,1), n) .> pi) .+ 1,
        )

    @transform!(data, :Y = rand(Normal(0, sigma), n) + mu[:Z])    
    @select!(data, :Obs_ID, :Y)
    @transform!(data, :p_Y_given_Z_1 = pdf.(Normal(mu[1], sigma), :Y))
    @transform!(data, :p_Y_given_Z_2 = pdf.(Normal(mu[2], sigma), :Y))

    return data
  end
```

Let's generate some data using our `gen_data()` function and examine the results:

```{code-cell}
using Random
Random.seed!(1234);
data = gen_data(n = 1000, pi = 0.8)
first(data, 10)

```

### EM algorithm: top-level function

The EM algorithm starts with an initial guess for $\pi$, $\hat{\pi}_0$. Then it iterates between two steps:

* E step: compute the cluster membership probabilities, $p(X|Y)$, for each observed outcome $Y$, using the current estimate of $\hat{\pi}$
* M step: re-estimate $\hat{\pi}$ using the previously estimated cluster membership probabilities, $\hat p(X|Y)$


```{code-cell}

# The `!` in the function name `function fit_model!()` indicates that this function may have side-effects; specifically, in this function, `data` and `progress` be passed by reference and modified

  function fit_model!(
    data; # one positional argument
    pi_hat_0 = 0.5, 
    tolerance = 0.00001,
    max_iterations = 1000,
    progress = DataFrame(
        iter = 1:(max_iterations+1), 
        pi_hat = Vector{Float64}(undef, max_iterations+1), 
        ll = Vector{Float64}(undef, max_iterations+1), 
        ll_diff = Vector{Float64}(undef, max_iterations+1)
        )
    )

    pi_hat = pi_hat_0
    E_step!(data, pi_hat) # E step
    ll = loglik!(data)
    # progress = [(iter = 0, pi_hat, ll, ll_diff = NaN)]
    progress[1,:] = (0, pi_hat, ll, NaN)
    
    last_iter = 0
    for i in 1:max_iterations
        pi_hat = M_step(data)
        E_step!(data, pi_hat)
        
        ll_old = ll
        ll = loglik!(data)
        ll_diff = ll - ll_old
        progress[i+1,:] = (i, pi_hat, ll, ll_diff)

        if ll_diff < tolerance
            last_iter = i
            break
        end
    end
    return progress[1:last_iter, :]
  end
```

### E step subroutine

The E step uses [Bayes' Theorem][bayes] to compute $\hat p(X|Y)$ using the current estimate of $\hat\pi$.

[bayes]: https://en.wikipedia.org/wiki/Bayes%27_theorem

```{code-cell}

function E_step!(data, pi_hat)
    @transform!(data, :pY_Z1 = :p_Y_given_Z_1 .* pi_hat)
    @transform!(data, :pY_Z2 = :p_Y_given_Z_2 .* (1- pi_hat))
    @transform!(data, :pY = :pY_Z1 + :pY_Z2)
    @transform!(data, :pZ1_given_Y = :pY_Z1 ./ :pY)
end
```
### M step subroutine

The M step treats the possible cluster memberships as weighted data, with the weights determined by the cluster membership probabilities from the E step.

```{code-cell}
function M_step(data)
    mean(data[!, :pZ1_given_Y])
end
```
### Convergence criterion

We check for convergence using the change in the observed-data log-likelihood:

```{code-cell}
function loglik!(data)
    sum(log.(data[!, :pY]))
end
```
### Timing our implementation

Let's speed-test our implementation:
```{code-cell}

@time results = fit_model!(data);
```

Note that most of the computation time was for pre-compiling our code. A second call to `fit_model!()` will be faster, since re-compilation isn't needed:

```{code-cell}

# for a fair comparison, let's reset `data` to its original state, since `gen_data()` augmented the previous copy.
Random.seed!(1234);
data = gen_data(n = 1000, pi = 0.8)

@time results = fit_model!(data);
```

### Examine the results

```{code-cell}
results
```

### Graph the algorithm's path towards the MLE

```{code-cell}
using Gadfly

Gadfly.plot(
  results,
  x = :pi_hat,
  y = :ll,
  Geom.point,
  Geom.line)

```
Compare these results with [an implementation in R][r_em]. Despite very similar code, the Julia implementation is much faster!

[r_em]: https://d-morrison.github.io/EM.examples/articles/mixmodel-benchmark-r.html#results

Network Construction
--------------------

The [Armed Conflict Location & Event Data][ACLED] (ACLED) are a collection of
data about political violence and protests around the world. In a recent
exploratory project with Ashish Shenoy (UC Davis) and Eoin McGuirk (Tufts), we
studied the relationships between events and tried to identify sequences of
related events. The ACLED consist of millions of records; we focused on data
about Africa from 2016 to 2020, which consists of 112,926 records. One of our
goals was to construct a network of related events based on nearness in time
and space and commonality of participants.

[ACLED]: https://acleddata.com/

We first developed a Python script to construct the network (as an edge list).
The script was prohibitively slow, so we translated it into Julia. The
translation required only minor (mostly syntactic) changes to the code. We also
developed an "optimized" version of the Python script that uses a more
efficient strategy for distance computations.

For comparison, here's the code for all three:


::::{tab-set}

:::{tab-item} Julia
```{code-block} julia
---
linenos: true
---
# Timing estimate: 7.41 seconds
using DataFrames
using Dates
using Distances
using Parquet


function pair_events_on_date(date, events, max_days = Dates.Day(30), max_km = 80)
    # Get all events on the date.
    d₀ = Dates.Day(0)
    dist_days = events[:, :event_date] - date
    events_on_date = events[dist_days .== d₀, :]

    # Get all events in max_days window.
    is_in_window = (d₀ .<= dist_days) .& (dist_days .<= max_days)
    events_in_window = events[is_in_window, :]
    events_in_window[:, :dist_days] .= dist_days[is_in_window]

    # For each event on date, get km distance to events in window.
    xy_date = Matrix{Float64}(events_on_date[:, [:x, :y]])'
    xy_window = Matrix{Float64}(events_in_window[:, [:x, :y]])'
    dist_km = pairwise(Euclidean(), xy_window, xy_date, dims = 2)
    dist_km ./= 1_000

    all_pairs = []
    for (i, event) in enumerate(eachrow(events_on_date))
        is_in_radius = dist_km[:, i] .<= max_km
        pairs = copy(events_in_window[is_in_radius, :])
        pairs[:, :dist_km] = dist_km[is_in_radius, i]
        pairs[:, :event1_id] .= event[:data_id]

        push!(all_pairs, pairs)
    end

    result = vcat(all_pairs...)
    select!(result, Not([:x, :y]), :data_id => :event2_id)
    return result
end


function make_proximal_events_edgelist(events)
    dates = unique(events[:, :event_date])
    
    paired = [pair_events_on_date(d, events) for d in dates]
    paired = vcat(paired...)
    
    return paired
end


# Read the data.
path = "../output/acled_africa_2016_2020.parquet"
df = DataFrame(read_parquet(path))
cols = [:event_date, :x, :y, :actor1, :actor2, :data_id]
df = df[:, cols]

# Convert dates to Date type.
dates = unix2datetime.(df[:, :event_date] / 1e6)
dates = convert.(Date, dates)
df[!, :event_date] .= dates;

make_proximal_events_edgelist(df)
```
:::


:::{tab-item} Python (naive)
```{code-block} python
---
linenos: true
---
# Timing estimate: 2040.30 seconds
import geopandas as gpd
import pandas as pd


def pair_events_on_date(
        date
        , events
        , max_days = pd.Timedelta(30, "days")
        , max_km = 80):
    # Get all events on the date.
    ZERO_DAYS = pd.Timedelta(0, "days")
    dist_days = events["event_date"] - date
    events_on_date = events.loc[dist_days == ZERO_DAYS]

    # Get all events in max_days window.
    is_in_window = (ZERO_DAYS <= dist_days) & (dist_days <= max_days)
    events_in_window = events.loc[is_in_window, :].copy()
    events_in_window.loc[:, "dist_days"] = dist_days[is_in_window]

    # For each event on date, get km distance to events in window.
    all_pairs = []
    for event in events_on_date.itertuples():
        # Compute distance and convert meters to kilometers.
        dist_km = events_in_window.distance(event.geometry)
        dist_km /= 1_000

        # Get all events in max_km radius.
        is_in_radius = (dist_km <= max_km)
        pairs = events_in_window.loc[is_in_radius, :].copy()
        pairs.loc[:, "dist_km"] = dist_km[is_in_radius]
        pairs.loc[:, "event1_id"] = event.data_id

        all_pairs.append(pd.DataFrame(pairs))

    # Clean up colnames and remove match with self.
    all_pairs = pd.concat(all_pairs, axis = 0)
    all_pairs.rename({"data_id": "event2_id"}, axis = 1, inplace = True)
    #all_pairs.query("event1_id != event2_id", inplace = True)
    all_pairs.drop("geometry", axis = 1, inplace = True)
    return all_pairs


def make_proximal_events_edgelist(events):
    dates = events["event_date"].unique()

    paired = [pair_events_on_date(d, events) for d in dates]
    paired = pd.concat(paired, axis = 0)

    paired.reset_index(drop = True, inplace = True)

    return paired


# Read the data.
path = "../output/acled_africa_2016_2020.parquet"
df = gpd.read_parquet(path)

make_proximal_events_edgelist(df)
```
:::


:::{tab-item} Python (optimized)
```{code-block} python
---
linenos: true
---
# Timing estimate: 367.60 seconds
import pandas as pd
from scipy.spatial.distance import cdist


def pair_events_on_date(
        date
        , events
        , max_days = pd.Timedelta(30, "days")
        , max_km = 80):
    # Get all events on the date.
    ZERO_DAYS = pd.Timedelta(0, "days")
    dist_days = events["event_date"] - date
    events_on_date = events.loc[dist_days == ZERO_DAYS]

    # Get all events in max_days window.
    is_in_window = (ZERO_DAYS <= dist_days) & (dist_days <= max_days)
    events_in_window = events.loc[is_in_window, :].copy()
    events_in_window.loc[:, "dist_days"] = dist_days[is_in_window]
    
    xy_date = events_on_date.loc[:, ["x", "y"]].to_numpy()
    xy_window = events_in_window.loc[:, ["x", "y"]].to_numpy()
    dist_km = cdist(xy_window, xy_date, "euclidean") / 1_000

    # For each event on date, get km distance to events in window.
    all_pairs = []
    for i, event in enumerate(events_on_date.itertuples()):
        # Get all events in max_km radius.
        is_in_radius = (dist_km[:, i] <= max_km)
        pairs = events_in_window.loc[is_in_radius, :].copy()
        pairs.loc[:, "dist_km"] = dist_km[is_in_radius, i]
        pairs.loc[:, "event1_id"] = event.data_id

        all_pairs.append(pd.DataFrame(pairs))

    # Clean up colnames and remove match with self.
    all_pairs = pd.concat(all_pairs, axis = 0)
    all_pairs.rename({"data_id": "event2_id"}, axis = 1, inplace = True)
    #all_pairs.query("event1_id != event2_id", inplace = True)
    all_pairs.drop(["x", "y"], axis = 1, inplace = True)
    return all_pairs


def make_proximal_events_edgelist(events):
    dates = events["event_date"].unique()

    paired = [pair_events_on_date(d, events) for d in dates]
    paired = pd.concat(paired, axis = 0)

    paired.reset_index(drop = True, inplace = True)

    return paired


# Read the data.
path = "../output/acled_africa_2016_2020.parquet"
df = pd.read_parquet(path)
cols = ["event_date", "x", "y", "actor1", "actor2", "data_id"]
df = df.loc[:, cols]

make_proximal_events_edgelist(df)
```
:::

::::

We benchmarked the scripts on a consumer-grade laptop (Intel Core i7-5500U at
2.4GHz; 12GB RAM). In our tests, the Python script took approximately 291 times
longer to run than the Julia script (2040 seconds versus 7 seconds). Even the
optimized Python script took approximately 52 times longer to run than the
Julia script (367 seconds). Note that in the Julia script we only specified
types where required; it may be possible to make the script even faster by
adding more type information or taking advantage of other optimization
strategies for Julia code.
