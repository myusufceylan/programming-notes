# Java Naming Conventions
We must obey some rules to keep our code easily readable and understandable.

## 1 Naming Packages
Should start with all lowercase domain name and rest of the part is up to organization's naming rules.

```java
package com.google.search.common;
package com.company.myapplication.web.controller;
package com.howtodoinjava.webapp.controller;
```

## 2 Naming Classes
Class names should be **nouns**. Each word in a class name should start with uppercase.

```java
public class ArrayList {}
public class Employee {}
public class Identity {}
```

## 3 Naming Interfaces
Interface names are generally **adjectives** but sometimes they can be **nouns**. First letter of each word in the interface name should be capitalized.

```java
public interface Serializable {}
public interface Clonable {}
public interface List {}
```

## 4 Naming Methods
Method names are always **verbs**. They represent actions and the name should clearly show what function it does.

```java
public void remove(Object o) {}
public Object update(Object o) {}
public Report getReportById(Long id) {}
```

## 5 Naming Variables
Variable names should be in camelcase format. They should be short and enough to describe. They cannot start with digits. They cannot be keywords (static, final, class). We must avoid whitespaces while naming. Temporary names can be one character e.g. counter.

```java
public Long id;
public EmployeeDao employeeDao;
private Properties properties;
```

## 6 Constant Naming Conventions
Constant name's letters must be all uppercase and words are seperated by underscore "_". We must use **final** keyword with constant names.

```java
public final String SECURITY_TOKEN = "...";
public final int INITIAL_SIZE = 16;
public final Integer MAX_SIZE = Integer.MAX;
```

## 7 Naming Generic Types
Generic type names should be a capital single letter.

```java
public interface Map <K,V> {}
public interface List<E> extends Collection<E>
Iterator<E> iterator() {}
```

## 8 Naming Enums
Enum names should be all uppercase letters.

```java
enum Direction {NORTH, EAST, SOUTH, WEST}
```

## 9 Naming Annotations
Annotation names follow title case notation. They can be name, verb or adjective.

```java
public @interface FunctionalInterface {}
public @Async Documented {}
public @Test Documented {}
```
