# Command

## Introduction: {#introduction}

​Encapsulate a request as an object, thereby letting you parameterize clients with different requests, queue or log requests, and support undoable operations

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* ​Parameterize objects by an action to perform. \(callback\)
* Specify, queue, and execute requests at different times.
* Support undo. \(Command history: Commands + Composite\)
* Support logging changes so that they can be reapplied in case of a system crash.
* Structure a system around high-level operations built on primitives operations.

## Component: {#component}

​

![](../.gitbook/assets/image%20%285%29.png)

#### Command:

* Declares an interface for executing operation.

#### Concrete Command:

* Defines a binding between a Receiver Object and an action.
* Implements Execute by invoking the corresponding operations on Receiver.

#### Client:

* Creates a Concrete Command object and sets its receiver.

#### Invoker:

* Asks the command to carry out the requests.

#### Receiver:

* Knows how to perform the operations associated with carrying out a request. \(Any class may serve as a Receiver\)

## Code Example - Golang {#code-example-golang}

```text
​​
```

**NOTE**:



## Related Patterns: {#related-patterns}



​

​

​

