
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

5.1 Leaders and Followers: The chapter begins by introducing the concept of leader-based replication, where one node is responsible for accepting writes and replicating changes to the followers to ensure data consistency.

5.2 Synchronous Versus Asynchronous Replication: This section discusses the trade-offs between synchronous and asynchronous replication, highlighting the impact on performance, data consistency, and fault tolerance.

5.3 Setting Up New Followers: The process of adding new follower nodes to the system is explored, along with the challenges of ensuring that new nodes have a consistent view of the data.

5.4 Handling Node Outages: This section discusses strategies for managing node failures, such as failover and load balancing, to ensure system availability and data durability.

5.5 Implementation of Replication Logs: The chapter delves into the implementation details of replication logs, which serve as the foundation for leader-based replication.


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


5.6 Problems with Replication Lag: The potential issues associated with replication lag are examined, including inconsistencies, stale reads, and constraint violations.

5.7 Reading Your Own Writes: Techniques to ensure that clients can read their own writes in a distributed system, even in the presence of replication lag, are presented.

5.8 Monotonic Reads: This section covers the concept of monotonic reads, which provide guarantees about the ordering of read operations to prevent clients from observing inconsistent data.

5.9 Consistent Prefix Reads: The chapter introduces consistent prefix reads, a guarantee that ensures clients will always see a consistent and ordered view of the data.

5.10 Solutions for Replication Lag: Finally, the chapter presents various techniques for addressing replication lag and improving data consistency, such as read-after-write consistency, quorum reads and writes, and version vectors.
