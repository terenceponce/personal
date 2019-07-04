---
title: "Bad start with Spring - Resolved"
date: 2011-03-06
draft: false
toc: false
images:
tags:
  - programming
  - java
---

In my [previous entry](/posts/2011/03/01/bad-start-with-spring/), I started to learn how to do simple CRUD operations using Spring-MVC’s JDBC feature. Unfortunately, it didn’t turn out to be as simple as I thought.

So my problem was with this unit test that was failing because it returns an empty result set:

```java
public void testGetCompany() {
	Company company = companyDao.getCompany(1);

	assertEquals("Benjo", company.getName());
}
```

This is `getCompany()`’s logic:

```java
public Company getCompany(int id) {
	logger.info("Getting company with id = " + id);
	Company company = getSimpleJdbcTemplate().queryForObject(
			"SELECT id, name FROM companies WHERE id = ?",
			new CompanyMapper(), id);
	return company;
}
```

A few days later, I got a chance to talk to my mentor. I described the problem to him as best as I can and I showed him my code as well. After a few minutes of discussion, he finally figured out where the problem was coming from.

Apparently, the problem had something to do with how I created the table in the database:

```sql
CREATE TABLE companies (
	id INTEGER NOT NULL AUTO_INCREMENT PRIMARY KEY,
	name varchar(100) NOT NULL
);
```

The problem had something to do with the id field being an auto-incrementing field, but this wasn’t actually the root of the problem. There’s nothing wrong with using auto-incrementing fields. The problem was caused by how I insert values into the database:

```sql
INSERT INTO companies (name) VALUES ('Benjo');
INSERT INTO companies (name) VALUES ('Lasam');
INSERT INTO companies (name) VALUES ('Juffy');
```

The reason why my unit test retrieves an empty result set is because I didn’t hard code the value for id when inserting a value into the database. That’s why it can’t find “Benjo”, which supposedly should have an id of 1. After realizing that, I changed the query into this:

```sql
INSERT INTO companies (id, name) VALUES (1, 'Benjo');
INSERT INTO companies (id, name) VALUES (2, 'Lasam');
INSERT INTO companies (id, name) VALUES (3, 'Juffy');
```

For some reason, the previous query doesn’t assign the value of id as 1 when I let the auto-increment feature do the assigning for me. My mentor says it had something to do with the internal counter of the auto-increment feature. Honestly, I didn’t understand why that was. I don’t know how MySQL works internally. If you ask me, I don’t see anything wrong with the previous queries that I used.

Either way, my problem’s resolved already. As of now, I still can’t understand the reason behind the problem. Hopefully, I’ll be able to know about it in the future. At least I know how to deal with this kind of problem now.
