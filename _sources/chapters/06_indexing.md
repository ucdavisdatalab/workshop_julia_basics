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


(indexing-in-pandas)=
Indexing in Pandas
==================

If you want to inspect the elements of a Series more closely, you can use
indexing. Conceptually, indexing a Series is very similar to indexing a list or
tuple, but Pandas offers additional ways to select and subset data via indexes.


(the-pandas-index)=
### What's an Index?

A Pandas index is more than just a positional location. Indexes serve three
important roles:

1. As metadata to provide additional context about a data set
2. As a way to explicitly and automatically align data
3. As a convenience for getting and setting subsets of data

The index of a series is available via the `index` attribute:

```{code-cell}
bill_value.index
```

Index labels can be numbers, strings, dates, or other values. Pandas provides
subclasses of `Index` for specific purposes, which you can read more about
[here][pd-index].

[pd-index]: https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Index.html

Indexes, like tuples, are **immutable**. The labels in an index cannot be
changed. That said, every index also has a name that _can_ be changed:

```{code-cell}
bill_value.name = "bill_value"
bill_value
```

The above also applies to DataFrames:

```{code-cell}
banknotes.index
```

Oftentimes, an index is a range of numbers, but this can be changed. The code
below uses the `.set_index` method to change the index of `banknotes` to
`country`:

```{code-cell}
banknotes.set_index("country", inplace = True)
banknotes.index
```

The `inplace` argument instructs Pandas to change the index directly without
making a copy first, so that we don't have to reassign `banknotes.index`
explicitly.


(indexing-by-position)=
### Indexing by Position

Changing the index affects how you select elements. There are three main
methods for accessing specific values in Pandas:
1. By integer position
2. By label/name
3. By a condition

To access elements in a series by integer position, use `.iloc`:

```{code-cell}
bill_value.iloc[5]
```

Using `.iloc` is extensible to sequences of values:

```{code-cell}
bill_value.iloc[[5, 15, 25, 35]]
```

Use a **slice** to select a range of elements. The syntax for a slice is
`start:stop:step`, with the second colon `:` and arguments being optional. This
syntax also applies to lists. For example:

```{code-cell}
bill_value.iloc[0:5]
```

Below, we use a slice to get every twentieth element in the Series:

```{code-cell}
bill_value.iloc[::20]
```

Slices also accept negative values. This counts back from the end of a
sequence. For instance:

```{code-cell}
bill_value.iloc[-5:]
```

The result is the same as if you had used the `.tail` method:

```{code-cell}
bill_value.tail()
```


(indexing-by-label)=
### Indexing by Label

Use `.loc` to index a Series or DataFrame by label:

```{code-cell}
:tags: [output_scroll]
banknotes.loc["Peru"]
```

You can select specific columns as well:

```{code-cell}
:tags: [output_scroll]
banknotes.loc["Peru", "name"]
```

Just as with `.iloc`, it's possible to pass sequences into `.loc`:

```{code-cell}
:tags: [output_scroll]
banknotes.loc[["Peru", "Serbia", "Ukraine"]]
```

This can be a very powerful operation, but it's easy to get mixed up when
labels are integers, as with the `bill_value` data.

For example, this:

```{code-cell}
bill_value.loc[0:5]
```

Is *NOT* the same as this:

```{code-cell}
bill_value.iloc[0:5]
```

Recall that bracket notation selects columns in DataFrames. With a Series, the
same notation acts as another way to perform `.loc` operations:

```{code-cell}
bill_value[0:5]
```

Finally, `.iloc` and `.loc` can be used in tandem with one another. This is
called **chaining**. Below, we use the country-indexed `banknotes` DataFrame to
select all rows with "Peru." Then, we select the second row from this subset.

```{code-cell}
banknotes.loc["Peru"].iloc[1]
```


(indexing-by-condition)=
### Indexing by a Condition

The last way to index in Pandas is by condition. Pandas does this by evaluating
a condition and returning a Boolean Series or array. This is by far the most
powerful method of indexing in Pandas.

For example, suppose you want to find bill values that are divisible by 25. You
can use the modulo operator `%` to get the remainder when one positive integer
is divided by another. So the condition to test for divisibility by 25 is:

```{code-cell}
bill_value % 25 == 0
```

The result is a Boolean Series with as many elements as `bill_value`. You can
use this condition in `.loc` to get only the elements where the result was
`True`:

```{code-cell}
bill_value.loc[bill_value % 25 == 0]
```

<!--
This is a **subset** of the Series.
-->

You can also use square brackets `[]` without `.loc` to index by condition:

```{code-cell}
bill_value[bill_value - 100 > 5]
```

With a DataFrame, indexing by condition gives you a subset of the rows:

```{code-cell}
:tags: [output_scroll]
banknotes[banknotes["currency_code"] == "MWK"]
```

If you want to specify specific columns, use `.loc`:

```{code-cell}
banknotes.loc[banknotes["current_bill_value"] == 10.0, "currency_name"]
```

The above lets you select multiple columns, but you could also do the
following:

```{code-cell}
cols = ["currency_code", "currency_name"]
banknotes[cols].loc[banknotes["current_bill_value"] == 10.0]
```
