# 9. Indexes and query plans

[Back to notes index](../README.md)

| [Previous: Data modeling with embedding and references](./08-data-modeling-embedding-and-references.md) | [Notes index](../README.md) | [Next: Aggregation pipelines](./10-aggregation-pipelines.md) |
| --- | --- | --- |

## Why indexes matter

Without a useful index, MongoDB may examine many documents to find the matches for a query. An index stores selected field values in an order that can help locate matching documents or return them in a requested order. Indexes can improve reads, but they use storage and add work to inserts, updates, and deletes.

MongoDB creates a unique index on `_id` for every collection. Other indexes should support real query patterns rather than every field that might someday be searched.

## Create a single-field index

If the application frequently finds orders by customer, an index can support that filter:

~~~javascript
db.orders.createIndex({ customerId: 1 })
db.orders.find({ customerId: ObjectId("66f1a47f5e7a123456789013") })
~~~

`1` means ascending order and `-1` means descending order. For a single equality lookup, either direction can often locate the same values, while sort requirements can make direction important.

## Design a compound index for a query

A compound index stores multiple fields in the specified order. Suppose the application finds a customer's recent orders:

~~~javascript
db.orders.createIndex({ customerId: 1, placedAt: -1 })

db.orders.find({ customerId: ObjectId("66f1a47f5e7a123456789013") })
  .sort({ placedAt: -1 })
  .limit(20)
~~~

The order of fields matters. A compound index can support queries on its leading field prefix. Put fields used for equality, sort, and range conditions in an order that matches the application's query shapes. Check the current index design guidance before adopting a general rule for a more complex query.

## Inspect a query plan

Use `explain("executionStats")` to see how a query ran and how many documents or index keys it examined:

~~~javascript
db.orders.find({ customerId: ObjectId("66f1a47f5e7a123456789013") })
  .sort({ placedAt: -1 })
  .limit(20)
  .explain("executionStats")
~~~

A `COLLSCAN` stage means the query scanned a collection. An `IXSCAN` stage means it scanned an index. Compare `nReturned`, `totalDocsExamined`, and `totalKeysExamined` against the size of the result. A plan should be judged against real data and the full query, not only by the presence of an index.

## Choose indexes carefully

- Add an index for frequent filters, sorts, and unique constraints.
- Use a unique index when duplicate values would violate a real data rule, such as an account email.
- A multikey index can index values in an array field, but it has specific restrictions.
- A TTL index can remove eligible documents after a time, but it is not a precise scheduler.
- Remove indexes that no longer support an active access pattern after checking their use.

~~~javascript
db.accounts.createIndex({ email: 1 }, { unique: true })
db.sessions.createIndex({ expiresAt: 1 }, { expireAfterSeconds: 0 })
~~~

A TTL index is appropriate for expiring temporary records. It should not be used when deletion must happen at an exact second.

## Check what you learned

1. What work can an index reduce for a read query?
2. What costs do extra indexes add?
3. What does the order of fields in a compound index affect?
4. What does a `COLLSCAN` stage indicate?
5. Which `explain()` values compare examined data with returned results?
6. When is a unique index useful?
7. What does a TTL index do, and why is it not an exact timer?
8. Why should indexes be based on real query patterns?

## References

- [MongoDB indexes](https://www.mongodb.com/docs/manual/indexes/)
- [Index types](https://www.mongodb.com/docs/manual/core/indexes/index-types/)
- [Compound indexes](https://www.mongodb.com/docs/manual/core/indexes/index-types/index-compound/)
- [Explain query plans](https://www.mongodb.com/docs/manual/reference/method/cursor.explain/)
