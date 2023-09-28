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

(modules-packages)=
Packages
========

A **package** is a reusable bundle of code. Packages usually include
documentation, and can also contain examples and data sets. Most packages are
developed by members of the Python community, so quality varies. 


### The SciPy Ecosystem

The SciPy ecosystem is a collection of scientific computing software for Python
introduced in 2001. SciPy is divided into several different Python packages.

Some of the most important packages in the SciPy ecosystem are:

* **NumPy**, which provides an n-dimensional array data structure and a variety
  of math functions
* **SciPy**, which provides additional math functions
* **Pandas**, which provides DataFrames
* **IPython**, which makes it possible to run Python code in Jupyter
* **Matplotlib**, which provides data visualization functions

You'll learn much more about NumPy, SciPy, and Pandas as you go through this
reader. By using JupyterLab, you've already used IPython. You'll use Matplotlib
indirectly later on, when you learn about visualization.


(installing-packages)=
### Installing packages

You can use Anaconda's conda utility to install additional packages. The conda
utility is a program, not part of Python. In JupyterLab, open a Terminal
(`File` -> `New` -> `Terminal`). Then enter:

```
conda install -c conda-forge <package-name>
```

The command `conda install <package-name>` installs the package called
`<package-name>`. The flag `-c conda-forge` tells conda to use a version from
the conda-forge package repository. Packages on conda-forge are usually more up
to date than the ones in Anaconda's default package repository.

You can learn more about Anaconda and conda in the [official
documentation][condadoc].

[condadoc]: https://docs.anaconda.com/anaconda/user-guide/tasks/install-packages/

(modules)=
### Modules

In Python, packages are further subdivided into **modules**, collections of
related functions and data structures. The best way to learn about the modules
provided by a package is to read the package's documentation. There are also
many modules that are built into Python, to provide extra features.

Most packages have a main module with the same name as the package. So the
NumPy package provides a module called `numpy`, and the Pandas package provides
a module called `pandas`. You can use the `import` command to load a module
from an installed package. Anaconda installs NumPy by default, so try loading
the `numpy` module:

```{code-cell}
import numpy
```

A handful of modules print out a message when loaded, but the vast majority do
not. Thus you can assume the `import` command was successful if nothing is
printed. If something goes wrong while loading a module, Python will print out
an error message explaining the problem.

Once a module is loaded, you can access its functions by typing the name of the
module, a dot `.`, and then the name of the function. For instance, to use the
`round` function provided by NumPy:

```{code-cell}
numpy.round(3.3)
```

Typing the full name of a module is inconvenient, so the `import` command
allows you to define an alias when you import a module. For popular packages,
there's usually a conventional alias for the main module. The conventional
alias for `numpy` is `np`. Using the conventional alias is a good habit,
because it makes it easier for other people to understand your code. Use the
`as` keyword to set an alias when you import a module:

```{code-cell}
import numpy as np
```

Now you can call NumPy functions by typing `np` instead of `numpy`:

```{code-cell}
np.round(3.4)
```

Note that NumPy's `np.round` is an entirely different function than Python's
built-in `round` function, even though they do the same thing. NumPy's math
functions are generally faster, more precise, and more convenient than Python's
built-in math functions.
