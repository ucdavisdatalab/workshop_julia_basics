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

(getting-started)=
Getting Started
===============

:::{admonition} Learning Objectives
* Run code in a Python console (in JupyterLab)
* Run and save code in a Jupyter notebook
* Create variables and call functions
* Write paths to files and directories
* Get or set the Python working directory
* Identify the format of a data file
* Select appropriate functions for loading common file formats
* Load a data set with Pandas and inspect its contents
:::

**[Python][]** is a popular general-purpose programming language. Python is also a
leading language for scientific computing due to the **[SciPy
ecosystem][SciPy]**, a collection of scientific computing software for Python.

[Python]: https://www.python.org/
[SciPy]: https://scipy.org/

The main way you'll interact with Python is by writing Python code or
**expressions**. Most people use "Python" as a blanket term to refer to both
the Python language and the Python software (which runs code written in the
language). Usually, the distinction doesn't matter, but it will be pointed out
if it does.

Code you write is **reproducible**: you can share it with someone else, and if
they run it with the same inputs, they'll get the same results. By writing
code, you create an unambiguous record of every step taken in your analysis.
This is one of the major advantages of Python and other programming languages
over point-and-click software like *Tableau* or *Microsoft Excel*. 

Another advantage of writing code is that it's often **reusable**. This means
you can:

* Automate repetitive tasks within an analysis
* Recycle code from one analysis into another
* Package useful code for distribution to your colleagues or the general
  public

At the time of writing, there were over 324,000 user-contributed packages
available for Python, spanning a broad range of disciplines.

Python is one of many programming languages used in data science. Compared to
other programming languages, Python's particular strengths are its:

* Interactivity
* Use in a wide variety of disciplines, not just data science
* Broad base of user-contributed packages
* Easy-to-learn syntax that encourages good habits


Prerequisites
-------------

Rather than installing Python directly, install **[Anaconda][]**, a collection
of free and open-source data science software. Anaconda includes three things
you'll need to follow along with this reader:

* Python 3
* SciPy ecosystem packages
* **[Conda][]**, a system for installing and managing software

You'll learn more about these later on. Anaconda also includes other popular
software, such as the R programming language. Install Anaconda by following
[this guide][anaconda-guide].

[Anaconda]: https://www.anaconda.com/
[Conda]: https://docs.conda.io/

[anaconda-guide]: https://ucdavisdatalab.github.io/install_guides/python-and-python-tools.html

In addition, you need to install **JupyterLab**. JupyterLab is an **integrated
development environment** (IDE), which means it's a comprehensive program for
writing, editing, searching, and running code. You can do all of these things
without JupyterLab, but JupyterLab makes the process easier. Install JupyterLab
by following [this guide][jupyterlab-guide].

[jupyterlab-guide]: https://ucdavisdatalab.github.io/install_guides/python-and-python-tools.html#jupyterlab

<!--
In addition to Anaconda, you'll need Visual Studio Code (VSCode). VSCode is an
*integrated development environment* (IDE), which means it's a comprehensive
program for writing, editing, searching, and running code. You can do all of
these things without VSCode, but VSCode makes the process easier. VSCode
supports a variety of programming languages, including Python, R, Java, C, and
C++. You can download Visual Studio Code for free [here][vscode], and can find
an install guide [here][vscode-guide].
[vscode]: https://code.visualstudio.com/Download
[vscode-guide]: TODO
-->


The Python Console
------------------

The first time you open JupyterLab, you'll see a window that looks like this:

```{image} ../img/jupyterlab_startup.png
:alt: The JupyterLab startup screen.
```

Don't worry if the text in the panes isn't exactly the same on your computer;
it depends on your operating system and version of JupyterLab.

Start by opening up a Python **console**. In JupyterLab, look for the "Python
3" button in the "Console" section of the pane on the right. If there are
multiple Python 3 buttons, click on the one that mentions "IPython" or
"ipykernel":

```{image} ../img/jupyterlab_console_button.png
:alt: The JupyterLab startup screen with the console button highlighted.
```

The console is a interactive, text-based interface to Python. If you enter a
Python expression in the console, Python will compute and display the result.
After you open the console, your window should look like this:

```{image} ../img/jupyterlab_console.png
:alt: A Python console running in JupyterLab.
```

At the bottom of the console, the text box beginning with `[ ]:` is called the
**prompt**. The prompt is where you'll type Python expressions. Ask Python to
compute the sum $2 + 2$ by typing the code `2 + 2` in the prompt and then
pressing `Shift`-`Enter`. Your code and the result from Python should look like
this:

```{image} ../img/jupyterlab_console_sum.png
:alt: A Python console running in JupyterLab, showing the sum of two numbers.
```

The Python console displays your code and the result on separate lines. Both
begin with the tag `[1]` to indicate that they are the first expression and
result. Python will increment the tag each time you run an expression. The tag
numbers will restart from 1 each time you open a new Python console.

Now try typing the code `3 - 1` in the prompt and pressing `Shift`-`Enter`:

```{image} ../img/jupyterlab_console_diff.png
:alt: A Python console running in JupyterLab, showing the difference of two numbers.
```

The tag on the code and result is `[2]`, and once again the result is displayed
after the tag.

Try out some other arithmetic in the Python console. Besides `+` for addition,
the other arithmetic operators are:

* `-` for subtraction
* `*` for multiplication
* `/` for division
* `%` for remainder division (modulo)
* `**` for exponentiation

You can combine these and use parentheses `( )` to make more complicated
expressions, just as you would when writing a mathematical expression. When
Python computes a result, it follows the standard order of operations:
parentheses, exponentiation, multiplication, division, addition, and finally
subtraction.

For example, to compute the area of a triangle with base 3 and height 4, you
can write:

```{code-cell}
0.5 * 3 * 4
```

You can write Python expressions with any number of spaces (including none)
around the operators and Python will still compute the result. As with writing
text, putting spaces in your code makes it easier for you and others to read,
so it's good to make it a habit. Put a single space on each side of most
operators, after commas, and after keywords. Later on, you'll learn about other
kinds of expressions where the spacing does matter.


### Variables

Python and most other programming languages allow you to create named values
called **variables**. You can create a variable with the assignment operator
`=` by writing a name on the left-hand side and a value or expression on the
right hand side. For example, to save the estimated area of the triangle in a
variable called `area`, you can write:

```{code-cell}
area = 3 * 4 / 2
```

In Python, variable names can contain any combination of letters, numbers, and
underscores (`_`). Variables cannot start with a number; spaces, dots, and 
other symbols are not allowed in variable names.

The main reason to use variables is to temporarily save results from
expressions so that you can use them in other expressions. For instance, now
you can use the `area` variable anywhere you want the area of the triangle.

Notice that when you assign a result to a variable, Python doesn't
automatically display that result. If you want to see the result as well, you
have to enter the variable's name as a separate expression:

```{code-cell}
area
```

Another reason to use variables is to make an expression clearer and more
general. For instance, you might want to compute the area of several triangles
with different bases and heights. Then the expression `3 * 4 / 2` is too
specific. Instead, you can create variables `base` and `height`, then rewrite 
the expression as `base * height / 2`. This makes the expression easier to
understand, because the reader does not have to intuit that `3` and `4` are the
base and height in the formula. Here's the new code to compute and display the
area of a triangle with base 3 and height 4:

```{code-cell}
base = 3
height = 4
area = base * height / 2
area
```

Now if you want to compute the area for a different triangle, all you have to
do is change `base` and `height` and run the code again (Python will not update
`area` until you do this). Writing code that's general enough to reuse across
multiple problems can be a big time-saver in the long run. Later on, you'll see
ways to make this code even easier to reuse.


(strings)=
### Strings

Python treats anything inside single or double quotes as literal text rather
than as an expression to evaluate. In programming jargon, a piece of literal
text is called a **string**. You can use whichever kind of quotes you prefer,
but the quote at the beginning of the string must match the quote at the end. 

```{code-cell}
'Hi'
```

```{code-cell}
"Hello!"
```

Numbers and strings are not the same thing, so for example Python considers `1`
different from `"1"`.


(comparisons)=
### Comparisons

Besides arithmetic, you an also use Python to compare values. Programming tasks
often involve comparing values. Use **comparison operators** to do so:

| Symbol | Meaning                  |
| :----: | :----------------------- |
| `<`    | less than                |
| `>`    | greater than             |
| `<=`   | less than or equal to    |
| `>=`   | greater than or equal to |
| `==`   | equal to                 |
| `!=`   | not equal to             |

Notice that the "equal to" operator is two equal signs. This is to distinguish
it from the assignment `=` operator.

Here are a few examples:

```{code-cell}
1.5 < 3
```

```{code-cell}
"a" > "b"
```

```{code-cell}
3 == 3.14
```

```{code-cell}
"hi" == "hi"
```

When you make a comparison, Python returns a **Boolean value**. There are only
two possible Boolean values: `True` and `False`. Booleans are commonly used for
expressions with yes-or-no responses.

Boolean values are values, so you can use them in other computations. For
example:

```{code-cell}
True
```

```{code-cell}
True == False
```


(calling-functions)=
### Calling Functions

Python can do a lot more than just arithmetic. Most of Python's features are
provided through **functions**, pieces of reusable code. You can think of a
function as a machine that takes some inputs and uses them to produce some
output. In programming jargon, the inputs to a function are called
**arguments**, the output is called the **return value**, and using a function
is called **calling** the function.

To call a function, write its name followed by parentheses. Put any arguments
to the function inside the parentheses. For example, the function to round a
number to a specified decimal place is named `round`. So you can round the
number `8.153` to the nearest integer with this code:

```{code-cell}
round(8.153)
```

Many functions accept more than one argument. For instance, the `round` function
accepts two arguments: the number to round, and the number of decimal places to
keep. When you call a function with multiple arguments, separate the arguments
with commas. So to round `8.153` to 1 decimal place:

```{code-cell}
round(8.153, 1)
```

When you call a function, Python assigns the arguments to the function's
**parameters**. Parameters are special variables that represent the inputs to a
function and only exist while that function runs. For example, the `round`
function has parameters `number` and `ndigits`. The next section,
{numref}`getting-help`, explains how to look up the parameters for a function.

Some parameters have **default arguments**. A parameter is automatically
assigned its default argument whenever the parameter's argument is not
specified explicitly. As a result, assigning arguments to these parameters is
optional. For instance, the `ndigits` parameter of `round` has a default
argument (round to the nearest integer), so it is okay to call `round` without
setting `ndigits`, as in `round(8.153)`. In contrast, the `numbers` parameter
does not have a default argument. {numref}`getting-help` explains how to look
up the default arguments for a function.

Python normally assigns arguments to parameters based on their position. The
first argument is assigned to the function's first parameter, the second to the
second, and so on. So in the code above, `8.153` is assigned to `number` and
`1` is assigned to `ndigits`.

You can make Python assign arguments to parameters by name with `=`, overriding
their positions. So two other ways you can write the call above are:

```{code-cell}
round(8.153, ndigits = 1)
```

```{code-cell}
round(number = 8.153, ndigits = 1)
```

```{code-cell}
round(ndigits = 1, number = 8.153)
```

All of these are equivalent. When you write code, choose whatever seems the
clearest to you. Leaving parameter names out of calls saves typing, but
including some or all of them can make the code easier to understand.

Parameters are not regular variables, and only exist while their associated
function runs. You can't set them before a call, nor can you access them after
a call. So this code causes an error:

```{code-cell}
:tags: [raises-exception]
number = 4.755
round(ndigits = 2)
```

In the error message, Python says that you forgot to assign an argument to the
parameter `number`. You can keep the variable `number` and correct the call by
making `number` an argument (for the parameter `number`):

```{code-cell}
round(number, ndigits = 2)
```

Or, written more explicitly:

```{code-cell}
round(number = number, ndigits = 2)
```

The point is that variables and parameters are distinct, even if they happen to
have the same name. The variable `number` is not the same thing as the
parameter `number`.


(objects-attributes)=
### Objects & Attributes

Python represents data as **objects**. Numbers, strings, data structures, and
functions are all examples of objects.

An **attribute** is an object attached to another object. An attribute usually
contains metadata about the object to which it is attached. An attribute can
also be a function, in which case it is called a **method**.

For example, all strings have a `capitalize` method. You can access attributes
and methods by typing a `.` after an object. Here's the code to capitalize a
string:

```{code-cell}
"snakes everywhere!".capitalize()
```

The built-in `dir` function lists all of the attributes attached to
an object. Here are the attributes for a string:

```{code-cell}
dir("hi")
```

Attributes that begin with two underscores `__` are used by Python internally
and are usually not intended to be accessed directly.


(getting-help)=
Getting Help
------------

Learning and using a language is hard, so it's important to know how to get
help. The first place to look for help is Python's built-in documentation. In
the console, you can access the help pages with the `help` function.

There are help pages for all of Python's built-in functions, usually with the
same name as the function itself. So the code to open the help page for the
`round` function is:

```{code-cell}
:tags: [output_scroll]
help(round)
```

For functions, help pages usually include a brief description and a list of
parameters and default arguments. For instance, the help page for `round` shows
that there are two parameters `number` and `ndigits`. It also says that
`ndigits=None`, meaning the default argument for `ndigits` is the special
`None` value, which you'll learn more about on day 2.

There are also help pages for other topics, such as built-in operators and
modules (you'll learn more about modules in {numref}`modules-packages`). To
look up the help page for an operator, put the operator's name in single or
double quotes. For example, this code opens the help page for the arithmetic
operators:

```{code-cell}
:tags: [output_scroll]
help("+")
```

It's always okay to put quotes around the name of the page when you use `help`,
but they're only required if the name contains non-alphabetic characters. So
`help(abs)`, `help('abs')`, and `help("abs")` all open the documentation for
`abs`, the absolute value function.

You can also browse the Python documentation [online][pydocs]. This is a good
way to explore the many different functions and data structures built into
Python. If you do use the online documentation, make sure to use the
documentation for the same version of Python as the one you have. Python
displays the version each time you open a new console, and the online
documentation shows the version in the upper left corner.

[pydocs]: https://docs.python.org/3/

Sometimes you might not know the name of the help page you want to look up. In
that case it's best to use an online search engine. When you search for help
with Python online, include "Python" as a search term.


### When Something Goes Wrong

As a programmer, sooner or later you'll run some code and get an error message
or result you didn't expect. Don't panic! Even experienced programmers make
mistakes regularly, so learning how to diagnose and fix problems is vital.

Try going through these steps:

1. If Python printed a warning or error message, read it! If you're not sure
   what the message means, try searching for it online.
2. Check your code for typographical errors, including incorrect
   capitalization, whitespace, and missing or extra commas, quotes, and
   parentheses.
3. Test your code one line at a time, starting from the beginning. After each
   line that assigns a variable, check that the value of the variable is what
   you expect. Try to determine the exact line where the problem originates
   (which may differ from the line that emits an error!).

If none of these steps help, try asking online. [Stack Overflow][stacko] is a
popular question and answer website for programmers. Before posting, make sure
to read about [how to ask a good question][goodq].

[stacko]: https://stackoverflow.com/
[goodq]: https://stackoverflow.com/help/how-to-ask
