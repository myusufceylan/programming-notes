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

An example for this:
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

## Identation
