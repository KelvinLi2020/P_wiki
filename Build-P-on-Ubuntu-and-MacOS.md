## Tool Dependencies

You must acquire and install these dependencies yourself.

1. [Microsoft .NET Core SDK (2.2)](https://dotnet.microsoft.com/download/dotnet-core/2.2)

2. A recent Java JRE (to run the [ANTLR](https://www.antlr.org/) parser generator)

   On Ubuntu this can be installed via apt: `sudo apt-get install default-jre`

   On MacOS an installer can be downloaded from the [official Java downloads page](https://java.com/en/download/manual.jsp)

## Getting the Bits

```Bash
# This will create a directory 'P' in the current working directory
git clone https://github.com/p-org/P.git
cd P
```

## Building the Compiler

Open a terminal prompt and navigate to the `P` directory you cloned from GitHub.  To build the P compiler, run:

```Bash
./Bld/build-compiler.sh
```

This will place the compiler at the path `./Bld/Drops/Release/Binaries/Pc.dll`.  The compiler is a .NET Core application, so you can run it via the `dotnet` command:

```
dotnet ./Bld/Drops/Release/Binaries/Pc.dll
```
