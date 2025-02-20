# Collections Framework
Java Collections Framework is a collection of interfaces and classes which helps storing and processing code efficiently.

## 1. List
A list is an ordered collection. There can be duplicate elements. We can insert or access elements by their indexes.

### 1.1 ArrayList
ArrayList is a resizable-array implementation of List interface. It implements all List operations.

```java
import java.util.*;

class JavaExample {
    public static void main(String[] args) {
        // creating ArrayList of string type
        ArrayList<String> arrList = new ArrayList<>();

        // adding few elements
        arrList.add("Cricket"); //list: ["Cricket"]
        arrList.add("Hockey"); //list: ["Cricket", "Hockey"]

        // Inserting an element at the first position (index 0)
        // ArrayList uses zero-based indexing
        arrList.add(0, "Basketball"); //list: ["BasketBall", "Cricket", "Hockey"]

        // Printing the elements of the ArrayList
        System.out.println("ArrayList Elements:");

        // Traversing ArrayList using an enhanced for loop
        for(String str : arrList) {
            System.out.println(str);
        }
    }
}
```

**Output:**
```java
ArrayList Elements:
Basketball
Cricket
Hockey
```
