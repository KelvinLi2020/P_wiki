(The old testP.bat is now retired. UnitTests is the only tool which should be used to run regression tests.)

### How to start using UnitTests in VS2017

1.	Install the test adapter into Visual Studio (if you use ReSharper, you do not need it):

https://marketplace.visualstudio.com/items?itemName=NUnitDevelopers.NUnit3TestAdapter

2.	msbuild.exe should be in your PATH. To make sure that it is, start VS from the VS  developer command prompt:
```shell
>P.sln
```

3.	Open the Test Explorer window:
Test -> Windows -> Test Explorer

4.	Set up the topmost folder for the tests that you want to run:
Open TestCaseLoader.cs in UnitTests project and choose an appropriate value of the TestDirs variable (or write your own).
Build the UnitTests project to discover the tests.

        There's an alternative way to select tests: in the Test Explorer window, highlight the tests that you wish to run (similar to the FileExplorer), right-click, "Run Selected Tests".

5.	Choose Settings for the tests that you want to run:
UnitTests properties -> Settings
ResetTests;
RunPWithPSharp is for running liveness tests under RegressionTests\Liveness with PTester;
the rest of the settings are the same as for testP.bat.
Please note that RunAll overrides RunPc, RunPrt, etc., so it should be set to False if using any of those settings.

6.	Start running the tests from the Test Explorer window.

To run tests in parallel, check "Running Tests in Parallel" in the Test Explorer window (located at the top of the window).

### How to run UnitTests on command-line

```shell
dotnet build
dotnet test
```
