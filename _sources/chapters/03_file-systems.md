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

File Systems
============

Most of the time, you won't just write code directly into the Python console.
Reproducibility and reusability are important benefits of Python over
point-and-click software, and in order to realize these, you have to save your
code to your computer's hard drive.

This section begins with a review of how files on a computer work. You'll need
to understand that before you can save your code, and it will also be important
later on for loading data sets.

Your computer's **file system** consists of **files** (chunks of data) and
**directories** (or "folders") to organize those files. For instance, the file
system on a computer shared by [Ada][ada] and [Charles][chuck], two pioneers of
computing, might look like this:

[ada]: https://en.wikipedia.org/wiki/Ada_Lovelace
[chuck]: https://en.wikipedia.org/wiki/Charles_Babbage

```{image} ../img/filesystem.png
:alt: An example of a file system.
:width: 50%
```

Don't worry if your file system looks a bit different from the picture.

File systems have a tree-like structure, with a top-level directory called the
_root directory_. On Ada and Charles' computer, the root is called `/`, which
is also what it's called on all macOS and Linux computers. On Windows, the root
is usually called `C:/`, but sometimes other letters, like `D:/`, are also used
depending on the computer's hardware.

A _path_ is a list of directories that leads to a specific file or directory on
a file system (imagine giving directions to someone as they walk through the
file system). Use forward slashes `/` to separate the directories in a path,
rather than commas or spaces. The root directory includes a forward slash as
part of its name, and doesn't need an extra one.

For example, suppose Ada wants to write a path to the file `cats.csv`. She can
write the path like this:

```
/Users/ada/cats.csv
```

You can read this path from left-to-right as, "Starting from the root
directory, go to the `Users` directory, then from there go to the `ada`
directory, and from there go to the file `cats.csv`." Alternatively, you can
read the path from right-to-left as, "The file `cats.csv` inside of the `ada`
directory, which is inside of the `Users` directory, which is in the root
directory."

As another example, suppose Charles wants a path to the `Programs` directory.
He can write:

```
/Programs/
```

The `/` at the end of this path is reminder that `Programs` is a directory, not
a file. Charles could also write the path like this:

```
/Programs
```

This is still correct, but it's not as obvious that `Programs` is a directory.
In other words, when a path leads to a directory, including a _trailing slash_
is optional, but makes the meaning of the path clearer. Paths that lead to
files never have a trailing slash.

:::{warning}
On Windows computers, the components of a path are usually separated with
backslashes ```\``` instead of forward slashes `/`.

Regardless of the operating system, most Python functions accept and understand
paths separated with forward slashes as arguments. In other words, you can use
paths separated with forward slashes in your Python code, even on Windows. This
is especially convenient when you want to share code with other people, because
they might use a different operating system than you.

On Windows, most Python functions *return* paths separated by backslashes. Be
careful of this if your code gets a path by calling a function and then edits
it (for example, by calling `os.getcwd` and then splitting the path into its
components). The separator will be a backslash on Windows, but a forward slash
on all other operating systems. Python's built-in [pathlib][] module provides
helper functions to edit paths that account for differences between operating
systems.
:::

[pathlib]: https://docs.python.org/3/library/pathlib.html


(absolute-relative-paths)=
### Absolute & Relative Paths

A path that starts from the root directory, like all of the ones we've seen so
far, is called an **absolute path**. The path is "absolute" because it
unambiguously describes where a file or directory is located. The downside is
that absolute paths usually don't work well if you share your code.

For example, suppose Ada uses the path `/Programs/ada/cats.csv` to load the
`cats.csv` file in her code. If she shares her code with another pioneer of
computing, say [Gladys][gladys], who also has a copy of `cats.csv`, it might
not work. Even though Gladys has the file, she might not have it in a directory
called `ada`, and might not even have a directory called `ada` on her computer.
Because Ada used an absolute path, her code works on her own computer, but
isn't portable to others.

[gladys]: https://en.wikipedia.org/wiki/Gladys_West

On the other hand, a **relative path** is one that doesn't start from the root
directory. The path is "relative" to an unspecified starting point, which
usually depends on the context.

For instance, suppose Ada's code is saved in the file `analysis.ipynb` (more
about `.ipynb` files in {numref}`jupyter-notebooks`), which is in the same
directory as `cats.csv` on her computer. Then instead of an absolute path, she
can use a relative path in her code:

```
cats.csv
```

The context is the location of `analysis.ipynb`, the file that contains the
code. In other words, the starting point on Ada's computer is the `ada`
directory. On other computers, the starting point will be different, depending
on where the code is stored.

Now suppose Ada sends her corrected code in `analysis.ipynb` to Gladys, and
tells Gladys to put it in the same directory as `cats.csv`. Since the path
`cats.csv` is relative, the code will still work on Gladys' computer, as long
as the two files are in the same directory. The name of that directory and its
location in the file system don't matter, and don't have to be the same as on
Ada's computer. Gladys can put the files in a directory
`/Users/gladys/from_ada/` and the path (and code) will still work.

Relative paths can include directories. For example, suppose that Charles wants
to write a relative path from the `Users` directory to a cool selfie he took.
Then he can write:

```
charles/cool_hair_selfie.jpg
```

You can read this path as, "Starting from wherever you are, go to the `charles`
directory, and from there go to the `cool_hair_selfie.jpg` file." In other
words, the relative path depends on the context of the code or program that
uses it.

:::{tip}
When use you paths in code, they should almost always be relative paths. This
ensures that the code is portable to other computers, which is an important
aspect of reproducibility. Another benefit is that relative paths tend to be
shorter, making your code easier to read (and write).
:::

When you write paths, there are three shortcuts you can use. These are most
useful in relative paths, but also work in absolute paths:

* `.` means the current directory.
* `..` means the directory above the current directory.
* `~` means the _home directory_. Each user has their own home directory, whose
  location depends on the operating system and their username. Home directories
  are typically found inside `C:/Users/` on Windows, `/Users/` on macOS, and
  `/home/` on Linux.

As an example, suppose Ada wants to write a (relative) path from the `ada`
directory to Charles' cool selfie. Using these shortcuts, she can write:

```
../charles/cool_hair_selfie.jpg
```

Read this as, "Starting from wherever you are, go up one directory, then go to
the `charles` directory, and then go to the `cool_hair_selfie.jpg` file." Since
`/Users/ada` is Ada's home directory, she could also write the path as:

```
~/../charles/cool_hair_selfie.jpg
```

This path has the same effect, but the meaning is slightly different. You can
read it as "Starting from your home directory, go up one directory, then go to
the `charles` directory, and then go to the `cool_hair_selfie.jpg` file."

The `..` and `~` shortcut are frequently used and worth remembering. The `.`
shortcut is included here in case you see it in someone else's code. Since it
means the current directory, a path like `./cats.csv` is identical to
`cats.csv`, and the latter is preferable for being simpler. There are a few
specific situations where `.` is necessary, but they fall outside the scope of
this text.


### Saving Code

:::{tip}
When you start a new project, it's a good idea to create a specific directory
for all of the project's files. If you're using Python, you should also store
your Python code in that directory. As you work, periodically save your code.
:::

The most common way to save Python code is as a **Python script** with the
extension `.py` (see {numref}`reading-files` for more about extensions).
Editing a script is similar to editing any other text document. You can write,
delete, copy, cut, and paste code.

You can create a new Python script in JupyterLab with this menu option:

```
File -> New -> Python File
```

Every line in a Python script must be valid Python code. Anything else you want
to write in the script (notes, documentation, etc.) must be placed in a
**comment**. In Python, comments begin with `#` and extend to the end of the
line:

```
# This is a comment.
```

Python will ignore comments when you run your code.

Arrange your code in the order of the steps to solve the problem, even if you
write some parts before others. Comment out or delete any lines of code that
you try but ultimately decide you don't need. Make sure to save the file
periodically so that you don't lose your work. Following these guidelines will
help you stay organized and make it easier to share your code with others
later.


(jupyter-notebooks)=
#### Jupyter Notebooks

For data science tasks, it is also common to use a [**Jupyter
notebook**][jupyter] with extension `.ipynb` to store code. In addition to
Python code, Jupyter notebooks have full support for formatted text, images,
and code from other programming languages such as Julia and R. The tradeoff is
that Jupyter notebooks can only be viewed in a web browser.

[jupyter]: https://jupyter.org/

:::{Tip}
"Jupyter" is short for "Julia, Python, Text, and R."
:::

Jupyter notebooks are more convenient than Python scripts for interactive work
such as data analysis and learning or experimenting with the language. On the
other hand, Python scripts are more appropriate for long-running code that does
not require user interaction (such as web scrapers or scientific simulations)
and for developing packages and software. The remainder of this reader assumes
you're using a Jupyter notebook rather than the Python console or a Python
script, unless otherwise noted.

You can create a new Jupyter notebook in JupyterLab with this menu option:

```
File -> New -> Notebook
```

JupyterLab will prompt you to select a **kernel** for the notebook. The kernel
is the software used to run code in the notebook. For a notebook that will
contain Python code, you should choose a Python kernel.

After you select the kernel, you'll see a pane like this:

```{image} ../img/jupyterlab_notebook.png
:alt: A Jupyter notebook open in JupyterLab.
```

Jupyter notebooks are subdivided into **cells**. You can create as many cells
as you like, but each cell can only contain one kind of content, usually code
or text.

New cells are code cells by default. You can run a code cell by clicking on the
cell and pressing `Shift`-`Enter`. The notebook will display the result and
create a new empty code cell below the result:

```{image} ../img/jupyterlab_notebook_prod.png
:alt: A Jupyter notebook open in JupyterLab, showing an evaluated code cell.
```

You can convert a code cell to a text cell by clicking on the cell and
selecting the "Markdown" option from the cell type dropdown menu:

```{image} ../img/jupyterlab_notebook_cell_menu.png
:alt: 
```

Markdown is a simple language you can use to add formatting to your text. For
example, surrounding a word with asterisks, as in `Let *sleeping* dogs lie`,
makes the surrounded word italic. You can find a short, interactive tutorial
about Markdown [here][mdtutorial]. If you "run" a text cell by pressing
`Shift`-`Enter`, the notebook will display the text with any formatting you
added.

[mdtutorial]: https://www.markdowntutorial.com/


### The Working Directory

```
:tags: [remove-cell]
# Save working dir to reset at the end of this section.
import os

_wd = os.getcwd()
```

{numref}`absolute-relative-paths` explained that relative paths have a
starting point that depends on the context where the path is used. The _working
directory_ is the starting point Python uses for relative paths. Think of the
working directory as the directory Python is currently "at" or watching.

Python's built-in `os` module provides functions to manipulate the working
directory. The function `os.getcwd` returns the absolute path for the current
working directory, as a string. It doesn't require any arguments:

```
import os

os.getcwd()
```

On your computer, the output from `os.getcwd` will likely be different. This is
a very useful function for getting your bearings when you write relative paths.
If you write a relative path and it doesn't work as expected, the first thing
to do is check the working directory.

The related `os.chdir` function changes the working directory. It takes one
argument: a path to the new working directory. Here's an example:

```
os.chdir("..")

# Now check the working directory.
os.getcwd()
```

Generally, you should avoid using calls to `os.chdir` in your Jupyter notebooks
and Python scripts. Calling `os.chdir` makes your code more difficult to
understand, and can always be avoided by using appropriate relative paths. If
you call `os.chdir` with an absolute path, it also makes your code less
portable to other computers. It's fine to use `os.chdir` interactively (in the
Python console), but avoid making your saved code dependent on it.
 
Another function that's useful for dealing with the working directory and file
system is `os.listdir`. The `os.listdir` function returns the names of all of
the files and directories inside of a directory. It accepts a path to a
directory as an argument, or assumes the working directory if you don't pass a
path. For instance:

```
# List files and directories in /home/.
os.listdir("/home/")

# List files and directories in the working directory.
os.listdir()
```

As usual, since you have a different computer, you're likely to see different
output if you run this code. If you call `os.listdir` with an invalid path or
an empty directory, Python raises a `FileNotFoundError`:

```
:tags: [raises-exception]
os.listdir("/this/path/is/fake/")
```

```
:tags: [remove-cell]
# Reset the working dir.
os.chdir(_wd)
```
