---
title: "A noob's attempt on Google App Engine using Java and Maven part 2"
date: 2011-03-26
draft: false
toc: false
images:
tags:
  - programming
  - java
---

In my [last entry](/posts/2011/03/12/a-noobs-attempt-on-google-app-engine-using-java-and-maven/), I modified my Maven project so it can be deployed to Google App Engine. This time, I’m going to work on my actual application so I can finally have something to show to other people.

Right now, my Spring web application works fine on Google App Engine. Let me go over the code that I have so far. I first made a bean called Manufacturer:

```java
package com.jenanderic.domain;

public class Manufacturer {
	private int id;
	private String name;
	private String address;

	// Constructors

	// Getters and Setters
}
```

Next, I made an implementation class of a service interface for the bean that I just made:

```java
package com.jenanderic.service;

// Imports

public class ManufacturerServiceImpl implements ManufacturerService {

	@Autowired
	private List<Manufacturer> manufacturers;

	// Getters and Setters
}
```

I made it autowired so Spring will do the injection for me. I then made the controller to handle the requests:

```java
package com.jenanderic.web;

// Imports

@Controller
@RequestMapping("/manufacturer")
public class ManufacturerController {

	private ManufacturerService manufacturerService;

	@Autowired
	public void setManufacturerService(ManufacturerService manufacturerService) {
		this.manufacturerService = manufacturerService;
	}

	@RequestMapping(method = RequestMethod.GET)
	public String getManufacturerList(Model model) {
		model.addAttribute("manufacturers", manufacturerService.getAllManufacturers());
		return "manufacturer_list";
	}
}
```

Then, I made the template page since the controller returns a page called manufacturer_list.jsp:

```java
<%@ include file="/WEB-INF/jsp/include.jsp" %>

<html>
	<head>
		<title><spring:message code="manufacturer.title" /></title>
	</head>
	<body>
		<h2><spring:message code="manufacturer.heading" /></h2>
		<c:forEach items="${manufacturers}" var="manufacturer">
			<p><c:out value="${manufacturer.id}"/> -
			<c:out value="${manufacturer.name}"/> - <c:out value="${manufacturer.address}"/></p>
		</c:forEach>
	</body>
</html>
```

Now that everything’s almost done, it’s time to make an actual bean to inject into the controller. I placed it in the Application’s context:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context-3.0.xsd">

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
		<property name="id" value="2" />
		<property name="name" value="Manufacturer #2" />
		<property name="address" value="Address2" />
	</bean>

	<bean id="manufacturer3" class="com.jenanderic.domain.Manufacturer">
		<property name="id" value="3" />
		<property name="name" value="Manufacturer #3" />
		<property name="address" value="Address3" />
	</bean>

</beans>
```

Since I have it autowired, I don’t need to explicitly inject it into the controller. Pretty cool, right?

Anyway, if I build and run the web app in GAE’s development server, it will run smoothly with no problems, so I’m sure that my web app runs 100% fine at this point.

Of course, I need to add persistence in the application since web apps are mostly about persistence. This is where it gets complicated. Even though I have plenty of experience working with database-driven applications, Google App Engine makes it seem otherwise.

Google App Engine works in a manner very different from traditional platforms. The most noticeable is the way it handles persistence; it doesn’t support relational databases. Google App Engine uses a non-relational database called the datastore. At the moment, I don’t have a full grasp of the pros and cons of not being able to use a relational database, but I’m pretty sure it has a great impact with the way you will build your application. Working with a non-relational database means that you have to manually maintain indexes with hand-written code. Aside from that, you have to manually write code for merging the results of multiple queries. Bummer.
