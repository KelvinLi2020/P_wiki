### Tool Dependencies
You must acquire and install these yourself.

1. [Microsoft .NET 4.6](http://www.microsoft.com/en-us/download/details.aspx?id=48130)
2. Visual Studio 2017 (The Free [Community Edition](https://www.visualstudio.com/downloads/) works, see below for setup instructions)
3. Windows 10 SDK version 10.0.15063.0

### Open-source Library Dependencies

1. Gardens Point Scanner Generator (http://gplex.codeplex.com/)
2. Gardens Point Parser Generator (http://gppg.codeplex.com/)
3. Formula (http://formula.codeplex.com/)
4. Zing (https://github.com/ZingModelChecker/Zing)
5. AGL (https://github.com/Microsoft/automatic-graph-layout)
6. PSharp (https://github.com/p-org/PSharp)

These are included in the "Ext" folder.

### Getting the bits

   `mkdir git`  
    `cd git`  
    `git clone https://github.com/p-org/P.git`  
    `cd P`

### Compiling PLANG from the Command Line

_Release x86 version_ - open a 2017 Visual Studio Developer Command Prompt:

`bld\build.bat release x86`

_Debug version_ :

`bld\build.bat debug x86`

Use 'x64' for 64 bit build.

Outputs of the build are placed in ~\Bld\Drops

### Compiling PLANG using Visual Studio

Do one command line build first because this builds the Zing compiler that we depend on.

Then you can load P.sln in Visual Studio, select the "x86" or "x64" build target (do not use AnyCPU).

### Running regression tests

See [Running P Regressions] (https://github.com/p-org/P/wiki/Running-P-Regressions).

As a side effect, PLANG will be rebuilt and 
regressions will be run against the x86 debug version placed in the Bld\Drops folder.

### Creating your first P program

See [Creating a P program](https://github.com/p-org/P/wiki/Creating-a-P-Program).

### Visual Studio Setup

Be sure to expand Languages and check the "C++" box.  Also expand "Windows and Web Development" and check the "Universal Windows App Development Tools" box as shown below:

![](https://github.com/p-org/P/wiki/images/vssetup.png)

