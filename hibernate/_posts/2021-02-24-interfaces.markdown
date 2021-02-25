---
layout: post
title:  "hibernate"
date:   2021-02-24 18:52:34 +0900
category: hibernate
---
# HIBERNATE

## HIBERNATE?
- Hibernate is an Object-Relational mapping (ORM) tool used to map java objects(POJO) and database tables.
- we can use JPA annotations by xml configurations 

## WHY HIBERNATE?
- we can reduce boiler-plate code when using JDBC
- we can use HQL so reach more OOP paradigm
- it provides transaction management inside of core
- when we get an error in runtime, Hibernate throws JDBC Exception or HibernateException, so there is no need to using try and catch
- hibernate using cache module for data incomming from database, so it can be better performance

## HIBERNATE INTERFACE
- Session Factory(org.hibernate.SessionFactory) - initialize once and it cached by one instances, it retrieve Session object for using database operations. it's one object per database connection
- Session(org.hibernate.Session) - using for connection that physically tasking CRUD operations between hibernate framework and database. also making number of transaction units, in other words transaction factory.
- Transaction(org.hibernate.Transaction) - atomic units of work

```java
SessionFactory factory = config.getSessionFactoryBuilder().build();
Session session = factory.openSession();
Transaction t = session.beginTransaction();
session.save(persistanceObj);
t.commit();
```

## Imp Annotations used in Hibernate
- javax.persistence.Entity: Used in domain layer, declare this class are entity beans
- javax.persistence.Table: this entity beans will mapping with db table name
- javax.persistence.Access: Hibernate can using getter setter method of this entity bean.
- javax.persistance.Id: you can declare at field. target of field matches primary key of db tables
- javax.persistance.Column: you can define custom table column name instead variable name. usually automatically using variable name;
