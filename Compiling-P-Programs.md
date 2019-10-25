
There are two ways of compiling a P programs: 
* Through commandline by passing all the p files to be compiled as arguments to the compiler.
* Through commandline by passing the pproj (P project) file as input to the compiler.

The P compiler has the following options:

```
>> <pathtocompiler>\Pc.exe
USAGE: Pc.exe file1.p [file2.p ...] [-t:tfile] [options]
USAGE: Pc.exe -proj:<.pproj file>
    -t:[tfileName]             -- name of output file produced for this compilation unit; if not supplied then file1
    -outputDir:[path]          -- where to write the generated files
    -generate:[C,P#]           -- select a target language to generate
        C   : generate C code using the Prt runtime
        P#  : generate C# code using the P# runtime
    -proj:[.pproj file]        -- the p project to be compiled
    -sourcemaps[:(true|false)] -- enable or disable generating source maps
                                  in the compiled C output. may confuse some
                                  debuggers.
    -h, -help, --help          -- display this help message
```

### Passing all P files as inputs to the compiler

The following command can be used for compiling the TwoPhaseCommit example in folder `\Src\Samples\TwoPhaseCommit` and generating the P# output.

```
>> <pathtocompiler>\Pc.exe .\PSrc\Client.p .\PSrc\Coordinator.p .\PSrc\Participant.p .\PSrc\Events.p .\PSrc\Spec.p .\PSrc\TestDriver.p .\PSrc\Timer.p -generate:P# -t:TwoPhaseCommit

Generated TwoPhaseCommit.cs...
```

### Passing .pprof (P Project) file to the compiler

It could be tedious to pass all the P files as commandline arguments to the P compiler.
P compiler also takes as input a P project file (.pproj) that provides all the necessary input to the compiler.
A sample project file for the TwoPhaseCommit example is presented below:
```
<Project name = "TwoPhaseCommit">
	<InputFiles>
		<PFile>./PSrc/Client.p</PFile>
		<PFile>./PSrc/Coordinator.p</PFile>
		<PFile>./PSrc/Events.p</PFile>
		<PFile>./PSrc/Participant.p</PFile>
		<PFile>./PSrc/Spec.p</PFile>
		<PFile>./PSrc/TestDriver.p</PFile>
		<PFile>./PSrc/Timer.p</PFile>
	</InputFiles>
	<Target>P#</Target>
	<TargetFileName>TwoPhaseCommit</TargetFileName>
	<OutputDir>./PGenerated/</OutputDir>
</Project>
```
Now the following command can be used for compiling the TwoPhaseCommit example:
```
>> <pathtocompiler>\Pc.exe -proj:.\twophasecommit.pproj
.... Parsing the project file: .\twophasecommit.pproj
Generated TwoPhaseCommit.cs...
```