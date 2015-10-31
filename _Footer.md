PLANG has dependencies that are not part of the PLANG project.

##Closed-source Dependencies
----------------------------------------------------------
You must acquire and install these yourself.

1. Microsoft .NET 4.5 (http://www.microsoft.com/en-us/download/details.aspx?id=30653)  
2. Visual Studio 2012 (Update 4) or Visual Studio 2013  

Open-source Dependencies
----------------------------------------------------------

1. Gardens Point Scanner Generator (http://gplex.codeplex.com/)
2. Gardens Point Parser Generator (http://gppg.codeplex.com/)
3. Formula (http://formula.codeplex.com/)
4. Zing (https://github.com/ZingModelChecker/Zing)
5. AGL (https://github.com/Microsoft/automatic-graph-layout)

Compiling PLANG
----------------------------------------------------------
Release version - open a command prompt:

cd Somewhere\Plang\Bld
build.bat

Debug version - open a command prompt:

cd Somewhere\Plang\Bld
build.bat -d

Outputs of the build are placed in Somewhere\Plang\Bld\Drops

Running regression tests
----------------------------------------------------------
cd Somewhere\Plang\Tst
testP.bat RegressionTests.txt

As a side effect, PLANG will be rebuilt with build.bat -d and
regressions will be run against the x86 debug version placed in the Bld\Drops folder.