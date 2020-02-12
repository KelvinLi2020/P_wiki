We provide an introduction to the P syntax and semantics using the two phase commit protocol.

The above detailed description is coming soon .. but for now we provide a quick overview below.

### Quick overview:

Two-phase commit protocol (2PC) is a distributed algorithm that coordinates all the processes that participate in a distributed atomic transaction on whether to commit or abort (roll back) the transaction (it is a specialized type of consensus protocol).
The protocol works in the following manner: one node is a designated coordinator, which is the master, and the rest of the nodes in the network are designated the participants.

The implementation of the 2PC protocol in P is available in the Tutorial folder: [2PC Implementation in P.](https://github.com/p-org/P/tree/master/Tutorial/TwoPhaseCommit)

The `PSrc` folder contains the implementation of the protocol, `PSpec` folder contains the safety and liveness specification for the 2PC protocol, and the `PTst` folder contains the test drivers (environment machines) and the test scenarios that are systematically tested using P model checker.

