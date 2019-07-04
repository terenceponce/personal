---
title: "Bad start with Spring part 2 - Resolved"
date: 2011-03-11
draft: false
toc: false
images:
tags:
  - programming
  - java
---

[Last time](/posts/2011/03/07/bad-start-with-spring-part-2/), I was kind of frustrated as well as lazy about my problem. As a recap, I had a problem with manually injecting beans in Spring 3.0 because I tried doing it the old fashioned way. After a long time of reading and thinking, I realized that I’m having problems because I didn’t embrace the sudden transition I had with annotations.

Since I’m the kind of person who embraces change, I decided to stop whining and use the auto wiring feature instead. I added the @Autowired annotation to the setter method inside the controller:

```java
package com.jenanderic.web;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.beans.factory.annotation.Autowired;
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

	@Autowired
	public void setManufacturerService(ManufacturerService manufacturerService) {
		this.manufacturerService = manufacturerService;
	}
}
```

Actually, now that I’ve thought about it already, I find the use of annotations much better and cleaner than the old method. It takes a while to learn for it to be readable enough though. It took me about half a day of reading to actually realize how cool annotations are. I even used multiple references to learn this.

As of now, I’m learning how Spring works little by little. I even finished a few chapters on the book that I’m reading. As of now, I’m trying out how to make Spring-MVC work with Google App Engine, but I think I’ll cover that on another entry.
