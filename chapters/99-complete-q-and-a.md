# 99. Complete questions and answers

[Back to notes index](../README.md)

| [Previous: All code samples](./98-all-code-samples.md) | [Notes index](../README.md) | [Next: Notes index](../README.md) |
| --- | --- | --- |

This appendix collects the review questions from the core chapters and gives a concise answer for each one.

## 1. Documents and MongoDB fundamentals

1. **Question:** Explain the relationship between a database, collection, and document.  
   **Answer:** A database contains collections, and each collection groups documents. A document is one stored record made of fields and values.
2. **Question:** In the learner example, identify one embedded document and one array.  
   **Answer:** `contact` is an embedded document, and `courses` is an array of course names.
3. **Question:** What does the `_id` field do?  
   **Answer:** It uniquely identifies a document within its collection. MongoDB creates an ObjectId for it when an insert omits `_id`.
4. **Question:** Why is BSON more expressive than plain JSON?  
   **Answer:** BSON includes types such as dates, binary data, ObjectId, and Decimal128 that plain JSON does not represent directly.
5. **Question:** What happens when `mongosh` switches to a database that has no stored data yet?  
   **Answer:** The shell changes its current database context. The database is persisted when data or metadata is written.
6. **Question:** Give one case where embedding related values could simplify a read.  
   **Answer:** A bounded list of line items embedded in an order can be returned with the order in one read.
7. **Question:** Give one case where a reference or separate collection could be a better fit.  
   **Answer:** An unbounded comment history or records shared by many parents are often better stored separately and referenced.
8. **Question:** Why should an unbounded list not grow inside one document?  
   **Answer:** It can make documents large, exceed the BSON document size limit, and make updates and reads increasingly costly.

## 2. Install MongoDB and connect with mongosh

1. **Question:** What is the difference between MongoDB Server and `mongosh`?  
   **Answer:** The server stores data and accepts connections. `mongosh` is a command-line client that sends commands to a server.
2. **Question:** Does installing `mongosh` by itself create a database server?  
   **Answer:** No. The shell is a client and needs a running MongoDB deployment to connect to.
3. **Question:** What does `127.0.0.1` mean in a local connection URI?  
   **Answer:** It refers to the same computer where the shell is running.
4. **Question:** What does the `ping` command help verify?  
   **Answer:** It checks that the connected server can respond to a simple database command.
5. **Question:** Why may a database name not appear after only running `use name`?  
   **Answer:** Selecting a database changes shell context but does not necessarily persist a database until a write or metadata operation occurs.
6. **Question:** Name two checks for an Atlas connection timeout.  
   **Answer:** Check that the client address is allowed and that the network can reach the cluster hostname and port.
7. **Question:** Why should credentials be supplied outside committed source files?  
   **Answer:** Public or shared source can expose them, allowing someone else to access the database.
8. **Question:** What should be done if a database password is accidentally published?  
   **Answer:** Rotate or revoke the credential immediately, then remove the exposed value from active files and history as appropriate.

## 3. Databases, collections, and CRUD overview

1. **Question:** Describe the relationship between a deployment, database, collection, and document.  
   **Answer:** A deployment hosts databases. Each database contains collections, and each collection stores documents.
2. **Question:** When does MongoDB create a collection automatically?  
   **Answer:** A normal first write creates the collection if it does not exist.
3. **Question:** What does CRUD stand for?  
   **Answer:** Create, read, update, and delete.
4. **Question:** Which method returns a cursor for multiple matching documents?  
   **Answer:** `find()` returns a cursor for matching documents.
5. **Question:** What information do `matchedCount` and `modifiedCount` provide?  
   **Answer:** They report how many documents matched an update filter and how many were actually changed.
6. **Question:** Why can an update match a document but report no modification?  
   **Answer:** The requested values may already be present, so no stored value changed.
7. **Question:** What should you do before calling `deleteMany()`?  
   **Answer:** Run the same filter with `find()` and inspect which documents it selects.
8. **Question:** Why is a dedicated practice database useful?  
   **Answer:** It gives a safe place to try commands without risking important application data.

## 4. Insert documents and understand BSON types

1. **Question:** What does `insertOne()` return?  
   **Answer:** It returns an acknowledged result with the inserted document's `_id` value.
2. **Question:** What happens when an inserted document has no `_id`?  
   **Answer:** MongoDB generates an ObjectId and stores it in `_id`.
3. **Question:** Why does MongoDB reject a duplicate `_id`?  
   **Answer:** The `_id` index is unique, so two documents in one collection cannot share the same identifier.
4. **Question:** Why should a date be stored as a BSON Date instead of formatted text?  
   **Answer:** A BSON Date can be compared and sorted as time without parsing a display string.
5. **Question:** When can `Decimal128` be a better choice than a JavaScript number?  
   **Answer:** It can represent decimal values such as prices without the binary floating-point rounding behavior of JavaScript numbers.
6. **Question:** How can a query inspect a field's BSON type?  
   **Answer:** Use the `$type` query operator or the aggregation `$type` expression.
7. **Question:** What size limit applies to one BSON document?  
   **Answer:** A BSON document can be at most 16 MiB.
8. **Question:** Where could an unbounded activity log be stored instead of inside one document?  
   **Answer:** Store each event in a separate collection with an account or owner identifier and query the needed range.

## 5. Find documents with query operators

1. **Question:** How does MongoDB combine two ordinary fields in one query document?  
   **Answer:** It treats them as an implicit AND, so both conditions must match.
2. **Question:** Which operator finds values greater than or equal to a threshold?  
   **Answer:** `$gte` means greater than or equal to.
3. **Question:** How does `$in` differ from `$all` when matching an array?  
   **Answer:** `$in` matches when an array contains any listed value. `$all` requires it to contain every listed value.
4. **Question:** What problem does `$elemMatch` solve?  
   **Answer:** It requires multiple conditions to match the same element of an array.
5. **Question:** How do you query a nested field such as `contact.city`?  
   **Answer:** Use dot notation in a string field path, such as `{ "contact.city": "Bengaluru" }`.
6. **Question:** How can a query distinguish a missing field from a field set to `null`?  
   **Answer:** Combine a null comparison with `$exists` to test whether the field is present.
7. **Question:** Why should the stored BSON type match the query value's type?  
   **Answer:** Values of different types can compare differently, causing a filter to miss records or behave unexpectedly.
8. **Question:** What does an empty filter match?  
   **Answer:** It matches every document in the collection.

## 6. Projection, sorting, limits, and cursors

1. **Question:** What is a projection used for?  
   **Answer:** It selects which fields are returned by a query.
2. **Question:** Which projection field is included by default?  
   **Answer:** `_id` is included unless it is explicitly excluded.
3. **Question:** Why add `_id` as a tie-breaker when sorting by a non-unique field?  
   **Answer:** It gives documents with equal primary sort values a stable unique order.
4. **Question:** Why is `toArray()` risky for an unbounded result set?  
   **Answer:** It loads all results into application memory and can exhaust memory for a large query.
5. **Question:** How does `limit()` help control a query response?  
   **Answer:** It caps the number of documents returned by the cursor.
6. **Question:** What is the main drawback of large `skip()` offsets?  
   **Answer:** The server may need to scan past many earlier records before returning the requested page.
7. **Question:** How does range pagination identify where the next page begins?  
   **Answer:** It filters on a value greater than or less than the final sort key from the previous page.
8. **Question:** What should an application do if cursor iteration stops early?  
   **Answer:** Close the cursor or otherwise release its resources.

## 7. Update and delete documents safely

1. **Question:** What is the difference between an update operator and a replacement document?  
   **Answer:** An update operator changes selected fields. A replacement document replaces the record's fields while retaining its `_id`.
2. **Question:** How can you change one nested field without replacing its whole parent object?  
   **Answer:** Use dot notation with `$set`, such as `{ $set: { "contact.city": "Mysuru" } }`.
3. **Question:** When is `$inc` useful?  
   **Answer:** It atomically adds or subtracts a numeric amount, such as incrementing a counter.
4. **Question:** How do `$push` and `$addToSet` differ?  
   **Answer:** `$push` always appends the value. `$addToSet` appends it only if an equal value is not already present.
5. **Question:** What does `upsert: true` do when a filter matches no document?  
   **Answer:** It inserts a new document based on the filter and update operation.
6. **Question:** Which fields disappear when `replaceOne()` replaces a document?  
   **Answer:** Fields absent from the replacement are removed; `_id` remains the document identifier.
7. **Question:** Why preview a filter with `find()` before calling `deleteMany()`?  
   **Answer:** The preview exposes an overly broad filter before it removes multiple records.
8. **Question:** What does an empty delete filter match?  
   **Answer:** It matches every document, so `deleteMany({})` removes the entire collection's documents.

## 8. Data modeling with embedding and references

1. **Question:** What questions should be answered before choosing a document shape?  
   **Answer:** Identify which data is read together, which values change independently, how lists grow, and what consistency each operation needs.
2. **Question:** Why can an order store a snapshot of item names and prices?  
   **Answer:** The order can preserve the details that applied when it was placed, even if the product later changes.
3. **Question:** What is one benefit of embedding values that are read together?  
   **Answer:** The application can retrieve them in one document read, and related field updates can be atomic.
4. **Question:** What responsibility stays with the application when documents store references?  
   **Answer:** The application decides how to handle a reference whose target is missing and maintains the relationship as needed.
5. **Question:** Give an example of a list that should not grow without a bound inside a document.  
   **Answer:** An account's complete event history can grow indefinitely and should usually be stored as separate event documents.
6. **Question:** When can duplicated values make sense?  
   **Answer:** A small snapshot can simplify common reads or preserve historical values when the source record changes.
7. **Question:** How might a many-to-many relationship use a linking collection?  
   **Answer:** Store one document per relationship with identifiers for both related records, plus any relationship-specific fields.
8. **Question:** Why is there no single modeling rule that suits every relationship?  
   **Answer:** Reads, writes, growth, sharing, and consistency requirements differ between applications.

## 9. Indexes and query plans

1. **Question:** What work can an index reduce for a read query?  
   **Answer:** It can help locate matching documents or provide their requested order without scanning every document.
2. **Question:** What costs do extra indexes add?  
   **Answer:** They use disk and memory and require updates during inserts, changes, and deletes.
3. **Question:** What does the order of fields in a compound index affect?  
   **Answer:** It affects which query prefixes and sort patterns the index can support efficiently.
4. **Question:** What does a `COLLSCAN` stage indicate?  
   **Answer:** MongoDB scanned documents in the collection to evaluate the query.
5. **Question:** Which `explain()` values compare examined data with returned results?  
   **Answer:** `nReturned`, `totalDocsExamined`, and `totalKeysExamined` help compare the work with the output.
6. **Question:** When is a unique index useful?  
   **Answer:** Use it when duplicate values would violate a collection-wide rule, such as one account per email address.
7. **Question:** What does a TTL index do, and why is it not an exact timer?  
   **Answer:** It makes expired documents eligible for removal, but background cleanup does not guarantee deletion at an exact instant.
8. **Question:** Why should indexes be based on real query patterns?  
   **Answer:** Each index has write and storage costs, so it should support an actual workload.

## 10. Aggregation pipelines

1. **Question:** What does an aggregation pipeline do?  
   **Answer:** It processes documents through stages that filter, reshape, group, join, or sort them into a result.
2. **Question:** How does the order of stages affect the data passed forward?  
   **Answer:** Each stage receives the previous stage's output, so earlier transformations determine the documents and fields later stages can use.
3. **Question:** What does `$unwind` do to an array?  
   **Answer:** It emits a pipeline document for each array element.
4. **Question:** How does `$group` choose which documents belong together?  
   **Answer:** Documents with the same value for the `$group` stage's `_id` expression are grouped together.
5. **Question:** When could an early `$match` help a pipeline?  
   **Answer:** It can reduce how many documents later stages need to process, especially when an index supports the filter.
6. **Question:** What does `$lookup` add to each input record?  
   **Answer:** It adds an array of matching records from another collection under the field named by `as`.
7. **Question:** Why inspect the result shape after `$unwind` and `$group`?  
   **Answer:** Those stages can change the number of records and the meaning or type of fields.
8. **Question:** How should pipeline performance be checked?  
   **Answer:** Use representative data, inspect `explain()` output, and measure the actual workload.

## 11. Schema validation and data consistency

1. **Question:** Why can a flexible collection still benefit from schema validation?  
   **Answer:** Validation can enforce required fields, known types, and allowed values while leaving room for optional fields.
2. **Question:** What is the purpose of the `required` list?  
   **Answer:** It names fields that must be present in every document accepted by the validator.
3. **Question:** How does `validationAction: "error"` differ from `"warn"`?  
   **Answer:** `error` rejects invalid writes. `warn` allows them while recording a warning.
4. **Question:** What happens when an invalid write is rejected?  
   **Answer:** MongoDB returns a validation error and does not apply that write.
5. **Question:** What kind of rule is better enforced with a unique index?  
   **Answer:** A uniqueness rule across documents, such as requiring each email value to appear once.
6. **Question:** Why inspect existing documents before tightening a validator?  
   **Answer:** Existing records or old write paths may violate the new rule and cause future writes or migrations to fail.
7. **Question:** How can a validator be changed after a collection exists?  
   **Answer:** Use the `collMod` command to change the collection's validation options.
8. **Question:** Why should bypassing validation be limited to deliberate operations?  
   **Answer:** A bypass can admit malformed data that breaks assumptions in application code and later queries.

## 12. Atomicity and transactions

1. **Question:** What does atomicity mean for a single-document write?  
   **Answer:** The write is applied as one unit, so other operations do not see only part of that document change.
2. **Question:** How can a filter enforce a balance condition in the same operation as an update?  
   **Answer:** Include a condition such as `balanceCents: { $gte: amountCents }` in the update filter and decrement with `$inc`.
3. **Question:** When is a multi-document transaction useful?  
   **Answer:** It is useful when several document changes must either all commit or all be rolled back together.
4. **Question:** What happens if an operation in a transaction throws before commit?  
   **Answer:** The transaction does not commit its partial changes; the transaction is aborted or the callback reports an error.
5. **Question:** Why should all transaction operations use the same session?  
   **Answer:** The session associates those operations with the same transaction boundary.
6. **Question:** Why should external side effects stay outside retryable transaction work?  
   **Answer:** A driver may retry database work, which could otherwise send an email or charge a service more than once.
7. **Question:** Which deployment types support multi-document transactions?  
   **Answer:** Replica sets and sharded clusters support them; a standalone server does not.
8. **Question:** Why should a transaction not replace careful document modeling?  
   **Answer:** Transactions add coordination cost, while a model aligned with common reads and writes can keep operations simpler.

## 13. Replica sets and read/write concerns

1. **Question:** What role does the primary play in a replica set?  
   **Answer:** It accepts writes and records operations for the other members to copy.
2. **Question:** How do secondary members receive data?  
   **Answer:** They replicate operations from the primary and apply them to their own data copies.
3. **Question:** What can happen if a primary becomes unavailable?  
   **Answer:** The remaining eligible members can elect a new primary.
4. **Question:** What does `w: "majority"` ask MongoDB to wait for?  
   **Answer:** It waits for acknowledgment from a majority of voting data-bearing members.
5. **Question:** What risk comes with reading from a secondary?  
   **Answer:** The secondary may lag and return data that does not include the latest write.
6. **Question:** How do read preference and read concern differ?  
   **Answer:** Read preference selects which members may serve a read. Read concern controls what consistency level the read requires.
7. **Question:** Why do replica-set copies not replace backups?  
   **Answer:** Replicated deletions and corruption also reach other members, while backups provide recoverable copies from an earlier point.
8. **Question:** Which `mongosh` command shows replica-set status?  
   **Answer:** Run `rs.status()` while connected to a replica-set member.

## 14. Sharding and horizontal scaling

1. **Question:** What problem does sharding help solve?  
   **Answer:** It distributes data and workload across servers when a single server no longer meets capacity needs.
2. **Question:** How does sharding differ from replication?  
   **Answer:** Sharding partitions data across shards. Replication keeps copies of data on multiple members for availability.
3. **Question:** What is the role of `mongos`?  
   **Answer:** It routes client requests to the shard or shards that own the matching data.
4. **Question:** Why is shard-key choice difficult to change after a collection grows?  
   **Answer:** The key determines how existing and future data is distributed and how queries are routed, so changing it can require a significant data movement operation.
5. **Question:** What does cardinality describe for a shard key?  
   **Answer:** It describes how many distinct values the key can take.
6. **Question:** Why can a monotonically increasing key create uneven write traffic?  
   **Answer:** New values can all land in the same end range, concentrating writes on one shard.
7. **Question:** What is a scatter-gather query?  
   **Answer:** It is a query routed to multiple or all shards because it cannot target a specific shard from its filter.
8. **Question:** Why should a team measure a scaling need before sharding?  
   **Answer:** Sharding adds operational complexity and is unnecessary if a simpler deployment already meets the workload.

## 15. Security, backups, and operations

1. **Question:** Why should an application use a restricted database role?  
   **Answer:** Least privilege limits the data and operations available if the application or its credentials are misused.
2. **Question:** What is the purpose of limiting network access?  
   **Answer:** It reduces the number of systems that can attempt to connect to the database.
3. **Question:** Where should connection credentials be kept?  
   **Answer:** Store them in protected environment configuration or a secrets manager, not in source control.
4. **Question:** What tools can create and restore a BSON backup?  
   **Answer:** `mongodump` creates a dump and `mongorestore` restores it.
5. **Question:** Why restore a practice backup into a separate database?  
   **Answer:** It verifies recovery without overwriting the original source data.
6. **Question:** Why is a replica set not a historical backup?  
   **Answer:** It copies current changes, including accidental deletions, rather than preserving an earlier independent state.
7. **Question:** Name four useful operational signals to watch.  
   **Answer:** Examples include connection failures, query latency, CPU use, disk capacity, replication lag, and error rates.
8. **Question:** What should a recovery exercise verify beyond whether a command exits successfully?  
   **Answer:** It should confirm expected collections, data, indexes, validation rules, access, and that the application can use the restored database.

## 16. MongoDB with the Node.js driver

1. **Question:** Which package provides the official MongoDB driver for Node.js?  
   **Answer:** Install the `mongodb` npm package.
2. **Question:** Why create and reuse one `MongoClient` for an application process?  
   **Answer:** The client manages connection pools, and reusing it avoids repeatedly creating pools and connections.
3. **Question:** Where should a connection URI with credentials be stored?  
   **Answer:** Use protected environment configuration or the hosting platform's secrets service.
4. **Question:** Why close the client in a `finally` block in a one-off script?  
   **Answer:** The `finally` block runs after success or failure, allowing the script to release its connections.
5. **Question:** Why should a web server avoid creating one client per request?  
   **Answer:** Each client can create a pool, wasting resources and increasing connection overhead.
6. **Question:** How can a filter and update object safely change a task status?  
   **Answer:** Match the task identifier and its current status, then use `$set` to set the new status and completion time.
7. **Question:** Why should an API bound its result size?  
   **Answer:** A maximum page size controls database work, network transfer, and application memory use.
8. **Question:** What should an application avoid including in public error responses and logs?  
   **Answer:** Do not expose credentials, connection strings, sensitive records, or raw internal stack traces to public clients.
