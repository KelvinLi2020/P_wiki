## Tool Dependencies

You must acquire and install these dependencies yourself.

1. [Microsoft .NET Core SDK (3.1)](https://dotnet.microsoft.com/download/dotnet-core/3.1)

   Successful installation can be verified with `dotnet --info`.

2. A recent Java JRE (to run the [ANTLR](https://www.antlr.org/) parser generator)

   On Ubuntu this can be installed via apt: `sudo apt-get install default-jre`

   On MacOS an installer can be downloaded from the [official Java downloads page](https://java.com/en/download/manual.jsp)

   Successful installation can be verified with `java -version`.

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
cd ..
```

This will place the compiler at the path `./Bld/Drops/Release/Binaries/Pc.dll`.  The compiler is a .NET Core application, so you can run it via the `dotnet` command:

```
dotnet ./Bld/Drops/Release/Binaries/Pc.dll
```


### [Optional] Adding P compiler and model checker alias to bash profile

We recommend that you add the following two alias to the bash profile (`~/.bash_profile`) so that you can invoke the P compiler (`pc`) and P model checker (`pmc`) from the commandline.

Add the following two lines to the `~/.bash_profile`.

```shell script
alias pc='dotnet <P-Folder>/Bld/Drops/Release/Binaries/Pc.dll'
alias pmc='dotnet <P-Folder>/packages/microsoft.coyote/1.0.5/lib/netcoreapp3.1/coyote.dll test'
```

and then

```shell script
source ~/.bash_profile
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
        C   : generate C code using the Prt runtime
        Coyote  : generate C# code using the Coyote runtime
    -proj:[.pprojfile]         -- the p project to be compiled
    -sourcemaps[:(true|false)] -- enable or disable generating source maps
                                  in the compiled C output. may confuse some
                                  debuggers.
    -h, -help, --help          -- display this help message
``` 
----

- After you compile and test your first P program, on running `pmc`, you should see:

```shell script
> pmc
usage: Coyote command path [schedule] [--?] [--method string] [--timeout uint] [--outdir string]
              [--verbose] [--break] [--version] [--iterations uint] [--max-steps uint]
              [--timeout-delay uint] [--fail-on-maxsteps] [--liveness-temperature-threshold uint]
              [--parallel uint] [--sch-random] [--sch-probabilistic uint] [--sch-pct uint]
              [--sch-fairpct uint] [--sch-portfolio] [--coverage string] [--instrument string]
              [--instrument-list string] [--explore] [--seed uint] [--wait-for-testing-processes]
              [--testing-scheduler-ipaddress string] [--testing-scheduler-endpoint string]
              [--graph-bug] [--graph] [--xml-trace] [--actor-runtime-log string]

The Coyote tool enables you to systematically test a specified Coyote test, generate a reproducible
bug-trace if a bug is found, and replay a bug-trace using the VS debugger.

Basic options:
--------------
  command                     : The operation perform (test, replay)
  path                        : Path to the Coyote program to test
  -m, --method string         : Suffix of the test method to execute
  -t, --timeout uint          : Timeout in seconds (disabled by default)
  -o, --outdir string         : Dump output to directory x (absolute path or relative to current
                                directory
  -v, --verbose               : Enable verbose log output during testing
  -b, --break                 : Attaches the debugger and also adds a breakpoint when an assertion
                                fails (disabled during parallel testing)
  --version                   : Show tool version

Systematic testing options:
---------------------------
  -i, --iterations uint       : Number of schedules to explore for bugs
  -ms, --max-steps uint       : Max scheduling steps to be explored during systematic testing (by
                                default 10,000 unfair and 100,000 fair steps).
                                You can provide one or two unsigned integer values
  --timeout-delay uint        : Controls the frequency of timeouts by built-in timers (not a unit of
                                time)
  --fail-on-maxsteps          : Consider it a bug if the test hits the specified max-steps
  --liveness-temperature-threshold uint
                              : Specify the liveness temperature threshold is the liveness
                                temperature value that triggers a liveness bug
  -p, --parallel uint         : Number of parallel testing processes (the default '0' runs the test
                                in-process)
  --sch-random                : Choose the random scheduling strategy (this is the default)
  -sp, --sch-probabilistic uint
                              : Choose the probabilistic scheduling strategy with given probability
                                for each scheduling decision where the probability is specified as
                                the integer N in the equation 0.5 to the power of N.  So for N=1,
                                the probability is 0.5, for N=2 the probability is 0.25, N=3 you get
                                0.125, etc.
  --sch-pct uint              : Choose the PCT scheduling strategy with given maximum number of
                                priority switch points
  --sch-fairpct uint          : Choose the fair PCT scheduling strategy with given maximum number of
                                priority switch points
  --sch-portfolio             : Choose the portfolio scheduling strategy

Replay and debug options:
-------------------------
  schedule                    : Schedule file to replay

Code and activity coverage options:
-----------------------------------
  -c, --coverage string       : Generate code coverage statistics (via VS instrumentation) with zero
                                or more values equal to:
                                 code: Generate code coverage statistics (via VS instrumentation)
                                 activity: Generate activity (state machine, event, etc.) coverage
                                statistics
                                 activity-debug: Print activity coverage statistics with debug info
  -instr, --instrument string
                              : Additional file spec(s) to instrument for code coverage (wildcards
                                supported)
  -instr-list, --instrument-list string
                              : File containing the paths to additional file(s) to instrument for
                                code coverage, one per line, wildcards supported, lines starting
                                with '//' are skipped

Advanced options:
-----------------
  --explore                   : Keep testing until the bound (e.g. iteration or time) is reached
  --seed uint                 : Specify the random value generator seed
  --wait-for-testing-processes
                              : Wait for testing processes to start (default is to launch them)
  --testing-scheduler-ipaddress string
                              : Specify server ip address and optional port (default: 127.0.0.1:0))
  --testing-scheduler-endpoint string
                              : Specify a name for the server (default: CoyoteTestScheduler)
  --graph-bug                 : Output a DGML graph of the iteration that found a bug
  --graph                     : Output a DGML graph of all test iterations whether a bug was found
                                or not
  --xml-trace                 : Specify a filename for XML runtime log output to be written to
  --actor-runtime-log string  : Specify an additional custom logger using fully qualified name:
                                'fullclass,assembly'

Optional Arguments:
-------------------
  -?, --?                     Show this help menu
Error: Missing required argument: 'path'
```

Great! You are all set to compile and test your first P program. Next: [**Compiling P Programs**](https://github.com/p-org/P/wiki/Compiling-P-Programs)