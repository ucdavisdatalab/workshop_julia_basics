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


Appendix
========

:::{admonition} Learning Objectives
* Create and access elements of tuples
* Create and access elements of dictionaries
* Explain what HTTP is, including request methods
* Use the requests package to make HTTP requests
* Explain what a web API is
* Load and access elements of JSON data
* Write loops to do things repeatedly
* Write comprehensions to do things repeatedly
* Save data to a CSV or other file format
:::

This appendix contains an optional part 5 of the workshop which demonstrates
how to use Python to download some non-tabular data from the internet, convert
it into a tabular format, and save it as a CSV file.


(containers)=
## Containers

Python provides a variety of containers for data. This section describes the
three most widely-used containers. You can find even more types of containers
in the [collections module][collections].

[collections]: https://docs.python.org/3/library/collections.html


### Lists

Lists were introduced in {numref}`lists`. A list is an ordered collection of
values. Lists are **mutable**, which means that the elements can be changed.

Here's a quick recap of things you can do with lists:

```
# Create a list with square brackets [ ]
x = [1, 3, 5]

# Get an element by position with square brackets [ ]
x[0]

# Set an element
x[2] = -1
```

You can also remove an element of a list with the `del` keyword. For instance:

```
del x[2]

x
```

The `del` keyword can also be used with dictionaries, which are described in
{numref}`dictionaries`.


### Tuples

A **tuple** is an ordered collection of values. Think of coordinates. Tuples
are **immutable**, which means that the elements of a tuple can't be changed.


```
y = ("hi", 1, 3.7)
y
```

```
type(y)
```

You can get elements of a tuple with indexing, just like a list:

```
y[0]
```

If you try to change the elements, Python raises an error:

```
:tags: [raises-exception]
y[1] = 3
```

You can also get the elements of a tuple by **unpacking** them. Unpacking is a
way to assign the elements of a tuple or list to multiple variables. Use `=` to
unpack values, making sure that the structure on the left-hand side matches the
structure on the right-hand side:

```
u, v, w = y
u
```

```
v
```

```
w
```

Unpacking also works with lists.

Tuples are slightly more efficient than lists, and are generally the best way
to return a fixed number of results from a function (see {numref}`functions`).


(dictionaries)=
### Dictionaries

A **dictionary** (or `dict`) is a one-to-one map from **keys** to **values**.
That means that you use keys to look up values in a dictionary. Like lists,
dictionaries are mutable.

You can make a dictionary by enclosing comma-separated `key: value` pairs in
curly braces `{ }`, like this:

```
x = {"hello": 1, 3: 5}
x
```

```
type(x)
```

The dictionary `x` has two keys: `"hello"` and `3`. Most of the time, the keys
in a dictionary will be strings, but you can use other types of keys. However,
the keys are required to be unique.

You can get and set elements of a dictionary by indexing with keys:

```
x["hello"]
```

You can also get elements with the `.get` method, which gets an element by key
or returns a default value if the key isn't in the dictionary:

```
x.get("hello", 10)
```

```
x.get("goodbye", 10) 
```

You can get the keys or the values in a dictionary with the `.keys` and `.dict`
methods respectfully:

```
x.keys()
```

```
x.values()
```

You can use the `list` function to convert either of these to an ordinary list:

```
list(x.keys())
```

Common use cases for dictionaries include creating lookup tables and returning
named results from a function.
