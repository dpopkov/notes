Design Patterns
===============

* [The Observer Pattern](#observer")
* [The Factory Method Pattern](#factory-method)
* [The Abstract Factory Pattern](#abstract-factory)


The Observer Pattern<a name="observer"></a>
--------------------

The Observer Pattern defines a one-to-meny dependency between objects so that when one object changes state, all of its dependents are notified and updated automatically.

![Observer](images/design-patterns/Observer.png)


The Factory Method Pattern<a name="factory-method"></a>
--------------------------

The Factory Method Pattern defines an interface for creating an object, but lets subclasses decide which class to instantiate. Factory Method lets a class defer instantiation to subclass.

![Factory Method](images/design-patterns/Factory_Method.png)


The Abstract Factory Pattern<a name="abstract-factory"></a>
----------------------------

The Abstract Factory Pattern provides an interface for creating families of related or dependent objects without specifying their concrete classes.

![Abstract Factory](images/design-patterns/Abstract_Factory.png)


The Command Pattern<a name="command"></a>
-------------------

The Command Pattern encapsulates a request as an object, thereby letting you parameterize other objects with different requests, queue or log requests, and support undoable operations.

The Command Pattern us used when you need to decouple an object making request from the objects that know how to perform the requests.

![Command](images/design-patterns/Command.png)

### Command Pattern (Remote Control API Design Example)

The Command Pattern is employed to logically decouple the RemoteControl class from the Vendor Classes, so that the RemoteControl doesn't require changes as new vendor classes are produced.

![Command example](images/design-patterns/Command-example.png)

