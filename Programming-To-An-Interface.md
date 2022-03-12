# Programming to an interface (Protocol Conformance)

One of the most fundamental techniques that can be leveraged for a successful Unit Testing project is favoring composition over inheritance.

```
[Composition over inheritance (or composite reuse principle)](https://en.wikipedia.org/wiki/Composition_over_inheritance) in object-oriented programming (OOP) is the principle that classes should achieve polymorphic behavior and code reuse by their composition (by containing instances of other classes that implement the desired functionality) rather than inheritance from a base or parent class.[2] This is an often-stated principle of OOP, such as in the influential book Design Patterns (1994).[3]
```

In a nutshell, this means that rather than creating complex inheritance hierarchies using various base classes etc., instead create interfaces that concrete types can be programmed to.

Since Swift (and Objective-C) use what are called `Protocols`, for the rest of these documents I will refer to it as "protocol conformance" instead of "interface programming."

I also want to note that [Apple prefers composition](https://developer.apple.com/videos/play/wwdc2015/408/) as well with a push toward value types such as `Struct`s and `enum`s that cannot inherent from a type, but can only conform to protocols.

## Example

While this is a trivial example, it highlights how much easier it is to fathom and unit test compositional protocol functionality.

### Inheritance

```
class Animal {
    func speak() { }
    
    func eat() { }
}

class Wolf: Animal {
    func bark() { 
        super.speak() 
    }
    
    override eat() { 
        attack()
    }
    
    private func attack() { }
}

class Cat: Animal {
    func meow() {
        super.speak()
    }
    
    func useLitterBox() { }
}
```

### Protocol Conformance

```
protocol Wolf {
    func eat()
}

protocol Dog: Wolf {
    func sleep()
}

protocol Cat {
    func meow()
}

struct LoneWolf: Wolf {
    func eat() { }
}

struct Chihuahua: Dog {
    func eat() { }
    
    func sleep() { }
}

struct OrangeTabby: Cat {
    func meow() { }
}
```