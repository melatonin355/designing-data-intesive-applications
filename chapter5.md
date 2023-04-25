
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
