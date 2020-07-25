(in progress)

We look at a more sophisticated program to illustrate other features of the P language.
This program implements a failure detection protocol.
A failure detector state machine is given a list of machines,
each of which represents a daemon running at a computing node in a distributed system.
The failure detector sends each machine in the list a `PING` event and determines
whether the machine has failed if it does not respond with a `PONG` event within a certain
time period.
The failure detector uses an OS timer to implement the bounded wait for the `PONG` event.

## `Timer` machine

We begin by showing the code for the `Timer` machine.
The `Timer` machine represents
an abstraction of the OS timer.
This abstraction is used to test the interaction of the failure detector protocol
with the OS.
For executing the protocol, its interaction with the `Timer` machine is replaced with stubs
for which the programmer must provide hand-written C code.

```
// Timer.p
// events from client to timer
event START: int;
event CANCEL;
// events from timer to client
event TIMEOUT: machine;
event CANCEL_SUCCESS: machine;
event CANCEL_FAILURE: machine;
machine Timer {
    var client: machine;
    start state Init {
        entry (payload: machine) {
            client = payload;
            goto WaitForReq;
        }
    }

    state WaitForReq {
        on CANCEL goto WaitForReq with {
            send client, CANCEL_FAILURE, this;
        }
        on START goto WaitForCancel;
    }

    state WaitForCancel {
        ignore START;
        on CANCEL goto WaitForReq with {
            if ($) {
                send client, CANCEL_SUCCESS, this;
            } else {
                send client, CANCEL_FAILURE, this;
                send client, TIMEOUT, this;
            }
        }
        on null goto WaitForReq with {
            send client, TIMEOUT, this;
        }
    }
}
```

Each instance of the `Timer` machine has a client whose address is stored in the `client` variable.
This client sends `START` and `CANCEL` events to the timer machine which responds
via `TIMEOUT`, `CANCEL_SUCCESS`, or `CANCEL_FAILURE` events.

The `Timer` machine has three states.
It starts executing in the state `Init` where it initializes the `client` variable
with the client's address obtained by looking up `payload`.
It waits in state `WaitForReq` for a request from the client, responding
with `CANCEL_FAILURE` to `CANCEL` event and moving to `WaitForCancel` state on `START` event.

In `WaitForCancel` state, any `START` event is dequeued and dropped without any action
(indicated by the `ignore` keyword).
The response to `CANCEL` event is nondeterministic, to model the race condition between
the arrival of `CANCEL` event from the client and the elapse of the timer.
This non-determinism is indicated by an `if` statement guarded by `$`.
The then-branch models the case when the `CANCEL` event arrives before the timer elapses;
in this case, `CANCEL_SUCCESS` is sent back to the client.
The else-branch models the case when the timer fires before the `CANCEL` event arrives;
in this case, `CANCEL_FAILURE` and `TIMEOUT` are sent back to the client one after another.
The last event handler `on null goto WaitForReq with { ... }` transfers the control
to `WaitForReq`, modeling that the case when timer has elapsed,
if neither of the other event handlers in `WaitForCancel` can execute.
The event `null` is a special event that is internally generated in the runtime in this case.

## Failure detection protocol

The failure detection protocol depends on the `Timer` machines and is based on
the interaction of two kinds of machines - `FailureDetector` and `Node` - shown below.
The code of the `FailureDetector` machine shows more features of P, including
container types, functions, `do` actions, `push` transitions and pop statements,
deferred events, and monitors.

P supports container types, including tuples, named tuples or structs, sequences, and maps:
* Tuple type `(t1,...,tn)` describes ordered collections of length `n`, with value types
`t1` through `tn` respectively.
* Struct type `(f1:t1,...,fn:tn)` describes named tuples with fields `f1` through `fn`
of types `t1` through `tn` respectively.
* Sequence type `seq[T]` describes variable-length ordered sequences of values of type `T`,
e.g., variable `nodes` of type `seq[machine]`.
* Map type `map[K,V]` describes associative arrays with key type `K` and value type `V`,
e.g., variable `pending` (in the `Safety` spec) of type `map[machine,int]`.
* Set type `set[T]` describes unordered collections of unique values of type `T`,
e.g., variable `alive` of type `set[machine]`.

P also supports functions that can be invoked from any code block.
A function can have named parameters that are local to its body.

State `Init` illustrates the use of `push` transitions.
After appropriately initializing variables in the entry block, the raised event
`UNIT` is handled by pushing state `SendPing` on top of the current state `Init`.
Thus, the use of `push` transitions creates a stack of states encoding the
control of a state machine.
As long as state `Init` is on the stack,
event handlers declared inside it are available to be executed regardless of
changes in control state above it.
The event handlers for `REGISTER_CLIENT` and `UNREGISTER_CLIENT` are such handlers.
A `push` transition from `Init` to `SendPing` (rather than a `goto` transition)
enables the declaration of these handlers in one place with reuse
everywhere else in `FailureDetector`.
In addition to offering reuse of event handlers, push transitions also enable
reuse of protocol logic for handling specific interactions with other machines.
We later explain this use of a `push` transition in handling the interaction between
`FailureDetector` and `Timer`.

State `SendPing` illustrates several new features of P.
When this state is entered, its entry block sends `PING` events to
all alive nodes that have not yet responded with a `PONG`,
and starts a timer with a timeout value of 100ms.
The machine stays in this state collecting `PONG` responses
until either all alive nodes have responded, or a `TIMEOUT` event is dequeued.
If each alive node has responded with a `PONG` before `TIMEOUT` is dequeued,
the timer is canceled and the event `TIMER_CANCELED` is raised.
Otherwise, the handler of `TIMEOUT` is executed to determine whether
another attempt to reach the potentially alive nodes should be made.
If the number of attempts has reached the maximum number of attempts (2 in our code),
failure notifications are sent.

```
// FailureDetector.p
// request from failure detector to node
event PING: machine;
// response from node to failure detector
event PONG: machine;
// register a client for failure notification
event REGISTER_CLIENT: machine;
// unregister a client from failure notification
event UNREGISTER_CLIENT: machine;
// failure notification to client
event NODE_DOWN: machine;
// local events for control transfer within failure detector
event UNIT;
event ROUND_DONE;
event TIMER_CANCELED;

machine FailureDetector {
    var nodes: seq[machine];        // nodes to be monitored
    var clients: set[machine];      // registered clients
    var attempts: int;              // number of PING attempts made
    var alive: set[machine];        // set of alive nodes
    var responses: set[machine];    // collected responses in one round
    var timer: machine;

    start state Init {
        entry (payload: seq[machine]) {
            nodes = payload;
            InitializeAliveSet();       // initialize alive to the members of nodes
            timer = new Timer(this);
            raise UNIT;
        }
        on REGISTER_CLIENT do (payload: machine) { clients += (payload); }
        on UNREGISTER_CLIENT do (payload: machine) { if (payload in clients) clients -= (payload); }
        on UNIT push SendPing;
    }

    state SendPing {
        entry {
            SendPings();            // send PING events to machines that have not responded
            send timer, START, 100; // start timer for intra-round duration
        }
        on PONG do (payload: machine) {
            // collect PONG responses from alive machines
            if (payload in alive) {
                responses += (payload);
                if (sizeof(responses) == sizeof(alive)) {
                    // status of alive nodes has not changed
                    send timer, CANCEL;
                    raise TIMER_CANCELED;
                }
            }
        }
        on TIMER_CANCELED push WaitForCancelResponse;
        on TIMEOUT do {
            // one attempt is done
            attempts = attempts + 1;
            // maximum number of attempts per round == 2
            if (sizeof(responses) < sizeof(alive) && attempts < 2) {
                goto SendPing;  // try again by re-entering SendPing
            } else {
                Notify();       // send any failure notifications
                goto Reset;
            }
        }
        on ROUND_DONE goto Reset;
    }

    state WaitForCancelResponse {
        defer TIMEOUT, PONG;
        on CANCEL_SUCCESS do { raise ROUND_DONE; }
        on CANCEL_FAILURE do { pop; }
    }

    state Reset {
        entry {
            // prepare for the next round
            attempts = 0;
            responses = default(set[machine]);
            send timer, START, 1000;  // start timer for inter-round duration
        }
        on TIMEOUT goto SendPing;
        ignore PONG;
    }

    fun InitializeAliveSet() {
        var i: int;
        i = 0;
        while (i < sizeof(nodes)) {
            alive += (nodes[i]);
            i = i + 1;
        }
    }

    fun SendPings() {
        var i: int;
        i = 0;
        while (i < sizeof(nodes)) {
            if (nodes[i] in alive && !(nodes[i] in responses)) {
                announce M_PING, nodes[i];
                send nodes[i], PING, this;
            }
            i = i + 1;
        }
    }

    fun Notify() {
        var i: int;
        var cs: set[machine];
        var c: machine;
        i = 0;
        while (i < sizeof(nodes)) {
            if (nodes[i] in alive && !(nodes[i] in responses)) {
                alive -= (nodes[i]);
                cs = clients;
                while (sizeof(cs) != 0) {
                    c = choose(cs);
                    cs -= (c);
                    send c, NODE_DOWN, nodes[i];
                }
            }
            i = i + 1;
        }
    }
}

machine Node {
    start state WaitPing {
        on PING do (payload: machine) {
            send payload, PONG, this;
        }
    }
}

event M_PING: machine;

spec Safety observes M_PING, PONG {
    var pending: map[machine, int];

    start state Init {
        on M_PING do (payload: machine) {
            if (!(payload in pending))
                pending[payload] = 0;
            pending[payload] = pending[payload] + 1;
            assert (pending[payload] <= 3);
        }
        on PONG do (payload: machine) {
            assert (payload in pending);
            assert (0 < pending[payload]);
            pending[payload] = pending[payload] - 1;
        }
    }
}
```

State `SendPing` illustrates the use of `do` actions.
The handler for `PONG` in `SendPing` is indicated by
the code `on PONG do { ... }`, which states that the code block
between the braces must be executed when `PONG` is dequeued.
Control remains in the state `SendPing` subsequent to this execution.
This event handler is useful for executing an event-driven
loop in a state, such as the one in `SendPing` for
collecting `PONG` responses.

State `SendPing` also illustrates the use of a `push` transition
to factor out the logic for handling timer cancelation.
When `TIMER_CANCELED` is raised after canceling the timer
because all alive nodes have responded with `PONG`,
the handler `on TIMER_CANCELED push WaitForCancelResponse`
pushes the state `WaitForCancelResponse` on top of `SendPing`.
The state `WaitForCancelResponse` handles the interaction with the timer
subsequent to its cancellation, returning control back to `SendPing` afterwards.
The timer may respond with either `CANCEL_SUCCESS` or `CANCEL_FAILURE`.
In the former case, the event `ROUND_DONE` is raised, which is not handled
in state `WaitForCancelResponse`, causing it to be popped and letting `SendPing`
handle that event.
In the latter case, the `pop` statement executes, causing `WaitForCancelResponse`
to be popped and a fresh event being dequeued in state `SendPing`.

The state `WaitForCancelResponse` uses the code `defer TIMEOUT, PONG`
to indicate that it is not willing to handle `TIMEOUT` and `PONG` states in
this state.
Therefore, while the machine is blocked in this state, the P runtime does
not dequeue these two events, instead skipping over them to retrieve any other event
in the input queue.

When the `FailureDetector` machine is blocked
in the state `WaitForCancelResponse`, its stack has three states on it.
Starting from the bottom of the stack, these states are `Init`, `SendPing`, and
`WaitForCancelResponse`.
The `Init` state specifies `do` handlers for `REGISTER_CLIENT` and `UNREGISTER_CLIENT`,
the `SendPing` state specifies `do` handlers for `PONG` and `TIMEOUT`,
and the `WaitForCancelResponse` state defers `PONG` and `TIMEOUT`.
The dequeue logic for P allows the execution of `do` handlers specified
anywhere in the stack unless deferred in a state above.
Therefore, in state `WaitForCancelResponse`, `REGISTER_CLIENT` and `UNREGISTER_CLIENT`
may be dequeued but `TIMEOUT` and `PONG` may not.

P allows programmers to write assertions in code blocks to express invariants
on a state local to a machine.
It is often useful to be able to write assertions about states across machines in a program.
P provides monitors to write such specifications.
Consider the problem of specifying a property that the difference in the number of `PING` events sent
to any node can never be three more than the number of `PONG` events sent by it.
We can code this specification using the state machine `Safety`.
This machine maintains the difference between the number of `PING` and `PONG` events
per node in a map and asserts that this number can be at most three.
A specification state machine must explicitly mention the events observed by it.
For example, the `Safety` specification observes `M_PING` and `PONG` events.
An event sent from one machine to another using the `send` statement is automatically
routed to any specification that observes it.
Although observedd events are most commonly represent program events
sent from one state machine to another, sometimes it is important to introduce events
specifically for the purpose of observing.
The event `M_PING` is such an event.
This event is generated by the failure detector using the statement
`monitor M_PING, nodes[i]` right before it sends a `PING` event to `nodes[i]`.
Unlike the `PING` event, whose payload is the identifier of the failure detector,
the payload of the `M_PING` event is the identifier of the target node.
Using the payloads of `M_PING` and `PONG`, the `Safety` machine is able to
implement its per-node check.

## Liveness
```
event M_START: map[machine, bool];

spec Liveness observes M_START, NODE_DOWN {
    var nodes: map[machine, bool];
    start state Init {
        on M_START goto Wait;
    }

    hot state Wait {
        entry (payload: map[machine, bool]) {
            nodes = payload;
        }
        on NODE_DOWN do (payload: machine) {
            nodes -= payload;
            if (sizeof(nodes) == 0)
                raise UNIT;
        }
        on UNIT goto Done;
    }

    state Done { }
}
```
In addition to safety specifications,
P also allows programmers to express liveness specifications,
such as absence of deadlocks and livelocks in the test program.
The simplest characterization of a deadlock is a finite execution at the end of which
no machine is enabled, that is, every machine is either halted or blocked waiting for
an event.
However, not all such executions would typically be considered erroneous.
To further refine the specification of bad behaviors, a liveness specification allows
certain states to be marked as `hot` states; if a specification machine is in a `hot` state
at the end of a deadlock execution, this execution is reported as an error.

As an example, consider the `Liveness` spec in the figure above.
This spec machine starts in the state `Init` and transitions to the state `Wait` upon receiving
the event `M_START`.
In the entry function of `Wait`, the `nodes` variable is
initialized to the set of addresses of all `Node` machines in the program.
Whenever a `NODE_DOWN` event from the `FailureDetector`
machine is observed by the spec machine, it removes
the machine whose failure was detected from the `nodes` map.
The spec machine exits the hot `Init` state only when the `nodes` map becomes empty, i.e.,
when the failures have been detected for all `Node` machines.
Thus, this spec expresses the property that failure of every `Node` machine
must be eventually detected.

Occasionally, a violation of liveness results not from a deadlock in a finite execution
but from an infinite execution in which one or more machines may be taking steps
but program is not making progress as a whole.
A liveness spec with hot states handles such infinite erroneous behaviors as well;
an infinite execution is erroneous if
the spec machine is in a hot state infinitely often in the execution.
With only hot states, a spec machine can define "eventually" properties,
i.e., something good happens eventually.
To generalize liveness specs to "infinitely-often" properties,
i.e., something good happens repeatedly,
P supports the notion of `cold` states as well.
A spec machine with both hot and cold states specifies an erroneous infinite execution as the one
which visits some hot state infinitely often
and visits cold states only finitely often.

To understand the meaning of hot and cold states, it is helpful to think
in terms of the temperature of an execution.
Every step in the execution at the end of which the liveness spec machine is in a hot state
increases the temperature of the execution by a small amount.
Entering a cold state resets the temperature to zero.
An (infinite) execution is erroneous if its temperature goes to infinity.
