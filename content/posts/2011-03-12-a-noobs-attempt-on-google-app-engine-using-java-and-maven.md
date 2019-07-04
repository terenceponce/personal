---
title: "A noob's attempt on Google App Engine using Java and Maven"
date: 2011-03-12
draft: false
toc: false
images:
tags:
  - programming
  - java
---

Since I solved most of the problems that I encountered with Spring MVC, I decided to take it up a notch by using [Google App Engine](http://code.google.com/appengine/). If you haven’t heard about GAE, it’s basically a host for web applications that are built in Java or Python. Also, it’s an engine for building web applications (as you can see from the name). One of the benefits that you get from GAE is that your web application gets to be hosted by the same servers that power Google. Best of all, it’s free! Well, not really. It’s free when you avail the basic package, which already has everything a startup company needs anyway. You’ll only need to pay if you need more hosting power or other services like custom domains for your web application.

To be honest, this isn’t my first attempt on GAE. A few months ago, I made a web application using Python and [Django-nonrel](http://www.allbuttonspressed.com/projects/django-nonrel), a modification of the Django framework for Google App Engine. It was all good but I stopped the development after a few weeks because I lack knowledge, references, and guidance at that time.

With that said, this is my second attempt at using GAE. I decided to use Java and Spring 3.0 this time since I’ve been focusing a lot on Java lately. Hopefully, I’ll be able to do it successfully this time.

Of course, the first thing I did was read through the [documentation](http://code.google.com/appengine/docs/java/gettingstarted/). Unfortunately, I didn’t really get much from the documentation because it didn’t have enough information about the tools that I’ll use for the project like Maven 2, and Spring 3.0. I didn’t use Ant for my project because I found Maven to be more convenient to use. I already have an existing project built with Maven 2 and Spring 3.0 that I want to migrate to GAE, so I had to find a way to get Maven to do all the build, dependency management, and deployment task for me. Luckily, I found a [plugin for Maven](http://code.google.com/p/maven-gae-plugin/) that already does what I need.

To start, let me show you the contents of the pom.xml of my existing Maven project:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.jenanderic</groupId>
	<artifactId>jenanderic</artifactId>
	<packaging>war</packaging>
	<version>1.0-SNAPSHOT</version>

	<name>Jen&amp;Eric Drugstore</name>
	<description>POS System for Jen&amp;Eric Drugstore</description>

	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>3.8.1</version>
		</dependency>

		<dependency>
			<groupId>commons-logging</groupId>
			<artifactId>commons-logging</artifactId>
			<version>1.1.1</version>
		</dependency>

		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>servlet-api</artifactId>
			<version>2.5</version>
		</dependency>

		<dependency>
			<groupId>taglibs</groupId>
			<artifactId>standard</artifactId>
			<version>1.1.2</version>
		</dependency>

		<dependency>
			<groupId>jstl</groupId>
			<artifactId>jstl</artifactId>
			<version>1.1.2</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
			<version>3.0.5.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>3.0.5.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context-support</artifactId>
			<version>3.0.5.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>3.0.5.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-beans</artifactId>
			<version>3.0.5.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>3.0.5.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>3.0.5.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>3.0.5.RELEASE</version>
		</dependency>

	</dependencies>

	<build>
		<finalName>JenAndEric</finalName>

		<plugins>

			<plugin>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>2.1</version>
				<configuration>
					<source>1.6</source>
					<target>1.6</target>
				</configuration>
			</plugin>

			<plugin>
				<groupId>org.codehaus.mojo</groupId>
				<artifactId>tomcat-maven-plugin</artifactId>
				<configuration>
					<url>http://localhost:8080/manager</url>
					<username>tomcat</username>
					<password>s3cret</password>
					<path>/JenAndEric</path>
				</configuration>
			</plugin>

		</plugins>
	</build>
</project>
```

As you can see from the dependencies, it’s a Spring 3.0 project. Don’t mind the server credentials you see from the bottom of the code. I was too lazy to encrypt it anyway so I don’t care if you see my Tomcat server’s credentials right now. Also, notice the name of the web application that I’m making. I’m trying to make a web application for my family’s drug store so I can finally help with the family business. I’m such a good son. I think I should be awarded for this! Kidding aside, I don’t mind releasing some of the source code here but the project’s repository on [BitBucket](http://bitbucket.org/) is marked as private so you won’t really see everything.

Of course, this project won’t be able to run on GAE as is. I need to use the [maven-gae-plugin](http://code.google.com/p/maven-gae-plugin/) for my project to be able to run on Google App Engine. For starters, I need to add the location of the repositories where Maven will download the plugin and dependencies since it’s not hosted on the global repos of Maven:

```xml
<repositories>
	<repository>
		<id>maven-gae-plugin-repo</id>
		<url>http://maven-gae-plugin.googlecode.com/svn/repository</url>
		<name>Maven Google App Engine Repository</name>
	</repository>
</repositories>

<pluginRepositories>
	<pluginRepository>
		<id>maven-gae-plugin-repo</id>
		<url>http://maven-gae-plugin.googlecode.com/svn/repository/</url>
		<name>Maven Google App Engine Repository</name>
	</pluginRepository>
</pluginRepositories>
```

Now that Maven knows where to download the plugins and dependencies that the project needs, it’s time to add the actual plugins themselves:

```xml
<build>
	<plugins>

		<!-- Other plugins ommitted for brevity -->

		<plugin>
			<groupId>net.kindleit</groupId>
			<artifactId>maven-gae-plugin</artifactId>
			<version>${gae.pluginVersion}</version>
			<dependencies>
				<dependency>
					<groupId>net.kindleit</groupId>
					<artifactId>gae-runtime</artifactId>
					<version>${gae.version}</version>
					<type>pom</type>
				</dependency>
			</dependencies>
		</plugin>

		<plugin>
			<artifactId>maven-release-plugin</artifactId>
			<configuration>
				<goals>gae:deploy</goals>
			</configuration>
		</plugin>

	</plugins>
</build>
```

Adding these plugins to your pom.xml allows you to use the gae:run and gae:deploy goals in your Maven project. The first one, **gae:run**, builds your project and deploys it to the development server that the GAE SDK comes with. The second one, **gae:deploy**, builds your project and deploys it to the actual server of your application inside Google App Engine.

Next up, we need to add the JAR files that the project needs to run on GAE. Of course, instead of adding them manually to the **lib** folder of the project, we’ll let Maven handle the dependency management for us:

```xml
<dependencies>

	<!-- Other dependencies ommitted for brevity -->

	<dependency>
		<groupId>com.google.appengine.orm</groupId>
		<artifactId>datanucleus-appengine</artifactId>
		<version>1.0.7.final</version>
	</dependency>

	<dependency>
		<groupId>org.datanucleus</groupId>
		<artifactId>datanucleus-core</artifactId>
		<version>1.1.5</version>
		<exclusions>
			<exclusion>
				<groupId>javax.transaction</groupId>
				<artifactId>transaction-api</artifactId>
			</exclusion>
		</exclusions>
	</dependency>

	<dependency>
		<groupId>com.google.appengine</groupId>
		<artifactId>datanucleus-jpa</artifactId>
		<version>1.1.5</version>
		<scope>runtime</scope>
	</dependency>

	<dependency>
		<groupId>com.google.appengine</groupId>
		<artifactId>geronimo-jpa_3.0_spec</artifactId>
		<version>1.1.1</version>
		<scope>runtime</scope>
	</dependency>

	<dependency>
		<groupId>com.google.appengine</groupId>
		<artifactId>appengine-api-1.0-sdk</artifactId>
		<version>${gae.version}</version>
	</dependency>

	<dependency>
		<groupId>com.google.appengine</groupId>
		<artifactId>appengine-tools-api</artifactId>
		<version>${gae.version}</version>
	</dependency>

</dependencies>
```

If you read the code thoroughly, you’ll notice that I used property references for some of the values. I did that so upgrading to a different version won’t be much of a hassle. Finally, I added the actual values for the property references that I made earlier:

```xml
<properties>
	<gae.home>C:\devtools\eclipse\plugins\com.google.appengine.eclipse.sdkbundle.1.4.2_1.4.2.v201102111811\appengine-java-sdk-1.4.2</gae.home>
	<gae.applicationName></gae.applicationName>
	<gae.pluginVersion>0.7.1</gae.pluginVersion>
	<gae.version>1.3.7</gae.version>
</properties>
```

As you can see, I haven’t given any name for my application yet. Why is it so hard to come up with names?

Now that we’re finished modifying the pom.xml of the project, it’s time to test if the project will indeed run on Google App Engine:

> $mvn gae:run

As I said earlier, **gae:run** builds and deploys the project to the development server. Aside from that, it also starts the development server so you can access it from your browser. Anyway, I’m too lazy to post the output, but I assure you that the project will indeed run when you go to [http://localhost:8080/](http://localhost:8080/)
