---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
---

References
==========

This chapter contains links to resources we've found helpful as we've learned
and used Julia.


Documentation & Tutorials
-------------------------

* [The Official Julia Documentation][julia-docs]
    + Noteworthy differences [from R][r-diff], [from Python][py-diff], and
      [from MATLAB][mat-diff]
    + [Integers and floating point numbers][julia-numbers]
* [The DataFrames.jl Documentation][df-docs]
* [Interactive Julia Data Frames Tutorial][df-tut]
* UCLA's Biostatistics 257 Course
    + [2022 Materials][ucla-22]
    + [2023 Materials][ucla-23]


[julia-docs]: https://docs.julialang.org/en/v1/manual/getting-started/
[r-diff]: https://docs.julialang.org/en/v1/manual/noteworthy-differences/#Noteworthy-differences-from-R
[py-diff]: https://docs.julialang.org/en/v1/manual/noteworthy-differences/#Noteworthy-differences-from-Python
[mat-diff]: https://docs.julialang.org/en/v1/manual/noteworthy-differences/#Noteworthy-differences-from-MATLAB
[julia-numbers]: https://docs.julialang.org/en/v1/manual/integers-and-floating-point-numbers/

[df-docs]: https://dataframes.juliadata.org/stable/
[df-tut]: https://github.com/bkamins/Julia-DataFrames-Tutorial

[ucla-22]: https://ucla-biostat-257.github.io/2022spring/schedule/schedule.html
[ucla-23]: https://ucla-biostat-257.github.io/2023spring/syllabus/syllabus.html


Communities
-----------

* [The Official Julia Discourse][julia-forum]
* [University of California Julia User Group][ucjug]

[julia-forum]: https://discourse.julialang.org/
[ucjug]: https://datalab.ucdavis.edu/julia-users-group/


Editors
-------

* [Pluto.jl][], reactive notebooks for Julia
* [Jupyter][], notebooks for Julia, Python, text, and R
* [Julia for VSCode][julia-vscode]

[Pluto.jl]: https://plutojl.org/
[julia-vscode]: https://www.julia-vscode.org/
[Jupyter]: https://jupyter.org/


Packages
--------

* [Revise.jl][], a package to watch and automatically reload Julia scripts when
  they change. Useful for testing code as you write it.
* [Literate.jl][] for literate programming—generate formatted documents from
  comments in a Julia source file.
* Statistical modeling
    + [Distributions.jl][] provides sampling functions and density/distribution
      functions for common probability distributions.
    + [Flux.jl][] provides native Julia support for statistical modeling and
      machine learning. Some models are GPU-accelerated.
    + [Turing.jl][] provides probabilistic programming and Bayesian modeling
      functions.
* [Unitful.jl][] to assign low- or no-overhead units to values and
  automatically perform appropriate conversions.
* [JuMP.jl][], a domain-specific language for mathematical optimization in
  Julia.
* [CUDA.jl][] for low-level GPU computing support.
* [Symbolics.jl][], a computer algebra system for Julia.
* [Genie][], a web framework similar to Django.

[Revise.jl]: https://github.com/timholy/Revise.jl
[Literate.jl]: https://github.com/fredrikekre/Literate.jl
[Distributions.jl]: https://github.com/JuliaStats/Distributions.jl
[Flux.jl]: https://github.com/FluxML
[Turing.jl]: https://github.com/TuringLang/Turing.jl
[Unitful.jl]: https://github.com/PainterQubits/Unitful.jl
[JuMP.jl]: https://github.com/jump-dev/JuMP.jl
[CUDA.jl]: https://github.com/JuliaGPU/CUDA.jl
[Symbolics.jl]: https://github.com/JuliaSymbolics/Symbolics.jl
[Genie]: https://genieframework.com/

