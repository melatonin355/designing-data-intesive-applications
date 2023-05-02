
## Part II: Distributed Data



## Big Picture

- https://github.com/donnemartin/system-design-primer#master-slave-replication 


### Chapter 5: Replication


- [5.1 Leaders and Followers](#51-leaders-and-followers)
- [5.2 Synchronous Versus Asynchronous Replication](#52-synchronous-versus-asynchronous-replication)
- [5.3 Setting Up New Followers](#53-setting-up-new-followers)
- [5.4 Handling Node Outages](#54-handling-node-outages)
- [5.5 Implementation of Replication Logs](#55-implementation-of-replication-logs)
- [5.6 Problems with Replication Lag](#56-problems-with-replication-lag)
   - [Reading Your Own Writes](#reading-your-own-writes)
   - [Monotonic Reads](#monotonic-reads)
   - [Consistent Prefix Reads](#consistent-prefix-reads)
- [5.10 Solutions for Replication Lag](#510-solutions-for-replication-lag)


## Replication in Distributed Systems


1. **Replication**: Keeping a copy of the same data on multiple machines connected via a network. Reasons for replication:
   a. Reduce latency by keeping data geographically close to users
   b. Increase availability by allowing the system to work despite part failures
   c. Increase read throughput by scaling out the number of machines serving read queries

2. Assumptions: Each machine can hold a copy of the entire dataset. Chapter 6 discusses **partitioning** for larger datasets.

3. Replication challenges arise when handling changes to replicated data.

4. Three popular replication algorithms:
   a. **Single-leader**
   b. **Multi-leader**
   c. **Leaderless replication**
   * Each has pros and cons, which are examined in detail.

5. Replication trade-offs:
   a. **Synchronous** vs. **asynchronous replication**
   b. Handling **failed replicas**
   * These are often database configuration options with similar principles across implementations.

6. Replication principles haven't changed much since the 1970s due to fundamental network constraints.

7. Developers often misunderstand issues such as **eventual consistency**. The chapter also discusses **read-your-writes** and **monotonic reads guarantees**.


## Single Leader Diagram
<pre>
        +---------+
        |         |
        |  User   |
    [1] O<--------| +Picture|
       /|\        |         |
        |         +---------+
        |             |
        |             v
        |
    [2] +----------------------+
        |                      |
        | Write/Read Request   |
        |                      |
        +----------------------+
                   |
                   v
              +-------+
              |       |
              | Change|
              +-------+
              /       \
             /         \
            /           \
[3]+------------+  +---------------+  +---------------+
   | Leader     |->| Follower      |->| Follower      |
   | Replica    |  | Replica 1     |  | Replica 2     |
   +------------+  +---------------+  +---------------+
                        \
                         \
                          v
[4]                      O  +-----------+
                         /|\ |           |
                          |  | End User  |
                          |  | (Read     |
                          |  | Replica)  |
                          +-----------+

</pre>

1. **User uploads a picture**: The user adds a picture to their data.
2. **Write/Read Request**: The user sends a write request to the Leader Replica to update the data with the new picture.
3. **Change propagation**: The Leader Replica processes the change and propagates it to the Follower Replicas (Follower Replica 1 and Follower Replica 2).
4. **End User reads data**: The end user reads the updated data, including the newly added picture, from the selected Follower Replica (in this case, Follower Replica 1).

# 5.1 Leaders and Followers:
The chapter begins by introducing the concept of leader-based replication, where one node is responsible for accepting writes and replicating changes to the followers to ensure data consistency.

## 5.2 Synchronous Versus Asynchronous Replication:


- **Synchronous replication**:
  - Strong consistency across replicas.
  - Higher latency for write operations.
  - Lower fault tolerance.
  
- **Asynchronous replication**:
  - Lower latency for write operations.
  - Weaker consistency across replicas.
  - Higher fault tolerance.

The choice between synchronous and asynchronous replication depends on the specific requirements of the system and the desired balance between consistency, performance, and fault tolerance.

## 5.3 Setting Up New Followers:


1. **Snapshot**: Take a consistent snapshot of the leader's database without locking the entire database.
2. **Copy**: Transfer the snapshot to the new follower node.
3. **Connect and Request**: The follower connects to the leader and requests data changes since the snapshot, using a specific position in the leader's replication log.
4. **Catch Up**: The follower processes the backlog of data changes and continues to process new changes from the leader as they occur.

The practical steps for setting up a follower vary by database. Some systems automate the process, while others require manual execution by an administrator.

## **5.4 Handling Node Outages:**

1. **Follower failure: Catch-up recovery**: Followers can recover from a crash or temporary network interruption using their local log of data changes. They reconnect to the leader and request all data changes that occurred during their disconnection.
2. **Leader failure: Failover**: Promoting a follower to be the new leader, reconfiguring clients to send writes to the new leader, and ensuring other followers consume data changes from the new leader. Failover can be manual or automatic, and involves:
   - Determining leader failure: Typically using a timeout.
   - Choosing a new leader: Through an election process or appointing a new leader.
   - Reconfiguring the system: Clients send write requests to the new leader, and the old leader must become a follower recognizing the new leader.
3. **Failover challenges**: Asynchronous replication can result in unreplicated writes or data loss; discarding writes can lead to inconsistency; split-brain scenarios can cause data loss or corruption; and choosing the right timeout for leader failure detection can impact recovery time and risk unnecessary failovers.

Some operations teams prefer manual failovers due to these challenges. These issues are fundamental problems in distributed systems and are discussed in greater depth in Chapters 8 and 9.

## **5.5 Implementation of Replication Logs:** 

Replication logs serve as the foundation for leader-based replication in distributed systems, ensuring data consistency across nodes. This summary is based on the book "Designing Data-Intensive Applications" and is tailored for software engineers.

<pre>
+-----------+       +-----------+       +-----------+
|  Leader   | ----> | Follower  | ----> | Follower  |
|           |       |           |       |           |
+----+------+       +-----+-----+       +-----+-----+
     |                   |                   |
     v                   v                   v
+------------+       +------------+       +------------+
| Replication |       | Replication |       | Replication |
|     Log     |       |     Log     |       |     Log     |
+------------+       +------------+       +------------+
</pre>

Replication logs are sequential records of updates in a distributed system. The book covers four types of replication logs:

1. **Statement-based replication:**
<pre>
Log Index | SQL Statement
-------------------------
1         | INSERT INTO users (id, name) VALUES (1, 'Alice')
2         | UPDATE users SET name = 'Alicia' WHERE id = 1
3         | DELETE FROM users WHERE id = 2
</pre>
Records SQL statements executed on the primary node and replicates them to secondary nodes. Suitable for **simple applications** with deterministic SQL statements and **minimal data transfer** requirements.

2. **Write-ahead log (WAL) shipping:**
<pre>
Log Index | Operation | Block | Offset | Data
---------------------------------------------
1         | INSERT    | 5     | 1024   | {record_data}
2         | UPDATE    | 5     | 1024   | {new_record_data}
3         | DELETE    | 5     | 1024   |
</pre>
Sends the primary node's WAL to secondary nodes, which replay the log to apply changes. Ensures **strong consistency** and **data durability**, ideal for PostgreSQL-based systems and those prioritizing minimal impact on primary node performance.

3. **Logical (row-based) log replication:**
<pre>
Log Index | Operation | Table | Row Data
-----------------------------------------
1         | INSERT    | users | {id: 1, name: 'Alice'}
2         | UPDATE    | users | {id: 1, name: 'Alicia'}
3         | DELETE    | users | {id: 2}
</pre>
Captures changes made to individual rows in the primary node's data. Suitable for applications with **non-deterministic operations**, prioritizing **performance**, and requiring **cross-platform compatibility**.

4. **Trigger-based replication:**
<pre>
Log Index | Trigger Event | Table | Old Row Data | New Row Data
---------------------------------------------------------------
1         | INSERT        | users |              | {id: 1, name: 'Alice'}
2         | UPDATE        | users | {id: 1, name: 'Alice'}  | {id: 1, name: 'Alicia'}
3         | DELETE        | users | {id: 2}      |
</pre>
Uses custom triggers to capture and propagate changes. Ideal for **custom replication solutions**, **real-time data processing or transformation**, and systems with heterogeneous databases.

The choice of replication method depends on system requirements and constraints, such as **consistency, performance, compatibility, and complexity**.

## **5.6 Problems with Replication Lag:**

- Replication is used for **high availability**, **scalability**, and reduced **latency**
- **Leader-based replication** requires writes to go through a single node, but read-only queries can go to any replica
- For workloads that consist mostly of reads and few writes, adding followers can increase the capacity for serving read-only requests
- This approach only works with **asynchronous replication** because synchronous replication would be unreliable
- Asynchronous replication can lead to **eventual consistency**, where followers may fall behind and return outdated information
- **Replication lag** can cause inconsistencies between the leader and followers
- Replication lag can increase with high system load or network problems, causing real problems for applications

## **Reading Your Own Writes:** 

- Asynchronous replication can cause new data to not yet have reached the replica when the user views it shortly after making a write
- Read-after-write consistency guarantees that the user will always see any updates they submitted themselves
- Implementing read-after-write consistency in a system with leader-based replication requires various techniques, such as reading from the leader for user-modifiable data or tracking the time of the last update
- Replicas distributed across multiple datacenters or accessed from multiple devices add complexity and require centralized metadata or routing requests to the datacenter containing the leader

- Another complication arises when the same user is accessing your service from multiple devices, for example a desktop web browser and a mobile app. In this case you may want to provide cross-device read-after-write consistency

Links to relevant Wikipedia articles:
- [Asynchronous replication](https://en.wikipedia.org/wiki/Asynchronous_replication)
- [Consistency model](https://en.wikipedia.org/wiki/Consistency_model)
- [Data center](https://en.wikipedia.org/wiki/Data_center)

## **Monotonic Reads:** 

- Reading from different asynchronous replicas can lead to a user seeing things moving backward in time
- Monotonic reads guarantee that time does not go backward when making multiple reads in sequence
- Monotonic reads are a lesser guarantee than strong consistency but stronger than eventual consistency
- One way to achieve monotonic reads is to make sure each user always makes their reads from the same replica, chosen based on a hash of the user ID



```sql
     Follower with little lag        Follower with greater lag
+-------------------------+     +-------------------------+
|                         |     |                         |
| User 2345 reads comment |     |                         |
| added by User 1234      |     |                         |
| Result A                |     |                         |
|                         |     |                         |
+-------------------------+     |                         |
                                 |                         |
                                 |                         |
                                 | User 2345 reads comment |
                                 | added by User 1234      |
                                 | No result returned      |
                                 |                         |
                                 +-------------------------+
```

This diagram represents the scenario where a user (User 2345) makes two queries to two different asynchronous replicas with different replication lags. The first query (Result A) is to a follower with little lag, and returns a comment added by another user (User 1234). The second query is to a follower with greater lag, and returns no results. This leads to the impression that time has gone backward for the user. To prevent this anomaly, we need monotonic reads.

One way of achieving monotonic reads is to make sure that each user always makes their reads from the same replica (different users can read from different replicas). For example, the replica can be chosen based on a hash of the user ID, rather than randomly. However, if that replica fails, the user’s queries will need to be rerouted to another replica.


Monotonic reads can be achieved by ensuring that each user always reads from the same replica. This can be implemented through:

1. Hash-based partitioning: Data is partitioned across replicas based on a hash of the user ID, ensuring that each user always reads from the same replica.

2. Sticky sessions: A load balancer can use sticky sessions to route requests from a user to the same replica based on their initial request.

3. Client-side routing: The client maintains state about the replicas and chooses the replica with the lowest replication lag for the user's reads.

4. Metadata service: A metadata service maintains a mapping of user IDs to replica IDs and returns the ID of the replica that the user should read from.

The mapping of users to replicas must be consistent and not change frequently to avoid inconsistent results.

##  **Consistent Prefix Reads:** 

Inconsistent replication lag can lead to violations of causality, where reads may appear to happen before writes. This can be prevented by ensuring consistent prefix reads, which guarantees that if a sequence of writes occurs in a certain order, anyone reading those writes will see them appear in the same order. In partitioned databases, different partitions may operate independently, making it challenging to ensure consistent prefix reads. One solution is to write causally related writes to the same partition, but this may not always be efficient. Algorithms that track causal dependencies can also help prevent causality violations.

##  **5.10 Solutions for Replication Lag:** 

**eventually consistent systems** 

The chapter presents various techniques for addressing replication lag and improving data consistency, such as read-after-write consistency, quorum reads and writes, and version vectors.

1. Read-after-write consistency: This approach ensures that when a client writes data to the system, it is only acknowledged once the write is propagated to a certain number of replicas. This guarantees that subsequent reads by the same client will return the latest written data. However, it may introduce latency during write operations, as the system needs to wait for the data to propagate.

2. Quorum reads and writes: Quorum-based systems rely on a consensus algorithm to determine the number of replicas that must acknowledge a read or write operation before it is considered successful. A common approach is to use a majority quorum, where an operation must be acknowledged by more than half of the replicas to succeed. This method helps maintain consistency, as it is less likely that an outdated replica will be used for read operations. However, it may introduce latency, as the system needs to wait for multiple acknowledgments.

3. Version vectors: Version vectors are data structures that keep track of the version history of each replica. They allow the system to detect and resolve conflicts arising from concurrent updates to the same data. By maintaining version information for each replica, the system can determine which replica has the most up-to-date data and use that replica for read operations. This approach can help address replication lag by ensuring that clients always read the latest data, even in the presence of network delays or other issues.

Each of these techniques comes with its own trade-offs in terms of performance, latency, and complexity. The choice of which method to use depends on the specific requirements and characteristics of the distributed system being designed.


# Further Ideas
- https://github.com/donnemartin/system-design-primer#replication


# Resources
Design Consitent Hashing - https://systemdesign.one/consistent-hashing-explained/