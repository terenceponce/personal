---
title: "Bad start with Spring part 2"
date: 2011-03-07
draft: false
toc: false
images:
tags:
  - programming
  - java
---

Recently, I realized that Spring 2.5 was really old already. I was even [called out for it](http://stackoverflow.com/questions/5102514/how-to-retrieve-a-row-in-a-database-and-convert-it-into-an-object-in-spring):

> Terence why do you start with Spring 2.5 now when Spring 3 is already over a year old? – [Sean Patrick Floyd](http://stackoverflow.com/users/342852/sean-patrick-floyd) Feb 24 at 10:50

The quote came from a comment in my question on StackOverflow.

Anyway, I decided to try and use Spring 3.0 because of that. One of the new things that I encountered was the way Spring 3.0 maps the request to controllers. The dispatcher servlet doesn’t handle the URL mapping to controllers now. Instead, you use annotations to handle it automatically.

**UPDATE**: Apparently, I was wrong about the annotations. Spring already has annotation support in 2.5. I just didn’t get a chance encounter it back then which is why I thought it was completely new.

So I tried making another simple web application to try it out. First of all, I made a bean class:

```java
package com.jenanderic.domain;

public class Manufacturer {

	private int id;
	private String name;
	private String address;

	public Manufacturer() {}

	public Manufacturer(String name, String address) {
		this.name = name;
		this.address = address;
	}

	public Manufacturer(int id, String name, String address) {
		this(name, address);
		this.id = id;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getAddress() {
		return address;
	}

	public void setAddress(String address) {
		this.address = address;
	}
}
```

I then made an interface for managing the bean:

```java
package com.jenanderic.service;

import java.io.Serializable;
import java.util.List;

import com.jenanderic.domain.Manufacturer;

public interface ManufacturerService extends Serializable {

	public List<Manufacturer> getManufacturers();

	public void setManufacturers(List<Manufacturer> manufacturers);
}
```

After that, I made an implementation class for the interface I just created:

```java
package com.jenanderic.service;

import java.util.List;

import com.jenanderic.domain.Manufacturer;

public class ManufacturerServiceImpl implements ManufacturerService {

	private List<Manufacturer> manufacturers;

	public List<Manufacturer> getManufacturers() {
		return manufacturers;
	}

	public void setManufacturers(List<Manufacturer> manufacturers) {
		this.manufacturers = manufacturers;
	}
}
```

Now that I have the classes that I need, I decided to make a controller class for it:

```java
package com.jenanderic.web;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.ui.Model;

import java.util.Date;
import java.util.List;

import com.jenanderic.domain.Manufacturer;
import com.jenanderic.service.ManufacturerService;

@Controller
@RequestMapping("/manufacturer_list")
public class ManufacturerListController {

	private ManufacturerService manufacturerService;

	@RequestMapping(method = RequestMethod.GET)
	public String manufacturerList(Model model) {
		Date today = new Date();
		model.addAttribute("today", today);
		model.addAttribute("manufacturers", manufacturerService.getManufacturers());
		return "manufacturer_list";
	}

	public void setManufacturerService(ManufacturerService manufacturerService) {
		this.manufacturerService = manufacturerService;
	}
}
```

Notice the @Controller and @RequestMapping annotation. Putting the @Controller annotation in your class automatically makes it a controller class in Spring 3.0. Due to this, you don’t need to declare it in your dispatcher servlet anymore. I guess that’s pretty cool.

Oh wait, since I can’t declare the URL mapping of the controller in the dispatcher servlet, how do I inject my beans to the controller manually now? This is what my dispatcher servlet looks like right now:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context-3.0.xsd">

	<context:component-scan base-package="com.jenanderic.web" />

	<bean class="org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping" />

	<bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter" />

	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/jsp/" />
		<property name="suffix" value=".jsp" />
	</bean>

	<!--
	<bean name="/manufacturer_list" class="com.jenanderic.web.ManufacturerListController">
		<property name="manufacturerService" ref="manufacturerService" />
	</bean>
	-->

	<bean id="manufacturerService" class="com.jenanderic.service.ManufacturerServiceImpl">
		<property name="manufacturers">
			<list>
				<ref local="manufacturer1" />
				<ref local="manufacturer2" />
				<ref local="manufacturer3" />
			</list>
		</property>
	</bean>

	<bean id="manufacturer1" class="com.jenanderic.domain.Manufacturer">
		<property name="id" value="1" />
		<property name="name" value="Manufacturer #1" />
		<property name="address" value="Address1" />
	</bean>

	<bean id="manufacturer2" class="com.jenanderic.domain.Manufacturer">
		<property name="id" value="1" />
		<property name="name" value="Manufacturer #2" />
		<property name="address" value="Address2" />
	</bean>

	<bean id="manufacturer3" class="com.jenanderic.domain.Manufacturer">
		<property name="id" value="1" />
		<property name="name" value="Manufacturer #3" />
		<property name="address" value="Address3" />
	</bean>
</beans>
```

Notice the part that I commented out. That’s what I would have done if I wasn’t using annotations. Unfortunately, Spring 3.0 frowns upon not using annotations.

Anyway, running the program as is would result in a null pointer exception since this method won’t be getting any bean from the dispatcher servlet:

```java
@RequestMapping(method = RequestMethod.GET)
public String manufacturerList(Model model) {
	Date today = new Date();
	model.addAttribute("today", today);
	model.addAttribute("manufacturers", manufacturerService.getManufacturers());
	return "manufacturer_list";
}
```

If I use the auto-wiring feature of Spring 3.0, I guess my problem would be resolved by now, but this excerpt from the book that I’m reading is giving me second thoughts of using the auto-wiring feature:

> Although the auto-wiring feature is very powerful, the cost is that it will reduce the readability of your bean configurations. Because auto-wiring is performed by Spring at runtime, you cannot derive how your beans are wired from the bean configuration file. In practice, we recommend applying autowiring only in applications whose component dependencies are not complicated.

This was taken from the book, Spring Recipes – A problem-solution approach, 2nd edition by Gary Mak, Josh Long, and Daniel Rubio.

Yes, I know. I can’t really call my application complicated yet. I guess I’m just too lazy or too stupid to fix this problem on my own right now. Either way, I lost the motivation to learn Spring as of the moment. I can’t find a good introductory resource to Spring 3.0 that’s why I’m having a hard time right now. I really hope I get accepted at the code camp that I applied for. They’re going to teach Spring there. That might be my chance to finally have a good start with Spring.
