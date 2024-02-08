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

Data Visualization
==================


Adapted from https://ucla-biostat-257.github.io/2023spring/slides/04-juliaplot/juliaplots.html

```{code-cell "load graphics packages"}
using Gadfly, Plots, PlotlyJS, PyPlot, Random, RCall
```

Let's load some data into Julia:

```{code-cell "prep-data-julia"}
using RDatasets, DataFramesMeta, Statistics
df = 
  @chain RDatasets.dataset("datasets", "ToothGrowth") begin
    groupby([:Supp, :Dose])
    @combine begin
      :se = std(:Len) / length(:Len)
      :len = mean(:Len)
      :n = length(:Len)
    end
  end

df


```

## Gadfly

`Gadfly.jl` is like `ggplot2` in R:

```{code-cell "gadfly"}
using Gadfly
df[!, :ymin] = df[!, :len] - df[!, :se]
df[!, :ymax] = df[!, :len] + df[!, :se]
Gadfly.plot(df, 
    x = :Dose, 
    y = :len, 
    color = :Supp, 
    Geom.point,
    Guide.xlabel("Dose"), 
    Guide.ylabel("Tooth Length"), 
    Guide.xticks(ticks = [0.5, 1.0, 1.5, 2.0]),
    Geom.line, 
    Geom.errorbar, 
    ymin = :ymin, 
    ymax = :ymax, 
    Scale.color_discrete_manual("orange", "skyblue"))
```

Gadfly provides many customization options:

```{code-cell}
Gadfly.plot(df, x = :Dose, y = :len, color = :Supp, Geom.point,
    Guide.xlabel("Dose"), Guide.ylabel("Tooth Length"), 
    Guide.xticks(ticks = [0.5, 1.0, 1.5, 2.0]),
    Theme(panel_fill = nothing, highlight_width = 0mm, point_size = 0.5mm,
    key_position = :inside, 
    grid_line_width = 0mm, panel_stroke = colorant"black"),
    Geom.line, Geom.errorbar, ymin = :ymin, ymax = :ymax, 
    Scale.color_discrete_manual("orange", "skyblue"))
```

## Plots.jl

```{code-cell}
using Plots, Random

Random.seed!(123) # set seed
x = cumsum(randn(50, 2), dims=1);
```

### Plots.jl using `PyPlot` backend
```{code-cell}
# Pkg.add("PyPlot")
pyplot()  # set the backend to PyPlot
Plots.plot(x, title="Random walk", xlab="time")
```

### Plots.jl using `GR` backend

```{code-cell}
gr()   # change backend to GR
Plots.plot(x, title="Random walk", xlab="time")
```

### Plotly backend

```{code-cell}
# Pkg.add("PlotlyJS")
plotlyjs()  # change backend to PlotlyJS
Plots.plot(x, title="Random walk", xlab="time")
```

