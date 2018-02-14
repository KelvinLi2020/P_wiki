(in progress)

The "Hello" program shows how to write and compile P programs using multiple files. In this example, the program consists of the following files: `Main.p`, `Continue.p`, `Timer.p`, `Env.p`, `TestScript.p`. This example also demonstrates non-determinism in P and shows how to use PTester to check a liveness property of a program.

## `Hello` machine

This is the main machine of the "Hello" example. 


```
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
The `Hello` machine starts by creating a timer with the `CreatetTimer` function declared in the `Timer` machine below. The ID of the `Hello` machine is passed to the `Timer` machine as the parameter `this` to the `CreateTimer` function. The `CreateTimer` function returns an ID of the `Timer` machine created, which is stored in the variable `timer` of type `TimerPtr`. The `TimerPtr` type is declared in the `Timer` machine as an alias of the `machine` type.
The  `Hello` machine then moves into the `GetInput` state. In that state, `Continue` function (declared below) returns a non-deterministic value of type `bool` (either `true` or `false`), which is assigned to the variable `b`. Depending on the value of `b`, the machine either goes into the `PrintHello` state, or into the `Stop` state.  In the `PrintHello` state, the timer is started, by calling the `StartTimer` function (declared in the `Timer` machine), which has two parameters: timer machine ID and timer interval. Upon receiving the `TIMEOUT` event (declared in the `Timer` machine), the `Hello` machine prints "Hello" and goes back into the `GetInput` state. The `Stop` state is a deadlock state; see the declaration of the `StopProgram` function below.

## `Timer` machine

The `Timer` machine represents an abstraction of a basic OS timer. 

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



## `Continue` header and function declaration
```
//ContinueHeader.p
fun Continue() : bool;
```

```
//Continue.p
fun Continue() : bool
{ 
    return $;
}
```
## `StopProgram` function 
```
//Env.p
fun StopProgram()
{
    
}
```
## Test Script
```
//TestScript.p
test Test0: main Hello in { Hello, Timer };
```