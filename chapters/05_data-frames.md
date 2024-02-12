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

This chapter uses the Airline On-Time Performance Data Set introduced in
{numref}`reading-and-writing-data`.


(inspecting-dataframe)=
Inspecting
----------

When you load a data set, it's a good idea to inspect it to make sure it was
loaded correctly and contains the data you expect. Julia and DataFrames.jl
provide several functions that are helpful for inspecting data frames:

* `describe` to get a summary
* `first`, `last` to get the first or last n rows
* `nrow`, `ncol`, `size`, `ndims` to get dimension information
* `names` to get column names
* `typeof`, `eltype` to get types

Let's take a look at the first 5 rows of the `air` data to refresh our memory:

```{code-cell}
first(air, 5)
```

The `size` function returns the number of rows and columns in a data frame (you
can use `nrow` and `ncol` to get these individually):

```{code-cell}
size(air)
```

The `names` function returns the names of a data frame's columns:

```{code-cell}
names(air)
```

:::{tip}
By default, Julia tries to make printed output fit on the screen. This is
unhelpful when you want to see all of a particular data structure. You can use
the `print` function to make Julia show everything. For instance, try this
code:

```julia
print(names(air))
```
:::

One way to characterize a data frame is by the types of elements in its
columns. In a Julia data frame, columns are generally Vectors, and the `eltype`
function gets the element type(s) of a Vector. To get the element types for all
columns, use the `eachcol` function to get an iterator over the columns, and
then broadcast `eltype` over the iterator:

```{code-cell}
eltype.(eachcol(air))
```

We can make this result easier to read by putting it in a data frame with the
column names (and possibly other summary information). The constructor function
`DataFrame` makes a new data frame:

```{code-cell}
air_types = DataFrame(name = names(air), type = eltype.(eachcol(air)))
air_types
```


Indexing
--------

Data frames use square brackets `[ ]` for indexing (like most other data
structures in Julia). Since data frames are two-dimensional, two indexes are
required. The following subsections describe different kinds of indexes you can
use, as well as some other ways to get data out of a data frame.

### By Position

You can use integer arguments to select elements by position. For example, to
extract the value in row 2, column 1 of the `air` data frame:

```{code-cell}
air[2, 1]
```

The first argument is the row index, while the second is the column index.


As with other data structures, you can also use an array of indexes to select
multiple values. For instance, to get rows 1, 3, and 1 again from column 5:

```julia
air[[1, 3, 1], 5]
```

You can also use a slice to select a range of values. For instance, to select
the values in the first 3 rows, column 5:

```{code-cell}
air[1:3, 5]
```

:::{tip}
You can use the `end` keyword in a slice to mean the last element. The `end`
keyword can be combined with arithmetic operators. For example, to get the last
2 rows, column 5:

```julia
air[end-1:end, 5]
```
:::

In DataFrames.jl, there are two different ways to indicate that you want all of
the elements along a dimension. A `:` selects all elements and returns a
*copy*, while a `!` selects all elements and returns a *view* (or reference).
It's generally safer to use a copy (especially if you're going to modify the
data), but more CPU- and memory-efficient to use a view. Here are examples of
both:

```{code-cell}
year = air[!, 1]
year_copy = air[:, 1]
```

:::{caution}
Returning a view with `!` is only possible if the resulting data are contiguous
in the original data frame.
:::

:::{tip}
More generally, you can use the `@view` macro to get a view based on indexing
even when you don't want all elements along an axis. For example:

```julia
@view air[1, 1]
```
:::


You can combine indexing with assignment (`=`) to reassign specific elements of
a data frame. Note that if you reassign elements of a view, the elements will
also change in the original data frame.


### By Name

You can use String arguments to select elements by name. For instance, to
select row 1 of the `Year` column:

```{code-cell}
air[1, "Year"]
```

You can also use Symbol arguments to select elements by name. In Julia, you can
write a literal Symbol by putting a colon `:` in front of text. For instance,
to select row 1 of the `Year` column:

```{code-cell}
air[1, :Year]
```

Indexing with Symbols is faster than indexing with Strings, so use Symbols when
possible.

As with positional indexes, you can use arrays of indexes to select multiple
elements. For example:

```{code-cell}
air[1:3, [:Year, :Month, :DayofMonth]]
```

Selection by name is primarily used for columns, since rows usually don't have
names. If you want to select an entire column, there are two more ways to do
it besides `[ ]`: attribute access (`.NAME`) and the `select` function. As an
example, here are three ways to select the entire `DayofMonth` column:

```{code-cell}
air[:, "DayofMonth"]
air.DayofMonth
select(air, :DayofMonth)
```

Note that the first two return arrays, while `select` returns a data frame.


### By Condition

The indexing operator `[ ]` also accepts arrays of Boolean values, to
facilitate getting elements based on a condition. For example, suppose you want
to get all rows where `DayofMonth` is less than `15`. You can test for these
rows with this condition:

```{code-cell}
air.DayofMonth .< 15
```

And the code to actually get the rows is:

```{code-cell}
air[air.DayofMonth .< 15, :]
```

DataFrames.jl also provides a `subset` function as an alternative (often more
efficient) way to get subsets. The first argument to the `subset` function is
the data set, while the second argument is a `Pair` that describes a condition.
In Julia, a `Pair` is a helper data structure that pairs two pieces of
information, and can be created with the `=>` operator. In this case, the Pair
should pair column name(s) with a test function to apply to the column(s). For
example, the anonymous function `x -> x .< 15` tests whether the elements of an
array are less than 15, so you can get all rows where `DayofMonth` is less than
`15` with this code:

```{code-cell}
subset(air, :DayofMonth => x -> x .< 15)
# Or: subset(air, :DayofMonth => ByRow(x -> x < 15))
```


Grouping & Aggregating
----------------------

Aggregation is especially useful when combined with grouping. You can group
sets of rows in a data frame with the `groupby` function. Its first argument is
the data and its second is the grouping columns. For example, to group the
`air` data by `Year`:

```{code-cell}
groupby(air, :Year)
```

Once data is grouped, you can use the `combine` function to call a function on
each group (or columns within a group).  For instance, to get the frequency for
each day of week value:

```{code-cell}
combine(groupby(air, :DayOfWeek), nrow)
```

You can also use `combine` with a Pair in the second argument to apply a
function to specific columns. For example, to get the mean (non-missing) delay
for flights by day of week:

```{code-cell}
using Statistics

combine(groupby(air, :DayOfWeek), :DepDelay => x -> mean(skipmissing(x)))
```

The DataFrames.jl documentation provides more examples of ways you can use
`groupby` and `combine`.



Helper Packages
---------------

The DataFrames.jl programming interface may seem awkward or excessively verbose
if you're coming to Julia from R or Python. The community is aware of this and
as a result, there are now several packages that provide more familiar
programming interfaces. In particular:

* [DataFramesMeta.jl][] provides a macro interface similar to R's dplyr
  package.
* [Pandas.jl][] is a wrapper around Python's Pandas package.

[DataFramesMeta.jl]: https://github.com/JuliaData/DataFramesMeta.jl
[Pandas.jl]: https://github.com/JuliaPy/Pandas.jl

