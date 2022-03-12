# What is Unit Testing

A unit test is an automated test that tests an individual unit of source code.

In regard to Swift, an individual unit can be:

* A `Struct`
* A `Class`
* A function/method
* Property

[Kent Beck](https://tidyfirst.substack.com/p/desirable-unit-tests?s=r) states the following attributes of desirable Unit Tests (Note: The descriptions listed are my summary of each bullet point):

### Isolated

Unit tests are completely isolated from each other and effectively must be run in their own "sandbox."

### Composable

Pieces must be able to be injected and isolated from the various pieces each test depends on.

### Deterministic

Items that have indeterministic functionality (such as dates, time, etc.), must be injected into the unit in a predicable fashion.

### Specific

This makes the test:

* Readable and easy to consume by teammates.
* Gives ample documentation as to the behavior that is being tested.
* Allows for quick discovery of source code when tests fail without extensive digging.

### Behavioral

Behavior of the system is tested, rather than functionality. Think user/dev functionality rather than architecture.

### Structure-insensitive

As mentioned above, the behavior of the unit is described and tested instead of testing specific coding.

### Fast

Thousands of tests should be able to be run in seconds.

### Writable

If composition is used over inheritance is used with excellent interface design, writing tests is easy.

### Readable

Focusing on a specific unit of code rather than a series of interconnected pieces.  Think "micro" NOT "macro."

### Automated

Must not be run manually

### Predictive

Unit tests don't verify that entire systems are working.  However, failing unit tests are a great indication that the entire system is faulty.

### Inspiring

A frequently-run unit test suite gives great confidence the programming is progressing.  The run fast and help lower developer anxiety that changes to the system will not introduce regressions.

## What Unit Testing is NOT

* A test that has to be run manually
* A test that verifies the composition of various pieces of a functional system
* Test cases
* System performance testing
* Integration testing

## What Unit Testing does for you

### Code Coverage

Since Unit Tests are run on individual units, it's easy to get fast code coverage metrics to very what parts of the system have tests. This is incredibly helpful when determining which pieces are more fragile to change than others.

### Specifications

If using a modern Behavioral Driven Development (BDD) testing framework, your tests will look more like specifications of how your units work rather than test functions. Some platforms actually call their tests, specs instead to highlight this. This has the amazing side effect of documenting your code.

### Better Architecture

Writing Unit Tests forces coding best practices.  For example:

1. Favors composition over inheritance.
2. Requires protocol oriented programming (programming to an interface).
3. Dependency injection when composing together other pieces of functionality.
4. Building consumer friendly interfaces for consumption.
5. Keeps development focus on simple implementations with an iteration mindset.
6. Minimizes unpredictable global state (static methods/properties, Singletons).