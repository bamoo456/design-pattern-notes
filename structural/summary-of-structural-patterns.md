# Summary of Structural Patterns

## Summary:

#### Adapter vs Bridge

* Adapter and Bridge patterns have some common attributes, but they have different intents.
* Adapter focuses on resolving incompatibilities between two existing interfaces.  \(The Adapter and Adaptee interface usually are different\)
* Bridge, on the other hand, bridges an abstraction and its implementations. \(The Bridge provides a stable interface to clients even as it lets you vary the classes that implement it\)
* The Adapter pattern makes things work after they are designed; Bridge makes them work before they are.

#### Composite vs Decorator

* Composite and Decorator has similar structure diagrams which rely on recursive composition.
* Decorator is designed to let you add responsibilities to objects without subclassing.
* Composite focuses on structuring classes so that many related objects can be treated uniformly. and multiple objects can be treated as one.
* Composite and Decorator patterns are often used in concert. In this case, they both will have common interface.  **From the Decorator's view, a composite is a Concrete Component.** **From the Composite's view, a decorator is a Leaf.**

#### Decorator vs Proxy

* Proxy and Decorator also have similar structure and behavior.
* In the Proxy Pattern, the key functionalities are provided by the child component. In the Decorator Pattern,  the child component only provide part of the functionality, the Decorator Object will provide the rest part of functionality.
* Most of time, the Decorator pattern is used dynamically. \(not in compiled time\)
* Most of time, the Proxy pattern is built in compiled time.
* Decorator and Proxy can work together as `Proxy-Decorator` or `Decorator-Proxy`



