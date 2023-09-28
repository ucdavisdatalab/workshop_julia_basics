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


Organizing Code
===============

:::{admonition} Learning Objectives
* Create code that only runs when a condition is satisfied
* Write functions to organize and encapsulate reusable code
:::

## Conditional Statements

Sometimes you'll need code to do different things depending on a condition. You
can use an **if-statement** to write conditional code.

An if-statement begins with the `if` keyword, followed by a condition and a
colon `:`. The condition must be an expression that returns a Boolean value
(`False` or `True`). The **body** of the if-statement is the code that will run
when the condition is `True`. Code in the body must be indented by 4 spaces.

For example, suppose you want your code to generate a different greeting
depending on an input name:

```{code-cell}
name = "Nick"

# Default greeting
greeting = "Nice to meet you!"

if name == "Nick":
    greeting = "Hi Nick, nice to see you again!"

greeting
```

Use the `else` keyword (and a colon `:`) if you want to add an alternative when
the condition is false. So the previous code can also be written as:

```{code-cell}
name = "Nick"

if name == "Nick":
    greeting = "Hi Nick, nice to see you again!"
else:
    # Default greeting
    greeting = "Nice to meet you!"

greeting
```

Use the `elif` keyword with a condition (and a colon `:`) if you want to add an
alternative to the first condition that also has its own condition. Only the
first case where a condition is `True` will run. You can use `elif` as many
times as you want, and can also use `else`. For example:

```{code-cell}
name = "Susan"

if name == "Nick":
   greeting = "Hi Nick, nice to see you again!"
elif name == "Peter":
   greeting = "Go away Peter, I'm busy!"
else:
   greeting = "Nice to meet you!"

greeting
```

You can create compound conditions with the keywords `not`, `and` and `or`. The
`not` keyword inverts a condition. The `and` keyword combines two conditions
and returns `True` only if both are `True`. The `or` keyword combines two
conditions and returns `True` if either or both are `True`.
For example:

```{code-cell}
name1 = "Arthur"
name2 = "Nick"

if name1 == "Arthur" and name2 == "Nick":
  greeting = "These are the authors."
else:
  greeting = "Who are these people?!"

greeting
```

You can write an if-statement inside of another if-statement. This is called
**nesting** if-statements. Nesting is useful when you want to check a
condition, do some computations, and then check another condition under the
assumption that the first condition was `True`.

:::{tip}
If-statements correspond to **special cases** in your code. Lots of special
cases in code makes the code harder to understand and maintain. If you find
yourself using lots of if-statements, especially nested if-statements, consider
whether there is a more general strategy or way to write the code.
:::
