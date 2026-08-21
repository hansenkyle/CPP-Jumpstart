# CPP Jumpstart

Build project using 

```
mkdir -p build
cd build
cmake ..
make -j
```

## Setting up a new project from this template

After cloning this repo to start a new project, update the following
(all currently use the placeholder name `myproject`):

- [CMakeLists.txt](CMakeLists.txt) — `project(myproject ...)`, the
  `myproject_core` library, the `myproject` executable, and the
  `MYPROJECT_BUILD_TESTS` / `MYPROJECT_ENABLE_COVERAGE` option names.
- [scripts/coverage.sh](scripts/coverage.sh) — the `-DMYPROJECT_BUILD_TESTS`
  and `-DMYPROJECT_ENABLE_COVERAGE` flags must match whatever you renamed
  the options to in `CMakeLists.txt`.
- This README's title and content.

Everything else (`src/`, `test/`, `scripts/format.sh`,
`scripts/check-format.sh`, `.github/workflows/ci.yml`) is name-agnostic
and can be left as-is.
