(in progress)

The "Hello" program shows how to write and compile programs using multiple files. 

## `Timer` machine

We begin by showing the code for a simple `Timer` machine which represents an abstraction of a basic OS timer. 

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
## Test Script
```
//TestScript.p
test Test0: main Hello in { Hello, Timer };
```