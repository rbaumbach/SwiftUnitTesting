# Swift Specific Testing Patterns - DispatchQueue

While most of the information given can generically be used for other strongly typed languages, this section will give some examples on how to test the usage of Grand Central Dispatch's, `DispatchQueue`.

## Challenges

GCD is a powerful suite of tools that can manage the work on queues in your application.  However, GCD out of the box doesn't have a very testable API. For this example, we will focus on `DispatchQueue`.

Some of the challenges are:

* `DispatchQueue` uses static methods
* Protocols don't exist
* Uses closures for handling asynchronous "work"

These challenges can be resolved by using the various techniques that were previously described:

1. Protocol Oriented Programming
2. Wrappers
3. Dependency Injection
4. Mocks

Note: For this exercise, we will focus on the following `DispatchQueue` method for dispatching on the main/global queues:

```
class DispatchQueue {
    func async(group: DispatchGroup? = nil, qos: DispatchQoS = .unspecified, flags: DispatchWorkItemFlags = [], execute work: @escaping @convention(block) () -> Void)
}
```

## Wrapper + Protocol Oriented Programming

Before we can think about testing the usage of `DispatchQueue`, the first thing we need to think about is how to "program to an interface" instead of using the concrete `DispatchQueue` type.  We also need to deal with the fact that we want to be able to dispatch on the main queue for UI updates, or a background queue for time intensive work.  A wrapper is a great pattern to use for this, with an associated protocol to conform to:

```
protocol DispatchQueueWrapperProtocol {
    func mainAsync(execute: @escaping () -> Void)
    func globalAsync(qos: DispatchQoS, execute: @escaping () -> Void)
}

final class DispatchQueueWrapper {
    // MARK: - Private properties
    
    private let mainDispatchQueue = DispatchQueue.main
    private let customQueue: DispatchQueue
    
    // MARK: - Init methods
    
    public convenience init() {
        self.init(label: "Default", attributes: [])
    }
    
    public init(label: String, attributes: DispatchQueue.Attributes) {
        customQueue = DispatchQueue(label: label, attributes: attributes)
    }
    
    // MARK: - Main Queue
    
    public func mainAsync(execute: @escaping () -> Void) {
        mainDispatchQueue.async(execute: execute)
    }
    
    // MARK: - Global Queue
    
    public func globalAsync(qos: DispatchQoS, execute: @escaping () -> Void) {
        DispatchQueue.global(qos: qos.qosClass).async(execute: execute)
    }
}
```

As shown above, a wrapper class called `DispatchQueueWrapper` was created to encapsulate the "static" state of `DispatchQueue.main` and `DispatchQueue.global` into two instance methods.  This wrapper then conforms to the protocol `DispatchQueueWrapperProtocol` that is an interface that can be used throughout your code.

## Dependency Injection

Now that we have a nice shiny interface to program to, we can use this when dispatching work:

```
class 
```