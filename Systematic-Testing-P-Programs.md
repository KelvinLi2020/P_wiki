We assume that you have compiled the P implementation of the Two Phase Commit Protocol by following the instructions: [Compiling P Programs](https://github.com/p-org/P/wiki/Compiling-P-Programs)
### Compiling the Generated C# Code
The generated C# code must be compiled into either a `dll` before passing it to the P model-checker.

```shell script
> dotnet build TwoPhaseCommit.csproj
```
Expected output:
```shell script
Microsoft (R) Build Engine version 16.5.0+d4cbfca49 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 434.73 ms for P/Src/PRuntimes/CoyoteRuntime/CoyoteRuntime.csproj.
  Restore completed in 434.73 ms for P/Tutorial/TwoPhaseCommit/TwoPhaseCommit.csproj.
  CoyoteRuntime -> P/Src/PRuntimes/CoyoteRuntime/D:/Workspace/P/Bld/Drops/Debug/Binaries/CoyoteRuntime.dll
  TwoPhaseCommit -> P/Tutorial/TwoPhaseCommit/bin/netcoreapp3.1/Debug/TwoPhaseCommit.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:01.63
```
The above command generates the `TwoPhaseCommit.dll` that is used by the model-checker to systematically test the protocol.

### Running the Systematic Testing Engine

We now run the P tester on the Two Phase Commit program.

The command below runs the model checker to explore `10000` schedules using `random` search strategy for the `Test0` test case. 

In the end, a coverage report is generated with the activities (states, and transition) explored.

```shell script
pmc ./bin/netcoreapp3.1/Debug/TwoPhaseCommit.dll -i 10000 --coverage activity -m TwoPhaseCommit.Test0.Execute
```

Expected output:

```shell script
. Testing ./bin/netcoreapp3.1/Debug/TwoPhaseCommit.dll
... Method TwoPhaseCommit.Test0.Execute
Starting TestingProcessScheduler in process 14777
... Created '1' testing task.
... Task 0 is using 'random' strategy (seed:402764580).
..... Iteration #1
..... Iteration #2
..... Iteration #3
..... Iteration #4
..... Iteration #5
..... Iteration #6
..... Iteration #7
..... Iteration #8
..... Iteration #9
..... Iteration #10
..... Iteration #20
..... Iteration #30
..... Iteration #40
..... Iteration #50
..... Iteration #60
..... Iteration #70
..... Iteration #80
..... Iteration #90
..... Iteration #100
..... Iteration #200
..... Iteration #300
..... Iteration #400
..... Iteration #500
..... Iteration #600
..... Iteration #700
..... Iteration #800
..... Iteration #900
..... Iteration #1000
..... Iteration #2000
..... Iteration #3000
..... Iteration #4000
..... Iteration #5000
..... Iteration #6000
..... Iteration #7000
..... Iteration #8000
..... Iteration #9000
..... Iteration #10000
... Emitting coverage reports:
..... Writing ./bin/netcoreapp3.1/Debug/Output/TwoPhaseCommit.dll/CoyoteOutput/TwoPhaseCommit.dgml
..... Writing ./bin/netcoreapp3.1/Debug/Output/TwoPhaseCommit.dll/CoyoteOutput/TwoPhaseCommit.coverage.txt
..... Writing ./bin/netcoreapp3.1/Debug/Output/TwoPhaseCommit.dll/CoyoteOutput/TwoPhaseCommit.sci
... Testing statistics:
..... Found 0 bugs.
... Scheduling statistics:
..... Explored 10000 schedules: 10000 fair and 0 unfair.
..... Number of scheduling points in fair terminating schedules: 58 (min), 71 (avg), 86 (max).
... Elapsed 36.0917208 sec.
. Done
```
Coverage report is available in file: `TwoPhaseCommit.coverage.txt`

Running the `Test1` testcase will find a liveness bug as 2PC does not guarantee progress in the presence of failures.

```shell script
> pmc ./bin/netcoreapp3.1/Debug/TwoPhaseCommit.dll -i 10000 --coverage activity -m TwoPhaseCommit.Test1.Execute
```

Expected output:
```shell script
. Testing ./bin/netcoreapp3.1/Debug/TwoPhaseCommit.dll
... Method TwoPhaseCommit.Test1.Execute
Starting TestingProcessScheduler in process 14878
... Created '1' testing task.
... Task 0 is using 'random' strategy (seed:3467474431).
..... Iteration #1
..... Iteration #2
... Task 0 found a bug.
... Emitting task 0 traces:
..... Writing ./bin/netcoreapp3.1/Debug/Output/TwoPhaseCommit.dll/CoyoteOutput/TwoPhaseCommit_0_0.txt
..... Writing ./bin/netcoreapp3.1/Debug/Output/TwoPhaseCommit.dll/CoyoteOutput/TwoPhaseCommit_0_0.dgml
..... Writing ./bin/netcoreapp3.1/Debug/Output/TwoPhaseCommit.dll/CoyoteOutput/TwoPhaseCommit_0_0.schedule
... Elapsed 0.1843672 sec.
... Emitting coverage reports:
..... Writing ./bin/netcoreapp3.1/Debug/Output/TwoPhaseCommit.dll/CoyoteOutput/TwoPhaseCommit.dgml
..... Writing ./bin/netcoreapp3.1/Debug/Output/TwoPhaseCommit.dll/CoyoteOutput/TwoPhaseCommit.coverage.txt
..... Writing ./bin/netcoreapp3.1/Debug/Output/TwoPhaseCommit.dll/CoyoteOutput/TwoPhaseCommit.sci
... Testing statistics:
..... Found 1 bug.
... Scheduling statistics:
..... Explored 2 schedules: 2 fair and 0 unfair.
..... Found 50.00% buggy schedules.
..... Number of scheduling points in fair terminating schedules: 34 (min), 53 (avg), 73 (max).
... Elapsed 0.2933865 sec.
. Done
```

Error trace is available in the file `TwoPhaseCommit_0_0.txt` above.

----

Now that you know how to compile and test P programs, it is time to write one of your own! Next: [**Syntax Highlighting for P Programs in IntelliJ**](https://github.com/p-org/P/wiki/SyntaxHighlighting)
