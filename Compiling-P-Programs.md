We will use the [Two-Phase-Commit](https://github.com/p-org/P/tree/master/Tutorial/TwoPhaseCommit) example in the Tutorials folder to describe how to compile P programs.
The details about the two phase commit example is provided [here](https://github.com/p-org/P/wiki/Two-Phase-Commit-Protocol-in-P). 

There are two ways of compiling a P programs: 
* [Recommended] Through commandline by passing the pproj (P project) file as input to the compiler.
* [Alternative] Through commandline by passing all the p files to be compiled as arguments to the compiler.

In this section, `pc` is the [alias](https://github.com/p-org/P/wiki/Build-P-on-Ubuntu-and-MacOS) to the P compiler.

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
....... project includes: P/Tutorial/TwoPhaseCommit/PSrc/Client.p
....... project includes: P/Tutorial/TwoPhaseCommit/PSrc/Participant.p
....... project includes: P/Tutorial/TwoPhaseCommit/PSrc/Coordinator.p
....... project includes: P/Tutorial/TwoPhaseCommit/PSrc/Timer.p
....... project includes: P/Tutorial/TwoPhaseCommit/PSrc/Events.p
....... project includes: P/Tutorial/TwoPhaseCommit/PSpec/Spec.p
....... project includes: P/Tutorial/TwoPhaseCommit/PTst/TestScripts.p
....... project includes: P/Tutorial/TwoPhaseCommit/PTst/TestDriver.p
Generated TwoPhaseCommit.cs...
```

### Passing all P files as inputs to the compiler (Alternative)

The following command can be used for compiling the TwoPhaseCommit example:

```shell
> pc .\PSrc\Client.p .\PSrc\Coordinator.p .\PSrc\Participant.p .\PSrc\Events.p .\PSrc\Spec.p .\PSrc\TestDriver.p .\PSrc\Timer.p -t:TwoPhaseCommit
```

----

Now that your first P program has been compiled, it is time to check it for errors. Next: [**Systematic Testing of P Programs**](https://github.com/p-org/P/wiki/Systematic-Testing-P-Programs)
