# Clean Coding in Java
Clean code is a code any programmer can read and change easily.

## Project Structure
Maven suggests the following structure:

* *src/main/java:* For source files
* *src/main/resources:* For resource files
* *src/test/java:* For test source files
* *src/test/java:* For test resource files

## Source File Structure
Order of elements in a source file should be like this:
- **Package statement**
- **Import statements**
  - All static imports
  - All non-static imports
- **Exactly one top-level class**
  - Class variables
  - Instance variables
  - Constructors
  - Methods

```java
# /src/main/java/com/baeldung/application/entity/Customer.java
package com.baeldung.application.entity;

import java.util.Date;

public class Customer {
    private String customerName;
    private Date joiningDate;
    public Customer(String customerName) {
        this.customerName = customerName;
        this.joiningDate = new Date();
    }

    public String getCustomerName() {
        return this.customerName;
    }

    public Date getJoiningDate() {
        return this.joiningDate;
    }
}
```

## Whitespaces
It is easy to read short texts comparing to long texts. Similarly, we use whitespaces in order to seperate code blocks and words to make them easily readable.
- Two blank lines before static blocks, fields, constructors and inner classes.
- One blank line after a method signature that is multiline.
- A single space seperating keywords if, for from an open parantheses; else from a closing parantheses.

## Indentation
We should use tab or 4 spaces while indenting. For methods, we should break after a comma; for expressions, we should break before an operator. After the break we should indent 2 spaces. The length of a line should be maximum 120 characters.

```java
List<String> customerIds = customer.stream()
  .map(customer -> customer.getCustomerId())
  .collect(Collectors.toCollection(ArrayList::new));
```

## Code Comments
Code comments explains the usage of our code to understand it better. However we should avoid writing the things already obvious. There are two types of comments: Documentation/JavaDoc comments are for users; Implementation/Block comments are for developers of the codebase.

```java
/**
* This method is intended to add a new address for the customer.
* However do note that it only allows a single address per zip
* code. Hence, this will override any previous address with the
* same postal code.
*
* @param address an address to be added for an existing customer
*/
/*
* This method makes use of the custom implementation of equals 
* method to avoid duplication of an address with same zip code.
*/
public addCustomerAddress(Address address) {
}
```

## SOLID
**Single Responsibility Principle:** Every interface, class or method should have one clear goal.
**Open-Closed Principle:** Our code should be open to extensions but closed to modification. Namely,our code should be well design and we should not need to modificate it but we can extend it for inheritence and composition.
**Liskov-Substitution Principle:** Every subclass or derived class should be substitutional to their parent or base class. This reduce coupling between classes.
**Interface Segregation Principle:** Interfaces are blueprints for classes. A class must not need to implement a method that It does not have to do. We need smaller and focussed interfaces.
**Dependency Inversion Principle:** Classes should only depend on abstractions not on concrete implementations. Classes should not be responsible for creating objects, implementations should be enjected into classes.

## DRY & KISS
**DRY:** means Don't Repeat Yourself. We should avoid duplication of code to keep it clean and focussed.

**KISS:** means Keep It Simple, Stupid. We should keep de code as simple as possible.

## TDD
Test Driven Development is used to test the code in everystep to find if there is any error immediately.
- First write the test code
- Then get an error because there is no funciton to test
- Then write and test the function
- If it is necessary we can improve our code (refactoring).
- Since we test in everystep our code becomes reliable.
