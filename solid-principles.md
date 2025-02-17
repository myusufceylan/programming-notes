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

        
