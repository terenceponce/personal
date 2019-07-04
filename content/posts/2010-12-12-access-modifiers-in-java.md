---
title: "Access Modifiers in Java"
date: 2010-12-12
draft: false
toc: false
images:
tags:
  - programming
  - java
---

As I’ve said in my [previous entry](/posts/2010/12/04/classes-and-objects-in-java/), classes should never be invoked like this:

```java
myCar.brand = "Porsche";
myCar.color = "Black";
myCar.name = "911 GT3";
myCar.speed = 120;
myCar.gear = 2;

System.out.println("myCar is a " + myCar.color + " " + myCar.brand + " " + myCar.name + ".");
System.out.println("myCar is running on " + myCar.speed " miles per hour on just gear " + myCar.gear ".");
```

The reason behind this is because too much data is exposed to the wild and they’re very prone to errors if you followed this approach.

Let’s say you wanted to change myCar’s color attribute to something different, say, White? Unfortunately, you’re pretty nervous at the moment and you entered “White123″ instead. Your professor/boss saw this little error in the program and he humiliated you in front of everyone over the stupid error. “Colors don’t have numbers on their names!”, he said while laughing. Don’t ask me why someone would laugh over something so trivial.

We could have prevented this tragic incident if we validated the data first before setting it as the attribute’s value. Let’s modify the class that we made in the previous entry and make a method that validates data and removes non-alphabet characters to make up for the mistake that we’ve done in the past.

```java
class Car {
	String brand;
	String color;
	String name;
	int speed = 0;
	int gear = 1;

	void setColor(String color) {
		color = color.replaceAll("[0-9]", "");
		this.color = color;
	}
}
```

Now that we have a method that removes all the non-alphabet characters, you might think that this solution will finally save you from the humiliation that you experienced, right? Not really. Why, you ask? Well, we can still call the attribute directly like this:

```java
myCar.color = "White123";
```

So you might be thinking, “What the heck did we make that method for if we can still ignore the method and assign a value to the attribute directly like before?”.

In Java, we can limit access to variables and methods through the use of Access Modifiers. There are 4 access modifiers in Java: Public, Private, Protected, and Default (No modifier).

Since I’m too lazy to define everything for you, let me quote this [tutorial’s](http://www.javabeginner.com/learn-java/introduction-to-java-access-modifiers) definition on all 4 access modifiers:

> **Public access modifier**
>
> Fields, methods and constructors declared public (least restrictive) within a public class are visible to any class in the Java program, whether these classes are in the same package or in another package.

> **Private access modifier**
>
> The private (most restrictive) fields or methods cannot be used for classes and Interfaces. It also cannot be used for fields and methods within an interface. Fields, methods or constructors declared private are strictly controlled, which means they cannot be accesses by anywhere outside the enclosing class. A standard design strategy is to make all fields private and provide public getter methods for them.

> **Protected access modifier**
>
> The protected fields or methods cannot be used for classes and Interfaces. It also cannot be used for fields and methods within an interface. Fields, methods and constructors declared protected in a superclass can be accessed only by subclasses in other packages. Classes in the same package can also access protected fields, methods and constructors as well, even if they are not a subclass of the protected member’s class.

> **Default access modifier**
>
> Java provides a default specifier which is used when no access modifier is present. Any class, field, method or constructor that has no declared access modifier is accessible only by classes in the same package. The default modifier is not used for fields and methods within an interface.

Now that we’re familiar with access modifiers, let’s modify the class once again. As a convention, we should declare class attributes as private variables to prevent outside classes from messing with them directly.

```java
class Car {
	private String brand;
	private String color;
	private String name;
	private int speed = 0;
	private int gear = 1;
}
```

Now that we made the attributes of the Car class private, how are we going to access them from outside the class now? We solve this by creating getter and setter methods inside the class and giving them the public access modifier.

```java
class Car {
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

What’s the logic behind this? Remember that method we created to validate the color’s name and remove any non-alphabet characters? We turned that method into a setter for the color attribute. Since we turned the color attribute into a private variable, you and other programmers will be forced to use the setter if you want to change the value of color. When making setter methods, it’s a good practice to put your validation inside of them to make sure that data will be handled accordingly.

Aside from setters, you need to make getter methods as well to retrieve the value of the attributes since there’s no way for you to retrieve them directly because of the private access modifier.

Now that we have a decent Car class, let’s make another class to create Car instances and invoke their attributes and methods:

```java
class CarDemo {
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

Notice how I still used “White123″ as the color’s new value? Since we made a validation for that earlier, we don’t have to worry about the numeric characters being included in the string. This will be the output of the program:

> The car is a White Porsche 911 GT3.
> The car runs at 120 miles per hour on gear 2.

What happens when we try to invoke the class attributes directly? Simple, we’ll get a compile error. That’s the reason why everyone will be forced to use the getter and setter methods. Capiche?
