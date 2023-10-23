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

Syntax and Features
===================


Tentative Outline
-----------------

1. Collections
2. Iteration
3. Conditionals and Flow Control
4. Functions


Editorial Comments
------------------

Some of the "features" discussion seems like a better fit for other sections. For example:
1. Speed: Why Julia, Case Study
2. Unicode support: Why Julia, Types
3. Easy multihreading: Why Julia, Case Study


### Template
```{code-cell}
```

Collections
-----------

The rest of the workshop will focus on working with tabular data, so data frames will be covered in those sections. This is a brief tour of the standard "programmer" collections.

### Default ordered collection: Vector

1. Vectors are the default generic collection. A Vector will automatically infer its type based on its contents.

```{code-cell}
x = [1, 2, 3]
y = ["a", "b", "c"]
z = [1, "b", x]

println(typeof(x))
println(typeof(y))
println(typeof(z))
```

2. Ordered collections are indexed from 1.

```{code-cell}
println(y[1])
println(z[3][1])
```

### Default keyed collection: Dict

```{code-cell}
d = Dict([("a", 1), ("b", 2), ("c", 3)])

println(typeof(d))
println(d["a"])
```

### Arrays (?)

All the collections appear to be based on Array (?), so how much do we want to say about them specifically?
cf. https://docs.julialang.org/en/v1/manual/arrays/

1. Array{T} where T is a concrete immutable type
2. Operations are matrix-wise by default


Iteration
---------

### Iterate over any ordered collection with a `for` loop
```{code-cell}
for i in x
    println(i)
end
```

Note that loops are fast in Julia, so you should prefer them to "vectorized" operations.

### Iterate over key/value pairs with a `for` loop
```{code-cell}
for (key, value) in d
    println(key, ":", value)
end
```

### Other methods of iteration
We won't cover these, but you may be interested in learning more about:

1. While loops
2. List comprehensions
3. Generators

Conditionals and Flow Control
-----------------------------

### Conditionals (if/then/else)
```{code-cell}
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
1. `true`, `false`, `missing`, and `nothing`
2. Using `Bool` type for testing? This is confusing and should be called out.
3. `in()` and `in.()` for collection contents

```{code-cell}
if 1 in x
    println("x contains 1")
else
    println("x does not contain 1")
end

println(in(1, x))
println(in.(1, x))
```

### Exceptions (try/catch/finally)

The brief introduction states that exceptions are slow and therefore not recommended. Is the preferred style/performance approach to use explicit testing instead?

Functions
---------

### Built-in functions and their signatures

1. What are good (i.e. useful) examples of built-in functions
2. What are good examples of built-in functions that use multiple dispatch?
3. Should we mention the distinction between Functions and Methods?

### Writing functions
```{code-cell}
function add_missing(item, collection)
    # Mutate collection in place
    if item in collection
        return collection
    else
        append!(collection, item)
        return collection
    end
end
```

### Namespaces?

