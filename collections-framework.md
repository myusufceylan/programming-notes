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

### 1.2 LinkedList
LinkedList is a linear data structure. Each element has the data and the address(pointer) to the next element.

```java
import java.util.*;

public class JavaExample {
    public static void main(String[] args) {
        LinkedList<String> linkList = new LinkedList<>();
        linkList.add("Apple"); //["Apple"]
        linkList.add("Orange"); //["Apple", "Orange"]

        // inserting element at first position
        linkList.add(0, "Banana"); //["Banana", "Apple", "Orange"]

        System.out.println("LinkedList elements: ");
        //iterating LinkedList using iterator
        Iterator<String> it = linkList.iterator();
        while(it.hasNext()) {
            System.out.println(it.next());
        }
    }
}
```

**Output:**
```java
LinkedList elements:
Banana
Apple
Orange
```

### 1.3 Vector
Vector is a synchronized resizable-array implementation of List interface but since it is synchronized it is slower than ArrayList.

```java
import java.util.*;

public class JavaExample {
    public static void main(String[] args) {
        Vector<String> v = new Vector<>();
        v.add("item1"); //["item1"]
        v.add("item2"); //["item1", "item2"]
        v.add("item3"); //["item1", "item2", "item3"]

        // removing an element
        v.remove("item2"); //["item1", "item3"]

        System.out.println("Vector elements: ");
        //iterating Vector using iterator
        Iterator<String> it = v.iterator();
        while(it.hasNext()) {
            System.out.println(it.next());
        }
    }
}
```

**Output:**
```java
Vector elements:
item1
item3
```

### 1.4 Stack
Stack extends class Vector. Stack is subclass of Vector. Stack uses Last In First Out (LIFO) concept. We can use push() method to insert elements at the end of the Stack and pop() method to remove the last element inserted in the Stack.

```java
import java.util.*;

public class JavaExample {
    public static void main(String[] args) {
        Stack<String> stack = new Stack<>();
        //push() method adds the element in the stack
        //and pop() method removes the element from the stack
        stack.push("Chaitanya"); //["Chaitanya"]
        stack.push("Ajeet"); //["Chaitanya", Ajeet]
        stack.push("Hari"); //["Chaitanya", "Ajeet", "Hari"]
        stack.pop(); //removes the last element
        stack.push("Steve"); //["Chaitanya", "Ajeet", "Steve"]
        stack.push("Carl"); //["Chaitanya", "Ajeet", "Steve", "Carl"]
        stack.pop(); //removes the last element

        System.out.println("Stack elements: ");
        for (String str : stack) {
            System.out.println(str);
        }
    }
}
```

**Output:**
```java
Stack elements:
Chaitanya
Ajeet
Steve
```

## 2. Set
A set is a Collection that cannot have duplicate elements.

### 2.1 HashSet
HashSet stores its elements in a hash table. It does not maintain insertion order.
```java


### 2.2 LinkedHashSet
It is same as HashSet but it maintains insertions order.

### 2.3 TreeSet
TreeSet stores its elements in a red-black tree. The elements are sorted in ascending order.
