# CPP Jumpstart

Build project using 

```
mkdir -p build
cd build
cmake ..
make -j
```

## Generating documentation

API documentation is generated from the Doxygen-style comments in
`src/` using [Doxygen](https://www.doxygen.nl/), themed with
[doxygen-awesome-css](https://jothepro.github.io/doxygen-awesome-css/).
The layout (tabs + resizable treeview sidebar, dark-mode toggle button,
MathJax for equations) is modeled on
[Cantera's C++ API docs](https://cantera.org/documentation/docs-3.0/doxygen/html/index.html).

Prerequisites: install Doxygen (e.g. `sudo apt install doxygen` or
`brew install doxygen`) and Graphviz (`sudo apt install graphviz` or
`brew install graphviz`), which draws the per-file `#include` dependency
graphs. Graphviz is optional -- if it's missing, CMake prints a warning
and the docs still build, just without those graphs.

Build the docs by configuring with `-DMYPROJECT_BUILD_DOCS=ON` and
building the `docs` target:

```
cmake -B build -DMYPROJECT_BUILD_DOCS=ON
cmake --build build --target docs
```

Then open `build/docs/html/index.html` in a browser. The Doxygen
configuration lives in [docs/Doxyfile.in](docs/Doxyfile.in), a template
that CMake fills in with real paths (including the fetched
doxygen-awesome-css theme) at configure time — edit that file to
change what gets documented or how it's styled.

## Setting up a new project from this template

After cloning this repo to start a new project, update the following
(all currently use the placeholder name `myproject`):

- [CMakeLists.txt](CMakeLists.txt) — `project(myproject ...)`, the
  `myproject_core` library, the `myproject` executable, and the
  `MYPROJECT_BUILD_TESTS` / `MYPROJECT_ENABLE_COVERAGE` /
  `MYPROJECT_BUILD_DOCS` option names.
- [docs/Doxyfile.in](docs/Doxyfile.in) — the `PROJECT_NAME` and
  `PROJECT_BRIEF`.
- [scripts/coverage.sh](scripts/coverage.sh) — the `-DMYPROJECT_BUILD_TESTS`
  and `-DMYPROJECT_ENABLE_COVERAGE` flags must match whatever you renamed
  the options to in `CMakeLists.txt`.
- This README's title and content.

Everything else (`src/`, `test/`, `scripts/format.sh`,
`scripts/check-format.sh`, `.github/workflows/ci.yml`) is name-agnostic
and can be left as-is.
