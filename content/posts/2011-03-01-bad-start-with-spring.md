---
title: "Bad start with Spring"
date: 2011-03-01
draft: false
toc: false
images:
tags:
  - programming
  - java
---

Recently, one of my good friends asked me to make a simple web application for them. Since I’ve been wanting to learn Spring for quite some time now, I decided to take on his request because I’m such a good person. Just kidding, I really just wanted to have an excuse to learn Spring.

After a few minutes of Google searching, I came across the [official tutorial for Spring MVC](http://static.springsource.org/docs/Spring-MVC-step-by-step/). I noticed that the tutorial was a bit outdated and had a few errors, but I still managed to finish the tutorial in spite of that.

Now that I already did all the necessary stuff to make a simple web application in Spring, I decided to add a bit more to get started with the project that my friend asked me to do. The first thing that I tried to do was learn how to do CRUD operations in Spring. I started by making a Java bean called company:

```java
package gpspeedometer.domain;

import java.io.Serializable;

public class Company implements Serializable {
	private int id;
	private String name;

	public Company() {

	}

	public Company(String name) {
		setName(name);
	}

	public Company(Company company) {
		this(company.getName());
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

	@Override
	public String toString() {
		StringBuffer buffer = new StringBuffer();
		buffer.append("Name: " + name);
		return buffer.toString();
	}

	@Override
	public boolean equals(Object object) {
		if (object != null && object instanceof Company) {
			Company company = (Company) object;
			if (company.getId() == getId() && company.getName().equals(getName())) {
				return true;
			}
		}
		return false;
	}

	@Override
	public int hashCode() {
		return getId() ^ getName().hashCode();
	}
}
```

I’m not sure if I’ve overridden `hashCode()` and `equals()` right, but that’s not really the issue here. Since the tutorial was all about TDD, I decided to make a unit test for the bean that I just created:

```java
package gpspeedometer.domain;

import gpspeedometer.domain.Company;

import junit.framework.TestCase;

public class CompanyTests extends TestCase {
	private Company company;

	protected void setUp() throws Exception {
		company = new Company();
	}

	public void testGetAndSetName() {
		String testName = "random name";
		assertNull(company.getName());
		company.setName(testName);
		assertEquals(testName, company.getName());
	}

	public void testOverrideToString() {
		String testToString = "Name: Random";
		company.setName("Random");
		assertEquals(testToString, company.toString());
	}

	public void testOverrideEquals() {
		company.setId(1);
		company.setName("Lasam");
		Company company2 = new Company();
		company2.setId(1);
		company2.setName("Lasam");
		assertEquals(company.equals(company2), true);
	}

	public void testOverrideHashCode() {
		company.setId(1);
		company.setName("Lasam");
		Company company2 = new Company();
		company2.setId(1);
		company2.setName("Lasam");
		assertEquals(company.hashCode(), company2.hashCode());
	}
}
```

Of course, a test as simple as this would most likely pass. Just kidding. To be honest, I’m not really sure about the tests on hashCode and equals, but again, that’s not the issue here. After making the bean, I made an interface to do handle the bean services:

```java
package gpspeedometer.service;

import java.io.Serializable;
import java.util.List;

import gpspeedometer.domain.Company;
import gpspeedometer.repository.CompanyDao;

public interface CompanyManager extends Serializable {

	public List<Company> getCompanies();

	public void setCompanies(List<Company> companies);

	public void setCompanyDao(CompanyDao companyDao);
}
```

I then made an implementation class for the interface that I just made:

```java
package gpspeedometer.service;

import java.util.ArrayList;
import java.util.List;

import gpspeedometer.domain.Company;
import gpspeedometer.repository.CompanyDao;

public class SimpleCompanyManager implements CompanyManager {
	private CompanyDao companyDao;
	private List<Company> companies;

	public List<Company> getCompanies() {
		return companyDao.getCompanyList();
	}

	public void setCompanies(List<Company> companies) {
		this.companies = companies;
	}

	public void setCompanyDao(CompanyDao companyDao) {
		this.companyDao = companyDao;
	}
}
```

After that, I made a unit test for the class that I just made:

```java
package gpspeedometer.service;

import java.util.ArrayList;
import java.util.List;

import gpspeedometer.domain.Company;
import gpspeedometer.repository.CompanyDao;
import gpspeedometer.repository.InMemoryCompanyDao;

import junit.framework.TestCase;

public class SimpleCompanyManagerTests extends TestCase {
	private SimpleCompanyManager companyManager;
	private List<Company> companies;

	private static int COMPANY_COUNT = 2;

	private static String COMPANY_ONE = "Bus Company #1";
	private static String COMPANY_TWO = "Bus Company #2";

	protected void setUp() throws Exception {
		companyManager = new SimpleCompanyManager();
		companies = new ArrayList<Company>();

		Company company = new Company();
		company.setName("Bus Company #1");
		companies.add(company);

		company = new Company();
		company.setName("Bus Company #2");
		companies.add(company);

		CompanyDao companyDao = new InMemoryCompanyDao(companies);
		companyManager.setCompanyDao(companyDao);
	}

	public void testGetCompaniesWithNoCompanies() {
		companyManager = new SimpleCompanyManager();
		companyManager.setCompanyDao(new InMemoryCompanyDao(null));
		assertNull(companyManager.getCompanies());
	}

	public void testGetCompanies() {
		List<Company> companies = companyManager.getCompanies();
		assertNotNull(companies);
		assertEquals(COMPANY_COUNT, companyManager.getCompanies().size());

		Company company = companies.get(0);
		assertEquals(COMPANY_ONE, company.getName());

		company = companies.get(1);
		assertEquals(COMPANY_TWO, company.getName());
	}
}
```

Well, hey, the tests passed again. Lucky me. At this point, I’m pretty sure you’re already bored reading this entry. I wouldn’t be surprised if you skipped over what I said over here. I’m feeling sad about this, so bear with me for a while. Anyway, let’s move on to the database part of the program. Again, I made another interface to handle the DAO of Company:

```java
package gpspeedometer.repository;

import java.util.List;

import gpspeedometer.domain.Company;

public interface CompanyDao {
	public List<Company> getCompanyList();

	public Company getCompany(int id);

	public void addCompany(Company company);

	public void editCompany(Company company);
}
```

I was on a roll until this happened. I made the implementation class for CompanyDao:

```java
package gpspeedometer.repository;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;

import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
import org.springframework.jdbc.core.simple.ParameterizedRowMapper;
import org.springframework.jdbc.core.simple.SimpleJdbcDaoSupport;

import gpspeedometer.domain.Company;

public class JdbcCompanyDao extends SimpleJdbcDaoSupport implements CompanyDao {
	protected final Log logger = LogFactory.getLog(getClass());

	public List<Company> getCompanyList() {
		logger.info("Getting companies.");
		List<Company> companies = getSimpleJdbcTemplate().query(
				"SELECT id, name FROM companies",
				new CompanyMapper());
		return companies;
	}

	public Company getCompany(int id) {
		logger.info("Getting company with id = " + id);
		Company company = getSimpleJdbcTemplate().queryForObject(
				"SELECT id, name FROM companies WHERE id = ?",
				new CompanyMapper(), id);
		return company;
	}

	public void addCompany(Company company) {
		logger.info("Inserting company: " + company.getName());
		int count = getSimpleJdbcTemplate().update(
				"INSERT INTO companies (name) VALUES (:name)",
				new MapSqlParameterSource()
					.addValue("name", company.getName()));
		logger.info("Rows affected: " + count);
	}

	public void editCompany(Company company) {
		logger.info("Editing company: " + company.getName());
		int count = getSimpleJdbcTemplate().update(
				"UPDATE companies SET name = :name WHERE id = :id",
				new MapSqlParameterSource()
					.addValue("name", company.getName())
					.addValue("id", company.getId()));
		logger.info("Rows affected: " + count);
	}

	private static class CompanyMapper implements ParameterizedRowMapper<Company> {
		public Company mapRow(ResultSet rs, int rowNum) throws SQLException {
			Company company = new Company();
			company.setId(rs.getInt("id"));
			company.setName(rs.getString("name"));
			return company;
		}
	}
}
```

If you look at the code at first glance, you wouldn’t see anything wrong with it. Unfortunately, that wasn’t the case. I learned that from the unit test that I made for the previous class:

```java
package gpspeedometer.repository;

import java.util.List;

import org.springframework.test.AbstractTransactionalDataSourceSpringContextTests;

import gpspeedometer.domain.Company;

public class JdbcCompanyDaoTests extends AbstractTransactionalDataSourceSpringContextTests {
	private CompanyDao companyDao;

	public void setCompanyDao(CompanyDao companyDao) {
		this.companyDao = companyDao;
	}

	@Override
	protected String[] getConfigLocations() {
		return new String[] {"classpath:test-context.xml"};
	}

	@Override
	protected void onSetUpInTransaction() throws Exception {
		super.deleteFromTables(new String[] {"companies"});
		super.executeSqlScript("file:db/load_data.sql", true);
	}

	public void testGetCompanyList() {
		List<Company> companies = companyDao.getCompanyList();

		assertEquals("Wrong number of companies?", 3, companies.size());
	}

	public void testGetCompany() {
		Company company = companyDao.getCompany(1);

		assertEquals("Benjo", company.getName());
	}

	public void testAddCompany() {
		Company company = new Company();
		company.setName("Ponce Inc.");
		companyDao.addCompany(company);

		company = companyDao.getCompany(4);
		assertEquals("Wrong name of company?", "Ponce Inc.", company.getName());
	}

	public void testEditCompany() {
		List<Company> companies = companyDao.getCompanyList();

		for (Company company : companies) {
			company.setName("Lasam Inc.");
			companyDao.editCompany(company);
		}

		List<Company> updatedCompanies = companyDao.getCompanyList();
		for (Company company : updatedCompanies) {
			assertEquals("Wrong name of company?", "Lasam Inc.", company.getName());
		}
	}
}
```

This test, testGetCompany is failing. Here’s the error that I got from JUnit in Ant:

```shell
[junit] Testcase: testAddCompany(gpspeedometer.repository.JdbcCompanyDaoTests): Caused an ERROR
[junit] Incorrect result size: expected 1, actual 0
[junit] org.springframework.dao.EmptyResultDataAccessException: Incorrect result size: expected 1, actual 0
[junit] at org.springframework.dao.support.DataAccessUtils.requiredSingleResult(DataAccessUtils.java:71)
[junit] at org.springframework.jdbc.core.JdbcTemplate.queryForObject(JdbcTemplate.java:722)
[junit] at org.springframework.jdbc.core.simple.SimpleJdbcTemplate.queryForObject(SimpleJdbcTemplate.java:169)
[junit] at gpspeedometer.repository.JdbcCompanyDao.getCompany(JdbcCompanyDao.java:29)
[junit] at gpspeedometer.repository.JdbcCompanyDaoTests.testAddCompany(JdbcCompanyDaoTests.java:44)
[junit] at org.springframework.test.ConditionalTestCase.runBare(ConditionalTestCase.java:76)
[junit]
[junit]
[junit] Test gpspeedometer.repository.JdbcCompanyDaoTests FAILED
```

For some reason, the query returns nothing, which results in an empty result set. The development came to a screeching halt the moment this error appeared. The reason why this sucked is because I’m still working my way around Spring and how it works. I haven’t got a clue as to how this happened since I don’t really know what’s happening behind the scenes with my code. Let’s take a look at the faulty methods here:

```java
public Company getCompany(int id) {
	logger.info("Getting company with id = " + id);
	Company company = getSimpleJdbcTemplate().queryForObject(
			"SELECT id, name FROM companies WHERE id = ?",
			new CompanyMapper(), id);
	return company;
}
```

```java
public void testGetCompany() {
	Company company = companyDao.getCompany(1);

	assertEquals("Benjo", company.getName());
}
```

On my database, there’s a table called company that has “Benjo” as its name on the first row, so I checked if the test data really made it into the database:

```java
@Override
protected void onSetUpInTransaction() throws Exception {
	super.deleteFromTables(new String[] {"companies"});
	super.executeSqlScript("file:db/load_data.sql", true);
}
```

The method loads the sql file called load_data before starting the tests. Here are its contents:

```sql
INSERT INTO companies (name) VALUES ('Benjo');
INSERT INTO companies (name) VALUES ('Lasam');
INSERT INTO companies (name) VALUES ('Juffy');
```

If this is the case, the method, getCompany(), should be able to retrieve a row from the database, but unfortunately, that didn’t happen. It always returned an empty result set.

Since I couldn’t handle the problem by myself anymore, I turned to [StackOverflow](http://stackoverflow.com/) for answers. My problem is still vague for me, so I decided to ask [a very generic question](http://stackoverflow.com/q/5102514/395972) about my problem. My question didn’t receive any good feedback. All I got was a guy asking me why I was still using Spring 2.5 instead of 3.0, which was out of the question. Honestly, I don’t even know how that is relevant to my problem. I’m pretty sure migrating to 3.0 won’t solve my problem since I’ll most likely be writing the same code if that happens.

Anyway, since I still haven’t received any help from anyone, and I’m still clueless as to how Spring works, I decided to blog about it right now. This really made me sad because I can’t even do simple CRUD operations. What more if I needed to do something more complex? Hopefully, someone will be patient enough to read this and help me with my problem.
