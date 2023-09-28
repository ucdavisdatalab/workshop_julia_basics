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
  name: julia
---
-->


(special-values)=
Special Values
==============

You may have noticed that some of the data in `banknotes` is missing. This is
common, and it's important to understand how to handle missing or invalid
values.

There are many reasons that could cause these values to be missing or
incomplete, and as a result, Pandas provides lots of flexibility for detecting
and handling these values.

In Pandas, these special values are generally treated as missing values in the
dataset, and are represented by the NumPy `nan` type. This reduces some of the
nuance of data values and types, but was seemingly done for computational
performance reasons.

```{code-cell}
banknotes.iloc[-25]
```

(missing-values-in-pandas)=
### Types of Values Considered Missing by Pandas

In addition to `np.nan` (which displays as NaN), Pandas interprets several
other values as missing.  This includes Python's `None` type, as well as
Pandas' experimental `NA` types.

Python's `None` type represents something that has no value. It often comes
about as the return of a function, if something hasn't been defined yet, or if
something wasn't found.

When creating a Series, we can pass this value:
```{code-cell}
pd.Series([None, "one", "two"])
```

Be aware that `None` is a Python object, and in the above example, the 
datatype of the series became 'object'. If we specify a datatype explicitly
then Pandas will convert it to one of its representations:
```{code-cell}
pd.Series([1.5, 2.0, 3, None], dtype="float")
```


(missing-values-in-csvs)=
### Reading in Missing Values from a CSV file

An obvious source of missing or incomplete values is the data itself. 
When the data was collected, there may have been reasons to code missing data.
For example, in collection of survey responses, there may be times where the 
answer was not applicable.

Another example would be if a measurement was not taken on some of the samples. 
Obviously, there are no rules on how this was represented in the data set. 
However there are several conventions, and Pandas is aware of many of them. 

When reading data from a CSV file, Pandas will automatically detect missing
values. By default, it will convert any empty cell, or string such as 'na',
'nan', 'null', 'N/A', and other variants to NaN.  A full list can be found in
the [Pandas documentation][readcsv].


[readcsv]: https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html


(detecting-missing-values)=
### Detecting Missing Values

To detect missing values, Pandas provides two complementary methods: `.isna`
and `.notna`.

We can see information about missing values with the `.count` method on 
DataFrames:

```{code-cell}
banknotes.count()
```

If we look at the `hover_text` columns of the DataFrame, we can see what 
those missing values look like.

```{code-cell}
ht = banknotes["hover_text"]
ht.count()
```

The return of `.isna` is a Boolean Series indicating which of the values are
considered missing:

```{code-cell}
ht.isna()
```

The reverse---to see which values are not considered missing---is returned 
with `.notna`:

```{code-cell}
ht.notna()
```

(replacing-missing-values)=
### Replacing Missing Values

We can use this Boolean Series to subset with `.loc`. For example, to keep only
the values that aren't missing:

```{code-cell}
ht.loc[ht.notna()]
```

Pandas also provides a shortcut with the `.dropna` method:

```{code-cell}
ht.dropna()
```

Another strategy may be to fill the missing values. We could do so using the 
`.fillna` method:

```{code-cell}
ht.fillna(-1, inplace=True)
ht
```

Additionally, the data set may have its own indicator for missing values, e.g
"" or 0. We can convert those to missing using the `.replace` method:

```{code-cell}
ht.replace(-1, np.nan)
```


Exercises
---------

### Exercise

Python's `range` function offers another way to create a sequence of numbers.
Read the help file for this function.

1. Create an example range. How does this differ from a list?
2. Describe the three arguments that you can use in `range`. Give examples of
  each.
3. Convert one of those ranges to a list and print it to screen. What changes
  in the way Python represents this sequence?


### Exercise

Return to the discussion in {numref}`coercion-conversion`.

1. Why does `"3" + 4` raise an error?
2. Why does `True - 1` return 0?
3. Why does `int(4.6) < 4.6` return `True`?


### Exercise

Use a search engine or consult StackOverflow to figure out how to subset a
DataFrame with multiple conditions.

1. Create a new DataFrame from `banknotes` with the following conditions:
  current bill value is less than or equal to 20; gender is female; contains
  the columns `country`, `name`, `comments`, `has_portrait`
2. Use a Pandas function to count the number of entries that have portraits.
  How many are there?
3. Return the last available comment. What does it say?

