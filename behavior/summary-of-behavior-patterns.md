# Summary of Behavior Patterns

## Summary: {#summary}

### Encapsulating Variation

The patterns usually define an abstract class that describes the encapsulating object, and the pattern derives its name from that object.

* A _**Strategy**_ object encapsulates an algorithm
* A _**State**_ object encapsulates a state-dependent behavior
* A _**Mediator**_ object encapsulates the protocol between objects
* An _**Iterator**_ object encapsulates the way you access and traverse the components   of an aggregate object.

But not all object behavioral patterns partition functionality like this. _**Chain of Responsibility**_ illustrates another difference in behavioral patterns. it prescribes communication between an open-ended number of objects.



### Objects as Arguments

Several patterns introduce an object that's always used as an argument.

* A _**Visitor**_ object is the argument to a polymorphic Accept operation on  the objects it visits.
* In   _**Command**_ , the token represents a request
* in _**Memento**_, it represents the internal state  of an object at a particular time.
* In both _**Command**_ and _**Memento**_, the token can have a complex internal   representation, but the client is never aware of it.



### Should Communication be Encapsulated orDistributed?

* _**Mediator**_ and _**Observer**_ are competing patterns.
* _**Observer**_ distributes communication by introducing _**Observer**_ and _**Subject**_   objects
* A _**Mediator**_ object encapsulates the communication between other   objects.
* In the _**Observer**_ _**pattern**_, there is no single object that encapsulates a constraint. Communication   patterns are determined by the way _**observers**_ and _**subjects**_ are interconnected
* The _**Mediator pattern**_ centralizes rather than distributes, it places the responsibility for maintaining a constraint squarely in the mediator. \(Only the _**Mediator objects**_ know how to work with other objects\)
* _**Observers and subjects**_ are usually connected shortly after they're  created, and it's hard to see how they are connected later in the program



## Decoupling Senders and Receivers

_**Command**_, _**Observer**_, _**Mediator**_, and _**Chain of Responsibility**_ address how you can decouple senders and receivers, but with different trade-offs.

### Command Pattern

* The _**Command Pattern**_ defining the sender-receiver connection in a separate object lets  the sender work with different receivers.  It keeps the sender decoupled from the receivers,  making senders easy to reuse.

![](../.gitbook/assets/image%20%2822%29.png)

### Observer Pattern

* The _**Observer pattern**_ decouples senders \(_**subjects**_\) from receivers \(_**observers**_\) by defining  an interface for signaling changes in subjects.  _**Observer**_ defines a looser sender receiver   binding than _**Command**_. The _**Observer pattern**_ is best for decoupling objects when   there are data dependencies between them

![](../.gitbook/assets/image%20%289%29.png)



### Mediator Pattern

* The _**Mediator pattern**_ decouples objects by having them refer to each other indirectly   through a _**Mediator object**_. A _**Mediator object**_ routes requests between Colleague objects and centralizes their communication. _**The Mediator pattern**_ can reduce subclassing in a system, because it centralizes communication   behavior in one class instead of distributing it among subclasses

![Colleagues can only talk to each other through the Mediator interface.](../.gitbook/assets/image%20%2817%29.png)



### Chain of Responsibility Pattern

* The _**Chain of Responsibility pattern**_ decouples the sender from the receiver by   passing the request along a chain of potential receivers. _**Chain of Responsibility**_ is a good way to decouple the sender   and the receiver if the chain is already part of the system's structure, and one of several   objects may be in a position to handle the request. It offers added   flexibility in that the chain can be changed or extended easily.

![](../.gitbook/assets/image%20%2819%29.png)



## Work with other patterns for building the System:

* A class in a _**Chain of Responsibility**_, for example, will probably include at least one application   of _**Template Method**_. The _**Template Method**_ can use primitive operations  to determine whether the object should handle the request and to choose the object to   forward to;  The chain can use the _**Command pattern**_ to represent requests as objects
* _**Interpreter**_ can use the _**State pattern**_ to define parsing contexts
* An _**iterator**_ can   traverse an aggregate, and a _**visitor**_ can apply an operation to each element in the   aggregate.
* A system that uses   the _**Composite pattern**_ might use a _**visitor**_ to perform operations on components of   the composition. It could use _**Chain of Responsibility**_ to let components access global   properties through their parent. It could also use _**Decorator**_ to override these   properties on parts of the composition. It could use the _**Observer**_ pattern to tie one   object structure to another and the _**State pattern**_ to let a component change its behavior  as its state changes. The composition itself might be created using the approach in   _**Builder**_, and it might be treated as a _**Prototype**_  by some other part of the   system.







