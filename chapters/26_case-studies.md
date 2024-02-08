---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Julia
  language: julia
  name: julia-1.9
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


