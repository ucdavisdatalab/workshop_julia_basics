# Workshop: Julia Basics

[![License: CC BY-SA 4.0](https://img.shields.io/badge/License-CC_BY--SA_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by-sa/4.0/)

_[UC Davis DataLab][datalab], [UC Julia Users Group][ucjug]_  
_Winter 2024_  
_Authors: Derek Devnich, Ezra Morrison, Carl Stahmer, Nick Ulle_  
_Maintainer: Nick Ulle <<naulle@ucdavis.edu>>_  

* [Reader](https://ucdavisdatalab.github.io/workshop_julia_basics/)

<!--
* [Event Page](https://datalab.ucdavis.edu/eventscalendar/YOUR_EVENT/)
-->

[datalab]: https://datalab.ucdavis.edu/
[ucjug]: https://datalab.ucdavis.edu/julia-users-group/

:warning: This workshop is still in development!

This workshop is an introduction to the Julia programming language for people
familiar R, Python, or MATLAB. Compared to those languages, Julia code
typically runs orders of magnitude faster but has a similar level of
abstraction, so you can focus on your research problem rather than hardware
minutiae. Julia also provides out-of-the-box Unicode support, an easy-to-use
package manager, multithreading facilities, a macro system, and a rich type
system to optimize and prevent bugs in your code. Workshop topics include a
concise overview of Julia's syntax and features, an end-to-end introduction to
using built-in functions and contributed packages to read, summarize, and
visualize tabular data, real-world examples where we've found Julia beneficial.

After this workshop, learners will be able to describe Julia's strengths and
weaknesses relative to other programming languages and get started using Julia
in their own research projects.

Learners must already be proficient in a language like R or Python. All
learners will need access to an internet-connected computer with the latest
versions of Zoom and Julia.


## Contributing

The course reader is a live webpage, hosted through GitHub, where you can enter
curriculum content and post it to a public-facing site for learners.

To make alterations to the reader:
	  
1.  Check in with the reader's current maintainer and notify them about your 
    intended changes. Maintainers might ask you to open an issue, use pull 
    requests, tag your commits with versions, etc.

2.  Run `git pull`, or if it's your first time contributing, see
    [Setup](#setup).

3.  Edit an existing chapter file or create a new one. Chapter files may be 
    either Markdown files (`.md`) or Jupyter Notebook files (`.ipynb`). Either 
    is fine, but you must remain consistent across the reader (i.e. don't mix 
    and match filetypes). Put all chapter filess in the `chapters/` directory.
    Enter your text, code, and other information directly into the file. Make 
    sure your file:

    - Follows the naming scheme `##_topic-of-chapter.md/ipynb` (the only 
      exception is `index.md/ipynb`, which contains the reader's front page).
    - Begins with a first-level header (like `# This`). This will be the title
      of your chapter. Subsequent section headers should be second-level
      headers (like `## This`) or below.

    Put any supporting resources in `data/` or `img/`.

4.  Run the command `jupyter-book build .` in a shell at the top level of the
    repo to regenerate the HTML files in the `_build/`.

5.  When you're finished, `git add`:
    - Any files you edited directly
    - Any supporting media you added to `img/`

    Then `git commit` and `git push`. This updates the `main` branch of the
    repo, which contains source materials for the web page (but not the web
    page itself).

6.  Run the command `ghp-import -n -p -f _build/html` in a shell at the top
    level of the repo to update the `gh-pages` branch of the repo. This uses
    the [`ghp-import` Python package][ghp-import], which you will need to
    install first (`pip install ghp-import`). The live web page will update
    automatically after 1-10 minutes.

[ghp-import]: https://github.com/c-w/ghp-import


## Setup

### Python Packages

We recommend using [mamba][] by way of [Miniforge][] to manage Python
dependencies. The `env.yaml` file in this repo contains a list of packages
necessary to build the reader. After installing Miniforge, you can create a new
environment with all of the packages listed in that file with this shell
command:

```sh
mamba env create --file env.yaml
```

[mamba]: https://mamba.readthedocs.io/en/latest/
[miniforge]: https://github.com/conda-forge/miniforge


### Julia Packages

To build the chapters with Julia code, you'll need Julia 1.9 and the [`IJulia`
package][IJulia]. You can install the package by running these commands in a
Julia prompt:

```julia
using Pkg
Pkg.add("IJulia")
```

[IJulia]: https://github.com/JuliaLang/IJulia.jl
