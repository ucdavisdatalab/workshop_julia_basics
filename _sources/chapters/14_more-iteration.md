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

(loops)=
Loops
=====

Now suppose you want to get the information for the first 15 people in SWAPI.
You could do this by writing 15 calls to `get_swapi_person`, but that would be
very tedious.

One major benefit of using a programming language like Python is that
repetitive tasks can be automated. A **loop** is a way to automate doing a
similar operation several times, so that you don't have to repeat the code
several times. Loops are a feature of almost all modern programming languages,
so it's useful to understand them.

In Python, there are two kinds of loops. We'll focus on **for-loops**.

For-loops **iterate** over some object, and compute something for each element.
Each one of these computations is one **iteration**. A for-loop begins with the
`for` keyword, followed by:

* A placeholder variable, which will automatically be assigned an element at
  the beginning of each iteration
* The `in` keyword
* An object with elements
* A colon `:`

Code in the body of the loop must be indented by 4 spaces.

For example, to print out the names in a list `names`, you can write:

```
names = ["Arthur", "Nick", "Cameron", "Pamela"]

for name in names:
    print(name)
```

The code in the body of the loop is evaluated once for each name in the list
(that is, once for each iteration).

A common technique when programming with for-loops is to iterate over a
sequence of numbers. Then you can use the numbers to index other objects. In
Python, the `range` function generates a sequence of numbers starting from 0.
As an example, here's a version of the loop above that uses indexes:

```
for i in range(4):
    print(names[i])
```

Sometimes you might want both the indexes and the values for an object. In that
case, you can use the `enumerate` function. When you use `enumerate` in a
loop, the loop syntax is slightly different:

```
for i, name in enumerate(names):
    print("This is iteration " + str(i))
    print(name)
```


Most of the time, you'll want to save the results from the iterations into a
variable for use later on, rather than print them all out. The best way to do
this is to create an empty list before the loop, and then append to it with the
`.append` method. For example, here's a loop that computes the cumulative sums
from left to right for the numbers in a list:

```
nums = [1, 3, 2, -2, 10]

sums = []
total = 0

for num in nums:
    total = total + num
    sums.append(total)

sums
```

You can use a loop to get the information about the first 15 people in SWAPI.
When you send HTTP requests (or call a function that sends HTTP requests) in a
loop, be careful to follow the guidelines for request etiquette described in
{numref}`request-etiquette`. For loops, limiting the number of requests per
second is especially important. Here's the code to get the information for the
first 15 people:

```
import time

people = []

for i in range(15):
    # i + 1 because range starts from 0
    person = get_swapi_person(i + 1)
    people.append(person)

    # Do nothing for 1/10 of a second, to prevent making requests too quickly
    time.sleep(0.1)
```

After running the loop, you can inspect the returned information in the list
`people`:

```
people[0]
```


### Comprehensions

Python provides an efficient and powerful alternative to loops called
**comprehensions**. Comprehensions automatically create a list or dictionary. A
**list comprehension** creates a list, and a **dictionary comprehension**
creates a dictionary.

Comprehensions are especially helpful if you have a list or dictionary and want
to transform each element in the same way. For instance, suppose you want to
get the eye color for each person in the `people` list. You can get the eye
color for the first person with the code:

```
people[0]["eye_color"]
```

You could use this code with a loop that changes the first index (`0`) in order
to get the eye color of every person. However, you can do the same thing as a
loop more concisely with a list comprehension. Here's the code:

```
eye_colors = [person["eye_color"] for person in people]

eye_colors[0]
```

The syntax for a list comprehension includes the keywords `for` and `in`, just
like a for-loop. The difference is that in the list comprehension, the repeated
code comes *before* the `for` keyword rather than after it, and the entire
expression is enclosed in square brackets `[ ]`.

You can use list comprehensions to get other information as well. For instance,
here's the code to get lists of names and heights for each person in the list:

```
names = [person["name"] for person in people]
heights = [person["height"] for person in people]
```

You can learn more about comprehensions in the [official Python
documentation][comprehensions].

[comprehensions]: https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions



## Exporting Data

The extracted `names`, `heights`, and `eye_colors` lists are like columns in a
DataFrame where each row is one person, so you might as well put them in a
DataFrame. You can use the `pd.DataFrame` function and a dictionary of columns
to do this:

```
import pandas as pd

people_df = pd.DataFrame({"name": names, "height": heights,
    "eye_color": eye_colors})

people_df
```

Pandas provides a variety of methods for saving DataFrames to a file. Most of
these begin with `.to_`. For example, to save a DataFrame to a CSV file, use
the `.to_csv` method:

```
people_df.to_csv("swapi_people.csv")
```

It's a good idea to save your data sets like this whenever you reach a
checkpoint in the problem you're trying to solve.


## Practice Exercises


### Exercise 1

Create a CSV file for the first 15 *vehicles* in SWAPI. The file should include
columns for cargo capacity, cost, length, manufacturer, model, and passengers.


### Exercise 2

Create a histogram from the heights of the first 30 people in SWAPI.

:::{admonition} Hint
You can use the `float` function to convert a string to a decimal number.
:::
