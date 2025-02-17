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
       //store customer into a database...
    }

    //This is another Responsibility
    public void generateCustomerReport(String customerName) {
       //generate a report...
    }
}
```

**Better Solution:**

- Customer Class
```java
