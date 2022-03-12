# Mocks

When using protocols in your code instead of concrete types, you can easily leverage mocks in your test bundle.  Since you're conforming to a protocol, you can easily just create a new type that conforms to the same protocol but allows you to bypass long-running or expensive tasks.  This is what keeps tests fast, predictable and isolated.

## Example

Let's say that you have a structure that has the following API:

```
protocol TacoTruckProtocol {
    func cook(item: String)
    func serve(item: String)
    
    func drive()
    func honk() -> String
}

struct TacoTruck: TacoTruckProtocol {
    func cook(item: String) -> String {
        longRunningCookingTask(item: item)
    }
    
    func serve(item: String, payment: Double) {
        handlePayment(payment: payment)
        prepare(item: item)
    }
    
    func drive() {
        // Demonstrating long running blocking task
        
        Sleep(20)
    }
    
    func honk() -> String {
        return "La Cucaracha"
    }
    
    private longRunningCookingTask(item: String) { }
    
    private handlePayment(payment: Double) { }
    
    private prepare(item: String) { }
}
```

Next, let's imagine that we are using this API somewhere in your application:

```
struct Downtown {
    private let tacoTruck: TacoTruckProtocol
    
    init(tacoTruck: TacoTruckProtocol = TacoTruck()) {
        self.tacoTruck: tacoTruck
    }
    
    func serveBurrito() {
        tacoTruck.drive()
        tacoTruck.cook(item: "Burrito")
        tacoTruck.serve(item: "Burrito", payment: "3.99")
    }
    
    func makeNoise() -> String {
        tacoTruck.honk()
    }
}
```

Since we are using the `TacoTruckProtocol` injected inside the `DownTown` structure instead of the concrete type, we can easily test this using a mock that we create.  This will prevent our tests from executing the long-running tasks that happen inside the `TacoTruck` methods.

```
class MockTacoTruck: TacoTruckProtocol {
    var capturedCookItem: String?
    var capturedServeItem: String?
    var capturedServePayment: Decimal?
    var didCallDrive = false
    var didCallHonk = false
    
    var mockHonk = "La Bamba"
    
    func cook(item: String) {
        capturedCookItem = item
    }
    
    func serve(item: String, payment: Decimal) {
        capturedServeItem = item
        capturedServePayment = payment
    }
    
    func drive() {
        didCallDrive = true
    }
    
    func honk() -> String {
        didCallHonk = true
        
        return mockHonk
    }
}
```

As you can see above, while the interface of the `MockTacoTruck` is the same as `TacoTruck`, it replaces the long running expensive tasks with trivial implementations that can be used to assert test conditions.

Now that we have this mock at our disposal, we now can test the `DownTown` structure with ease:

```
import Quick
import Nimble

class DownTownSpec: QuickSpec {
    override func spec() {
        describe("DownTownSpec") {
            var subject: DownTown!
            var mockTacoTruck: MockTacoTruck!
            
            beforeEach() {
                mockTacoTruck: MockTacoTruck()
                
                subject = Downtown(tacoTruck: mockTacoTruck()
            }
            
            describe("when serving a burrito") {
                beforeEach() {
                    subject.serveBurrito()
                }
            
                it("has a taco truck serve a burrito") {
                    expect(mockTacoTruck.didCallDrive).to(beTruthy())
                    expect(mockTacoTruck.capturedCookItem).to(equal("Burrito"))
                    expect(mockTacoTruck.capturedServeItem).to(equal("Burrito"))
                    expect(mockTacoTruck.capturedServePayment).to(equal("3.99"))
                }        
            }
            
            describe("when making noise") {
                beforeEach() {
                    subject.makeNoise()
                }
            
                it("has a taco truck honk loudly!") {
                    expect(mockTacoTruck.didCallHonk).to(beTruthy())
                }  
            }
        }
    }
} 
```

As you can see, since we are using this mock, we have fast tests that no longer perform time-consuming logic. We control our testing environment completely now, and have the flexibility to extend or extinguish future usage of the TacoTruck instance in future iterations.