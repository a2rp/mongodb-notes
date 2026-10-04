# 13. Replica sets and read/write concerns

[Back to notes index](../README.md)

| [Previous: Atomicity and transactions](./12-atomicity-and-transactions.md) | [Notes index](../README.md) | [Next: Sharding and horizontal scaling](./14-sharding-and-horizontal-scaling.md) |
| --- | --- | --- |

## What a replica set provides

A replica set is a group of MongoDB servers that maintain the same data set. One member is primary and accepts writes. Secondary members copy the primary's operations and can be used for eligible reads. If the primary becomes unavailable, the remaining members can elect another primary.

Replication helps keep data available through some server failures. It does not replace backups. A mistake or unwanted delete can replicate to every member.

In `mongosh`, inspect the current replica set status when connected to a member:

~~~javascript
rs.status()
~~~

A local standalone server is not a replica set, so this command and replica-set behavior require an appropriate deployment configuration.

## Choose how writes are acknowledged

Write concern describes how much acknowledgment MongoDB should wait for before reporting a write as successful. A common option is `w: "majority"`, which waits for acknowledgment from a majority of the voting data-bearing members:

~~~javascript
db.orders.insertOne(
  { orderNumber: "ORD-1001", status: "placed" },
  { writeConcern: { w: "majority" } }
)
~~~

A stronger acknowledgment can affect latency and availability during network or member failures. Choose a write concern that matches the application's durability requirement.

## Choose where reads go

Read preference controls which replica-set members may serve a read. The default is `primary`. A secondary read can distribute some read traffic, but a secondary may not yet have copied the newest write. Do not use secondary reads when the user expects to immediately read their latest change unless the application's consistency design accounts for replication delay.

Read concern controls the consistency and isolation level of returned data. `majority` asks for data acknowledged by a majority of replica-set members. Configure read preference and read concern based on what the feature promises to the user.

~~~javascript
const client = new MongoClient(uri, {
  readPreference: "primary",
  readConcern: { level: "majority" },
  writeConcern: { w: "majority" }
})
~~~

Client-level settings are defaults for operations and can be overridden at database, collection, or transaction scope. Avoid changing them without understanding the effect on latency and consistency.

## Keep replication and backup separate

Replica-set members provide redundant live copies. They are not independent historical backups because deletions and corruption can be copied. Back up data on a separate schedule and test that it can be restored into an isolated deployment.

## Check what you learned

1. What role does the primary play in a replica set?
2. How do secondary members receive data?
3. What can happen if a primary becomes unavailable?
4. What does `w: "majority"` ask MongoDB to wait for?
5. What risk comes with reading from a secondary?
6. How do read preference and read concern differ?
7. Why do replica-set copies not replace backups?
8. Which `mongosh` command shows replica-set status?

## References

- [Replica sets](https://www.mongodb.com/docs/manual/replication/)
- [Replica set elections](https://www.mongodb.com/docs/manual/core/replica-set-elections/)
- [Read preference](https://www.mongodb.com/docs/manual/core/read-preference/)
- [Read concern](https://www.mongodb.com/docs/manual/reference/read-concern/)
- [Write concern](https://www.mongodb.com/docs/manual/reference/write-concern/)
