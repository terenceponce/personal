---
title: "Inheritance in Java"
date: 2011-01-29
draft: false
toc: false
images:
tags:
  - programming
  - java
---

**DISCLAIMER**: This article is a bit hard to swallow so I’ll try to explain it to you as simple as I can. I also need to mention that there are other concepts that will also be introduced in this chapter aside from Inheritance.

This is my first post for 2011, so happy new year! Anyway, as a preparation for my upcoming preliminary exam in Java, I decided to write about Inheritance in this article.

Let’s say you’re at work right now. Your boss asked you to make an application that records your company’s employees’ information.

He mentioned the things you need to record:

- Name
- Salary ($500 being the default salary, unless specified)
- Employee Number
- Department Handled (For managers only)

Since you’re already familiar with classes, you decided to start immediately by creating classes that models the employees of the company. You started by making a class for the regular employee:

```java
public class Employee {
	private String name;
	private double salary;
	private int empNo;

	public Employee(String name, double salary, int empNo) {
		setName(name);
		setSalary(salary);
		setEmpNo(empNo);
	}

	public String getName() {
		return this.name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public double getSalary() {
		return this.salary;
	}

	public void setSalary(double salary) {
		this.salary = salary;
	}

	public int getEmpNo() {
		return this.empNo;
	}

	public void setEmpNo(int empNo) {
		this.empNo = empNo;
	}

	public void getDetails() {
		System.out.println("Name: " + getName());
		System.out.println("Salary: " + getSalary());
		System.out.println("EmpNo: " + getEmpNo());
	}
}
```

Now that you’re finished creating a class for the regular employee, you decided to make another class for the manager, since your boss specifically told you that there are 2 types of employees at the company:

```java
public class Manager {
	private String name;
	private double salary;
	private int empNo;
	private String department;

	public Manager(String name, double salary, int empNo, String department) {
		setName(name);
		setSalary(salary);
		setEmpNo(empNo);
		setDepartment(department);
	}

	public String getName() {
		return this.name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public double getSalary() {
		return this.salary;
	}

	public void setSalary(double salary) {
		this.salary = salary;
	}

	public int getEmpNo() {
		return this.empNo;
	}

	public void setEmpNo(int empNo) {
		this.empNo = empNo;
	}

	public String getDepartment() {
		return this.department;
	}

	public void setDepartment(String department) {
		this.department = department;
	}

	public void getDetails() {
		System.out.println("Name: " + getName());
		System.out.println("Salary: " + getSalary());
		System.out.println("EmpNo: " + getEmpNo());
		System.out.println("Department: " + getDepartment();
	}
}
```

Wait, what’s this? Did you notice how similar the 2 classes are? They’re so similar, you can’t even tell how different they are at first glance (Well, aside from the file name of course). This may seem fine at first, but what if your company introduced several new types of employee? It would be pretty ridiculous to have to write almost the same thing everytime a new type of employee is introduced, right? If only there was a way to turn all those redundant code into something we can reuse so we wouldn’t have to write them every single time.

Fortunately, we can! One of the main features of Object-Oriented Programming is Inheritance. Let me quote [Wikipedia](http://en.wikipedia.org/wiki/Inheritance_%28object-oriented_programming%29) on this one:

> In Object-Oriented Programming (OOP), ***Inheritance*** is a way to compartmentalize and reuse code by creating collections of attributes and behaviors called objects which can be based on previously created objects. In classical inheritance where objects are defined by classes, classes can inherit other classes. The new classes, known as subclasses (or derived classes), inherit attributes and behavior of the pre-existing classes, which are referred to as superclasses (or ancestor classes). The inheritance relationships of classes gives rise to a hierarchy.

Basically, what we do with Inheritance is we make a base class (super class) that contains the most common attributes and methods among all the classes, and make other classes (sub classes), which inherits from the base class, to customize and extend the functionality of the base class into their own. We do this to prevent code redundancy and promote code reuse. Lesser code redundancy = faster, and more efficient code.

Now that we’re familiar with Inheritance, let’s try to incorporate it in our code. Based from the boss’ specifications, it’s safe to say that Employee is the super class while Manager is the sub class. Since Employee is the base class, we don’t need to modify it in any way. Manager is the one containing the redundant code so let’s modify that instead:

```java
// Manager.java version 2
public class Manager extends Employee {
	private String department;

	public Manager(String name, double salary, int empNo, String department) {
		super(name, salary, empNo);
		setDepartment(department);
	}

	public String getDepartment() {
		return this.department;
	}

	public void setDepartment(String department) {
		this.department = department;
	}

	public void getDetails() {
		super.getDetails();
		System.out.println("Department: " + getDepartment());
	}
}
```

ZOMG! We just shrunk Manager.java from 52 lines down to 21. Just because this version has less lines than the previous one doesn’t mean it’s simpler. In fact, it’s so complicated, it’s hard to read… at first. Let me demistify the code one by one:

```java
public class Manager extends Employee {
```

We used the word **extends** to make the Manager class inherit from the Employee class. This creates a relationship and a hierarchy between the two classes. It’s like saying that Manager is an Employee, but not the other way around. From this point, Manager will inherit everything from Employee, and by everything, I mean the attributes, and methods, but not the constructors.

---

```java
private String department;
```

Noticed how we only declared one attribute here? Since Manager inherits from Employee, it already possesses all the attributes and methods from Employee, so there’s no need to declare them anymore.

---

```java
public Manager(String name, double salary, int empNo, String department) {
	super(name, salary, empNo);
	setDepartment(department);
}
```

We made a constructor for the manager class, but this time our constructor has an additional argument, department, since managers has to handle a department in the company.

Line 2 calls the super class’ constructor, which returns the created Employee object.

Line 3 sets a value to the Manager’s department attribute using **setDepartment()**.

After all that is done, a Manager object is now created with values for the name, salary, employee number, and department attributes.

---

```java
public String getDepartment() {
	return this.department;
}

public void setDepartment(String department) {
	this.department = department;
}
```

As I said earlier, since Manager inherits from Employee, we only need to make a getter and setter method for department.

---

```java
public void getDetails() {
	super.getDetails();
	System.out.println("Department: " + getDepartment());
}
```

Since I kept repeating how we don’t need to remake methods that we already made in the super class, shouldn’t this be illegal or something? Of course not! Actually, what we did here is a technique called **Method Overriding**. Again, let me quote [Wikipedia](http://en.wikipedia.org/wiki/Method_overriding) on this one:

> **Method Overriding**, in Object Oriented Programming, is a language feature that allows a subclass or child class to provide a specific implementation of a method that is already provided by one of its super classes or parent classes. The implementation in the subclass overrides (replaces) the implementation in the super class by providing a method that has same name, same parameters or signature, and same return type as the method in the parent class. The version of a method that is executed will be determined by the object that is used to invoke it. If an object of a parent class is used to invoke the method, then the version in the parent class will be executed, or If an object of the subclass is used to invoke the method, then the version in the child class will be executed.

Since **getDetails()** from Employee only prints the name, salary, and the employee number, we need to override it inside the Manager class so it prints the department as well.

At line 2, we call the **getDetails()** method of Employee, which prints the name, salary, and employee number. After that, we print the department at line 3.

---

Now that we’re done creating our model classes, let’s make another class to demonstrate what we learned in this article:

```java
public class EmployeeDemo {
	public static void main(String args[]) {
		Employee e = new Employee("Your name", 25000.00, 0001);
		Manager m = new Manager("Terence", 100000.00, 0002, "Administration");

		e.getDetails();
		m.getDetails();
	}
}
```

Isn’t it cool how I’m your boss even though you’re employee #1? Kidding aside, this will be the output of the class:

> Name: Your name
>
> Salary: 25000.00
>
> EmpNo: 0001

> Name: Terence
>
> Salary: 100000.00
>
> EmpNo: 0002
>
> Department: Administration

Isn’t Inheritance neat? I’m pretty sure you still don’t have any solid idea where to use this, but by the time you’re designing a large or complex software, you’ll find that Inheritance makes a lot of things easier in a design point of view.
