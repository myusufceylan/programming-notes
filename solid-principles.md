# SOLID Principles

SOLID is a couple of principles to write a good quality code defined by Uncle Bob in early 2000s. Before SOLID we should know two concepts:  

**Coupling:** is the degree of dependence between classes or methods that are linked to each other.  
**Cohesion:** is the measure of two or more software parts working together provides better results than working individually.  

In order to obtain a good software we should get **low coupling** and **high cohesion**.

**S**ingle Responsibility Principle
**O**pen Closed Principle
**L**iskov Substitution Principle
**I**nterface Segregation Principle
**D**ependency Inversion Principle

## Single Responsibility Principle (SRP)
It means every class should have only one task to do. Otherwise it would cause high coupling and our code would be difficult to maintain.

**Violation of SRP:**

```java
public class Customer {

    private String name;

    // getter and setter methods..

    //This is a Responsibility
    public void storeCustomer(String customerName) {
       // store customer into a database...
    }

    //This is another Responsibility
    public void generateCustomerReport(String customerName) {
       // generate a report...
    }
}
```

**Better Solution:**

- Customer class:
```java
public class Customer {

    private String name;

    // getter and setter methods
}
```

- CustomerDB class for persistence responsibility:
```java
public class CustomerDB {

    public void storeCustomer(String customerName) {
       // store customer into a database...
    }
}
```

- CustomerReportGenerator for the report generation responsibility:
```java
public class CustomerReportGenerator {

    public void generateReport(String customerName) {
       // generate a report...
    }
}
```

## Open Closed Principle (OCP)
According to this rule; classes, methods etc. should be **open** to extensions but **closed** to modifications. So we should be able to add new features to our code without needing to modify the code.

**Violation of OCP:**
- We have Rectangle class:
```java
public class Rectangle {

    private int width;
    private int height;

    // getter and setter methods...
}
```
- Also, we have a Square class:
``` java
public class Square {

    private int side;

    // getter and setter methods...
}
```
- And we have a ShapePrinter class that draws several types of shapes:
```java
public class ShapePrinter {

    public void drawShape(Object shape) {

        if (shape instance of Rectangle) {
            // Draw Rectangle...
        } else if (shape instance of Square) {
            // Draw Square...
        }
    }
}
```

**Solution:**
- We added a Shape abstract class:
```java
public abstract class Shape {
    abstract void draw();
}
```
- Refactor Rectangle class to extends from Shape:
```java
public class Rectangle extends Shape {

    private int width;
    private int height;

    // getter and setter methods...

    @Override
    public void draw() {
        // Draw the Rectangle...
    }
}
```
- Refactor Square class to extends from Shape:
```java
public class Square extends Shape {

    private int side;

    // getter and setter methods...

    @Override
    public void draw() {
        // Draw the Square...
    }
}
```

## Liskov Substitution Principle (LSP)
A subclass should be replaceable with its superclass without breaking anything. Methods work on the parent class should also work on the child class without any issue.

**Violation of LSP:**
- We have a Rectangle class:
```java
public class Rectangle {

    private int width;
    private int height;

    public void setWidth(int width) {
        this.width = width;
    }

    public void setHeight(int height) {
        this.height = height;
    }

    public int getArea() {
        return width * height;
    }
}
```
- And a Square class:
```java
public class Square extends Rectangle {

    @Override
    public void setWidth(int width) {
        super(setWidth(width));
        super(setHeight(width));
    }

    @Override
    public void setHeight(int height) {
        super(setWidth(height));
        super(setHeight(height));
    }
}
```

**Solution:**
- Refactoring of Rectangle:
```java
public class Rectangle:

    private final int width;
    private final int height;

    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }

    public int getArea() {
        return width * height;
    }
}
```
- Refactoring of Square to extends Rectangle:
```java
public class Square extends Rectangle {

    public Square(int side) {
        super(side, side);
    }
}
```

## Interface Segregation Principle (ISP)
A class should only implement the interface that it will use its features. Defining specific interfaces is better than general purpose interfaces.

**Violation of ISP:**
- We have a Car interface:
```java
public interface Car {
    void startEngine();
    void accelerate();
}
```
- And a Mustang class that implements the Car:
```java
public class Mustang implements Car {

    @Override
    public void startEngine() {
        // start engine...
    }

    @Override
    public void accelerate() {
        // accelerate...
    }
}
```
Now we define a new car model DoloRean which has a unique feature ability to travel in time.

- Add two new methods for our DoloRean in the Car interface:
```java
public interface Car {
    void startEnging();
    void accelerate();
    void backToThePast();
    void backToTheFuture();
}
```
- Now our DoloRean class implements Car:
```java
public class DoloRean implements Car {

    @Override
    public void startEngine() {
        // start engine...
    }
 
    @Override
    public void accelerate() {
        // accelerate...
    }
 
    @Override
    public void backToThePast() {
        // back to the past...
    }
 
    @Override
    public void backToTheFuture() {
        // back to the future...
    }
}
```
- But now Mustang class is forced to implement the new methods to comply with the Car interface:
```java
public class Mustang implements Car {
 
    @Override
    public void startEngine() {
        // start engine...
    }
 
    @Override
    public void accelerate() {
        // accelerate...
    }
 
    @Override
    public void backToThePast() {
        // because a Mustang can not back to the past!
        throw new UnsupportedOperationException();
    }
 
    @Override
    public void backToTheFuture() {
        // because a Mustang can not back to the future!
        throw new UnsupportedOperationException();
    }
}
```

**Solution:**
- Refactor Car interface:
```java
public interface Car {
    void startEngine();
    void accelerate();
}
```
- Add a TimeMachine interface:
```java
public interface TimeMachine {
    void backToThePast();
    void backToTheFuture();
}
```
- Refactor Mustang (only implements Car interface):
```java
public class Mustang implements Car {
 
    @Override
    public void startEngine() {
        // start engine...
    }
 
    @Override
    public void accelerate() {
        // accelerate...
    }
}
```
- Refactor DoloRean (implements Car and TimeMachine):
```java
public class DeloRean implements Car, TimeMachine {
 
    @Override
    public void startEngine() {
        // start engine...
    }
 
    @Override
    public void accelerate() {
        // accelerate...
    }
 
    @Override
    public void backToThePast() {
        // back to de past...
    }
 
    @Override
    public void backToTheFuture() {
        // back to de future...
    }
}
```

## Dependency Inversion Principle (DIP)
A class should not directly depend on another class but should depend on an abstract. This makes our code reusable.

**Violation of DIP:**
- We have a DeliveryDriver class that represents a driver that works for a company:
```java
public class DeliveryDriver {

    public void deliverProduct(Product product) {
        // deliver product...
    }
}
```
- DeliveryCompany that handles shipments:
```java
public class DeliveryCompany {

    public void sendProduct(Product product) {
        DeliveryDriver deliveryDriver = new DeliveryDriver();
        deliveryDriver.deliverProduct(product);
    }
}
```
DeliveryCompany creates and uses DeliveryDriver concretions. Therefore a high-level class (DeliveryCompany) is dependent on a low-level class (DeliveryDriver).

**Solution:**
- We create a DeliveryService interface to have an abstraction:
```java
public interface DeliveryService {
    void deliverProduct(Product product);
}
```
- Refactor DeliveryDriver class to implements DeliveryService:
```java
public class DeliveryDriver implements DeliveryService {

    @Override
    public void deliverProduct(Product product) {
        // deliver product...
    }
}
```
- Refactor DeliveryCompany that now depends on an abstraction and not on a concretion.
```java
public class DeliveryCompany {

    private DeliveryService deliveryService;

    public DeliveryCompany(DeliveryService deliveryService) {
        this.deliveryService = deliveryService;
    }

    public void sendProduct(Product product) {
        this.deliveryService.deliverProduct(product);
    }
}
```
