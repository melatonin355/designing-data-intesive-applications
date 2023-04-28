
## Part II: Distributed Data

### Chapter 5: Replication

- 5.1 Leaders and Followers ........................................................... 151
- 5.2 Synchronous Versus Asynchronous Replication ............................. 153
- 5.3 Setting Up New Followers .......................................................... 155
- 5.4 Handling Node Outages ............................................................ 156
- 5.5 Implementation of Replication Logs ............................................. 158
- 5.6 Problems with Replication Lag .................................................... 161
- 5.7 Reading Your Own Writes .......................................................... 162
- 5.8 Monotonic Reads ................................................................... 164
- 5.9 Consistent Prefix Reads ............................................................. 165
- 5.10 Solutions for Replication Lag .................................................... 167



- 5.11 Multi-Leader Replication .......................................................... 168
  - 5.11.1 Use Cases for Multi-Leader Replication ........................... 168
  - 5.11.2 Handling Write Conflicts ................................................ 171
  - 5.11.3 Multi-Leader Replication Topologies ................................ 175
- 5.12 Leaderless Replication .............................................................. 177
  - 5.12.1 Writing to the Database When a Node Is Down .................. 177
  - 5.12.2 Limitations of Quorum Consistency .................................. 181
  - 5.12.3 Sloppy Quorums and Hinted Handoff ................................ 183
  - 5.12.4 Detecting Concurrent Writes .............................................. 184
- 5.13 Summary .............................................................................. 192


## 5.1 - 5.10


Chapter 5 of "Designing Data-Intensive Applications" is titled "Replication" and provides an in-depth exploration of various replication techniques used to improve the availability, performance, and fault tolerance of distributed systems. The chapter covers the following topics:

# 5.1 Leaders and Followers:
The chapter begins by introducing the concept of leader-based replication, where one node is responsible for accepting writes and replicating changes to the followers to ensure data consistency.

**5.2 Synchronous Versus Asynchronous Replication:** This section discusses the trade-offs between synchronous and asynchronous replication, highlighting the impact on performance, data consistency, and fault tolerance.

**5.3 Setting Up New Followers:** The process of adding new follower nodes to the system is explored, along with the challenges of ensuring that new nodes have a consistent view of the data.

**5.4 Handling Node Outages:** This section discusses strategies for managing node failures, such as failover and load balancing, to ensure system availability and data durability.

**5.5 Implementation of Replication Logs:** The chapter delves into the implementation details of replication logs, which serve as the foundation for leader-based replication.


```
+-----------+       +-----------+       +-----------+
|  Leader   | --->  | Follower  | --->  | Follower  |
|           |       |           |       |           |
+-----------+       +-----------+       +-----------+
     ||                 ||                 ||
     \/                 \/                 \/
+-----------+       +-----------+       +-----------+
| Replication|       | Replication|       | Replication|
|    Log     |       |    Log     |       |    Log     |
+-----------+       +-----------+       +-----------+
```

In this illustration, the "Leader" node accepts write requests and maintains a replication log. The "Follower" nodes receive updates from the replication log to ensure data consistency across the distributed system.

A replication log is a sequential record of all updates made to the data in a distributed system. Each entry in the log corresponds to a specific operation, such as insert, update, or delete, along with any necessary metadata, such as timestamps or version numbers. Here's an example of a simplified replication log for a key-value store:

```
Log Index | Operation |   Key    | Value  | Timestamp
------------------------------------------------------
       1  |  INSERT   |  "user1" | "John" | 1001
       2  |  UPDATE   |  "user1" | "Jane" | 1005
       3  |  INSERT   |  "user2" | "Mark" | 1010
       4  |  DELETE   |  "user1" |        | 1015
       5  |  INSERT   |  "user3" | "Lucy" | 1020
```

In this example, each row in the table represents an entry in the replication log. The "Log Index" column indicates the position of the entry in the log, while the "Operation" column specifies the type of operation performed (INSERT, UPDATE, or DELETE). The "Key" and "Value" columns represent the key-value pair being modified, and the "Timestamp" column provides a timestamp for the operation.

In a leader-based replication system, the leader node would maintain this log and send updates to follower nodes, which would then apply the changes in the order they appear in the log to ensure data consistency across the system.

**The book covers a few types of replicaton logs: **


1. **Statement-based replication:** MySQL uses statement-based replication in its earlier versions. In this method, the SQL statements executed on the primary (leader) node are recorded and replicated to the secondary (follower) nodes.

Example of a statement-based replication log:
```
Log Index | SQL Statement
-------------------------
1         | INSERT INTO users (id, name) VALUES (1, 'Alice')
2         | UPDATE users SET name = 'Alicia' WHERE id = 1
3         | DELETE FROM users WHERE id = 2
```

2. **Write-ahead log (WAL) shipping:** PostgreSQL utilizes WAL shipping for replication. The primary node's write-ahead log is sent to the secondary nodes, which then replay the log to apply the changes.

Example of a WAL-based replication log (simplified):
```
Log Index | Operation | Block | Offset | Data
---------------------------------------------
1         | INSERT    | 5     | 1024   | {record_data}
2         | UPDATE    | 5     | 1024   | {new_record_data}
3         | DELETE    | 5     | 1024   | 
```

3. **Logical (row-based) log replication:** MySQL, starting from version 5.1, supports row-based replication in addition to statement-based replication. In this method, the changes made to individual rows in the primary node's data are recorded and replicated.

Example of a row-based replication log:
```
Log Index | Operation | Table | Row Data
-----------------------------------------
1         | INSERT    | users | {id: 1, name: 'Alice'}
2         | UPDATE    | users | {id: 1, name: 'Alicia'}
3         | DELETE    | users | {id: 2}
```

4. **Trigger-based replication:** Trigger-based replication can be implemented in various database systems, including MySQL and PostgreSQL, using custom triggers to capture and propagate changes.

Example of a trigger-based replication log (simplified):
```
Log Index | Trigger Event | Table | Old Row Data | New Row Data
---------------------------------------------------------------
1         | INSERT        | users |              | {id: 1, name: 'Alice'}
2         | UPDATE        | users | {id: 1, name: 'Alice'}  | {id: 1, name: 'Alicia'}
3         | DELETE        | users | {id: 2}      | 
```

When to use each one? The book doesn't cover that so here is a brief summary: 

1. Statement-based replication:
- Simple applications where the majority of SQL statements are deterministic and unlikely to cause inconsistency issues between replicas.
- Systems where minimizing the amount of data transferred between nodes is essential, as only SQL statements are replicated rather than the actual data changes.
- Environments where compatibility with older versions of MySQL is required.

2. Write-ahead log (WAL) shipping:
- Systems that require strong consistency between replicas and prioritize data durability, as WAL shipping ensures that all changes are applied in the same order on each replica.
- PostgreSQL-based systems, since WAL shipping is the native replication method in PostgreSQL.
- Systems where the ability to create replicas with minimal impact on the primary node's performance is crucial, as the primary node's WAL can be shipped and replayed on secondary nodes with relatively low overhead.

3. Logical (row-based) log replication:
- Applications where non-deterministic operations or functions are common, as row-based replication captures the actual data changes rather than the SQL statements, avoiding potential inconsistencies.
- Systems where performance is a priority, as row-based replication reduces the need to re-execute SQL statements on the secondary nodes.
- When cross-platform compatibility is essential, as row-based replication is more portable across different database systems than WAL shipping.

4. Trigger-based replication:
- Custom replication solutions that need to support specific application logic or business requirements, as triggers can be tailored to capture and propagate only the desired changes.
- Applications that require real-time processing or transformation of data during replication, as triggers can be used to apply additional logic before propagating changes to secondary nodes.
- Systems using heterogeneous databases, where a standardized replication method is not available or not feasible to implement, as trigger-based replication can be implemented in various database systems, including MySQL and PostgreSQL.

The choice of replication method depends on the specific requirements and constraints of the distributed system being designed, such as consistency, performance, compatibility, and complexity.


## 5.6 Problems with Replication Lag: 

The potential issues associated with replication lag are examined, including inconsistencies, stale reads, and constraint violations.

**5.7 Reading Your Own Writes:** Techniques to ensure that clients can read their own writes in a distributed system, even in the presence of replication lag, are presented.

**5.8 Monotonic Reads:** This section covers the concept of monotonic reads, which provide guarantees about the ordering of read operations to prevent clients from observing inconsistent data.

**5.9 Consistent Prefix Reads:** The chapter introduces consistent prefix reads, a guarantee that ensures clients will always see a consistent and ordered view of the data.

**5.10 Solutions for Replication Lag:** Finally, the chapter presents various techniques for addressing replication lag and improving data consistency, such as read-after-write consistency, quorum reads and writes, and version vectors.

1. Read-after-write consistency: This approach ensures that when a client writes data to the system, it is only acknowledged once the write is propagated to a certain number of replicas. This guarantees that subsequent reads by the same client will return the latest written data. However, it may introduce latency during write operations, as the system needs to wait for the data to propagate.

2. Quorum reads and writes: Quorum-based systems rely on a consensus algorithm to determine the number of replicas that must acknowledge a read or write operation before it is considered successful. A common approach is to use a majority quorum, where an operation must be acknowledged by more than half of the replicas to succeed. This method helps maintain consistency, as it is less likely that an outdated replica will be used for read operations. However, it may introduce latency, as the system needs to wait for multiple acknowledgments.

3. Version vectors: Version vectors are data structures that keep track of the version history of each replica. They allow the system to detect and resolve conflicts arising from concurrent updates to the same data. By maintaining version information for each replica, the system can determine which replica has the most up-to-date data and use that replica for read operations. This approach can help address replication lag by ensuring that clients always read the latest data, even in the presence of network delays or other issues.

Each of these techniques comes with its own trade-offs in terms of performance, latency, and complexity. The choice of which method to use depends on the specific requirements and characteristics of the distributed system being designed.
