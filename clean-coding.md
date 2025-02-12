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
