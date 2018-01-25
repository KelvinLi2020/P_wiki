P is a domain-specific language for implementing asynchronous 
event-driven systems.
The goal of P is to provide language primitives to succinctly and 
precisely capture protocols that are inherent to communication among
components in asynchronous systems.
The computational model underlying a P program is state machines 
communicating via messages,
an approach commonly used in building embedded, networked, and 
distributed systems.
Each state machine has an input queue, states, transitions, event handlers, 
and machine-local store for a collection of variables.
The state machines run concurrently with each other, 
each executing an event handling loop that dequeues a
message from the input queue, examines the local store, 
and can execute a sequence of operations. 
Each operation either updates the local store, 
sends messages to other machines, or creates new machines. 
In P, a send operation is non-blocking; 
the message is simply enqueued into the input queue of the target machine. 

In the rest of this tutorial, 
we will introduce the features of P through a series of examples.
We will first present [a simple program](https://github.com/p-org/P/wiki/The-PingPong-program) 
that illustrates the basics of the state machine programming model.
Next, we present a more sophisticated example of a failure detection protocol 
(Section [#sec-failure-detection-program]) that illustrates advanced features of 
the P language.
P is a domain-specific language for implementing protocols in an asynchronous application;
it is expected that the parts of the application other than the protocols would be written in a 
host language such as C.
We will describe the foreign code interface important for interoperability between P code and foreign code written
in C (Section [#sec-foreign-code]).
Finally, we will provide an overview of the tools for processing a P program (Section [#sec-tools])
and conclude with a glossary of all features in the P language (Section [#sec-glossary]).
