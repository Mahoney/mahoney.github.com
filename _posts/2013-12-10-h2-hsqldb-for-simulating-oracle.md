---
layout: post
title: H2 & HSQLDB for Simulating Oracle
date: '2013-12-10T16:22:00.001Z'
author: Robert Elliot
tags: 
modified_time: '2014-01-16T13:43:11.107Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-3762527646654832426
blogger_orig_url: http://blog.lidalia.org.uk/2013/12/h2-hsqldb-for-simulating-oracle.html
---

H2 & HSQLDB are two Java in-memory databases. They both offer a degree of support for simulating an Oracle database in your tests. This post describes the pros and cons of each.

## H2
### How to setup:

```java
import org.h2.Driver;
import javax.sql.DataSource;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

Driver.load();
DataSource dataSource = new DriverManagerDataSource(
    "jdbc:h2:mem:MYDBNAME;MVCC=true;DB_CLOSE_DELAY=-1;MODE=Oracle",
    "sa",
    "");

```

DB_CLOSE_DELAY is vital here or the database is deleted whenever the number of connections drops to zero - a highly unintuitive situation.

### Pros:
In general I've found I had to make fewer compromises on my SQL syntax in general and my DDL syntax in particular using H2's Oracle compatibility mode. For instance it supports sequences and making the default value of a column a select from a sequence, which HSQLDB does not.

### Cons:
The transaction capabilities are not as good as HSQLDB. Specifically, if you use MVCC=true in the connection string then H2 does not support a transaction isolation of serializable, only read committed. If you do not set MVCC=true then a transaction isolation of serializable does work but only by doing a full table lock, which is not at all how Oracle does it.

## HSQLDB
### How to setup:
```java
import org.hsqldb.jdbc.JDBCDriver;
import javax.sql.DataSource;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

JDBCDriver.class.getName();
DataSource dataSource = new DriverManagerDataSource(
    "jdbc:hsqldb:mem:MYDBNAME",
    "sa",
    "")
JdbcTemplate&nbsp; jdbcTemplate = new JdbcTemplate(dataSource)
jdbcTemplate.execute("set database sql syntax ORA TRUE;");
jdbcTemplate.execute("set database transaction control MVCC;");

```


### Pros:
MVCC with a transaction isolation of serializable works as expected - other transactions can continue to write whilst a transaction sees only the state of the DB when it started.

### Cons:
Support for Oracle syntax, particularly in DDL, is patchy - I was unable to run the following, which works fine in Oracle:
```sql
CREATE SEQUENCE SQ_TABLE_A;
CREATE TABLE TABLE_A (
  ID NUMBER(22,0) NOT NULL DEFAULT (SELECT SQ_TABLE_A.NEXTVAL from DUAL),
  SOME_DATA NUMBER(22,0) NOT NULL,
  TSTAMP TIMESTAMP(3) DEFAULT CURRENT_TIMESTAMP);

```
