We assume that you have compiled the P implementation of the Two Phase Commit Protocol by following the instructions [here]()
### Compiling the Generated C# Code
The generated C# code must be compiled into either a `dll` or `exe` before passing it to the model-checker.

```shell
cd /P/Src/Samples/TwoPhaseCommit
dotnet build .\TwoPhaseCommit.csproj 
```

The above command generates the `TwoPhaseCommit.dll`.

### Running the Systematic Testing Engine

We now run the P tester (coyote) on the Brick Manager models.
`Coyote` provides a lot of commandline options for different types of search explorations, coverage generation, parallel search, etc.

The command below runs the model checker to explore `10000` schedules using a `porfolio` of different search strategies that implement hieristics for finding concurrency bugs. The model checker checks the `Test0`
test case. In the end, a coverage report is generated with the activities (states, and transition) explored.

```shell
cd /P/Src/Samples/TwoPhaseCommit
dotnet ~/.nuget/packages/microsoft.coyote/1.0.0-rc2/lib/netcoreapp2.2/coyote.dll test /Users/ankushpd/Workspace/P/Src/Samples/TwoPhaseCommit/bin/netcoreapp2.2/Debug/TwoPhaseCommit.dll  -i 10000 --sch-portfolio --coverage activity -m TwoPhaseCommit.Test0.Execute
```

Running the `Test1` testcase will find a bug.

```shell
dotnet ~/.nuget/packages/microsoft.coyote/1.0.0-rc2/lib/netcoreapp2.2/coyote.dll test /Users/ankushpd/Workspace/P/Src/Samples/TwoPhaseCommit/bin/netcoreapp2.2/Debug/TwoPhaseCommit.dll  -i 10000 --sch-portfolio --coverage activity -m TwoPhaseCommit.Test1.Execute
```
