<!--
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
-->

Working with Tabular Data
=========================

Reading a data set is the first step in most analyses. Members of the Julia
community have created packages for reading many common formats. To determine
which package to use, first you need to identify the data set's format.

The table below shows several formats that are frequently used to distribute
data sets.

| Format                      | Extension       | Package
| :-------------------------- | :-------------- | :--------------
| [Apache Arrow][arrow]       | `.arrow`        | [Arrow-julia.jl][]
| Delimited File              | `.csv`, `.tsv`  | [CSV.jl][]
| Fixed-width File            | `.fwf`          | Planned for [CSV.jl][]
| HDF5                        | `.hdf5`         | [HDF5.jl][]
| JavaScript Object Notation  | `.json`         | [JSON.jl][]
| [Apache Parquet][parquet]   | `.parquet`      | [Parquet.jl][]
| Microsoft Excel             | `.xls`, `.xlsx` | [XLSX.jl][]
| Extensible Markup Language  | `.xml`          | [XML.jl][]
| Arbitrary File              |                 |

[CSV.jl]: https://github.com/JuliaData/CSV.jl
[XLSX.jl]: https://github.com/felipenoris/XLSX.jl
[HDF5.jl]: https://github.com/JuliaIO/HDF5.jl
[Arrow-julia.jl]: https://github.com/apache/arrow-julia
[Parquet.jl]: https://github.com/JuliaIO/Parquet.jl
[XML.jl]: https://github.com/JuliaComputing/XML.jl
[JSON.jl]: https://github.com/JuliaIO/JSON.jl

[arrow]: https://arrow.apache.org/
[parquet]: https://parquet.apache.org/

A tabular data set is one that's structured as a table, with rows and columns.
We'll focus on tabular data sets for most of this reader, since they're easier
to get started with.

A **text** file is one that contains human-readable lines of text. You can
check this by opening the file with a text editor such as Microsoft Notepad or
macOS TextEdit. Many file formats use text in order to make the format easier
to work with. For instance, a comma-separated values (CSV) file records a
tabular data using one line per row, with commas separating columns.

A **binary** file is one that's not human-readable. You can't just read off the
data if you open a binary file in a text editor, but they have a number of
other advantages. Compared to text files, binary files are often faster to read
and take up less storage space (bytes).


Ways to Read a CSV
==================

```julia
using Pkg

Pkg.add("CSV")
#Pkg.add("DataFrames")
```

```julia
using CSV

path = "data/On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2023_1.csv"
x = CSV.File(path)
```

What is [Tables.jl][]

```julia
#Pkg.add("DataFrames")
using DataFrames

x = CSV.read(path, DataFrame)
```


[Tables.jl]: https://github.com/JuliaData/Tables.jl
[DataFrames.jl]: https://github.com/JuliaData/DataFrames.jl

<!-- How to read other kinds of delimited data? Other formats? -->


(reading-files)=
Reading Files
=============

### Hello, Data!

Over the next few sections, you'll explore data about banknotes and the people 
depicted on them. This data is derived from a data set compiled by [The 
Pudding][pud], which features [an article][art] about it. To download the 
version you'll need for this workshop, click [here][data]. You may need to 
choose `File -> Save As...` in your browser's menu.

[pud]: https://pudding.cool/
[art]: https://pudding.cool/2022/04/banknotes/
[data]: https://raw.githubusercontent.com/ucdavisdatalab/workshop_r_basics/dev/data/banknotes.csv

The data set is a file called `banknotes.csv`, which suggests it's a CSV file.
In this case, the extension is correct, so you can read the file with Pandas'
`read_csv` function. The first argument is the path to where you saved the
file, which may be different on your computer. The `read_csv` function returns
the data set, but Python won't keep the data in memory unless you assign the
returned result to a variable:

```{code-cell}
:tags: [output_scroll]
import pandas as pd

banknotes = pd.read_csv("data/banknotes.csv")
banknotes
```

The variable name `banknotes` here is arbitrary; you can choose something
different if you want. However, in general, it's a good habit to choose
variable names that describe the contents of the variable somehow.

If you tried running the line of code above and got an error message, pay
attention to what the error message says, and remember the strategies to get
help in {numref}`getting-help`. The most common mistake when reading a file is
incorrectly specifying the path, so first check that you got the path right.

If you ran the line of code, there was no error message, and you can see a
table of data, then congratulations, you've read your first data set into
Python!


(inspecting-dataframe)=
Inspecting a DataFrame
----------------------

Now that you've loaded the data, you can take a look at it. When working with a
new data set, it often isn't a good idea to print the whole thing to screen, at
least until you know how big it is. Large data sets can take a long time to
print, and the output can be difficult to read.

Instead, use the Pandas `.head` method to print only the beginning, or head, of
the data.

```{code-cell}
:tags: [output_scroll]
banknotes.head()
```

This data is tabular, as you might have already guessed, since it came from a
CSV file. Pandas represents it as a **DataFrame**: data structured as rows and
columns. In general rows are observations and columns are variables. Each entry
is called a cell.

You can check to make sure Pandas has indeed created a DataFrame with the
`type` function, which is discussed in more detail in {numref}`data-types`:

```{code-cell}
type(banknotes)
```

Everything looks good here. To see the bottom of this data, use `tail`:

```{code-cell}
:tags: [output_scroll]
banknotes.tail()
```

Both `head` and `tail` accept an optional argument that specifies the number of
rows to print to screen:

```{code-cell}
:tags: [output_scroll]
banknotes.head(10)
```

If there are many columns in your DataFrame, as is the case here, Pandas will
often squeeze the output into a condensed display, with `...` representing
additional columns.

One way to get a quick idea of what your data looks like without having to
shuffle through all the columns and rows is by inspecting its **shape**. This
is the number of rows and columns in a DataFrame, and you can access this
information with the `.shape` attribute:

```{code-cell}
banknotes.shape
```

```{note}
Notice how you accessed this DataFrame's `.shape` attribute using a very
similar syntax to the way you called one of its methods. The key difference is
the parentheses `()` at the end. Parentheses are necesary when you want to call
a method, but not when you want just want to access the value of attribute.
```

To display the names of each column, access the `.columns` attribute:

```{code-cell}
banknotes.columns
```

(summarizing-data)=
### Summarizing Data

More granular information about a DataFrame and its contents is available with
the `.info` method. In addition to attributes like the DataFrame's shape and
its column names, `.info` provides a brief summary of the number of cells that
contain data, the type of data in each cell, and the total memory usage of the
DataFrame:

```{code-cell}
banknotes.info()
```

The next chapter discusses data types in more detail. For now, just take note
that there are multiple types (`bool`, `float64`, `int64`, and `object` in
`banknotes`).

In contrast to `.info`, the `.describe` method provides summary statistics
about a DataFrame. The latter will only return information about numeric
columns:

```{code-cell}
banknotes.describe()
```

(summarizing-columns)=
### Selecting Columns

Individual columns may be selected with **bracket notation**. Put the name of
the column in quotes and place that inside of square brackets `[]`:

```{code-cell}
banknotes["current_bill_value"]
```

Just as with `.describe`, you can compute information about a column using
Pandas methods. Here is the mean:

```{code-cell}
banknotes["current_bill_value"].mean()
```

And here is the smallest value in the column:

```{code-cell}
banknotes["current_bill_value"].min()
```

Functions from other libraries, especially those in the SciPy ecosystem, can
also (but not always) work with Pandas. Here is the largest value in the
column, computed with NumPy's `max`:

```{code-cell}
np.max(banknotes["current_bill_value"])
```

Finally, you can assign new values to a DataFrame using the same notation as
above. Below, this code overwrites all the values in the `currency_code`
column:

```{code-cell}
banknotes["currency_code"] = "USD"
banknotes["currency_code"]
```

The next chapter will return to working with columns, showing you how to
generate new data from a DataFrame. You'll also learn how to select rows and
subsets of the data, as well as groups of columns.


Exercises
---------

### Exercise

In a string, an **escape sequence** or **escape code** consists of a backslash
`\` followed by one or more characters. Escape characters make it possible to:

* Write quotes or backslashes in a string
* Use spaces in file paths
* Write characters that don't appear on your keyboard (for example characters
  in a different script system)

For example, the escape sequence `\n` means "newline character." A full list of
these sequences is available at [W3Schools][w3].

[w3]: https://www.w3schools.com/python/gloss_python_escape_characters.asp

1. Assign a string that contains a newline to the variable `newline`. Then
  display `newline` via the Python console.
2. The `print` function renders output in a properly formatted manner. Use this
  function to print `newline`.
3. How does the output between these two displays differ? Why do you think this
  is?

### Exercise

1. Chose a directory on your computer that you're familiar with, such as your
  current working directory. Determine the path to the directory, then use
  `os.listdir` to display its contents. Do the files displayed match what you see
  in your systems file browser?
2. Send a path to `os.path.exists` and inspect its output. What does this
  function do? See if you can change its output. If you can, why did it change?

### Exercise

1. Open the help file for the Pandas `read_csv` function. The `sep` parameter
  controls which characters Pandas looks for when determining the columns in a
  file. What is the default character?
2. A TSV file is similar to CSV files, except it uses tabs to delimit columns.
  Tabs are represented by escape sequences in Python. Find the right sequence
  and explain how you would load a TSV file with `read_csv`.
3. Reload the `banknotes` data, but this time specify `\s` for the `sep`
  parameter. `\s` represents a space. When you load the data using this
  sequence, what happens? Why?
