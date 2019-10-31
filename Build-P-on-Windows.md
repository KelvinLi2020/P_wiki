## Tool Dependencies

You must acquire and install these dependencies yourself.

1. [Microsoft .NET Core SDK (>= 2.1)](https://dotnet.microsoft.com/download/dotnet-core/2.1)
3. Visual Studio 2017 (The Free [Community Edition](https://www.visualstudio.com/downloads/) works, see below for setup instructions)
4. Windows 10 SDK version 10.0.15063.0

## Getting the Bits

```Powershell
# This will create a directory 'P' in the current working directory
git clone "https://github.com/p-org/P.git"
cd P
```

## Building the Compiler

Open a PowerShell prompt and navigate to the `P` directory you cloned from GitHub.  To build the P compiler, run:

```Powershell
.\Bld\build-compiler.ps1
```

This will place the compiler at the path `.\Bld\Drops\Release\Binaries\win-x64\Pc.exe`