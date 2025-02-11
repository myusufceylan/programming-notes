# Object Oriented Programming Notes

## What is Object Oriented Programming?
We define objects which have states and behaviors by class blueprint. State means the attributes of the object and the behavior means the methods of the object. In real world, we can define a car as an object. Car has model, colour, speed etc. All of them are states of the Car object. Also, cars accelerate, stop, or park and these are the behaviours of the object Car. We can define different Car objects with these states and behaviours in the same class. This is object-oriented programming.

Object-Oriented Programming has 4 critical concepts: **Abstraction, Encapsulation, Inheritance, and Polymorphism.**

## 1. Abstraction
We show to users only the things related to them. They do not need to know how the code works. In the real world, people use bank accounts. They only enter the ID and the password. They do not need to know how the backend works when they enter their information.

```java
// Abstract class
abstract class Animal {
    // Abstract method
    public abstract void animalSound();
}

public class Dog extends Animal {
    public void animalSound() {
        System.out.println("Woof");
    }
    
    public static void main(String args[]) {
        Animal obj = new Dog();
        obj.animalSound();
    }
}
```

**Output:**
```java
Woof
```

## 2. Encapsulation
We protect some part of our code that we do not want direct access from users. This prevents changing covered parts that we want to hide and from the deterioration of the code. Public parts are accessible everywhere, but private parts are only accessible by specific methods. There is another type which is called `protected`, used to access only in the same class and subclass.

```java
class EmployeeCount
{
   private int numOfEmployees = 0;
   public void setNoOfEmployees (int count)
   {
       numOfEmployees = count;
   }
   public double getNoOfEmployees () 
   {
       return numOfEmployees;
   }
}
public class EncapsulationExample
{
   public static void main(String args[])
   {
      EmployeeCount obj = new EmployeeCount ();
      obj.setNoOfEmployees(5613);
      System.out.println("No Of Employees: "+(int)obj.getNoOfEmployees());
    }
}
```

**Output:**
```java
No Of Employees: 5613
```

## 3. Inheritance
We can derive subclasses from the base class. We do not need to create the same states and behaviors again and again. When we create a subclass it automatically has the same state variables and behaviour methods as in the base class but we can give specific parameters for the subclass. It prevents the reusage of the same code and make the code visibly clear and prevents from waste of memory.

```java
class Teacher {
   String designation = "Teacher";
   String college = "Beginnersbook";
   void does(){
	System.out.println("Teaching");
   }
}
public class MathTeacher extends Teacher{
   String mainSubject = "Maths";
   public static void main(String args[]){
      MathTeacher obj = new MathTeacher();
      System.out.println(obj.college);
      System.out.println(obj.designation);
      System.out.println(obj.mainSubject);
      obj.does();
   }
}
```

**Output:**
```java
Beginnersbook
Teacher
Maths
Teaching
```

## 4. Polymorphism
If we want to use the same method but with different attributes we can call the same method without needing to create a new method. In real time, we have two different animal but they both make a sound. So the method is the same but the outcome of the methods are different. The lion class is with "Roar" sound and the cat class is with "Meow" sound. We have two types of polymorphism static (overloading) and dynamic (overriding).

**Overloading:** When we use the same method in the same class but with different attributes we can use overloading. It calls the method according to the arguments that we give.

```java
class DisplayOverloading
{
    public void disp(char c)
    {
         System.out.println(c);
    }
    public void disp(char c, int num)  
    {
         System.out.println(c + " "+num);
    }
}
public class ExampleOverloading
{
   public static void main(String args[])
   {
       DisplayOverloading obj = new DisplayOverloading();
       obj.disp('a');
       obj.disp('a',10);
   }
}
```

**Output:**
```java
a
a 10
```

**Overriding:** We can derive subclasses from classes and we can use the same method for base class and child class. While defining a new object in the main function if we use base class constructor it calls base class' method and if we use child class constructor it calls child class' method and it is called *overriding*.

```java
class Animal{
   public void animalSound(){
	System.out.println("Default Sound");
   }
}
public class Dog extends Animal{

   public void animalSound(){
	System.out.println("Woof");
   }
   public static void main(String args[]){
	Animal obj = new Dog();
	obj.animalSound();
   }
}
```

**Output:**
```java
Woof
```

## Constructors
Contructor is a block of code that we use to initialize an object we created. Constructor has the same name as the object name. 

```java
public class Hello {
   String name;
   //Constructor
   Hello(){
      this.name = "Programmer";
   }
   public static void main(String[] args) {
      Hello obj = new Hello();
      System.out.println(obj.name);
   }
}
```

**Output:**
```java
Programmer
```

## Type Promotion 
When a data type of smaller size is promoted to a data type of bigger size we call it type promotion. For example byte can be promoted to short, float can be promoted to double etc.

```java
class Promotion {
   void disp(int a, double b) {
	System.out.println("Method A");
   }
   void disp(int a, double b, double c) {
	System.out.println("Method B");
   }
   public static void main(String args[]) {
	Promotion obj = new Promotion();
	/* I am passing float value as a second argument but
         * it got promoted to the type double, because there
     	 * wasn't any method having arg list as (int, float)
     	 */
	obj.disp(100, 20.67);
   }
}
```

**Output:**
```java
Method A
```
| From Type | Can be promoted to |
|-----------|--------------------|
| `byte`    | `short → int → long → double` |
| `short`   | `int → long → float → double` |
| `int`     | `long → float → double` |
| `float`   | `double` |
| `long`    | `float → double` |
| `char`    | `int → long → float → double` |
