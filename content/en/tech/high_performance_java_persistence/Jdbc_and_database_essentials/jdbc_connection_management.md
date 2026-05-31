---
title: "JDBC connection management"
date: 2023-10-22T13:45:04Z
draft: false
meta_image: tech/high_performance_java_persistence/hpjp_horizontal.jpg
image: tech/high_performance_java_persistence/hpjp_horizontal.jpg
weight : 5
---

{{< featuredImage >}}

These notes are for the book - [High Performance Java Persistence](https://vladmihalcea.com/books/high-performance-java-persistence/)

The JDBC API provides a common interface for communicating with the database server. To communicate with a database server, a Java program must first obtain a _java.sql.Connection_. _java.sql.Driver_ is the actual database connection provider. _java.sql.DriverManager_ provides more convenience since it can also resolve the JDBC driver associated with the current database connection URL.

#### DriverManager
Every time the _getConnection()_ method is called, the driver manager will request a new physical connection from the underlying driver.

In a two-tier architecture, the application is run by single user and each instance uses a dedicated db connection. Each database server, based on the underlying resources can only offer a limited number of connections.

{{< img src=/tech/high_performance_java_persistence/Jdbc_and_database_essentials/driver_manager_connections.png title="High performance java persistence" caption="DriverManager connections" alt="DriverManager connections" width="700px" position="center" >}}

#### DataSource

##### Intermediate data layer
In a three-tier architecture, the middle tier acts as a bridge between user requests and various data sources (relational databases, messaging queues).

This intermediate layer offers numerous advantages. It acts as a database connection buffer that can handle user request traffic spikes by buffering requests, without depleting database connections or discarding incoming traffic. The application server provides only logical connections (_proxies or handles_), allowing it to intercept and monitor how the client API interacts with the connection object.

A three-tier architecture can accommodate multiple data sources or messaging queue implementations. To span a single transaction over multiple sources of data, a _distribution transaction manager_ becomes mandatory. In Java Transaction API (JTA), the transaction manager must be aware of the logical connections the client has acquired as it has to commit or roll them back according to the global transaction outcome. 

By providing logical connections, the application server can decorate the db connection handles with JTA transaction semantics.

_java.sql.DriverManager_ - is a physical connection factory.
_java.sql.DataSource_ - is a logical connection provider interface.

{{< img src=/tech/high_performance_java_persistence/Jdbc_and_database_essentials/datasource_wo_connection_pooling.png title="High performance java persistence" caption="Datasource without connection pooling" alt="Datasource without connection pooling" width="700px" position="center" >}}

1. Application data layer asks the DataSource for a db connection.
2. The DataSource will use the underlying driver to open a physical connection.
3. A physical connection is created, and TCP socket is opened.
4. The DataSource doesn't wrap the physical connection and simply lends it to the application layer.
5. The application executes statements using acquired db connection.
6. When the connection is no longer needed, the _application_ closes the physical connection along with the underlying TCP socket.

{{< notice info "Advantages of Reusing Connections" >}} 
Opening and closing database connections is a very expensive operation. Reusing them has the following advantages:
* It avoids both the database and driver overhead for establishing a TCP connection.
* It prevents destroying the temporary memory buffers associated with each database connection.
* It reduces client-side JVM object garbage.
{{< /notice >}}

When using a connection pooling solution, the connection acquisition time is between two and four orders of magnitude smaller.

##### Why Is Pooling Much Faster?
{{< img src=/tech/high_performance_java_persistence/Jdbc_and_database_essentials/connection_acquisition_flow.png title="High Performance Java Persistence" caption="Connection Acquisition Flow" alt="Connection Acquisition Flow" width="700px" position="center" >}}

1. When a connection is requested, the pool looks for unallocated connections.
2. If the pool finds a free connection, it hands it to the client.
3. If there is no free connection, the pool tries to grow to its maximum allowed size.
4. If the pool has already reached its maximum size, it will retry several times before giving up with a connection acquisition failure exception.
5. When the client closes the logical connection, the connection is released and returned to the pool without closing the underlying physical connection.

Most connection pooling exposes a DataSource implementation that either wraps an actual database specific DataSource or the underlying DriverManager utility.

{{< img src=/tech/high_performance_java_persistence/Jdbc_and_database_essentials/logical_connection_lifecycle.png title="High Performance Java Persistence" caption="Logical Connection Lifecycle" alt="Logical Connection Lifecycle" width="700px" position="center" >}}

1. The connection pool offers a proxy or a handle instead of a physical connection to the client.
2. When the connection is in use, the pool changes its state to allocated (to prevent two concurrent threads from using the same database connection).
3. The proxy intercepts the connection close method call and notifies the pool to change the connection state to unallocated.

A connection pool acts as a bounded buffer for incoming connection requests. If there is a traffic spike, the connection pool will handle it, instead of saturating all available database resources.

Configuring the right pool size is non-trivial. Connection pool provisioning requires understanding:
1. Application-specific database access patterns
2. Connection usage monitoring

Whenever the number of incoming requests surpass the available handlers, there are two options to avoid system overloading.
1. Discarding the overflowing traffic (affects availability)
2. Queuing requests and wait for busy resources to be available (increasing response time)

Discarding the surplus traffic is usually a last resort measure. Most connection pooling solutions first attempt to enqueue overflowing incoming requests. By putting an upper bound on the connection request wait time, the queue is prevented from growing indefinitely and saturating the application server resources.

For a given incoming request rate, the relationship between queue size and average enqueuing time is given by one of the most fundamental laws of queuing theory.

#### Queuing Theory Capacity Planning
##### Little's Law
Little's Law is a general-purpose equation applicable to a queuing system that is in a stable state.

Assuming that the application-level transaction uses the same db connection through its life-cycle,

* If the average transaction response time is 100 milliseconds (0.1 seconds).
* If the average connection acquisition rate is 50 requests per second.

The average number of connection requests in the system is 50 * 0.1 = 5 connection requests (both the requests being serviced and the ones waiting in the queue).

A pool size of 5 can accommodate the _average_ incoming traffic without having to enqueue any connection requests.
If the pool size is set to 3, then on average 2 requests are enqueued and waiting for connections to be available.

{{< alert theme="warning" dir="ltr" >}} 
Little's Law operates with **long-term** averages and might not be suitable when taking into consideration the intermittent traffic bursts. In real-life scenario, the connection pool must adapt to short-term traffic spikes. It is important to consider the actual connection pool throughput.
{{< /alert >}}

##### Queuing theory

* If the average service time is 100 milliseconds (0.1 seconds).
* If the average connection acquisition rate is 50 requests per second.
We concluded that the pool can offer at most 5 connections (there are at most 5 in-service requests).

In queuing theory, throughput is represented by the departure rate (μ). For a connection pool, it represents the number of connections offered in a given unit of time:

{{< notice info "Throughput" >}} 
**(μ) = Ls / Ws**

Where,
`Ls` = Pool size
`Ws` = Connection lease time
{{< /notice >}}

If the arrival rate outgrows the connection pool throughput, the overflowing requests must wait for connections to become available.

A one-second traffic burst of 150 requests is handled as follows:
* The first 50 requests can be served in the first second.
* The following 100 requests are enqueued and processed in the following two seconds.

For a constant throughput, the number of enqueued connection requests is proportional to the connection acquisition time.

The total time required to process the spike is given by the formula:

{{< notice info "Time required to process spike" >}} 
**W = Lspike / (μ)**

Where, 
`Lspike` is the total number of requests in any given spike.
`(μ)` is the throughput
{{< /notice >}}

Assuming a traffic spike of 250 requests per second for 3 seconds, then the Lspike is 750 requests. If the throughput (μ) is 50 requests per second, the total time to process all requests is 15 seconds.

#### A Real-Life Connection Pool Monitoring Example

The dynamics of enterprise systems are difficult to express with equations like queuing theory, so metrics become fundamental for resource provisioning. By continuously monitoring connection usage patterns, it is easier to react and adjust the pool size when initial configurations no longer hold.

In case of an unforeseen traffic spike, the connection acquisition time could reach the DataSource timeout threshold.

Following were the observations made while conducting an experiment using an opensource connection pool and batch processor.

{{< alert theme="success" dir="ltr" >}} 
1. The more incoming concurrent connection requests, the higher the response time for obtaining a pooled connection.
2. The average value masks outliers, which is why percentiles are preferred in application performance monitoring. Percentiles make outliers visible while capturing the immediate effect of a given traffic change.
3. It is important to set the idle connection timeout threshold so the pool can release unused connections and the database can provide them to other clients as well.
4. Holding connections for long periods of time can increase the connection acquisition time and reduce resources available to other incoming requests.
5. Long-running transactions might hold database locks, which in turn might increase the serial portion of the current execution context, hindering parallelism. (This can be addressed by indexing slow queries or by splitting the application-level transaction over multiple database transactions.)
{{< /alert >}}
   
