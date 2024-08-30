---
title: Performance and Scaling
date: 2023-10-21 T 14:58:16-1697925496.196
draft: false
enableToc: true
collapsible: false
meta_image: tech/high_performance_java_persistence/hpjp_horizontal.jpg
image: tech/high_performance_java_persistence/hpjp_horizontal.jpg
weight: 5
---
{{< featuredImage >}}
This notes is for the book - [High performance java persistence](https://vladmihalcea.com/books/high-performance-java-persistence/)

An enterprise application needs to store and retrieve as much data and as fast as possible. In application performance management, the two most important metrics are,
* response time
* throughput

The lower the response time, the more responsive the application becomes. Scaling is about maintaining low latencies while the system load increases. 

_Response time_ is measure of performance.
_Throughput_ is the measure of scalability.

#### Response time and throughput
The transaction response time is measured as the time it takes to _complete a transaction_. So it encompasses the following segments
1. Database connection acquisition time
2. Time taken to send all the statements over the wire
3. Execution time for all the statements
4. Time taken for sending the results back to the db client
5. The time the transaction is _idle_ due to application level computations prior to releasing the db connection.

_Throughput_ is the rate of completing incoming load. In db context, throughput can be calculated as the number of transactions executed within a time interval.

X = transaction count / time

Lowering the execution time of the transaction, the system can accommodate more requests.

The throughput measured from a single request becomes baseline for further concurrency based improvements.

If the system was to scale linearly, adding more db connections would yield a proportional throughput increase. But, due to _contention_ on db resources and cost of maintaining _coherency_ across multiple concurrent db sessions, the relative throughput follows a _curve_ instead of a straight line.

The _Universal Scalability law_ can approximate the _maximum relative throughput_ (system capacity) in relation to the number of load generators (database connections). USL formula is an extension to the widely known _Amdahl's law_.

* Contention coefficient - Serializable portion of the data processing routine. Contention has the effect of leveling up the scalability
* Coherency coefficient - Cost of maintaining consistency across all concurrent db sessions. Coherency is responsible for the inflection point in the scalability curve. Its effect becomes more significant as the number of concurrent sessions increases.

When coherency coefficient is zero,  USL overlaps the Amdahl's law.

The number of db connections for which the system hits its maximum capacity depends on USL coefficients solely.

#### Database connections boundaries
Every connection requires a TCP socket from client (app) to the server (db).

The total number of connections offered by a database server depends on the underlying hardware resources. 

PostgreSQL is implemented using a simple "process per user" client/server model. In this model there is one *client process* connected to exactly one *server process*. [[1]](https://www.postgresql.org/docs/9.5/connect-estab.html)

Database system internals reveal tight dependency on CPU, Memory and Disk resources. The database uses _buffer pool_ to map into memory the underlying data and index pages. Changes are first applied in memory, and flushed to disk in batches to achieve better write performance.

The db might still need to access disk to fetch associated data pages into memory buffer pool. To provide data consistency, locks are used to protect data blocks from being updated concurrently. This means that a _high-throughput_ db application will experience contention CPU, Memory, Disks and Locks. When all the db server resources are in use, adding more work will only increase the contention, thereby reducing the throughput.

**Resources might get saturated due to improper system configuration, the first step to improve the system throughput is to tune it according to current data access patterns**

{{< notice info "Note on capacity planning" >}} 
Capacity planning is a feedback driven mechanism, and it requires constant application monitoring. Any optimization must be reinforced by application performance metrics.
{{< /notice >}}

#### Scaling up and scaling out
Scaling is the effect of increasing the capacity by adding more resources.
* Scaling vertically - Adding more resource to a single machine.
* Scaling horizontally - Increasing the number of available machines.

Traditionally, scaling vertically has been the preferred way of increasing the database capacity. Distributed systems are much more complex to manage than the centralized ones, hence scaling horizontally is more challenging. Instead of a  high performance server, for the same price, one could buy multiple commodity machines whose sum of available resources is much greater than the single dedicated server.

No matter how powerful a single dedicated server may be, it is still a **single point of failure**.

##### Master-Worker replication
When **read / write** ratio is high, a Master - Worker replication scheme is suitable for increasing availability. The master is the system of record and the only node accepting writes. All changes recorded by master node are replayed onto workers.

A _binary replication_ uses Master node WAL (Write Ahead Log), while a _statement-based replication_ replays on the worker machine, the exact statements executed on master.

* _Asynchronous replication_ is common when there are more worker nodes to update. The worker nodes are eventually consistent. In case a master node crashes, there is a _cluster-wide_ voting process to elect the new master. This is usually node that has the most recent update record from the list of all available workers. The Asynchronous replication topology is also referred to as warm standby because the election process doesn't happen instantaneously. When only asynchronous workers are available, the newly elected worker might lag behind failed master, in which consistency and durability are traded for lower latency and higher throughput.
* _Synchronous replication_ allows system to ensure there is data consistency in case of Master node failure, since the synchronous worker is an exact copy of the master. The synchronous Master-worker replication is called _hot standby topology_ because the synchronous worker node is readily available for replacing the master node.

Aside from eliminating the single point of failure, db replication can also increase the throughput. Since the worker node only accept read-only transactions, therefore routing read traffic away from the Master node. This reduced the master node resource contention, and lowers the read-write transaction response time. If the master node can no longer keep up with ever increasing read-write traffic, a multi master replication might be a better alternative.

##### Multi-Master replication
In this scheme, all nodes are equal and can accept both read-only and read-write transactions. Splitting the load among multiple nodes can not only increase the transaction throughput but also reduce the response time.

Ensuring **data consistency** is challenging in Multi-Master replication scheme because there is no longer a single source of truth. Same data can be modified concurrently on separate nodes, so there is a possibility of conflicting updates. The replication schema can either avoid conflicts or detect them and apply automatic conflict resolution algorithm.

A _two phase_ commit protocol can be used to enlist all participating nodes in one distributed transaction. This allows all the nodes to be in-sync all the time, at the cost of increasing the transaction response time (by slowing down write operations).

If nodes are separated by WAN (Wide area network), synchronization latencies can increase dramatically. If one node is no longer reachable, the synchronization could fail and the transaction will roll back on all masters.

  * Synchronous replication - Although it provides data consistency, might incur high transaction response time.
  * Asynchronous replication - Although it provides better through put, it has to do it at the cost of having to resolve conflicts. The async master-master replication requires a conflict detection and automatic conflict resolution algorithms. When a conflict is detected, that automatic resolution tries to merge the two conflicting branches, and if it fails, manual intervention is required.

  ##### Sharding
  When the data size grows beyond the overall capacity of replicated multi-node environment, splitting data becomes unavoidable. Sharding means distributing data across multiple nodes, so that each node has only subset of data.
  As opposed to horizontal partitioning (distribute data across multiple tables within the same db server), sharding requires a distributed systems topology so that the data is spread across multiple machines.

  Each shard must be self contained (each transaction should use data only from a single shard). Joins across shards is usually prohibited because the cost of distributed locking and the networking overhead would lead to long transaction response times. 

  By reducing the data size to be store in each node, indexes will require less space and can it into main memory. With less data to query, the transaction response time can get shorter.

  Each sharding topology includes at least two separate data centers. Each can serve a dedicated geographical region, so that the load is balanced across geographical regions. Smaller tables can be duplicated on each nodes to increase performance. Asynchronous replication mechanism can be employed to keep the shards in-sync.

  Sharding is usually the last resort strategy, after exhausting all other available options such as
  1. Optimizing data layer to deliver lower transaction response time
  2. Scaling each replicated node to a cost-effective configuration
  3. Adding more replicated nodes until synchronization latencies start dropping below acceptable threshold.