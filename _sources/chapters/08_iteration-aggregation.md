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
  name: julia-1.10
---
-->


Exploring Data
==============

Now that you have a solid foundation in the basic functions and data structures
of Python, you can move on to using it for data analysis. In this chapter,
you'll learn how to efficiently explore and summarize with visualizations and
statistics. Along the way, you'll also learn how to apply functions along
entire sets of data in Pandas DataFrames and Series.

:::{admonition} Learning Objectives
* Describe how Python iterates over data
* Write loops to do things repeatedly
* Write list comprehensions to do things repeatedly
* Use Pandas aggregation methods to explore a data set
* Prepare data for visualization
* Describe the grammar of graphics
* Use the grammar of graphics to produce a plot
* Identify where to go to learn more about making effective visualizations
:::


Setup
-----

### Packages

As in the last chapter, you will be working with two primary packages: NumPy
and Pandas. Later, you will load another set of packages to visualize your
data.

```
import numpy as np
import pandas as pd
```


### Data

We will continue working with the banknotes data set. Once you've imported your
packages, load this data in as well.

```
banknotes = pd.read_csv("data/banknotes.csv")
```

You're now ready to go.


(iteration)=
Iterating Over Data
-------------------

Before we go into data exploration in full, it's important to understand how
Python/Pandas computes summary statistics about a data set.
{numref}`summarizing-columns` introduced column-wise operations in Pandas; you
will learn more of them below. These operations are a convenient and efficient
way to compute multiple results at once, and with only a few lines of code.

Under the hood, Pandas has to **iterate** over each value in a cell to perform
operations like `.mean` or `.min`. We can do this too using a **for-loop**.


### For-Loops

For-loops iterate over some object and compute something for each element. Each
one of these computations is one **iteration**. A for-loop begins with the
`for` keyword, followed by:

* A placeholder variable, which will be automatically signed to an element at
  the beginning of each iteration
* The `in` keyword
* An object with elements
* A colon `:`

Code in the body of the loop must be indented by 4 spaces.

For example, to print out all the column names in `banknotes.columns`, you can
write:

```
for column in banknotes.columns:
    print(column)
```

Within the indented part of a for-loop, you can compute values, check
conditions, etc.

```
:tags: [output_scroll]
for value in banknotes["bill_count"]:
    if value < 1:
        print(value)
```

Oftentimes you want to save the result of the code you perform within a
for-loop. The easiest way to do this is by creating an empty list and using
`append` to add values to it.

```
:tags: [output_scroll]
result = []
for value in banknotes["current_bill_value"]:
    if value % 25 == 0:
        result.append(value)

result
```


### List Comprehensions

A more efficient and succinct way to perform certain `append` operations is
with a **list comprehension**. A list comprehension is very similar to a
for-loop, but it automatically creates a new list based on what your iterations
do. This means you do not need to create an empty list ahead of time.

The syntax for a list comprehension includes the keywords `for` and `in`, just
like a for-loop. The difference is that in the list comprehension, the repeated
code comes *before* the `for` keyword rather than after it, and the entire
expression is enclosed in square brackets `[ ]`.

Here's a list comprehension that divides each value in the `current_bill_value`
column by 2:

```
:tags: [output_scroll]
[value / 2 for value in banknotes["current_bill_value"]]
```

List comprehensions can optionally include the `if` keyword and a condition at
the end, to filter out some elements of the list:

```
:tags: [output_scroll]
[year for year in banknotes["first_appearance_year"] if year > 2012]
```

This is similar to subsetting in Pandas.

Note that you can assign the results of a list comprehension to a new variable
and then perform further computations on them:

```
recent_years = [year for year in banknotes["first_appearance_year"] if year > 2012]
np.median(recent_years)
```

You can learn more about comprehensions in the [official Python
documentation][comprehensions].

[comprehensions]: https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions


(aggregate-functions)=
Aggregate Functions
-------------------


(aggregating-a-column)=
### Aggregating a Column

In {numref}`summarizing-columns`, you learned how to compute the mean, minimum,
and maximum values from a Series. Pandas offers a more generalized way to
handle these functions through its `.aggregate` method. This method
**aggregates** the elements of Series, reducing the Series to a smaller number
of values (usually one value).

For example, to compute the median of all values in `first_appearance_year`:

```
banknotes["first_appearance_year"].aggregate('median')
```

The `.agg` method is an alias for `.aggregate`. The [Pandas
documentation][pandasdocs] advises that you use the alias:

[pandasdocs]: https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.aggregate.html

```
banknotes["first_appearance_year"].agg('median')
```

You can pass functions to `.agg` in addition to names of functions:

```
banknotes["first_appearance_year"].agg(np.median)
```

The method is particularly powerful for its ability to handle multiple
functions at once, using a list. Below, we compute the mean, median, and
standard deviation for `bill_count`:

```
banknotes["current_bill_value"].agg([np.mean, np.median, np.std])
```

Aggregation methods can also work on multiple columns at once:

```
banknotes[["current_bill_value", "scaled_bill_value"]].agg(np.mean)
```


(aggregating-within-groups)=
### Aggregating within Groups

Aggregation is especially useful when combined with grouping. The `.groupby`
method groups rows of a DataFrame using the columns you specify. The grouping
columns should generally be categories rather than decimal numbers. For
example, to group the banknotes by `gender` and then count how many entries are
in each group:

```
banknotes.groupby("gender").size()
```

Use bracket notation to look at a specific column for each group:

```
banknotes.groupby("gender")["current_bill_value"].mean()
```

It's also possible to group by multiple conditions:

```
banknotes.groupby(["gender", "profession"]).size()
```

By default, the grouping columns are moved to the index of the result. You can
prevent this by setting `as_index = False` in `.groupby`:

```
banknotes.groupby(["gender", "profession"], as_index = False).size()
```

:::{tip}
You can also reset the index on a DataFrame, so that the current indexes
become columns with the `.reset_index` method.
:::

Leaving the grouping columns in the index is often convenient because you can
easily access results for the groups you're interested in:

```
grouped = banknotes.groupby(["gender", "profession"]).size()

grouped.loc[:, "Visual Artist"]
```

A few aggregation functions only make sense when used together with groups. One
is the `.first` method, which returns the first element or row. The `.first`
method is especially useful if all the values in a group are the same and you
want to reduce the data to one row per group. For instance, the same country
appears across multiple rows in our data set. With `.first`, you can select the
corresponding currency code:

```
banknotes.groupby("country")["currency_code"].first()
```
