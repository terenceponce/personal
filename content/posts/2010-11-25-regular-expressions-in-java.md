---
title: "Regular Expressions in Java"
date: 2010-11-25
draft: false
toc: false
images:
tags:
  - programming
  - java
---

This semester, I have a class in Java, specifically, Java EE. It’s about time I have another programming class. It’s been 2 years already since my last class in Java. So anyway, for our first exercise, the professor asked us to make a basic Java application.

The requirement for the application is to use classes and packages. The classes should have private attributes, public methods, getter and setter methods. I figured I should try using regular expressions since one of the class attributes’ setter method seems to be screaming for one. Here’s the code that I made:

```java
package com.sem2.domain;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class Student {
	private String name;
	private int age;
	private String studno;

	public Student(String name, int age, String studno) {
		setName(name);
		setAge(age);
		setStudno(studno);
	}

	public String getName() {
		return this.name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return this.age;
	}

	public void setAge(int age) {
		if (age >= 0) {
			this.age = age;
		} else {
			this.age = 0;
		}
	}

	public String getStudno() {
		return this.studno;
	}

	public void setStudno(String studno) {
		int count = studno.length();
		String regex = "^[0-9]{4}-[0-9]{6}$";
		Pattern pattern = Pattern.compile(regex);
		Matcher matcher = pattern.matcher(studno);

		if (count != 11) {
			System.out.println("\nERROR! Student number must be 11 characters. Example: 2007-007065");
		} else {
			if (matcher.matches()) {
				this.studno = studno;
			} else {
				System.out.println("\nERROR! Student number must be in proper format. Example: 2007-007065");
			}
		}
	}

	public void printInfo() {
		System.out.println("Name: " + this.name);
		System.out.println("Age: " + this.age);
		System.out.println("Student Number: " + this.studno);
	}
}
```

The class has 3 attributes, the name of the student, their age, and their student number. The student number is based on our university’s student numbering system, so the student number format is something similar to this: 2007-007065 (YEAR-NUMBER). In the past, I used to pull my hair out if I’m required to make a validation for something like that manually. Today, I know how to use a bit of Regular Expressions, so I used that instead of the gruesome manual method. Why didn’t they teach us Regex at school, anyway?!

Using Regular Expressions in Java is pretty simple. First of all, you should import the following classes:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;
```

Now that we imported the classes that we need, let’s move into my code:

```java
public void setStudno(String studno) {
	int count = studno.length();
	String regex = "^[0-9]{4}-[0-9]{6}$";
	Pattern pattern = Pattern.compile(regex);
	Matcher matcher = pattern.matcher(studno);

	if (count != 11) {
		System.out.println("\nERROR! Student number must be 11 characters. Example: 2007-007065");
	} else {
		if (matcher.matches()) {
			this.studno = studno;
		} else {
			System.out.println("\nERROR! Student number must be in proper format. Example: 2007-007065");
		}
	}
}
```

The magic happens at line 4 and 5. At line 4, we declare a Pattern object called **pattern** (very original name). Then, we assign a value to it using **Pattern.compile()**. **Pattern.compile()** takes a string argument and converts it to a regular expression pattern.

Line 5 compares the regex pattern to the string argument in pattern.**matcher()**. Line 10 checks if the string argument matches the regex pattern. **matches()** returns 1 or True if it matches or 0 or False if otherwise.

That’s pretty much it when it comes to using Regular Expressions in Java. Oh wait, I forgot to mention the pattern that I used:

```java
String regex = "^[0-9]{4}-[0-9]{6}$";
```

**-** means to match the character literally since the student number format has a - in it.

```java
^[0-9]{4}-**[0-9]{6}**$
```

I just started to learn Regular Expressions recently so this one took a while for me to figure out. I’ll go over them one by one so I don’t forget.

The caret (^) means there aren’t any prefixes allowed to match the pattern.

**0-9** allows only numerical values from 0-9 to match the pattern.

**{4}** goes with **0-9** (The previous one) which means allow only 4 numerical characters. I used 4 to match the year part of the student number format.

**-** means to match the character literally since the student number format has a - in it.

Any other character is blocked after **$**. I used this because we don’t want any other characters after the student number.

I’m not sure if there’s a shorter way to do this but this was what I came up with after an hour of thinking.
