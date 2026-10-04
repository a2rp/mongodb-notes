# 14. Sharding and horizontal scaling

[Back to notes index](../README.md)

| [Previous: Replica sets and read/write concerns](./13-replica-sets-and-read-write-concerns.md) | [Notes index](../README.md) | [Next: Security, backups, and operations](./15-security-backups-and-operations.md) |
| --- | --- | --- |

## When sharding is useful

Sharding distributes a collection's data across multiple servers. It can help a workload that has outgrown the storage or processing capacity of one server. A sharded cluster normally includes:

- **Shards:** store portions of the collection data. A shard is commonly deployed as a replica set for availability.
- **`mongos`:** routes client requests to the shard or shards that own the matching data.
- **Config servers:** store cluster metadata and configuration.

Replica sets copy data for availability. Sharding partitions data for scale. A deployment can use both.

## Understand the shard key

The shard key is one or more fields that MongoDB uses to distribute documents. A query that includes the shard key can often be routed to the relevant shard. A query without a useful shard-key condition may need to contact multiple shards and combine results.

For a multi-tenant event collection, a compound key might use a tenant identifier and an event identifier:

~~~javascript
{ tenantId: 1, eventId: 1 }
~~~

This is only an example shape, not a recommended key for every application. Choose a key by considering:

- **Cardinality:** how many distinct values it has.
- **Distribution:** whether values spread data and traffic across shards.
- **Query patterns:** which fields appear in common filters and sorts.
- **Growth and write patterns:** whether new records all target one range or shard.

A monotonically increasing key can concentrate new writes at one end of a range-based distribution. A hashed key can distribute values more evenly, but it may not support range queries in the same way. Measure the actual workload and test the expected access patterns.

## Know the cost of scatter-gather queries

When a query does not include enough shard-key information to target a shard, `mongos` may send the query to many shards. This is called a scatter-gather operation. It can add network work and coordination. A sharded design should therefore preserve the queries the application needs, rather than only spreading storage.

Use sharding only after measuring a real scaling need. It adds operational responsibilities for monitoring, backups, deployment changes, and troubleshooting. A well-sized replica set or simpler deployment is easier to operate when it meets the workload.

## Plan before deployment

Review expected data volume, growth, write distribution, frequent filters, and the shard key's long-term suitability. Check current MongoDB guidance and deployment requirements before enabling sharding. Avoid copying a production shard-key command from a small example without testing its effect on representative data.

## Check what you learned

1. What problem does sharding help solve?
2. How does sharding differ from replication?
3. What is the role of `mongos`?
4. Why is shard-key choice difficult to change after a collection grows?
5. What does cardinality describe for a shard key?
6. Why can a monotonically increasing key create uneven write traffic?
7. What is a scatter-gather query?
8. Why should a team measure a scaling need before sharding?

## References

- [Sharding](https://www.mongodb.com/docs/manual/sharding/)
- [Shard keys](https://www.mongodb.com/docs/manual/core/sharding-shard-key/)
- [Shard key index requirements](https://www.mongodb.com/docs/manual/core/sharding-shard-key-indexes/)
- [Sharded cluster components](https://www.mongodb.com/docs/manual/core/sharded-cluster-components/)
