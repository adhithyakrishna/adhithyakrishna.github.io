---
title: "Preface"
date: 2023-10-21 T 12:22:16-1697916136.744
draft: false
enableToc: true
meta_image: tech/high_performance_java_persistence/hpjp_horizontal.jpg
image: tech/high_performance_java_persistence/hpjp_horizontal.jpg
weight : 4
---
{{< featuredImage >}}
This notes is for the book - [High performance java persistence](https://vladmihalcea.com/books/high-performance-java-persistence/)

_More than half of the application performance bottlenecks originate in the database_

To get the most out of a persistence layer, the data access logic must resonate with underlying database system.

{{< img src=/tech/high_performance_java_persistence/preface/data_access_skill_stack.png title="High performance java persistence" caption="Data access skill stack" alt="Data access skill stack" width="700px" position="center" >}}

#### The database server and the connectivity layer
Getting familiar with SQL standard and database specific feature can make the difference. The fear of database portability can lead to avoiding highly effective features and lead to a sluggish database layer. All data access frameworks rely on JDBC API for communicating a database server. 

JDBC offers performance optimizations techniques aiming to reduce transaction response time and accommodate more traffic.

#### The application data access layer
There are data access patterns that have proven their effectiveness in many enterprise application scenarios.

##### The ORM framework

Beside the ORM pattern, the ORM frameworks also employ techniques such as
1. Unit of work
2. Identity Map
3. Lazy Loading
4. Embedded Value
5. Entity inheritance
6. Optimistic and Pessimistic locking

###### Unit of work
In Hibernate ORM, a unit of work is a design pattern that keeps track of all changes made to objects during a business transaction. At the end of the transaction, the unit of work sends the changes to the database in a single batch. This reduced the number of database round trips and increase the efficiency of the application. 

###### Identity map
In Hibernate ORM, the Identity Map is an identity hash map that maps the primary key of an object to the object itself. This is used to ensure that each object is only loaded from the database once per session. The Identity Map is also used to compare objects for **equality**, using the object's **primary key** instead of its identity hash code.

In Java, the Object class's identityHashCode() method returns an integer that is the identity hash code of the object. The identity hash code is a unique identifier for an object that is _not affected_ by the object's state. 

###### Lazy Loading
Lazy loading in Hibernate is a technique that delays the loading of associated objects until they are actually needed. This can improve performance by reducing the number of queries that are executed against the database. Ensure that associated objects are unloaded when you are finished with them. If you do not unload the objects, you can increase memory usage.

###### Embeded Value
The @Embeddable and @Embedded annotations in Hibernate are used to map an object’s properties to columns in a database table. These annotations are used in combination to allow the properties of one class to be included as a value type in another class and then be persisted in the database as part of the containing class. [[1]](https://www.geeksforgeeks.org/hibernate-embeddable-and-embedded-annotation/)

###### Entity Inheritance
Entity inheritance in Hibernate is a feature that allows you to inherit from a parent entity class to a child entity class. This can be useful when you have a common set of properties that you want to share between multiple entities.

###### Optimistic and Pessimistic locking
*   Optimistic locking

    A record is locked only when changes are committed to the database. Optimistic locking is used to avoid concurrent changes on the same data. It allows and detects changes happening at the same time.

*   Pessimistic locking

    A record is locked while it is edited. Pessimistic locking is used to prevent concurrent changes.

Optimistic locking is more efficient when update collisions are expected to be infrequent. Pessimistic locking is more efficient when update collisions are expected to occur often.


ORM tools can boost application development speed. The only way to address the inherent complexity of bridging relational data with application domain model is to full understand the ORM framework in use.

Reading the source code is inevitable when facing performance related problems. 

**JPA** excels in writing data because all DML (**Data manipulation language**) statements are automatically updates whenever the _persistence model changes_ therefore speeding up the iterative development process.

##### The native query builedr framework
Using native SQL is unavoidable in any non-trivial enterprise application. JAP makes it possible to abstract DML statements and common entity retrival queries, but when it comes to reading and processing data, nothing can beat native SQL.

* _JPQL_ abstracts the common SQL syntax by subtracting database specific querying features, so it lacks support for database specific querying features. (JPQL is the query language defined by JPA)

* _JOOQ_ embraces database specific query features and provide type-safety query builder which can protect against SQL injection attacks even for dynamic native queries.




