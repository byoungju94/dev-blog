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
        <property name="password"></property>
    </class>
</hibernate-mapping>
```

- filename should be java_class_name.hbm.xml
- hibernate-mapping: root element
- class: define class using entity bean
- id: using this field for table of primary key
- generator: there are different ways to create auto primary key.

## flow of task to create hibernate app
1. create pojo object for mapping table
2. create mapping files
3. create configuration file
4. storing the data to pojo retrieving from database.

## Difference between openSession and getCurrentSession
- getCurretionSession() method returns the session bound to the context.
- Since this session object belongs to the context of Hibernate, it is okay if you don't close it.
- Once the sessionFactory is closed, this session object gets closed.
- openSession() method helps in opening a new session
- You should close this session object once you are done with all the database operations. And also, you should open a new session for each request in a multi-threaded enviromnet.

## Difference between Session get() and load() method
- get() loads the data as soon as it's called whereas load() returns a proxy object and loads data only when it's actually required, so load() is better because it support lazy loading.
- Since load() throws exception when data is not found, we should use it only when we know data exist.
- we should use get() when we want to make sure data exists in the database

## hibernate caching - Types
- there are three types of caching in hibernate. First Level Cache, Second level Cache, Query Cache.

## hibernate caching - first level cache
- hibernate caches query data to make our application faster and improves performance.
- Hibernate first level cache is associated with the Session Object.
- Hibernate first level cache is enabled by default and there is no way to disable it.
- Still hibernate provides methods through which we can delete selected objects from the cache or clear the cache completely.     
- any object cached in a session will not be visible to other sessions and when the session is closed, all the cached objects will also be lost.

## hibernate caching - second level cache
- Hibernate Second Level cache is disabled by default but we can enabled it through configuration
- Currently EHCache and Infinispan provides implementation for Hibernate Second level cache and we can use them.

## configure Hibernate Second Level Cache
- add hibernate-ehcache dependency in your maven project

```xml
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-ehcache</artifactId>
</dependency>
```

- add below properties in hibernate configuration file.

```xml
<property name="hibernate.cache.region.factory_class">org.hibernate.cache.ehcache.EhCacheRegionFactory</property>
<property name="hibernate.cache.use_second_level_cache">true</property>
<property name="hibernate.cache.use_query_cache">true</property>
<property name="net.sf.ehcache.configurationResourceName">/doublejehcache.xml</property>
```

- create EHCahce configuration file.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
    xsi:noNamespaceSchemaLocation="ehcache.xsd" updateCheck="true"
    monitoring="autodetect" dynamicConfig="true">

    <diskStore path="java.io.tmpdir/ehcache" />

    <defaultCache maxEntriesLocalHeap="10000" eternal="false" timeToIdleSecondes="120" timeToLiveSeconds="120"
        diskSpoolBufferSizeMB="30" maxEntriesLocalDisk="10000000" diskExpiryThreadIntervalSeconds="120"
        memoryStoreEvictionPolicy="LRU" statistics="true">
        <persistence strategy="local_TempSwap" />
    </defaultCache>

    <cache name="member" maxEntriesLocalHeap="10000" eternal="false" timeToIdleSeconds="5" timeToLiveSeconds="10">
        <persistence strategy="localTempSwap" />
    </cache>

    <cache name="org.hibernate.cache.internal.StandardQueryCache" maxEntriesLocalHeap="5" eternal="false" timeToLiveSeconds="120">
        <persistence strategy="localTempSwap" />
    </cache>

    <cache name="org.hibernate.cache.spi.UpdateTimestampsCache" maxEntriesLocalHeap="5000" eternal="true">
        <persistence strategy="localTempSwap" />
    </cache>

</ehcache>
```

- diskStore means, if my cache overflows, cache will store to path location.
- default cache means, the caches which has no specific region, will follow defaultCache configuration.
- you can customize caching strategy for some different cache object.
- we can also cached org.hibernate.cache.internal.StandardQueryCache and org.hibernate.cache.spi.UpdateTimestampsCache

<br />
- Annotate entity beans with @Cache annotation and choose strategy.

```java
import org.hibernate.annotations.Cache;
import org.hibernate.annotations.CacheConcurrenctStrategy;

@Entity
@Table(name = "tbl_member")
@Cache(usage = CacheConcurrencyStrategy.READ_ONLY, region="member")
public class Member {
    
}
```

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

### Nowadays
- even though you're using mysql, hibernate choose sequence strategy using the table to emulate sequence for default strategy.

```java
@Entity
@Table
public class Account {
    
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String name;
}
```

- hibernate generated these tables.

```sql
create table hibernate_sequence (
    next_val bigint
) engine=MyISAM

create table account (
    id bigint not null,
    firstName varchar(255),
    lastName varchar(255),
    primary key (id)
) engine=MyISAM
```

- we can add this property in persistence.xml not using sequence strategy when declare auto annotation

```xml
<persistence ...>
    <persistence-unit name="..">
        <properties>
            <property name="hibernate.id.new_generator_mappings" value="false"></property>
        </properties>
    </persistence-unit>
</persistence>
```

### @Column
```java
@Entity
@Table
public class Account {

    @Id
    @GenerateValue(strategy = GenerationType.AUTO)
    @Column(name = "account_id")
    private Long id;

    @Column(name = "user_name", length = 50)
    private String username;

    // wrong, max key length is 1000 bytes
    // @Column(unique = true, name = "phone_number")
    @Column(unique = true, name = "phone_number",  length = 250)
    private String phone;
}
```
- we can customize column name to declare annotation at field.
- hibernate generate column name to field name if there is no custom settings.
- when we use unique constraint to specific column without any configuration of length, we can see

```
Caused by: java.sql.SQLSyntaxErrorException: Specified key was too long; max key length is 1000 bytes
``` 

- that's because in mysql, column which get unique or indexing constraints, the maximum length should be 1000 bytes.
- but hibernate, by default, type of String can be 255 characters, one character size is 4 bytes, and it's over then 1000 bytes.
- so we customize length that doesn't overflow 1000 bytes when using unique or index constraints.

<br />
and there is another settings below.

```java
@Target({METHOD, FIELD}) 
@Retention(RUNTIME)
public @interface Column {

    /**
     * (Optional) The name of the column. Defaults to 
     * the property or field name.
     */
    String name() default "";

    /**
     * (Optional) Whether the column is a unique key.  This is a 
     * shortcut for the <code>UniqueConstraint</code> annotation at the table 
     * level and is useful for when the unique key constraint 
     * corresponds to only a single column. This constraint applies 
     * in addition to any constraint entailed by primary key mapping and 
     * to constraints specified at the table level.
     */
    boolean unique() default false;

    /**
     * (Optional) Whether the database column is nullable.
     */
    boolean nullable() default true;

    /**
     * (Optional) Whether the column is included in SQL INSERT 
     * statements generated by the persistence provider.
     */
    boolean insertable() default true;

    /**
     * (Optional) Whether the column is included in SQL UPDATE 
     * statements generated by the persistence provider.
     */
    boolean updatable() default true;

    /**
     * (Optional) The SQL fragment that is used when 
     * generating the DDL for the column.
     * <p> Defaults to the generated SQL to create a
     * column of the inferred type.
     */
    String columnDefinition() default "";

    /**
     * (Optional) The name of the table that contains the column. 
     * If absent the column is assumed to be in the primary table.
     */
    String table() default "";

    /**
     * (Optional) The column length. (Applies only if a
     * string-valued column is used.)
     */
    int length() default 255;

    /**
     * (Optional) The precision for a decimal (exact numeric) 
     * column. (Applies only if a decimal column is used.)
     * Value must be set by developer if used when generating 
     * the DDL for the column.
     */
    int precision() default 0;

    /**
     * (Optional) The scale for a decimal (exact numeric) column. 
     * (Applies only if a decimal column is used.)
     */
    int scale() default 0;
}
```

- table: we can have secondary table using these property.
- precision: we can store type of decimal data which numbers after dots.
- scale: using the scale property for exact numeric columns.

<br />

### @Temporal
in database, there are many kinds of different type for store time data like DATETIME, TIME(), TIMESTAMP()

<br />

before java 7, we using java.util.Date or java.util.Calander for time data. if we store current time info in database table, we should get current time using java.util.Date, and java.util.Date returns like '2021-03-03' and it store to databse changed to '2021-03-03 00:00:00'. it stored as timestamp in the database. because date type represented date and time. but in some cases, if you don't want to store the time, not date, you can use @temporal

<br />

```java
@Entity
@Table
public class Account {
    
    @Id
    private Long id;
    private String username;

    // before java 7
    @Temporal(TemporalType.DATE)
    private java.util.Date birthDate;

    // after java 8
    // mysql date type
    private LocalDate birthDate;

    // after java 8
    // mysql time type
    private LocalDateTime birthDate2;
}
EntityManagerFactory factory = Persistence.createEntityManagerFactory("me.byoungju94");
EntityManager em = factory.createEntityManager();
EntityTranscation transcation = em.getTransaction();

transaction.begin();

Account account = new Account(id, username, new Date(), LocalDate.of(2021, 03, 04));
em.persist(account);

transcation.commit();
em.close()
```

using TemporalType.DATE annotation, birthDate will store like 2021-03-04 without hour, minute, second.
since java 7, java add new apis for the date. java recommends don't using java.util.Date because it's not immutable, instead using java.time.LocalDate. LocalDate automatically map to the date type in database column.
also LocalDateTime will map to time data type in database column.













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