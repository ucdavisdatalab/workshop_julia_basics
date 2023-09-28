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

(data-visualization-in-python)=
Data Visualization in Python
============================


```{image} ../img/visualization_landscape.png
:alt: A network of Python visualization packages.
```

_Image from [Jake VanderPlas][jake]. See [here][viz] for a version with links
to all of the packages!_

[jake]: http://vanderplas.com/
[viz]: https://rougier.github.io/python-visualization-landscape/landscape-colors.html

Creating aggregated information about a data set is often done with the intent
to share your results. A data visualization is an effective medium for
displaying results, and there are many ways to create one in Python. In fact,
so many visualization packages are available that there is even [a
website][pyviz] dedicated to helping people decide which to use. This reader
focuses on **static visualization**, where the visualization is a still image.
Some popular packages for creating static visualizations are:

[pyviz]: https://pyviz.org/

* **[matplotlib][]** is the foundation for most other visualization packages.
  matplotlib is low-level, meaning it's flexible but even simple plots may take
  [5 lines of code or more][ex]. It's good to know a little bit about
  matplotlib, but it probably shouldn't be your primary visualization package.
  Familiarity with MATLAB makes it easier to learn matplotlib.

* **[pandas][]** provides built-in plotting functions, which can be convenient
  but are more limited than what you'll find in dedicated visualization
  packages. They're also inconsistent about the expected format of the data.

* **[plotnine][]** is a copy of the popular R package [ggplot2][]. The package
  uses the **grammar of graphics**, a convenient way to describe visualizations
  in terms of layers. Familiarity with R's [ggplot2][] or Julia's
  [Gadfly.jl][gadfly] package makes it easier to learn plotnine (and
  vice-versa).

* **[seaborn][]** is designed specifically for making statistical plots. It's
  well-documented and stable.

[matplotlib]: https://matplotlib.org/
[ex]: https://dsaber.com/2016/10/02/a-dramatic-tour-through-pythons-data-visualization-landscape-including-ggplot-and-altair/
[pandas]: https://pandas.pydata.org/docs/user_guide/visualization.html
[ggplot2]: https://ggplot2.tidyverse.org/
[plotnine]: https://plotnine.readthedocs.io/en/stable/
[gadfly]: http://gadflyjl.org/stable/
[seaborn]: https://seaborn.pydata.org/

There are also many packages available for making **interactive
visualizations**.

This reader focuses on plotnine, so that the visualization skills you learn
here will also be relevant if you end up using R or Julia. plotnine has
detailed [documentation][plotnine]. It's also useful to look at the [ggplot2
documentation][ggplot2] and [cheatsheet][ggplot2-cheat].

[ggplot2-cheat]: https://github.com/rstudio/cheatsheets/blob/master/data-visualization-2.1.pdf


(preparing-to-visualize)=
Preparing to Visualize
----------------------

Before building a visualization, you will need to do a few preparatory steps.

(install-import-plotnine)=
### Install and Import plotnine

:::{note}
As of writing, conda installs plotnine 0.9 and matplotlib 3.6.1 by default.
This is a problem, because plotnine 0.9 is incompatible with matplotilb >= 3.6.

Incompatibility between different versions of packages is common and exactly
what conda was designed to solve. In this case, you can simply make sure that
you have a slightly older version of matplotlib by running this command in the
Terminal:

```
conda install -c conda-forge 'matplotlib<3.6'
```

The plotnine developers have already fixed the problem in the newest version of
plotnine, so once it's available through conda, this workaround will no longer
be necessary.
:::

While Matplotlib is included with Anaconda, plotnine is not. You will need to
install the plotnine package in order to use it. {numref}`installing-packages`
showed you how to install packages with conda via the Terminal:

```
conda install -c conda-forge plotnine
```

In {numref}`modules`, you learned how to import a module in a Python package
with the `import` keyword. Python also provides a `from` keyword to import
specific objects from within a module, so that you can access them without the
module name as a prefix. The syntax is:

```python
from MODULE import OBJECT
```

Replace `MODULE` with the name of the module and `OBJECT` with the name of the
object that you want to import.

For instance, if you import Pandas using:

```python
from pandas import DataFrame
```

You can then write:

```python
df = DataFrame()
```

You can also use the `from` keyword to import all objects in a module with the
wildcard character `*`. Generally you shouldn't do this, because objects in a
module will overwrite objects in your code if they have the same name. However,
the plotnine package was designed to be imported this way:

```{code-cell}
from plotnine import *
```


(configure-jupyter)=
### Configure Jupyter

Jupyter notebooks can display most static visualizations and some interactive
visualizations. If you're going to use visualization packages that depend on
Matplotlib (such as plotnine), it's a good idea to set up your notebook by
running:

```{code-cell}
# Initialize matplotlib
import matplotlib.pyplot as plt

plt.rcParams["figure.figsize"] = [10, 8]
```

The last line sets the default size of plots. You can increase the numbers to
make plots larger, or decrease them to make plots smaller.

:::{note}
In older versions of Jupyter and IPython, it was also necessary to run the
special IPython command `%matplotlib inline` to set up a notebook for plotting.
This is no longer necessary in modern versions, but you may still see people
use or mention it online. You can read more about this change in [this
StackOverflow question][so-mpl-inline].

[so-mpl-inline]: https://stackoverflow.com/questions/65934740/is-matplotlib-inline-still-needed
:::


(data-cleaning)=
### Data Cleaning

Finally, we need to do a small amount of data cleaning. The plots below will
focus on two variables, `death_year` and `scaled_bill_value`. But some rows
lack information for these variables, so they need to be removed. Along the
way, we will ensure that the variables' datatypes are set correctly.

:::{tip}
When making potentially destructive changes to a data set, it's a good idea to
reassign the altered data to a new variable.
:::

**Death year**

```{code-cell}
no_death = banknotes["death_year"].isin([np.nan, "-"])
to_plot = banknotes[no_death == False].copy()
to_plot["death_year"] = to_plot["death_year"].astype(int)
```

**Scaled bill value**

```{code-cell}
no_scaled = to_plot["scaled_bill_value"].isna()
to_plot = to_plot[no_scaled == False]
```

You are now ready to make a plot.


(grammar-of-graphics)=
The Grammar of Graphics
-----------------------

Recall that plotnine is a clone of ggplot2. The "gg" in ggplot2 stands for
*grammar of graphics*. The idea of a grammar of graphics is that visualizations
can be built up in layers. Visualizations that adhere to this grammar must
have:

* Data
* Geometry
* Aesthetics

There are also several optional layers. Here are a few:

| Layer       | Description                                        |
| :---------- | :------------------------------------------------- |
| scales      | Title, label, and axis value settings              |
| facets      | Side-by-side plots                                 |
| guides      | Axis and legend position settings                  |
| annotations | Shapes that are not mapped to data                 |
| coordinates | Coordinate systems (Cartesian, logarithmic, polar) |

With all this in mind, it's time to make a plot. But what kind of plot should
we make? It depends on what we want to know about the data set. Suppose we want
to understand the relationship between a banknote's value and how long ago the
person on the banknote died, as well as whether this is affected by gender. One
way to show this is to make a scatter plot.


### Layer 1: Data

The data layer determines the data set used to make the plot. plotnine is
designed to work with *tidy* data. Tidy means:
1. Each observation has its own row
2. Each feature has its own column
3. Each value has its own cell

Tidy data sets are convenient in general. A later lesson will cover how to make
an untidy data set tidy. Until then, we'll take it for granted that the data
sets we work with are tidy.

To set up the data layer, call the ``ggplot` function on a Data Frame:

```{code-cell}
ggplot(to_plot)
```

This returns a blank plot. We still need to add a few more layers.


### Layer 2: Geometry

The geometry layer determines the shape or appearance of the visual elements of
the plot. In other words, the geometry layer determines what kind of plot to
make: one with points, lines, boxes, or something else.

There are many different geometries available in plotnine. The package provides
a function for each geometry, always prefixed with `geom_`.

To add a geometry layer to the plot, choose the `geom_` function you want and
add it to the plot with the `+` operator:

```{code-cell}
:tags: [raises-exception]
ggplot(to_plot) + geom_point()
```

This returns an error message that we're missing aesthetics `x` and `y`. We'll
learn more about aesthetics in the next section, but this error message is
especially helpful: it tells us exactly what we're missing. When you use a
geometry you're unfamiliar with, it can be helpful to run the code for just the
data and geometry layer like this, to see exactly which aesthetics need to be
set.

As we'll see later, it's possible to add multiple geometries to a plot.


### Layer 3: Aesthetics

The aesthetic layer determines the relationship between the data and the
geometry. Use the aesthetic layer to map features in the data to aesthetics
(visual elements) of the geometry.

The `aes` function creates an aesthetic layer. The syntax is:

```python
aes(AESTHETIC = FEATURE, ...)
```

The names of the aesthetics depend on the geometry, but some common ones are
`x`, `y`, `color`, `fill`, `shape`, and `size`. There is more information about
and examples of aesthetic names in the documentation.

For example, we want to put `death_year` on the x-axis and `scalled_bill_value`
on the y-axis. It's best to use `scaled_bill_value` here rather than
`current_bill_value` because different countries use different scales of
curency. One United States Dollar is worth approximately one hundred Japanese
Yen, for example. Below, we will set the aesthetics for both of these values.
Notice however that the aesthetic layer is not added to the plot with the `+`
operator. Instead, it is passed as the second argument to the `ggplot`
function:

```{code-cell}
ggplot(
    to_plot,
    aes(x = "death_year", y = "scaled_bill_value")
) + geom_point()
```

**Per-geometry Aesthetics**

When you add the aesthetic layer or pass it to the `ggplot` function, it
applies to the entire plot. You can also set an aesthetic layer individually
for each geometry by passing the layer as the first argument in the `geom_`
function:

```{code-cell}
(ggplot(to_plot) +
    geom_point(aes(x = "death_year", y = "scaled_bill_value"))
)
```

:::{tip}
Enclose expressions with `()` to create multiline code. It would be possible to
write out all of the above on one line, but this would come at the expense of
readability.
:::


This is really only useful when you have multiple geometries. As an example,
let's color-code the points by gender. To do so, we need to convert `gender` to
*categorical* data, which measures a qualitative category.

```{code-cell}
(ggplot(to_plot) +
    geom_point(aes(x = "death_year", y = "scaled_bill_value", color = "factor(gender)"))
)
```

Now let's add labels to each point. To do this, we need to add another
geometry:

```{code-cell}
(ggplot(to_plot,
    aes(x = "death_year", y = "scaled_bill_value", color = "factor(gender)",
        label = "name")) +
    geom_point() + 
    geom_text()
)
```

Where you put the aesthetics matters:

```{code-cell}
(ggplot(to_plot,
    aes(x = "death_year", y = "scaled_bill_value", label = "name")) + 
    geom_point() + 
    geom_text(aes(color = "factor(gender)"))
)
```

**Constant Aesthetics**

If you want to set an aesthetic to a constant value, rather than one that's
data dependent, do so in the geometry layer rather than the aesthetic layer.
For instance, suppose you want to use point shape rather than color to indicate
gender, and you want to make all of the points blue.

```{code-cell}
(ggplot(to_plot,
    aes(x = "death_year", y = "scaled_bill_value", shape = "factor(gender)")) +
    geom_point(color = "blue")
)
```

If you set an aesthetic to a constant value inside of the aesthetic layer, the
results you get might not be what you expect:

```{code-cell}
:tags: [raises-exception]
(ggplot(to_plot,
    aes(x = "death_year", y = "scaled_bill_value", shape = "factor(gender)",
        color = "blue")) +
    geom_point()
)
```


### Layer 4: Scales

The scales layer controls the title, axis labels, and axis scales of the plot.
Most of the functions in the scales layer are prefixed with `scale_`, but not
all of them.

The `labs` function is especially important, because it's used to set the title
and axis labels. All graphs need a title and axis labels.

```{code-cell}
(ggplot(to_plot,
    aes(x = "death_year", y = "scaled_bill_value", shape = "factor(gender)")) + 
    geom_point() +
    labs(x = "Death Year", y = "Scaled Bill Value",
         title = "Does death year affect bill value?", shape = "Gender")
)
```

### Saving Plots

If you assign a plot to a variable, you can use the `save` method or the
`ggsave` function to save that plot to a file:

```
plot = (
    ggplot(to_plot,
    aes(x = "death_year", y = "scaled_bill_value", shape = "factor(gender)")) +
    geom_point() +
    labs(x = "Death Year", y = "Scaled Bill Value", 
         title = "Does death year affect bill value?", shape = "Gender")
)

ggsave(plot, "myplot.pdf")
```

The file format is selected automatically based on the extension. Common
formats are PNG and PDF.


(example-bar-plot)=
### Example: Bar Plot

Now suppose you want to plot the number of banknotes with people from each
profession in the banknotes data set. A bar plot is an appropriate way to
represent this visually.

The geometry for a bar plot is `geom_bar`. Since bar plots are mainly used to
display frequencies, the `geom_bar` function automatically computes frequencies
when used in conjunction with the `factor()` syntax from above.

We can also use a fill color to further breakdown the bars by gender. Here's
the code to make the bar plot:

```{code-cell}
(ggplot(to_plot,
    aes(x = "factor(profession)", fill = "factor(gender)")) +
    geom_bar(position = "dodge") + 
    theme(axis_text_x=element_text(rotation = 90))
)
```

The setting `position = "dodge"` instructs `geom_bar` to put the bars
side-by-side rather than stacking them. Adding `theme` allows you to change how
the axis labels and ticks are formatted.

In some cases, you may want to make a bar plot with frequencies you've already
computed. To prevent `geom_bar` from computing frequencies automatically, set
`stat = "identity"`.


(visualization-design)=
### Visualization Design

Designing high-quality visualizations goes beyond just mastering which Python
functions to call. You also need to think carefully about what kind of data you
have and what message you want to convey. This section provides a few
guidelines.

The first step in data visualization is choosing an appropriate kind of plot.
Here are some suggestions (not rules):

| Feature 1   | Feature 2   | Plot                          |
| :---------- | :---------- | :---------------------------- |
| categorical | categorical | bar, dot                      |
| categorical | categorical | bar, dot, mosaic              |
| numerical   |             | box, density, histogram       |
| numerical   | categorical | box, density, ridge           |
| numerical   | numerical   | line, scatter, smooth scatter |

If you want to add a:

* 3rd numerical feature, use it to change point/line size
* 3rd categorical feature, use it to change point/line style
* 4th categorical feature, use side-by-side plots

Once you've selected a plot, here are some rules you should almost always
follow:

* Always add a title and axis labels. These should be descriptive, not variable
  names!
* Specify units after the axis label if the axis has units. For instance,
  "Height (ft)"
* Don't forget that many people are colorblind! Also, plots are often printed
  in black and white. Use point and line styles to distinguish groups; color is
  optional
* Add a legend whenever you've used more than one point or line style
* Always write a few sentences explaining what the plot shows. Don't describe
  the plot, because the reader can just look at it. Instead, explain what they
  can learn from the plot and point out important details that may be
  overlooked
* For side-by-side plots, use the same axis scales for both plots so that
  comparing them is not deceptive

Visualization design is a deep topic, and whole books have been written about
it. One resource where you can learn more is DataLab's [Principle's of Data
Visualization Workshop Reader][rdr].

[rdr]: https://ucdavisdatalab.github.io/workshop_data_viz_principles/


Exercises
---------

### Exercise

1. Compute the number of banknotes that feature a person who died before 1990.
2. Of those people, how many were activists?

### Exercise

1. Compute the range of `first_appearance_year` for each country.
    + Hint: this would be a good place to try out a multi-function
      aggregation...
2. How many unique values are there among the first and last values of
  `first_appearance_year`?

### Exercise

1. Compute the set of banknotes who died in this century.
2. Use plotnine's `geom_segment` function to create a plot which shows the
  timespan between death year and first appearance as a horizontal segment for
  each banknote. Put the name of each person on the y-axis. Color code the
  segments by gender.
    + Hint: you can make the plot more visually appealing if you first sort the
      death year. You can use the `.sort_values` method to sort a DataFrame on
      a column, or set of columns. Be aware that the default parameter for one
      of the arguments is probably not what you're expecting.
