# Dependency Injection

One of the most valuable tools at a developer's disposal is Dependency Injection. Dependency injection at it's core is basically just having the ability to inject a concrete type into a consumer of said type.  This is different than instantiating the type itself in the code directly.

Using dependency injection instead of creating types in code allows for the passing in of mock data types in tests that allow us to bypass expensive or long-running tasks.  You can see an example of this in the [Mocks Section.](/Mocks.md)

## Basic example

In most "untesable" code, we will see instances being created inside a code that is consuming it:

```
struct Kitchen {
   private let oven: OvenProtocol = Oven()
   var stove: StoveProtocol = Stove()
   
   init() { }
   
   func cook(food: String) {
      oven.turnOn()
      oven.cook(food: String)
      stove.cook(food: String)
   }
   
   func serve(food: String) {
     cook(food: String)
   }
   
   func fry(item: String) {
     stove.turnOn()
     stove.fry(item: item)
   }
   
   static func turnOnLight(light: LightProtocol = Light()) {     
     randomLight.turnOnRandomLight()
   }
}
```

If we attempted to test the `Kitchen` structure as is, it would be impossible to use mock objects for the `Oven` and `Stove`.  This means that if we ever called `cook()` or `serve()` it would run any potentially expensive or long running tasks.

There are 3 techniques you can use for dependency injection to solve this problem:
1. Constructor (Init) Injection
2. Property Injection
3. Method Injection 

You will see the techniques below.

## Constructor (Init) Injection

The easiest way to inject an object into a structure is to use init method injection.  This works if we don't need to worry about the dependency changing based on the internal state of the structure at any point in time, as this dependency can only be created/stored once when the structure is initialized:

```
struct Kitchen {
   private let oven: OvenProtocol
   var stove: StoveProtocol = Stove()
   
   init(oven: OvenProtocol = Oven()) {
     self.oven = oven
   }
   
   // ... 
```

Since we are now injecting the oven in the `init()` method with a default instance being created we have the option of providing a mock in the tests, but keeping a real instance in the actual code:

```
// In code a real instance is used:

let kitchen = Kitchen()

// In the test a mock instance can be used:

class MockOven: OvenProtocol { }

let testSubject = Kitchen(oven: MockOven())
```

This allows us to mock out any potential operations that a real oven would require in the code while keeping our unit tests predictable and fast.

## Property Injection

The next method is property injection.  This is a more flexible approach if we decide we want to swap out our dependencies in our structures as we use them.

Using our kitchen example, let's say we want to cook something that uses the default stove, and then we decide that we want to air fry an item instead: 

```
// Again, in code a real instance is used:

let kitchen = Kitchen()

// In the test a mock instance can be used:

class MockOven: OvenProtocol { }

class MockAirFryer: OvenProtocol { }

let testSubject = Kitchen(oven: MockOven())

// More complex test scenarios later, we want to swap out the mock
// with another:

testSubject.stove = MockAirFryer()
```

As you can see, with init injection, we aren't able to change the dependency after the structure is created, but with property injection we are able to as we need to.

## Method Injection

The last technique is to use method injection.  It is similar to property injection.  It allows us to be more flexible to changes, but it is specific to the scope of the method only.  This is useful for harder to test `static` methods where you're unable to control the state of your structure's dependencies.

```
struct Kitchen {
   // ... 
   
   static func turnOnLight(light: LightProtocol = Light()) {     
     randomLight.turnOnRandomLight()
   }
}
```

As you can see, we can also leverage "defaults" that keeps the interface simple for the user to use, but allows us in the tests to mock out.

```
let kitchen = Kitchen()

// In the test a mock instance can be used:

class MockLight: LightProtocol { }

Kitchen.turnOnLight(light: MockLight())
```