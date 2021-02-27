---
layout: post
title:  "hibernate"
date:   2021-02-24 18:52:34 +0900
category: hibernate
---
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
- javax.persistance.GeneratedValue: choose strategy of generating auto primary key.
- javax.persistsance.OneToOne: relationship between each other two entity beans by one-to-one mapping, there are more relationship such as OneToMany, ManyToOne and ManyToMany
- org.hibernate.annotations.Cascade: choose cascade stategy between two entity beans. each other different types define in org.hibernate.annotations.CascadeType
- javax.persistance.PrimaryKeyJoinColumn: define the property for foreign key using org.hibernate.annotations.GenericGenerator, org.hibernate.annotations.Parameter

## 3 types of assosication mappings in hibernate
- OneToOne
- ManyToOne
- ManyToMany

## OneToOne
```java
public class Member {
    
    private Long id;
    private String username;
    private String password;
    private String phone;

    @OneToOne(targetEntity = BodyInfo.class)
    private BodyInfo bodyInfo;
}

public class BodyInfo {
    
    private Long id;
    private String height;
    private String weight;
    private String vision;
}

```

## ManyToOne
```java
public class Member {
    
    private Long id;
    private String username;
    private String phone;
    
    @ManyToOne(cascade = CascadeType.ALL)
    private School school;
}
```

## ManyToMany
```java
public class Member {
    
    private Long id;
    private String username;
    private String phone;

    @ManyToMany(targetEntity = Car.class, cascade = { CascadeType.ALL })
    @JoinTable(name = "MemberCar",
                joinColumns = {@JoinColumn(name = "memberId")}),
                inverseJoinColumns = {@JoinColumn(name = "carId")}
    private List<Car> carList;
}
```
One Member can own multiple cars, also single car type has multiple owner. many-to-many relationship create new table that includes two primary key of each entity beans. in @JoinTable, the joinColumns property points one entity beans of primary key, and inverseJoinColumn points the other entity beans' primary key.

## setting hibernate configuration by xml format
```xml
<?xml version='1.0' encoding='UTF=8'?>
<!DOCTYPE hibernate-configuration PUBLIC
    "-//Hibernate/Hibernate Configuration DTD 5.3//EN"
    "http://hibernate.sourceforge.net/hibernate-configuration-5.3.dtd">
<hibernate-configuration>
    <session-factory>
        <property name="hbm2ddl.auto">update</property>
        <property name="dialect">org.hibernate.dialect.MySQL5Dialect</property>
        <property name="connection.url">jdbc:mysql://localhost:3306/family_db?useSSL=false&characterEncoding=UTF-8&serverTimezone=UTC</property>
        <property name="connection.username">byoungju94</property>
        <property name="connection.password">secret1234</property>
        <property name="connection.driver_class">com.mysql.cj.jdbc.Driver</property>

        <mapping resource="member.hbm.xml" />
    </session-factory>
</hibernate-configuration>
```
- session factory per one database, if there is another datasource, you have to define another session factory.
- this file name is hibernate.cfg.xml
- initialize SessionFactory by configurations of specific database.

## hibernate mapping file
```xml
<?xml version='1.0' encoding='UTF-8'?>
<!DOCTYPE hibernate-mapping PUBLIC 
    "-//Hibernate/Hibernate Mapping DTD 5.3//EN"
    "http://hibernate.sourceforge.net/hibernate-mapping-5.3dtd>
<hibernate-mapping>
    <class name="me.byoungju94.app.Member" table="tbl_member">
        <id name="id">
            <generator class="identity">
        </id>
        <property name="username"></property>
        <property name="lastName"></property>
    </class>
</hibernate-mapping>
```
- filename should be java_class_name.hbm.xml
- hibernate-mapping: root element
- class: define class using entity bean
- id: using this field for table of primary key
- generator: there are different ways to create auto primary key.

## flow of task to create hibernate app
1. create entity beans(pojo)
2. create mapping files
3. create configuration file
4. create or get data from db and mapping with entity bean 

## Difference between openSession and getCurrentSession

## Primary key in Hibernate
### Natural key
```java
@Entity
public class Member {
    
    @Id
    private String ssn;
}
```
- this key has already made from real world. it is really meaningful key and database just stored in specific column.

### Surrogate key
```java
@Entity
public class Member {
    
    @Id
    private Long id;
}
```
- if you using surrogate key, it just a number, it doesn't have any meaning in Member domain model.
- database generate new key which is number automatically.
- domain model, type of different all of exists in world, surrogate key can be consistent.
- using reference type for surrogate key is better than primitive type.
- because primitive type has default value zero, not null. so hibernate can be confused that object is really exists.

## Generation Strategies
if we decide using surrogate key, there is no need to assign primary key manually, we can ask for hibernate to automatically generate those keys. in hibernate, there are four different generation strategies of primary key. it just ask database to automatically generate keys, not generate keys in hibernate. we can using this feature in javax.persistance.GeneratedValue and javax.persistance.GenerationType.

### javax.persistence.GenerationType.IDENTITY
```java
@Entity
public class Member {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
}
```
- it supported database like mysql, sql server and etc.
- in mysql, when using AUTO_INCREMENT option at column in tables, this column effect unique only it's table.
- tables has own primary key each other, not using primary key globally or shared.

### javax.persistence.GenerationType.SEQUENCE
```java
@Entity
public class Member {
    
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private Long id;
}
```
- this startegy does not support all kinds of database. it supports oracle, h2sql.
- multiple tables in one database, they shared primary key.
- there is some benefits using sequence instead identity. if there is a situation that merge two different table consists of identity primary key, it will be conflict and hard to merge thoes tables, but consist of sequence primary key, it is easy to merge tables.  
<br />
    ```java
    Member member = new Member("byoungju94", "password1");
    Location location = new Location("seoul", "gangdongju");
    em.persist(member);
    em.persist(location):
    ```
- when using identity primary key, it is slower than sequence type, insert row performance.
- identity primary key ask to member table what is the last primary key, and ask to location table twice.
- but sequence primary key can ask only global sequence table. so it can faster insert performance than identity primary key.


## Using JPA.i.e Hibernate EntityManager instead Hibernate's property API
```xml
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" version="2.2"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name="edu.mum.cs">
        <description>
            Persistence unit for Hibernate
        </description>

        <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>

        <properties>
            <property name="packagesToScan" value="edu.mum.cs.domain"/>
            <property name="javax.persistence.jdbc.url" value="jdbc:mysql://localhost:3306/hibernate01?useSSL=false"/>
            <property name="javax.persistence.jdbc.driver" value="com.mysql.cj.jdbc.Driver"/>
            <property name="javax.persistence.jdbc.user" value="root"/>
            <property name="javax.persistence.jdbc.password" value="1234"/>
            <property name="javax.persistence.schema-generation.database.action" value="drop-and-create"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.MySQL5Dialect"/>
            <property name="hibernate.cache.provider_class" value="org.hibernate.cache.NoCacheRegionFactoryAvailableException"/>
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.format_sql" value="true"/>
        </properties>

    </persistence-unit>
</persistence>
```