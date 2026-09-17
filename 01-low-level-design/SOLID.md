# SOLID Principles

SOLID is a set of five object-oriented design principles that help us write software that is easier to maintain, understand, test, and extend.

The five principles are:

* **S — Single Responsibility Principle**
* **O — Open/Closed Principle**
* **L — Liskov Substitution Principle**
* **I — Interface Segregation Principle**
* **D — Dependency Inversion Principle**

The overall goal is not to blindly apply five rules. The goal is to reduce unnecessary coupling and complexity and make the design easier to change.

---

## 1. Single Responsibility Principle — SRP

> A class should have only one reason to change.

Marker Entity:

```java
class Marker{
    private String name;
    private String color;
    private int year;
    private int price;

    public Marker(String name, String color, int year, int price){
        this.name = name;
        this.color = color;
        this.year = year;
        this.price = price;
    }
}
```

Consider an `Invoice` class that calculates an invoice, prints it, and saves it to a database.

```java
class Invoice {
    private Marker marker;
    private int quantity;

    public Invoice(Marker marker, int quantity) {
        this.marker = marker;
        this.quantity = quantity;
    }

    public int calculateTotal() {
        return marker.price * quantity;
    }

    public void printInvoice() {
        // print invoice
    }

    public void saveToDB() {
        // save invoice
    }
}
```

The problem is that the class has multiple independent responsibilities:

* invoice calculation
* invoice printing
* database persistence

Therefore, there are multiple reasons that could require the class to change.

A possible refactoring is:

```java
class Invoice {
    private Marker marker;
    private int quantity;

    public Invoice(Marker marker, int quantity) {
        this.marker = marker;
        this.quantity = quantity;
    }

    public int calculateTotal() {
        return marker.price * quantity;
    }
}
```

Persistence can be moved to another class:

```java
class InvoiceDao {
    private Invoice invoice;

    public InvoiceDao(Invoice invoice) {
        this.invoice = invoice;
    }

    public void saveToDB() {
        // save invoice
    }
}
```

Printing can also have its own responsibility:

```java
class InvoicePrinter {
    private Invoice invoice;

    public InvoicePrinter(Invoice invoice) {
        this.invoice = invoice;
    }

    public void print() {
        // print invoice
    }
}
```

The important idea I took from SRP is:

**A class should not have multiple independent reasons to change.**

---

## 2. Open/Closed Principle — OCP

> Software entities should be open for extension but closed for modification.

Suppose an existing `InvoiceDao` saves invoices to a database:

```java
class InvoiceDao {
    Invoice invoice;

    public InvoiceDao(Invoice invoice) {
        this.invoice = invoice;
    }

    public void saveToDB() {
        // save invoice to DB
    }
}
```

Now a new requirement arrives: invoices should also be saved to a file.

One approach is to modify the existing class:

```java
class InvoiceDao {
    Invoice invoice;

    public InvoiceDao(Invoice invoice) {
        this.invoice = invoice;
    }

    public void saveToDB() {
        // save to DB
    }

    public void saveToFile(String filename) {
        // save to file
    }
}
```

The existing class has now been modified to accommodate a new behavior.

A better design is to introduce an abstraction:

```java
interface InvoiceDao {
    void save(Invoice invoice);
}
```

Then different implementations can provide different behaviors:

```java
class DatabaseInvoiceDao implements InvoiceDao {

    @Override
    public void save(Invoice invoice) {
        // save to DB
    }
}
```

```java
class FileInvoiceDao implements InvoiceDao {

    @Override
    public void save(Invoice invoice) {
        // save to file
    }
}
```

Now new implementations can be added without changing the existing implementations.

The key idea:

**Prefer extending behavior through abstractions rather than repeatedly modifying stable code.**

---

## 3. Liskov Substitution Principle — LSP

> If `B` is a subtype of `A`, objects of `B` should be usable wherever objects of `A` are expected without breaking the program's expected behavior.

Consider:

```java
class Vehicle {
    public Integer getNumberOfWheels() {
        return 2;
    }

    public Boolean hasEngine() {
        return true;
    }
}
```

Then:

```java
class MotorCycle extends Vehicle {
}
```

```java
class Car extends Vehicle {
    @Override
    public Integer getNumberOfWheels() {
        return 4;
    }
}
```

Now consider:

```java
class Bicycle extends Vehicle {

    @Override
    public Boolean hasEngine() {
        return null;
    }
}
```

If client code expects every `Vehicle` to have an engine:

```java
for (Vehicle vehicle : vehicleList) {
    System.out.println(vehicle.hasEngine().toString());
}
```

passing a `Bicycle` can break the expected behavior.

The deeper problem is the abstraction itself.

A better model is to avoid claiming that every `Vehicle` has an engine.

For example:

```java
class Vehicle {
    public Integer getNumberOfWheels() {
        return 2;
    }
}
```

Then create a more specific abstraction for engine-powered vehicles:

```java
class EngineVehicle extends Vehicle {
    public boolean hasEngine() {
        return true;
    }
}
```

Now:

```java
class Car extends EngineVehicle {
}
```

```java
class MotorCycle extends EngineVehicle {
}
```

while:

```java
class Bicycle extends Vehicle {
}
```

The important lesson:

**Inheritance should represent a valid behavioral substitution, not merely a convenient code-reuse mechanism.**

---

## 4. Interface Segregation Principle — ISP

> Clients should not be forced to depend on methods they do not need.

Consider:

```java
interface RestaurantEmployee {
    void washDishes();
    void serveCustomers();
    void cookFood();
}
```

A waiter may only need to serve customers and take orders:

```java
class Waiter implements RestaurantEmployee {

    public void washDishes() {
        // not my responsibility
    }

    public void serveCustomers() {
        // serve customer
    }

    public void cookFood() {
        // not my responsibility
    }
}
```

The waiter is forced to implement methods that are unrelated to its responsibilities.

A better approach is to split the interface:

```java
interface WaiterInterface {
    void serveCustomers();
    void takeOrder();
}
```

```java
interface ChefInterface {
    void cookFood();
    void decideMenu();
}
```

Now clients depend only on the behavior they actually need.

The key idea:

**Prefer smaller, focused interfaces over large interfaces that force unrelated implementations.**

---

## 5. Dependency Inversion Principle — DIP

> High-level code should depend on abstractions rather than concrete implementations.

Consider:

```java
class MacBook {

    private final WiredKeyboard keyboard;
    private final WiredMouse mouse;

    public MacBook() {
        keyboard = new WiredKeyboard();
        mouse = new WiredMouse();
    }
}
```

The `MacBook` is tightly coupled to concrete implementations.

If we want to support Bluetooth devices, the `MacBook` class itself has to change.

Instead, depend on abstractions:

```java
class MacBook {

    private final Keyboard keyboard;
    private final Mouse mouse;

    public MacBook(Keyboard keyboard, Mouse mouse) {
        this.keyboard = keyboard;
        this.mouse = mouse;
    }
}
```

Now different implementations can be supplied:

```text
Keyboard
├── WiredKeyboard
└── BluetoothKeyboard

Mouse
├── WiredMouse
└── BluetoothMouse
```

The key idea:

**The class should depend on what the object can do, rather than on a specific implementation of how it does it.**

---

## What I'm taking away from SOLID

The most useful way I'm currently thinking about SOLID is not as five rules to memorize.

Instead, I'm trying to use them as questions:

* **SRP:** What are the independent reasons this class can change?
* **OCP:** Can I add this behavior without unnecessarily modifying stable code?
* **LSP:** Can this subtype genuinely replace its parent without breaking expectations?
* **ISP:** Am I forcing a client to depend on behavior it doesn't need?
* **DIP:** Am I coupling high-level logic to a concrete implementation?

These questions make SOLID much more practical than memorizing their definitions.

