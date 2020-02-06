## Tool Dependencies

You must acquire and install these dependencies yourself.

1. [Microsoft .NET Core SDK (2.2)](https://dotnet.microsoft.com/download/dotnet-core/2.2)

2. A recent Java JRE (to run the [ANTLR](https://www.antlr.org/) parser generator)

   On Ubuntu this can be installed via apt: `sudo apt-get install default-jre`

   On MacOS an installer can be downloaded from the [official Java downloads page](https://java.com/en/download/manual.jsp)

## Getting the Bits

```shell script
# This will create a directory 'P' in the current working directory
git clone https://github.com/p-org/P.git
cd P
```
In the rest of this section, we assume `<P-Folder>` represents the path to the folder where you cloned P.

## Building the Compiler

Open a terminal prompt and navigate to the `P` directory you cloned from GitHub.  To build the P compiler, run:

```shell script
cd <P-Folder>/Bld/
./build-compiler.sh
```

This will place the compiler at the path `./Bld/Drops/Release/Binaries/Pc.dll`.  The compiler is a .NET Core application, so you can run it via the `dotnet` command:

```
dotnet ./Bld/Drops/Release/Binaries/Pc.dll
```


### [Optional] Adding P compiler and model checker alias to bash profile

We recommend that you add the following two alias to the bash profile (`~\.bash_profile`) so that you can invoke the P compiler (`pc`) and P model checker (`pmc`) from the commandline.

Add the following two lines to the `~\.bash_profile`.

```shell script
alias pc='dotnet <P-Folder>/Bld/Drops/Release/Binaries/Pc.dll'
alias pmc='dotnet $HOME/.nuget/packages/microsoft.coyote/1.0.0-rc9/lib/netcoreapp2.2/coyote.dll test'
```

and then

```shell script
source ~\.bash_profile
```
-----
- On running `pc`, you should see:

```shell script
> pc
At least one .p file must be provided
USAGE: Pc.exe file1.p [file2.p ...] [-t:tfile] [options]
USAGE: Pc.exe -proj:<.pproj file>
    -t:[tfileName]             -- name of output file produced for this compilation unit; if not supplied then file1
    -outputDir:[path]          -- where to write the generated files
    -generate:[C,Coyote]       -- select a target language to generate
        C       : generate C code using the Prt runtime
        Coyote  : generate C# code
    -proj:[.pprojfile]         -- the p project to be compiled
    -sourcemaps[:(true|false)] -- enable or disable generating source maps
                                  in the compiled C output. may confuse some
                                  debuggers.
    -h, -help, --help          -- display this help message
``` 
----

- on running `pmc`, you should see:

```shell script
> pmc
usage: Coyote command path [schedule] [--?] [--method string] [--timeout uint] [--outdir string] [--verbose]
              [--iterations uint] [--max-steps uint] [--parallel uint] [--sch-random]
              [--sch-pct uint] [--sch-fairpct uint] [--sch-portfolio] [--break] [--coverage string]
              [--instrument string] [--instrument-list string] [--explore] [--sch-seed int]
              [--wait-for-testing-processes] [--testing-scheduler-ipaddress string]
              [--testing-scheduler-endpoint string] [--graph-bug] [--graph]
              [--actor-runtime-log string]

The Coyote tool enables you to systematically test a specified Coyote test, generate a reproducible
bug-trace if a bug is found, and replay a bug-trace using the VS debugger.

Basic options:
--------------
  command                     : The operation perform (test, replay)
  path                        : Path to the Coyote program to test
  -m, --method string         : Suffix of the test method to execute
  -t, --timeout uint          : Timeout in seconds (disabled by default)
  -o, --outdir string         : Dump output to directory x (absolute path or relative to current directory
  -v, --verbose               : Enable verbose log output during testing

Systematic testing options:
---------------------------
  -i, --iterations uint       : Number of schedules to explore for bugs
  -ms, --max-steps uint       : Max scheduling steps to be explored (disabled by default).
                                You can provide one or two unsigned integer values
  -p, --parallel uint         : Number of parallel testing processes (the default '0' runs the test in-process)
  --sch-random                : Choose the random scheduling strategy (this is the default)
  --sch-pct uint              : Choose the PCT scheduling strategy with given maximum number of priority switch points
  --sch-fairpct uint          : Choose the fair PCT scheduling strategy with given maximum number of priority switch points
  --sch-portfolio             : Choose the portfolio scheduling strategy

Replay and debug options:
-------------------------
  schedule                    : Schedule file to replay
  -b, --break                 : Attach debugger and break at bug

Code and activity coverage options:
-----------------------------------
  -c, --coverage string       : Generate code coverage statistics (via VS instrumentation) with zero or more values equal to:
                                 code: Generate code coverage statistics (via VS instrumentation)
                                 activity: Generate activity (state machine, event, etc.) coverage
                                statistics
                                 activity-debug: Print activity coverage statistics with debug info
  -instr, --instrument string
                              : Additional file spec(s) to instrument for code coverage (wildcards supported)
  -instr-list, --instrument-list string
                              : File containing the paths to additional file(s) to instrument for code coverage, one per line,
                                wildcards supported, lines starting with '//' are skipped

Advanced options:
-----------------
  --explore                   : Keep testing until the bound (e.g. iteration or time) is reached
  --sch-seed int              : Specify the random seed for the tester
  --wait-for-testing-processes
                              : Wait for testing processes to start (default is to launch them)
  --testing-scheduler-ipaddress string
                              : Specify server ip address and optional port (default: 127.0.0.1:0))
  --testing-scheduler-endpoint string
                              : Specify a name for the server (default: CoyoteTestScheduler)
  --graph-bug                 : Output a DGML graph of the iteration that found a bug
  --graph                     : Output a DGML graph of all test iterations whether a bug was found or not
  --actor-runtime-log string  : Custom runtime log to use instead of the default

Optional Arguments:
-------------------
  -?, --?                     Show this help menu
Error: Missing required argument: 'path'
```

Great! You are all set to compile and test your first P program. Next: [**Two Phase Commit Example in P**](https://github.com/p-org/P/wiki/Two-Phase-Commit-Protocol-in-P)