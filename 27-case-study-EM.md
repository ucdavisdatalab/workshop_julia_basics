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
pwd()
```

# Implementing an EM algorithm for Gaussian mixture modeling

The following code implements an Expectation-Maximization (EM) algorithm to find the maximum likelihood estimate of the mixing parameter of a two-component Gaussian mixture model (a form of k-means clustering which assumes that each cluster has a Gaussian distribution; c.f. https://scikit-learn.org/stable/modules/mixture.html). This "algorithm"^[it's really a meta-algorithm framework, not a specific algorithm] is a very frequently-used approach for finding maximum likelihood estimates.

In this case, we are going to assume that we already know the distribution of data from each component, and we only need to estimate the mixing probability (the relative sizes of the clusters), $\pi$.

## Data-generating simulation

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

## EM algorithm: top-level function

The EM algorithm starts with an initial guess for $pi$, $\hat{\pi}_0$. Then it iterates between two steps:

* E step: compute the cluster membership probabilities, $p(X|Y)$, for each observed outcome $Y$, using the current estimate of $\hat{\pi}$
* M step: re-estimate $\hat{\pi}$ using the previously estimated cluster membership probabilities, $\hat p(X|Y)$


```{code-cell}

# The `!` in the function name `function fit_model!()` indicates that this function may have side-effects; specifically, in this function, `data` and `progress` be passed by reference and modified

  function fit_model!(
    data; # one positional argument
    pi_hat_0 = 0.5, 
    tolerance = 0.0001,
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

# E step subroutine

The E step is an application of (Bayes' Theorem)[https://en.wikipedia.org/wiki/Bayes%27_theorem]:

```{code-cell}

function E_step!(data, pi_hat)
    @transform!(data, :pY_Z1 = :p_Y_given_Z_1 .* pi_hat)
    @transform!(data, :pY_Z2 = :p_Y_given_Z_2 .* (1- pi_hat))
    @transform!(data, :pY = :pY_Z1 + :pY_Z2)
    @transform!(data, :pZ1_given_Y = :pY_Z1 ./ :pY)
end
```
# M step subroutine

The M step treats the possible cluster memberships as weighted data, with the weights determined by the cluster membership probabilities from the E step.

```{code-cell}
function M_step(data)
    mean(data[!, :pZ1_given_Y])
end
```

We check for convergence using the change in the observed-data log-likelihood:

```{code-cell}
function loglik!(data)
    sum(log.(data[!, :pY]))
end
```

Let's speed-test our implementation:
```{code-cell}
data = gen_data(n = 1000, pZ1 = 0.8)
@time fit_model!(data)
```
