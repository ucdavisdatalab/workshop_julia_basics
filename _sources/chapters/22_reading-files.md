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

Reading Files
=============

Reading a data set is the first step in most analyses.


Navigating File Systems
-----------------------

Julia's `Base.Filesystem` module provides functions for working with file
systems. Here are a few that are useful for checking the working directory and
that files are where you think they are:

| Function  | Description                          |
| :-        | :-                                   |
| `pwd`     | Get the working directory            |
| `cd`      | Change the working directory         |
| `readdir` | List files in a directory            |
| `ispath`  | Check whether a path exists          |
| `isfile`  | Check whether a path leads to a file |
| `isdir`   | Check whether a path leads to a file |

The Julia documentation has a [complete list of functions in the
`Base.Filesystem` module][filesystem].

[filesystem]: https://docs.julialang.org/en/v1/base/file/

<!--
TODO: Add examples and have the user use `Downloads.download` to download a
file
-->

Example: Reading a CSV File
---------------------------

<!--
TODO: Host the data somewhere so that folks can follow along more easily.
-->

The [U.S. Bureau of Transportation Statistics][bts] publishes and regularly
updates the [Airline On-Time Performance Data Set][airline-data], which
includes departure and arrival times for all domestic flights since 1987. The
data set is distributed as a collection of comma-separated value (CSV) files,
with one for each month-year combination. You can download a subset of the data
set [here][airline-download].

[bts]: https://www.bts.gov/
[airline-data]: https://www.transtats.bts.gov/tables.asp?qo_vq=EFD
[airline-download]: #

Let's try reading the data set into Julia. There's no built-in function to read
CSV files, but the [CSV.jl][] package provides one. The CSV format is tabular,
so let's read the data as a data frame (a tabular data structure). In Julia,
the [DataFrames.jl][] package provides data frames. Install both packages if
you haven't already, and then load them:

[DataFrames.jl]: https://github.com/JuliaData/DataFrames.jl

::::{tab-set}

:::{tab-item} Load
```julia
using CSV
using DataFrames
```
:::

:::{tab-item} Install
```julia
using Pkg

Pkg.add("CSV")
Pkg.add("DataFrames")
```
:::
::::

```{code-cell}
:tags: ["remove-cell"]
using CSV
using DataFrames
```

You can read a CSV file with the `CSV.read` function:

```{code-cell}
path = "data/On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2023_1.csv"
air = CSV.read(path, DataFrame)
typeof(air)
````

The second argument is a **sink function**, a function that converts raw
tabular data to a specific Julia type. The sink function determines the type of
the value `CSV.read` returns. In this case, the returned value is a
`DataFrame`.

:::{note}
The [Tables.jl][] package defines a standard programming interface for working
with tabular data in Julia. The CSV.jl and DataFrames.jl packages both use this
interface, as well as [many other Julia packages][tables-integrations].

Any type that satisfies the Tables.jl interface is called a **source**. For
example, the CSV.jl package's `CSV.File` type is a source, although the
`CSV.read` function hides this detail. Any function that can take a source
instance as input and return a table of a specific type is called a sink. The
DataFrames.jl package's `DataFrame` function is a sink. Coincidentally, the
`DataFrame` type is also a source (this facilitates transformations of data
frames).

The CSV.jl package also provides a way to read a CSV file:
```julia
source = CSV.File(path)

# air = DataFrame(source)
```
Compared to `CSV.read`, this is less efficient for reading a CSV as a data
frame: it makes a copy of the data.
:::

[Tables.jl]: https://github.com/JuliaData/Tables.jl
[tables-integrations]: https://github.com/JuliaData/Tables.jl/blob/main/INTEGRATIONS.md

You can use the `first` function to get just the first few rows of a data
frame:

```{code-cell}
first(air, 5)
```

The second argument, `5`, is the number of rows to return.

The `describe` function is also useful for inspecting data frames. It provides
a statistical summary of each column:

```{code-cell}
describe(air)
```

:::{tip}
You can learn more about how to use data frames from the DataFrames.jl
[documentation][df-docs] and [cheat sheet][df-cheat].
:::

[df-docs]: https://dataframes.juliadata.org/
[df-cheat]: https://www.ahsmart.com/assets/pages/data-wrangling-with-data-frames-jl-cheat-sheet/DataFramesCheatSheet_v1.x_rev1.pdf


Reading and Writing Formatted Files
-----------------------------------

Functions to read and write popular file formats are generally provided by
packages rather than built into Julia. Here are a few (find more by searching
online):


| Format                       | Extension               | Package
| :--------------------------- | :---------------------- | :---------------
| [Apache Arrow][arrow]        | `.arrow`                | [Arrow-julia.jl][]
| Delimited File               | `.csv`, `.tsv`, ...     | [CSV.jl][]
| Fixed-width File             | `.fwf`                  | Planned for [CSV.jl][]
| Geospatial Vector Data       | `.geojson`, `.shp`, ... | [ArchGDAL.jl][]
| HDF5                         | `.hdf5`                 | [HDF5.jl][]
| JavaScript Object Notation   | `.json`                 | [JSON.jl][]
| [Apache Parquet][parquet]    | `.parquet`              | [Parquet.jl][]
| Geospatial Raster Data       | `.tiff`, ...            | [ArchGDAL.jl][]
| [TOML][toml]                 | `.toml`                 | [built-in][toml-jl]
| Microsoft Excel              | `.xls`, `.xlsx`         | [XLSX.jl][]
| Extensible Markup Language   | `.xml`                  | [XML.jl][]
| [YAML][yaml]                 | `.yaml`                 | [YAML.jl][]
| [MessagePack][msgpack]       |                         | [MsgPack.jl][]

[Arrow-julia.jl]: https://github.com/apache/arrow-julia
[CSV.jl]: https://github.com/JuliaData/CSV.jl
[ArchGDAL.jl]: https://github.com/yeesian/ArchGDAL.jl
[HDF5.jl]: https://github.com/JuliaIO/HDF5.jl
[JSON.jl]: https://github.com/JuliaIO/JSON.jl
[Parquet.jl]: https://github.com/JuliaIO/Parquet.jl
[toml-jl]: https://docs.julialang.org/en/v1/stdlib/TOML/
[XLSX.jl]: https://github.com/felipenoris/XLSX.jl
[XML.jl]: https://github.com/JuliaComputing/XML.jl
[YAML.jl]: https://github.com/JuliaData/YAML.jl
[MsgPack.jl]: https://github.com/JuliaIO/MsgPack.jl

[arrow]: https://arrow.apache.org/
[parquet]: https://parquet.apache.org/
[toml]: https://toml.io/
[yaml]: https://yaml.org/
[msgpack]: https://msgpack.org/

Often there's more than one package with functions to read and write a specific
format. We chose the packages in the table based on popularity and signs of
active development or maintenance.


Reading and Writing Text and Bytes
----------------------------------

Sometimes you might need to read and write text or bytes directly. For example,
you might need to work with a file that has an obscure or custom format.

Julia's built-in `open` function opens a file. Let's open a file called
`hello.txt` in write mode:

```{code-cell}
file = open("hello.txt", "w")
```

You can use the `print`, `println`, or `write` function to write to a file. Try
it out with a few lines:

```{code-cell}
write(file, "Don't worry, be happy!\n")
```

```{code-cell}
println(file, "Hello world!")
```

```{code-cell}
print(file, "Print?\n")
```

:::{important}
The `print` and `println` functions convert objects to strings in the encoding
of the open file (set by the call to `open`; the default is UTF-8) before
output.

The `write` function outputs objects as bytes. For strings, this means the
encoding is determined by the string itself rather than the file.

In general, use `print` and `println` to write text to files, and use `write`
to write bytes to files.
:::

Make sure to use the `close` function to close files when you're finished using
them. When writing to a file, this ensures that all of the writes are
completed.

```{code-cell}
close(file)
```

Now let's read the file to check that the lines were written. The idiomatic way
to open a file and do something (read or write) with it is to use a `do` block.
Try running this code to read the lines from the file:

```{code-cell}
lines =
    open("hello.txt") do f
        readlines(f)
    end
```

A `do` block is syntactic sugar for defining an anonymous function and passing
it as the first argument to another function. As an example, consider this call
with an anonymous function as the first argument:

```{code-cell}
map(x -> x^2, [1, 2, 3])
```

The equivalent using a `do` block is:

```{code-cell}
map([1, 2, 3]) do x
    x^2
end
```

:::{caution}
Since a `do` block defines an anonymous function, variables you define inside
of the block are not visible from outside of the block.
:::

The `open` function has a method that takes a function as its first argument.
The file is opened, the function is called on the open file, the file is
closed, and the result is returned. This approach is safer than manually
closing the file with `close`, because the `open` function ensures that the
file is always closed, even if something goes wrong in the computation.




<!--
`read`
`readchomp`
-->

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
