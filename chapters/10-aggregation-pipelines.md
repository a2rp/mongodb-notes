# 10. Aggregation pipelines

[Back to notes index](../README.md)

| [Previous: Indexes and query plans](./09-indexes-and-query-plans.md) | [Notes index](../README.md) | [Next: Schema validation and data consistency](./11-schema-validation-and-consistency.md) |
| --- | --- | --- |

## Transform records in stages

An aggregation pipeline sends documents through an ordered sequence of stages. Each stage filters, reshapes, groups, joins, or sorts the documents before passing its result to the next stage. The final result is a cursor.

A useful way to read a pipeline is from top to bottom: identify the starting collection, note what each stage receives, and check the fields that remain in the output.

## Filter, group, and sort

Assume paid orders contain an `items` array. This pipeline filters to paid orders, expands each item into its own working document, groups by SKU, calculates units and gross cents, then returns the ten highest totals:

~~~javascript
db.orders.aggregate([
  { $match: { status: "paid" } },
  { $unwind: "$items" },
  {
    $group: {
      _id: "$items.sku",
      units: { $sum: "$items.quantity" },
      grossCents: {
        $sum: {
          $multiply: ["$items.unitPriceCents", "$items.quantity"]
        }
      }
    }
  },
  { $sort: { grossCents: -1 } },
  { $limit: 10 },
  {
    $project: {
      _id: 0,
      sku: "$_id",
      units: 1,
      grossCents: 1
    }
  }
])
~~~

`$unwind` creates one pipeline document per array element. `$group` collects documents that share the same `_id` expression. `$sum` and `$multiply` compute numeric values. `$project` shapes the final result.

## Common stages

| Stage | Purpose |
| --- | --- |
| `$match` | Keep documents that satisfy a query filter |
| `$project` | Include, remove, rename, or calculate fields |
| `$set` | Add or replace fields while retaining other fields |
| `$unwind` | Emit one pipeline document for each array element |
| `$group` | Collect documents by a key and calculate summaries |
| `$sort` | Order the pipeline output |
| `$limit` | Keep only a bounded number of results |
| `$lookup` | Match related documents from another collection |

A `$lookup` can add related records to each input document. It is useful when the query needs a join-like result, but modeling and indexes should be reviewed so a lookup is not doing avoidable work.

~~~javascript
db.orders.aggregate([
  {
    $lookup: {
      from: "customers",
      localField: "customerId",
      foreignField: "_id",
      as: "customer"
    }
  },
  { $unwind: "$customer" },
  { $project: { orderNumber: 1, "customer.name": 1, status: 1 } }
])
~~~

## Make pipelines easier to reason about

- Start with a selective `$match` when it can reduce the documents entering later work.
- Use indexes to support the initial collection filter and sort where possible.
- Keep only the fields needed by later stages when that improves clarity.
- Check the shape after `$unwind` and `$group`; these stages can change the number and meaning of documents.
- Use `explain()` and representative data to investigate performance.
- Remember that results have document size and memory limits. Large outputs should be paged or written intentionally.

The query optimizer can perform some field pruning automatically. Do not add stages solely because they seem likely to make a pipeline faster; measure the actual plan.

## Check what you learned

1. What does an aggregation pipeline do?
2. How does the order of stages affect the data passed forward?
3. What does `$unwind` do to an array?
4. How does `$group` choose which documents belong together?
5. When could an early `$match` help a pipeline?
6. What does `$lookup` add to each input record?
7. Why inspect the result shape after `$unwind` and `$group`?
8. How should pipeline performance be checked?

## References

- [Aggregation operations](https://www.mongodb.com/docs/manual/aggregation/)
- [Aggregation pipeline stages](https://www.mongodb.com/docs/manual/reference/operator/aggregation-pipeline/)
- [Aggregation stages reference](https://www.mongodb.com/docs/manual/reference/operator/aggregation-pipeline/#aggregation-stages)
- [Aggregation limits](https://www.mongodb.com/docs/manual/reference/limits/)
