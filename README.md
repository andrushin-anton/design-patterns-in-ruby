# Design Patterns in Ruby

* [Template Method](#template-method)
* [Strategy](#strategy)


## Five Main Points

1. Separate the things that change from the things that stay the same.
    * promotes DRY code
    * improves maintainability

2.  Program to an interface, not an implementation.

    Code should belong to the most general object possible, and specific classes should inherit from the general class.
    * increases modularity

3. Prefer composition over inheritance

   Avoid saying that an object is a kind of something and instead say that it has something.  In other words, it's often better to reference other classes or modules than to put functionality in superclasses.
    * increases encapsulation
    * increases modularity, implementations can be easily swapped

4. delegate, delegate, delegate
  Let other classes handle functionality within their domain

5. You ain't gonna need it

   Don't implement features or design in flexibility that you don't immediately need because you will probably never need it.


----------------------------------------------------------------

## Design Patterns

### TEMPLATE METHOD
1. Create a **skeletal class** with methods that are common between algorithms.
2. Create a **subclass** for each algorithm and override the common methods from the skeletal class.

Disadvantages:

  * no runtime flexibility
---
### STRATEGY

The basic idea is to **delegate tasks to encapsulated algorithms which are interchangable at runtime**.

In the Strategy pattern we have an object (the *context*) that is trying to get something done. But to get that thing done, we need to supply the context with a second object, called the *strategy*, that helps get the thing done.

1. Define a family of objects which all do the same thing (ex: format output, generate graphics etc.).
2. Ensure the family of objects share the same interface so that they are interchangable.

There are two strategies to passing data from the *context* object to the *strategy* object.  We can pass the data as parameters when the strategy is called, or we can pass the context object as the single parameter.

If the strategies are very simple and have only one method, we can even use code blocks for our algorithms and simply use `block.call`.  However, if multiple methods are needed, the strategies must be structured as separate classes.

Advantages:

* algorithms are interchangable at runtime
* promotes modularity

---
### OBSERVER

The observer pattern is for **event driven programming**.

An object, called the **subject**, maintains a list of its dependents, called **observers**, and notifies them automatically of any state changes, usually by calling one of their methods.

The Ruby Standard Library includes an [Observable module](http://ruby-doc.org/stdlib/libdoc/observer/rdoc/Observable.html) that implements this pattern.

It's also possible to use code blocks as observers.  This isn't supported by the Observable module in the Ruby Standard Library, but it's easy to implement.

#### Additional Concerns
There are a few additional concerns that should be addressed when working with the Observable pattern.

##### Push vs Pull
In the default implementation, the notification sent to the observer doesn't specify which of the Subjects attributes has changed.  To find out which attribute has changed, the Observer has to check the Subjects attributes, this is the *pull* method.

Another approach would be the *push* method where the notification includes other attributes which provide the Observer with additional information like the examples below.

````ruby
observer.update(self, :salary_changed)
observer.update(self, :salary_changed, old_salary, new_salary)
````

An observer may only need to know when a specific attribute of the subject changes.  The simple implementation would notify the observer when any attribute changes.

##### Atomic Event Notifications
If multiple attributes of a Subject are being updated and the updates are not independent, notifying Observers before all the updates are executed could cause an inconsistent state.

````ruby
fred = Employee.new('Fred', 'Crane Operator', 30000)
fred.salary = 1000000
# Warning! Inconsistent state here!
fred.title = 'Vice President of Sales'
````

##### What to do When an Observer Raises an Exception
It's also important to address what should happen when a notification causes an Observer to raise an exception. The correct way to handle exceptions will vary from case to case.

---
### PROXY

#### Types of Proxies

##### Protection Proxy
A protection proxy protects an object from unauthorized access.  To ensure methods can only be run by authorized users, we can run an authorization check before messages are passed to the underlying object.

##### Remote Proxy
Remote proxies provides access to objects which are running on remote machines.

A great example of a remote proxy is [Distributed Ruby (DRb)](http://ruby-doc.org/stdlib/libdoc/drb/rdoc/DRb.html), which allows Ruby programs to communicate over a network. With DRb, the client machines runs a proxy which handles all of the network communications behind the scenes.

##### Virtual Proxy
Virtual proxies allow us to delay the creation of an object until it is absolutely necessary.  This is useful when creating the object is computationaly expensive.

#### Using Message Passing to Simplify Proxies
When building a proxy, we could implement a method for each method in the underlying object.  However, this leads to a lot of repeated code and tightly couples the proxy with the underlying object.  A better alternative is to pass method calls direcly to the underlying object.  Ruby includes a method that is perfect for this situation called *method_missing*.

In Ruby, when you call a method on an object, Ruby looks for the method in the initial object and it's modules and then works it's way up the stack to that objects superclass and then it's superclass and so on.  If the method is not found, Ruby then looks for the method *method_missing* in the initial object, then it's parent, and it's parent… etc.

Rather than implement each of the underlying objects methods in the proxy, we can use *method_missing* to simply pass method calls to the underlying object.

Again, this has to big advantages:

1. The proxy is greatly simplified by having far fewer methods
2. The proxy is not coupled to the underlying object and can therefore be reused for multiple object classes.

---
### Builder

The very simple idea behind the Builder pattern is that you encapsulate object construction logic behind a class of its own. The builder class takes charge of assembling all the components of a complex object. Each builder has an interface that lets you specify the configuration of your new object step by step.

In a sense, a builder is a sort of like a multipart new method, where objects are created in an extended process instead of all in one shot.
