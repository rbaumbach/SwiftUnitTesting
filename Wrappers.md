# Wrappers

While it's easy with our own code to adhere to technical choices that make testing easier, it becomes a lot more challenging when consuming an API that doesn't.  This is where wrappers come to our rescue.

## Static methods

One common problem that I've run into is dealing with APIs that have lots of static methods that are used. Since they are static, it's difficult to mock them in our testing environment. What we can do in this scenario is wrap them up.

```
class Chef {
    static cook(item: String, completionHandler: () -> Void) {
        // very long task
    }
    
    static cleanUp(completionHandler: () -> Void) {
        // an even longer task
    }
}
```

In this example, we don't have access to change the source of Chef, but what we can do instead is manufacture a wrapper class that we can use:

```
protocol ChefWrapperProtocol {
    func cook(item: String, completionHandler: () -> Void)
    func cleanUp(completionHandler: () -> Void)
}

class ChefWrapper: Chef {
    func cook(item: String, completionHandler: () -> Void) {
        Chef.cook(item: item)
    }
    
    func cleanUp(completionHandler: () -> Void) {
        Chef.cleanUp()
    }
}
```

Now rather than using the `Chef` static methods directly, we can just use standard dependency injection instead.  This allows for easy testability.

```
class Resturant {
    private let chefWrapper: ChefWrapperProtocol
    
    init(chefWrapper: ChefWrapperProtocol = ChefWrapper()) {
        self.chefWrapper = chefWrapper
    }
}
```

Note: This is an excellent technique to use for `DispatchQueues`.

## Forced protocol conformance

Another common problem is the inability to create a mock object for an API.  A simple way to deal with this is to force a protocol conformance using an extension.

```
protocol AnInternalAPIProtocol { 
    func writeToDB()
    func readFromDB()
    
    var name: String
    var hobbies: [Hobby]
}

extension AnInternalAPI: AnInternalAPIProtocol { }
```

The `AnInternalAPI` has various methods that we want to use.  We can copy these, and add these to a protocol we create that we then use for protocol conformance.  Once this is in place, it's now trivial to create a mock:

```
class MockAnInternalAPI: AnInternalAPIProtocol {
    func writeToDB() { }
    func readFromDB() { }
    
    var name: String = "Pancho"
    var hobbies: [Hobby] = [.walksOnTheBeach, .fencing, .bjj]
}
```

Note: This works great for many of Apple's native APIs.