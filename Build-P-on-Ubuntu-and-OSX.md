## Dependencies
Dependencies to build P on Ubuntu 16.04 or OSX are .Net Core (>= 2.1.5), Java (as we use [ANTLR](https://www.antlr.org/)) and cmake(>=2.8); Other *nix are untested but should also work.

Installation List (Ubuntu 16.04):
1. Install [.Net Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-2.1.2)
2. Install [cmake](https://cmake.org/install/)
3. Install Java ```sudo apt-get install default-jre```


## Building P Compiler
- Once you have installed the dependencies, you should be able to compile P with just:

```{r, engine='bash', count_lines}
  $ cd Bld; ./build-core.sh
```
