The "Hello" program shows how to write (and compile) P programs by using multiple files, in particular, how a machine can use a function or an event declared in another file. This example also demonstrates non-determinism in P and shows how to use PTester to check a liveness property of a program.
In this example, the program consists of the following files: `Main.p`, `Continue.p`, `Timer.p`, `Env.p`, `TestScript.p`.
You can find the `Hello` example [here](https://github.com/p-org/P/tree/master/Tutorial/Hello).

## `Hello` machine

This is the main machine of the "Hello" example. 
```
//Main.p
machine Hello
{
  var timer: TimerPtr;
  start state Init {  
    entry { 	
      timer = CreateTimer(this);
      goto GetInput; 
    } 
  }

  state GetInput {
	  entry {
      var b: bool;
      b = Continue();
      if (b) 
        goto PrintHello;
      else
        goto Stop;
	  }
  }

  state PrintHello {
    entry {
      StartTimer(timer, 100);
    }
    on TIMEOUT goto GetInput with {
      print "Hello\n";      
    }
  }

  state Stop { 
    entry {
      StopProgram();
    }
  }
}
```
The `Hello` machine starts by creating a timer with the `CreatetTimer` function declared in the `Timer` machine below. The address of the `Hello` machine is passed to the `Timer` machine as the parameter `this` to the `CreateTimer` function. The `CreateTimer` function returns an address of the `Timer` machine created, which is stored in the variable `timer` of type `TimerPtr`. The `TimerPtr` type is declared in the `Timer` machine as an alias of the `machine` type. Next, `goto` statement `goto GetInput;` is executed, and the machine moves into the `GetInput` state. In that state, `Continue` function (see below) returns a non-deterministic value of type `bool` (either `true` or `false`), which is assigned to the variable `b`. The non-deterministic value is denoted in P by the `$` symbol. Depending on the value of `b`, the machine either goes into the `PrintHello` state, or into the `Stop` state.  In the `PrintHello` state, the timer is started by calling the `StartTimer` function (declared in the `Timer` machine, see below), which has two parameters: timer machine address and timer interval. Upon receiving the `TIMEOUT` event (declared in the `Timer` machine), the `Hello` machine prints "Hello" and goes back into the `GetInput` state. Here, the construct `on TIMEOUT goto GetInput with { print "Hello\n"; }` is an example of a `goto` statement with an attached code block, which is executed before transitioning to the destination state.
The `Stop` state is a deadlock state; see the declaration of the `StopProgram` function below.

## `Timer` machine

The `Timer` machine represents an abstraction of a basic OS timer. For more details on the `receives` and `sends` annotations, see section ["Modules"](https://github.com/p-org/P/wiki/Modules) of the Tutorial.

```
// Timer.p
type TimerPtr = machine;

// from client to timer
event START: int;
// from timer to client
event TIMEOUT: TimerPtr;

//Functions for interacting with the timer machine
fun CreateTimer(owner : machine): TimerPtr {
	var m: machine;
	m = new Timer(owner);
	return m;
}

fun StartTimer(timer: TimerPtr, time: int) {
	send timer, START, time;
}

machine Timer
//receives/sends annotations:
receives START;
sends TIMEOUT;
{
  var client: machine;

  start state Init {
    entry (payload: machine) {
      client = payload;
      goto WaitForReq;
    }
  }

  state WaitForReq {
    on START goto WaitForTimeout;
  }

  state WaitForTimeout {
    ignore START;
    on null goto WaitForReq with { 
	  send client, TIMEOUT, this; 
	}
  }
}
```
The construct `ignore START;` indicates that the `WaitForTimeout` state does not handle the `START` event; it is used to avoid the "unhandled event" exception that P runtime would raise otherwise.
The last event handler `on null goto WaitForReq with { ... }` transfers the control to WaitForReq, modeling the case when the timer has elapsed, if neither of the other event handlers in WaitForTimeout can execute. The event null is a special event that is internally generated in the runtime in this case.


## `Continue` function declaration
```
//Continue.p
fun Continue() : bool
{ 
    return $;
}
```
## `StopProgram` function declaration 
```
//Env.p
fun StopProgram()
{
    
}
```

## Compilation and testing
To compile the `Hello` example for testing by PTester and to see the files generated after each step, run [build.cmd](https://github.com/p-org/P/blob/master/Tutorial/Hello/build.cmd) script found in the `Hello` example folder.
The script contains:
* compilation of the dependencies: Timer.p and Env.p
* compilation of the `Hello` machine - files `Main.p` and `Continue.p` - with the dependencies
* linking command, which uses a test script `TestScript.p`
* command for running PTester - `pt.exe` - for the DLL generated by the linker.

The `TestScript.p` file provides information to the linker about the main machine name of the program (`Hello`) and other machines involved in the test (`Timer` in this example):
```
//TestScript.p
test Test0: main Hello in { Hello, Timer };
```
`TestScript.p` is a simplest version of a test declaration. In this case, its only purpose is to declare the `Hello` machine as the main machine, and to indicate that the single test declared in the script file involves another machine - `Timer`. A test declaration in general could contain a module declaration, declarations of multiple tests, and properties that the PTester will check. For more on modules, interfaces and test declarations, see section ["Modules"](https://github.com/p-org/P/wiki/Modules) of the Tutorial.

Here's the output of running the PTester on the `Hello` program:
```
pt.exe /psharp Test0.dll
... Task 0 is using 'Random' strategy (seed:).
..... Iteration #1
Bugs found: 1
<CreateLog> Created Machine Hello-1
<CreateLog> Main machine Hello was created by machine Runtime
<StateLog> Machine Hello-1 entering State Hello_Init
<FunctionLog> Machine Hello-1 executing Function CreateTimer

ERROR: Liveness violation
Dumping coverage information
... Writing coverage.txt
... Writing coverage.dgml
```
The `/psharp` option is necessary for liveness checking; PTester uses PSharpTester's liveness algorithm for liveness properties. For the `Hello` example, PTester finds liveness violation (deadlock); it is presented in the `coverage.txt` file:
```
//coverage.txt

Total event coverage: 0.0%
Machine: Hello
***************
Machine event coverage: 0.0%

	State: Hello_PrintHello is uncovered

	State: Hello_Stop is uncovered

	State: Hello_GetInput is uncovered

	State: Hello_Init is uncovered
```
The state in `coverage.txt` file are prefixed by the machine name `Hello`. The states explored by the algorithm in presented bottom-to-top; 