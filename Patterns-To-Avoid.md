# Patterns to Avoid

When I was learning how to Unit Test years ago, I was told the following:

```
Writing tests is easy, writing testable code is hard.
```

If you write your code to be testable, writing tests is pretty straight forward.  Here are some patterns to avoid making to make your code more testable.

### Don't instantiate your dependencies in your code

If you have a structure that you need to test that instantiates it's dependencies internally, it's impossible to replace them with mock ones in your tests.

```
// Don't Do

func doSomething() {
    let thingy = Thingy()
    
    thingy.doSomethingExpensive()
}

// Do

func doSomething(thingy: ThingyProtocol = Thingy()) {
    thingy.doSomethingExpensive()
}
```

Note: You can [see more dependency injection techniques here.](/Dependency-Injection.md)

### Avoid static methods and properties

While using static methods and properties seems simpler to use, it causes problems when attempting to mock out this logic. Rather than falling into the trap of using static methods/properties as a shortcut for usage, keep them instance methods/properties so it's consumers can test them effectively.

```
// Don't Do

func doSomeWork() {
    ConstructionWorker.dril()
    ConstructionWorker.hammer()
    ConstructionWorker.takeBreak()
}

// Do

// Make ConstructionWorker have instance methods instead of static ones

func doSomeWork(constructionWorker: ConstructionWorkerProtocol()) {
    constructionWorker.dril()
    constructionWorker.hammer()
    constructionWorker.takeBreak()
}
```

### Avoid "Global" state

Global state is hard to test.  It is similar to static methods in that they are impossible to mock out if they are used.

```
// Don't Do

// Note this function is global:

func globalGenerateRandomNumber() -> Int { }

func printRandomNumber() {
    print(globalGenerateRandomNumber())
}

// Do

struct RandomNumber {
    func generate() -> Int { }
}

func printRandomNumber(randomNumber: RandomNumberProtocol = RandomNumber()) {
    print(randomNumber.generate())
}

```

This also goes for Singletons as well.