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

<!-- Run at top level of repo. -->
```{code-cell}
:tags: ["remove-cell"]
cd("..")
```

Syntax and Features
===================

This section is a brief tour of common programming idioms as implemented in Julia. Most of them should be conceptually familiar from your previous programming experience.

Collections
-----------

Julia has most of the standard collection types you would expect in any langague. This section will focus on generic collection types; in a later lesson, we'll discuss Julia's implementation of data frames for working with tabular data.

### Default ordered collection: Vector

Vectors are the simplest kind of collection. Every collection in Julia has a type that includes the type of its contents. For example, a Vector containing only integers has a type of `Vector{Int}`, while a Vector containing both integers and strings will have a type of `Vector{Any}`. A Vector will automatically infer its type based on its contents.
```julia
v1 = [1, 2, 3]
v2 = ["a", "b", "c"]
v3 = [1, "b", v1]

println(typeof(v1))
println(typeof(v2))
println(typeof(v3))
```

In Julia, ordered collections are indexed from 1.
```julia
println(v2[1])
println(v3[3][1])
```

You can slice a collection to return a subset. Slices are inclusive, so this code:
```julia
v1[1:2]
```
...returns all items from the start index (`1`) up to and including the stop index (`2`).

### Default keyed collection: Dict

A Dict allows you to store a set of key/value pairs. This is analogous to a dictionary in Python, labeled list in R, or hashmap in Java.
```julia
d = Dict([("a", 1), ("b", 2), ("c", 3)])

println(typeof(d))
```

Each value is indexed by its unique key.
```julia
println(d["a"])
```

### Arrays

cf. https://docs.julialang.org/en/v1/manual/arrays/

Arrays are multidimensional ordered collections. They are the most commonly-used collection in Julia (a Vector is actually a 1-dimensional Array). You can create them by hand or by using one of the many built-in functions.
```julia
# Create an array of zeros, defaulting to Float64
zeros(2,4)

# Specify the type of the array
zeros(Int8, 2, 4)
```

Arrays are indexed column by row. This means that that their default orientation is vertical, and 1-dimensional Arrays are vertical by default. This can be a source of confusion because the `print()` and `println()` functions will print them horizontally in the Julia REPL.
```julia
a = reshape(collect(1:10), (2,5))
a[1,2]
```

You can concatenate arrays vertically or horizontally.
```julia
# Concatenate subarrays into a single (vertical) vector using the ; operator
a1 = [[1,2] ; [3,4]]

# Concatenate subarrys into a 2x2 matrix using the ;; operator
a2 = [[1,2] ;; [3,4]]
```

Arrays are passed by reference. In this example, Arrays `a` and `b` are both Views onto the same contiguous chunk of memory.
```julia
a = [3;4;5]
b = a
b[1] = 1
println(a)
```

Different arrangements of an Array are Views onto the same underlying array. If you want to avoid modifying the original array, you can explicitly copy it using `copy()` or `deepcopy()`.
```julia
# Makes a random 2x2 matrix
a = rand(2,2)

# Makes a view to the 2x2 matrix which is a 1-dimensional array
b = vec(a)

display(a)
display(b)
b[3] = 0.7
display(a)
```

Slicing an array returns a copy of the relevant chunk
```julia
c = a[1:2, 1]
a[1] = 0.5
display(c)
```

Linear algebra operations are matrix-wise by default
```julia
a = rand(2,4)
b = rand(4,2)

# Matrix-wise multiplication
a * b
```

If you want to do element-wise operations on an Array, you can use the broadcast operator `.` to apply a function to each element of the Array rather than the Array as a whole.
```julia
# Generically, apply function `f(x)` to each element of x
x .= f.(x)

# Example
x = rand(10)
x .= x .+ sqrt.(x)
```

To write this even more concisely you can wrap your code in the broadast macro, which applies the dot operater to all of the operators.
```julia
# Template
@. x = f(x)

# Example
@. x = x + sqrt(x)

# Or even more concisely
@. x += sqrt(x)
```

Iteration
---------

### Iterate over any ordered collection with a `for` loop

```julia
x = collect(10)
for i in x
    println(i)
end
```

### (Optional) Iterate over an Array

Loops are fast in Julia, so you can use them when doing element-wise calculations with Arrays. However, you usually won't want to explicity use the `for` loop syntax. Instead, you should use the broadcast operator, which is a more concise way to write the same code.
```julia
# Template
for i = 1:length(x)
  x[i] = f(x[i])
end

# Example
x = rand(10)
for i = 1:length(x)
  x[i] = x[i] + sqrt(x[i])
end
```

### Iterate over key/value pairs with a `for` loop

```julia
for (key, value) in d
    println(key, ":", value)
end
```

### Functional approaches to iteration

A common programming task is iterating through the members of a collection, performing an operation on (or with) each member, and returning a new collection contaning the results of those operations. Many languages provide a concise syntax for this task, referred to as a comprehension (cf. https://docs.julialang.org/en/v1/manual/arrays/#man-comprehensions).

By default, a comprehension returns a Vector (if 1-dimensional) or an Array (if multidimensional).
```julia
vec = [x for x in 1:20 if x % 2 == 0]
```

A comprehension produces a new collection of items immediately. In contrast, a generator expression allows you to produce each new items on demand. You can rewrite any comprehension as a generator expression by enclosing it in parentheses instead of square brackets.
```julia
gen = (x for x in 1:20 if x % 2 == 0)

for item in gen
    println(item)
end
```

Comprehensions and generator expressions are useful for building custom data structures.
```julia
using LinearAlgebra

diag = Diagonal([1/x for x in 1:20 if x % 2 == 0])
```

Julia has a large collection of higher-level functions (functions that act on functions), including `map`, `reduce`, `foldl`, `accumulate`, and various derivatives (e.g., `mapreduce`).

Conditionals and Flow Control
-----------------------------

### Conditionals (if/then/else)

```julia
i = 1
j = 2
if i < j
    println("i is less than j")
elseif i > j
    println("i is greater than j")
else
    println("i and j are equal")
end
```

### Truth testing

The built-in Boolean values in Julia are `true` and `false`.

Missing data is represented with `missing`.

Null values are represented with `nothing`.

You can check whether a collection includes an item by using the keyword `in` or the `in()` function:
```julia
if 1 in v
    println("v contains 1")
end

println(in(6, v))
```

Functions
---------

### Writing functions

Julia's function syntax is similar to other languages. If your function mutates an item (i.e. changes it in place), the function name should end with an exclamation point to let the user know this (e.g., `myfun` vs `myfun!`).
```julia
function add_missing!(item, collection)
    # Mutate collection in place
    if item in collection
        return collection
    else
        append!(collection, item)
        return collection
    end
end

println(v)
add_missing!(7, v)
println(v)
```

### Exceptions (try/catch/finally)

cf. https://docs.julialang.org/en/v1/manual/control-flow/#Exception-Handling

```julia
function root(item)
    try
        return sqrt(item)
    catch e
        println("You should have entered a numeric value")
        println(e)
    finally
        println("This always succeeds")
    end
end

root(10)
root("ten")
```

The Julia documentation cautions against overusing exceptions: "One thing to think about when deciding how to handle unexpected situations is that using a try/catch block is much slower than using conditional branching to handle those situations."

### Modules and Namespaces

Julia allows you to define custom modules and import them. Julia offers fine-grained control over what is imported (individual elements vs. entire module) and how it is imported (qualified by module namespace or unqualified). Read the documentation here: https://docs.julialang.org/en/v1/manual/modules/
