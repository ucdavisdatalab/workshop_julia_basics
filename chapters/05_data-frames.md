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

using CSV
using DataFrames
path = "data/On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2023_1.csv"
air = CSV.read(path, DataFrame)
```

(data-frames)=
Data Frames
===========

Data frames are the premier data structure for working with tabular data. In
Julia, the [DataFrames.jl][] package provides data frames and functions to work
with them. Besides reading this chapter, you can learn more about how to use
the package from the [official documentation][df-docs] and [cheat
sheet][df-cheat].

[DataFrames.jl]: https://github.com/JuliaData/DataFrames.jl
[df-docs]: https://dataframes.juliadata.org/
[df-cheat]: https://www.ahsmart.com/assets/pages/data-wrangling-with-data-frames-jl-cheat-sheet/DataFramesCheatSheet_v1.x_rev1.pdf


(inspecting-dataframe)=
Inspecting a DataFrame
----------------------

:::{warning}
This section is still a work in progress.
:::

Everything looks good here. To see the bottom of this data, use `last`:

```{code-cell}
last(air, 5)
```

One way to get a quick idea of what your data looks like without having to read
through all the columns and rows is by inspecting its dimensions. You can use
the `nrow` and `ncol` functions to do this:

```{code-cell}
nrow(air)
```

```{code-cell}
ncol(air)
```

The `names` function returns the names of a data frame's columns:

```{code-cell}
names(air)
```

(summarizing-columns)=
### Selecting Columns

Individual columns may be selected with **bracket notation**. Put the name of
the column in quotes and place that inside of square brackets `[]`:

```
air[!, "FlightDate"]
```

Finally, you can assign new values to a DataFrame using the same notation as
above. Below, this code overwrites all the values in the `currency_code`
column:

```
banknotes["currency_code"] = "USD"
banknotes["currency_code"]
```

The next chapter will return to working with columns, showing you how to
generate new data from a DataFrame. You'll also learn how to select rows and
subsets of the data, as well as groups of columns.
