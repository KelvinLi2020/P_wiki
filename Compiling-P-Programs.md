We will use the [Two-Phase-Commit](https://github.com/p-org/P/tree/master/Tutorial/TwoPhaseCommit) example in the Tutorials folder to describe how to compile P programs.
The details about the two phase commit example is provided [here](https://github.com/p-org/P/wiki/Two-Phase-Commit-Protocol-in-P). 

There are two ways of compiling a P programs: 
* [Recommended] Through commandline by passing the pproj (P project) file as input to the compiler.
* Through commandline by passing all the p files to be compiled as arguments to the compiler.

### Passing .pproj (P Project) file to the compiler (Recommended)

P compiler takes as input a P project file (.pproj) that provides all the necessary input to the compiler.
A sample project file for the TwoPhaseCommit example is presented below:
```
<Project name = "TwoPhaseCommit">
<InputFiles>
	<PFile>./PSrc/</PFile>
	<PFile>./PSpec/</PFile>
	<PFile>./PTst/</PFile>
</InputFiles>
<Target>Coyote</Target>
<TargetFileName>TwoPhaseCommit</TargetFileName>
<OutputDir>./PGenerated/</OutputDir>
</Project>
```
The following command can be used for compiling the TwoPhaseCommit example:
```shell
> pc -proj:TwoPhaseCommit.pproj 
```

Expected output:
```
.... Parsing the project file: TwoPhaseCommit.pproj
....... project includes: p/tutorial/twophasecommit/psrc/timer.p
....... project includes: p/tutorial/twophasecommit/psrc/coordinator.p
....... project includes: p/tutorial/twophasecommit/psrc/client.p
....... project includes: p/tutorial/twophasecommit/psrc/participant.p
....... project includes: p/tutorial/twophasecommit/psrc/events.p
....... project includes: p/tutorial/twophasecommit/pspec/spec.p
....... project includes: p/tutorial/twophasecommit/ptst/testdriver.p
....... project includes: p/tutorial/twophasecommit/ptst/testscripts.p
Generated TwoPhaseCommit.cs...

```

`pc` is the [alias](https://github.com/p-org/P/wiki/Build-P-on-Ubuntu-and-MacOS) to the P compiler.

### Passing all P files as inputs to the compiler

The following command can be used for compiling the TwoPhaseCommit example:

```shell
>> pc .\PSrc\Client.p .\PSrc\Coordinator.p .\PSrc\Participant.p .\PSrc\Events.p .\PSrc\Spec.p .\PSrc\TestDriver.p .\PSrc\Timer.p -t:TwoPhaseCommit
```

`pc` is the [alias](https://github.com/p-org/P/wiki/Build-P-on-Ubuntu-and-MacOS) to the P compiler.




