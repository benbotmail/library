# Trilinos Source-of-Truth Map

Priority order for claims:

1. Official Trilinos docs portal: <https://trilinos.github.io/documentation.html>
2. Upstream repo docs: `README.md`, `INSTALL.rst`, `doc/`, `demos/`
3. Build system sources: top-level CMake/TriBITS files
4. Package-local docs/examples/tests under `packages/<name>/`
5. Wiki/community references (explicitly labeled)

## Key upstream entrypoints identified
- `README.md`
- `INSTALL.rst`
- `PackagesList.cmake`
- `TPLsList.cmake`
- `doc`
- `demos`
- `sampleScripts`
- `.github/CODEOWNERS`
