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

Introduction
============

Installation
------------

1. Download the Julia installer for your operating system: https://julialang.org/downloads/#official_binaries_for_manual_download
2. Follow the installation instructions for your operating system: https://julialang.org/downloads/platform/
3. Install the Pluto notebook (cf. https://plutojl.org/#install)
   - Start Julia via the "Julia" program or by running the `julia` command in a terminal.
   - Run the following commands from the Julia prompt:
     ```julia
     import Pkg
     Pkg.add("Pluto")
     ```
4. Start Pluto by running the following commands from the Julia prompt:
   ```julia
   import Pluto
   Pluto.run()
   ```

For more information about using Pluto, consult the FAQ: https://github.com/fonsp/Pluto.jl/wiki


Why Julia?
----------

In the beginning, there was Fortran. Fortran offered (and offers) unparalleled speed paired with a deeply unpleasant programming experience. Every subsequent scientific computing language has attempted to answer the question, "What if Fortran didn't suck?". Polite language designers refer to this as "ergonomics."

Julia is a modern scientific computing language that offers unparalleled speed paired with a remarkably pleasant programming experience. In particular, it offers:

1. Fast compiled performance for both general-purpose and specialized numerical programs
2. Clean, internally-consistent syntax
3. An interactive, dynamically-typed development workflow
4. A reasonably complete and growing library of utility modules for general-purpose development
5. Native code parallelization

### Julia deployments in the wild

Julia is currently being used for large-scale scientific computing projects, displacing Python/NumPy as the language of choice for new development. Examples include:

- Climate modeling: https://clima.caltech.edu/
- Astronomy: https://juliahub.com/case-studies/celeste/
- Drug discovery: https://juliahub.com/case-studies/pfizer/
- Other case studies: https://juliahub.com/case-studies/

### Local migrations to Julia

The UC Davis DataLab chose Julia for several of its recent research computing projects and acn talk about their experiences, as well as the tradeoffs involved in choosing it for new work.


Comparison with other languages
-------------------------------

A high-level discussion of architectual differences between Julia and other languages:
https://docs.julialang.org/en/v1/#man-julia-compared-other-languages

Noteworthy differences between Julia and other scientific computing languages:
https://docs.julialang.org/en/v1/manual/noteworthy-differences/

Comparing Julia Data Frames with Data Frames in other languages:
https://dataframes.juliadata.org/stable/man/comparisons/
