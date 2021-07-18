# Malmo Library

This is the Malmo library (0.37.0) extracted from main repository of Project Malmo, it's only containing core of library which the part of c++ (without Java, C# and python wrapper).

## Build

The build process would download [Hunter](https://github.com/cpp-pm/hunter)(package manager) and source code of dependencies, then build them on your local machine, so it would taking some time.

```bash
mkdir build
cd build
cmake ..
make
```

