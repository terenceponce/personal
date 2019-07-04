---
title: "Code Refactoring"
date: 2010-12-12
draft: false
toc: false
images:
tags:
  - programming
  - java
---

In the real world, you’re not a one-man team like you once were during college (because apparently, it’s cheating in your professor’s eyes). You will work with other people when developing software. If that’s the case, it’s inevitable that those other people you’re working with will also be looking at your code. Not only that, they’ll also be working with the code that you will write.

One of the things a developer must strive for is better code. What do I mean by better code? Take a look at the code you’re working on right now. If you show it to other programmers, will they be able to figure out how your code works immediately without having to look at it extensively?

Does your code have too much comments? Do most of the comments just explain the “what” and not the “why” of the code? It’s not that I have anything against using comments. I simply believe that comments should only be used to indicate why you did the following approach in writing the code to which to comment points to instead of what the code does.

Do you find yourself repeating a lot of code in your software?

Take a look at some of the code that you made in the past. Do you still understand how everything works?

If you answered No to the first and last question and Yes to the rest of the questions, then you, dude, have bad code. It’s time to give your code a makeover a.k.a. code refactoring.

[Code Refactoring](http://en.wikipedia.org/wiki/Code_refactoring), by definition of Wikipedia, is the process of changing a program’s source code without affecting its external functional behavior to improve some of the non-functional aspects of the program. You usually change it for the better.

Code Refactoring has the following benefits:

- You make your code more readable to yourself and others as well.
- Since it makes your code more readable, it makes your software easier to maintain and extend.
- More often than not, refactoring results to lesser lines of code, so it actually saves you some bytes of memory when refactoring
- It results to lesser comments in your code since the code is pretty much self-explanatory already.
- It saves you from being screamed at by your fellow programmers for showing them bad code.
- To further my discussion on the concept of code refactoring, let me show you a basic example of how refactoring works.

We’ll use the code that we made in my [last entry](/posts/2010/12/12/access-modifiers-in-java/). Here are both the source files we made:

```java
public class Car {
	private String brand;
	private String color;
	private String name;
	private int speed = 0;
	private int gear = 1;

	public String getBrand() {
		return this.brand;
	}

	public void setBrand(String brand) {
		this.brand = brand;
	}

	public String getColor() {
		return this.color;
	}

	public void setColor(String color) {
		color = color.replaceAll("[0-9]", "");
		this.color = color;
	}

	public String getName() {
		return this.name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getSpeed() {
		return this.speed;
	}

	public void setSpeed(int speed) {
		this.speed = speed;
	}

	public int getGear() {
		return this.gear;
	}

	public void setGear(int gear) {
		this.gear = gear;
	}
}
```

```java
public class CarDemo {
	public static void main(String[]args) {
		Car myCar = new Car();

		myCar.setBrand("Porsche");
		myCar.setColor("White123");
		myCar.setName("911 GT3");
		myCar.setSpeed(120);
		myCar.setGear(2);

		System.out.println("The car is a " + myCar.getColor() + " " + myCar.getBrand() + " " myCar.getName() + ".");
		System.out.println("The car is runs at " + myCar.getSpeed() + " miles per hour on gear " + myCar.getGear());
	}
}
```

Let’s focus our attention to CarDemo.java. Look at how we invoked the Car class in this example. You first declare and instantiate an object then you have to set values to them one by one. Isn’t it too much of a hassle to create objects with this approach? I’m pretty lazy to have to write all these so it’s only natural to find a way to do them in shorthanded way.

Speaking of laziness, did I mention it’s one of the virtues of a good programmer? No, not that kind of laziness where you bum around and do nothing. Here’s the definition of laziness according to the book, [ProgrammingPerl](http://c2.com/cgi/wiki?LazinessImpatienceHubris):

> **Laziness**: The quality that makes you go to great effort to reduce overall energy expenditure. It makes you write labor-saving programs that other people will find useful, and document what you wrote so you don’t have to answer so many questions about it. Hence, the first great virtue of a programmer. Also hence, this book.

Now that we know how being lazy is a good trait for a programmer, we need to do something about how we create Car objects.

Let’s say one of your co-workers, Bob, who also happens to be your team mate in the program you’re working on, wanted Car objects to have specific default values for their attributes when he creates them so he can mass produce them (Well, mass producing is a bit of an exaggeration but just go with it). He told you that he only wants to specify the brand and name of the car and he wants every car that he makes to have it’s default color set to “White”, its speed set to 0 (obviously) and its gear set to 1. How will you do this?

When we talk about creating objects, we’re talking about the constructor, specifically. Since we didn’t explicitly make a constructor for the Car class in the last entry, let’s make one for Bob:

```java
public Car (String brand, String name) {
	setBrand(brand);
	setColor("White");
	setName(name);
}
```

What this constructor does is get the values of its brand and name argument and assign them to the object using its setter methods. If you looked at it closely, you’ll notice that we didn’t call the setter for speed and gear. Why? Take a look at Car.java again. We already assigned default values for the attributes, speed and gear.

```java
private int speed = 0;
private int gear = 1;
```

Every instance of Car will possess these assigned values by default when you create them.

If you still remember Bob’s requirements, you’ll see that it’s exactly what he wanted. If we go to CarDemo.java right now, we can now create objects in this fashion:

```java
Car bobsCar = new Car("Mitsubishi", "Lancer EX");
```

Isn’t this convenient? We can now create objects and assign values to their attributes in a single line. If we want to change them, we can just invoke the setter methods and be done with it. This will now be our new CarDemo.java:

```java
public class CarDemo {
	public static void main(String[]args) {
		Car bobsCar = new Car("Mitsubishi", "Lancer EX");

		System.out.println("The car is a " + bobsCar.getColor() + " " + bobsCar.getBrand() + " " bobsCar.getName() + ".");
		System.out.println("The car is runs at " + bobsCar.getSpeed() + " miles per hour on gear " + bobsCar.getGear());
	}
}
```

This will be the output:

> The car is a White Mitsubishi Lancer EX.
> The car runs at 0 miles per hour on gear 1.

Will you look at that? We shrunk that long 6-liner code into just one line. See how convenient code refactoring is?

We can still shrink the main method of CarDemo.java into just 2 lines. Look at the rest of the code, did you notice how long it is to write the code to print the values of the object to the screen? Well, not really, but what if you made a million objects and you have to print all of their values to the screen? Wouldn’t that be a nightmare?

If you find yourself typing the same code multiple times, you can turn all those repeated code into methods. That way, you only have to type the code once and just call it when you need it.

Let’s cut and paste our printing code to Car.java, put it inside a method, and tweak it a little bit so it would suit our needs:

```java
public void printInformation() {
	System.out.println("The car is a " + getColor() + " " + getBrand() + " " getName() + ".");
	System.out.println("The car is runs at " + getSpeed() + " miles per hour on gear " + getGear());
}
```

I’m pretty sure you’ll have a good idea of how CarDemo.java would look right now, but I’ll still show it to you:

```java
public class CarDemo {
	public static void main(String[]args) {
		Car bobsCar = new Car("Mitsubishi", "Lancer EX");

		bobsCar.printInformation();
	}
}
```

We shrunk it into 2 lines (Well, 3, actually, because of the whitespace)! It will still produce the same output as last time, but now it takes less effort to do the same thing. Isn’t code refactoring wonderful? Now you will be saved from getting screamed at by your co-workers.
