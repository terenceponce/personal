---
title: "Classes and Objects in Java"
date: 2010-12-04
draft: false
toc: false
images:
tags:
  - programming
  - java
---

**DISCLAIMER**: Do not use any of the code found in this entry for real life exercises. They do not adhere to the standards of OOP. I intentionally wrote wrong code in this entry for the sake of discussion. I will smack you in the face if you used the following code in real life exercises.

I’ve been trying to teach some of my classmates about the concepts of OOP, so I thought I’d turn them into a series blog posts for future reference. I’m also going through the process of re-learning Java and OOP in my Java class so I’m going to write about them to make sure I leave no holes unplugged.

### OBJECTS

First of all, let’s talk about Objects. Objects are the core of OOP. I mean, why the heck would they call it Object-Oriented Programming if it wasn’t about Objects, right?

Objects in OOP are very similar to real-life objects. You could say that they’re based on them. Real-life objects have attributes and behaviors and Objects in OOP are modeled based on them. Let’s take a car for example: a car is an object, it has attributes (brand, color, name, speed, gear) and behaviors (accelerate, decelerate, change gear).

In OOP, we create objects based on what they are in real life. Objects may vary in complexity based on what they are in real life because some objects may have complicated behaviors (may involve complicated math or algorithms) or attributes (having other objects as their attributes). Understanding objects is the key to learning and thinking in OOP.

### CLASSES

Let’s say you went outside for a walk and you saw John doing his usual rounds of jogging in the neighborhood. Behind him are Bob and Mary who are running a wee bit slower than John because they’re both still in the process of reducing their weight. You decided to jog with them and you saw that hot girl, Samantha, with her friends, Chloe and Alicia, while jogging. After jogging, you decided to go home and reflect on what happened while you were outside. While reflecting, you realized that you saw a lot of people today. Sure, they all have their differences, but they all have one thing in common: they’re people.

Based on the scenario above, we can safely say that John, Bob, Mary, Samantha, Chloe, and Alicia are objects. One thing they have in common is that they’re all people; human beings. If you haven’t guessed what Classes are yet, they’re basically an abstraction of a set of objects. Based on this definition, we can say that the people mentioned above are objects of the class, People (or Person if we want to be specific).

Another good analogy for Classes are houses. People can build hundreds of houses using just one blue print. Houses are the objects, the blue print is the class.

Now that we understand what Classes are, let’s try to make a class. It should look something like this:

```java
class Car {
	String brand;
	String color;
	String name;
	int speed = 0;
	int gear = 1;
}
```

This is, technically, a complete class already. If you look at it pretty closely, you’ll notice that it’s a representation of the car example that I used earlier. In Java, instead of attributes, it has fields (brand, color, name, speed, gear). Isn’t that easy to understand?

Right now, we have the blue print for making cars, but just having the blue print isn’t enough. We have to start building them to make cars. Let’s make another class that creates car objects and invokes their fields and manipulates them:

```java
class CarDemo {
	public static void main(String[]args) {
		Car myCar = new Car();
		Car yourCar = new Car();

		myCar.brand = "Porsche";
		myCar.color = "Black";
		myCar.name = "911 GT3";
		myCar.speed = 120;
		myCar.gear = 2;

		yourCar.brand = "Volkswagen";
		yourCar.color = "Green";
		yourCar.name = "Beetle";
		yourCar.speed = 10;
		yourCar.gear = 5;

		System.out.println("myCar is a " + myCar.color + " " + myCar.brand + " " + myCar.name + ".");
		System.out.println("myCar is running on " + myCar.speed " miles per hour on just gear " + myCar.gear ".");

		System.out.println("yourCar, however, is a " + yourCar.color + " " + yourCar.brand + " " + yourCar.name + ".");
		System.out.println("yourCar is running on " + yourCar.speed " miles per hour on gear " + yourCar.gear " already. Yuck!");
	}
}
```

As I’ve said in the disclaimer earlier, this is the wrong way to code. I did this so you can be aware of what is wrong and right in OOP. I’ll show you how to code the right way in my next entry.

The output of this program will look like this:

> myCar is a Black Porsche 911 GT3.
> myCar is running on 120 miles per hour on just gear 2.
> yourCar, however, is a Green Volkswagen Beetle.
> yourCar is running 10 miles per hour on gear 5 already. Yuck!

This is me trying to pull off a car joke even though I barely know anything about cars. Kidding aside, let’s go over the code one by one:

```java
Car myCar = new Car();
Car yourCar = new Car();
```

Here, we create 2 instances/objects of the class, Car. This is the way to create objects in Java:

```java
Classname variableName = new Constructor();
```

This statement has 2 parts. On the left hand side, we declare an object. Take note that it hasn’t been officially created yet since we only did a declaration. On the right hand side, this is the part where we construct the object and then assign the newly constructed object to the declared object on the left hand side.

I’m a bit skeptical about teaching you the syntax since I wanted to teach you concepts instead of language-specific syntax. Anyway, let’s move on to the next one:

```java
myCar.brand = "Porsche";
myCar.color = "Black";
myCar.name = "911 GT3";
myCar.speed = 120;
myCar.gear = 2;

yourCar.brand = "Volkswagen";
yourCar.color = "Green";
yourCar.name = "Beetle";
yourCar.speed = 10;
yourCar.gear = 5;
```

In this chunk of code, I’ve manipulated the fields of both myCar and yourCar objects and assigned values to them.

```java
System.out.println("myCar is a " + myCar.color + " " + myCar.brand + " " + myCar.name + ".");
System.out.println("myCar is running on " + myCar.speed " miles per hour on just gear " + myCar.gear ".");

System.out.println("yourCar, however, is a " + yourCar.color + " " + yourCar.brand + " " + yourCar.name + ".");
System.out.println("yourCar is running on " + yourCar.speed " miles per hour on gear " + yourCar.gear " already. Yuck!");
```

Here, we call the value of the fields myCar and yourCar and print them on the screen. In Java, you call them by

```java
objectName.[fieldName or methodName]
```

In conclusion, Objects are the core of OOP, they are also modeled according to real-life objects, and Classes are the blue prints for making objects. Understanding how objects and classes work are the key to having an OOP state of mind. This is very important when doing OOP.

In my [next entry](/posts/2010/12/12/access-modifiers-in-java/), we’ll go over the right way of coding in OOP. We will also cover the advanced stuff when it comes to classes and objects.
