# Object Oriented Programming Notes
## What is Object Oriented Programming?
We define objects which have states and behaviors by class blueprint. State means the arguments of the object and the behavior means the methods of the object. In real world, we can define a car as an object. Car has model, colour, speed etc. All of them are states of the Car object. Also Cars accelerate, stop or park and these are the behaviours of the object Car. We can define different Car objects with these states and behaviours in the same class. This is object-oriented programming.

Object Oriented Programming have 4 critical concepts: Abstraction, Encapsulation, Inheritance and Polymorphism.
## 1. Abstract
We show to users only the things related to them. They do not need to know how the code works. In real world, people use bank accounts. They only enter the ID and the password. They do not need to know how the backend works when they enter their information.
'''java
//abstract class
abstract class Animal {
  //abstract method
  public abstract void animalSound();
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
'''

**Output:
'''java
Woof
'''

## 2. Encapsulation
We protect some part of our code that we do not want direct access from user. This prevent from changing covered parts that we want to hide and from deterioration of the code. Public parts are accessible by everywhere but private parts are only accessible by specific methods. There is another type which is called 'protected' used to access only in the same class and subclass.

