# 4. Insert documents and understand BSON types

[Back to notes index](../README.md)

| [Previous: Databases, collections, and CRUD overview](./03-databases-collections-and-crud.md) | [Notes index](../README.md) | [Next: Find documents with query operators](./05-query-filters-and-operators.md) |
| --- | --- | --- |

## Insert one document

`insertOne()` adds one document to a collection. If the document does not contain `_id`, MongoDB creates an ObjectId for it:

~~~javascript
const result = db.books.insertOne({
  title: "A Practical Database",
  author: "Mina Rao",
  pages: 240,
  available: true
})

result.insertedId
db.books.findOne({ _id: result.insertedId })
~~~

Keeping the returned identifier makes it easy to fetch the exact record that was inserted. ObjectIds are commonly used identifiers, but an application can supply another unique value when that fits its design.

## Insert multiple documents

Use `insertMany()` when several documents belong in one operation. Each document still needs a unique `_id`:

~~~javascript
db.books.insertMany([
  { title: "Query Basics", pages: 180, tags: ["queries", "beginner"] },
  { title: "Index Notes", pages: 210, tags: ["indexes"] },
  { title: "Aggregation Notes", pages: 260, tags: ["aggregation"] }
])
~~~

If an insert contains a duplicate `_id`, MongoDB rejects the duplicate because `_id` has a unique index. For bulk imports, decide how the application should handle a partial result or an error. Do not assume a large import is one all-or-nothing transaction.

## Use values with the right type

MongoDB stores BSON values. Common types include strings, integers, doubles, booleans, dates, arrays, embedded documents, ObjectIds, and decimal values. Types affect comparisons and arithmetic, so two values that look similar in a display can behave differently if one is a string and another is numeric.

~~~javascript
db.products.insertOne({
  name: "Notebook",
  quantity: 12,
  price: Decimal128("19.95"),
  active: true,
  createdAt: new Date("2026-10-04T12:00:00Z"),
  tags: ["paper", "stationery"],
  dimensions: { widthMm: 148, heightMm: 210 }
})
~~~

`Decimal128` is useful when decimal precision matters, such as a monetary amount. JavaScript floating-point numbers cannot exactly represent every decimal fraction. Store a date as a BSON Date when it must be sorted or compared as time. Keep display formatting in the application.

## Inspect stored types

Use `$type` in a query or inspect the document in `mongosh` to understand what was stored:

~~~javascript
db.products.find(
  { name: "Notebook" },
  { name: 1, quantity: 1, price: 1, createdAt: 1 }
)

db.products.aggregate([
  { $match: { name: "Notebook" } },
  { $project: { name: 1, priceType: { $type: "$price" } } }
])
~~~

The shell's output is a display of BSON values. A date may appear in an ISO-like form and an ObjectId in a constructor-like form; those displays do not make them strings.

## Keep document size and shape practical

Documents have a maximum BSON size of 16 MiB. Avoid embedding large files or an unlimited activity log. Store large files with GridFS or in a file store, then keep their metadata and reference in a document. Repeated records can go in their own collection.

Fields in a collection should have a predictable purpose. Optional fields are fine, but using the same field for unrelated types makes queries and updates harder to reason about. Add validation once the expected shape is clear.

## Check what you learned

1. What does `insertOne()` return?
2. What happens when an inserted document has no `_id`?
3. Why does MongoDB reject a duplicate `_id`?
4. Why should a date be stored as a BSON Date instead of formatted text?
5. When can `Decimal128` be a better choice than a JavaScript number?
6. How can a query inspect a field's BSON type?
7. What size limit applies to one BSON document?
8. Where could an unbounded activity log be stored instead of inside one document?

## References

- [Insert documents](https://www.mongodb.com/docs/manual/reference/method/db.collection.insertone/)
- [BSON types](https://www.mongodb.com/docs/manual/reference/bson-types/)
- [ObjectId](https://www.mongodb.com/docs/manual/reference/method/ObjectId/)
- [Document size limits](https://www.mongodb.com/docs/manual/reference/limits/)

