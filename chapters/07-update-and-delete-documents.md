# 7. Update and delete documents safely

[Back to notes index](../README.md)

| [Previous: Projection, sorting, limits, and cursors](./06-projection-sorting-limits-and-cursors.md) | [Notes index](../README.md) | [Next: Data modeling with embedding and references](./08-data-modeling-embedding-and-references.md) |
| --- | --- | --- |

## Change selected fields with update operators

`updateOne()` and `updateMany()` take a filter and an update document. Use update operators to change selected fields without replacing the rest of the document:

~~~javascript
db.learners.updateOne(
  { email: "mina@example.com" },
  {
    $set: { level: "intermediate" },
    $currentDate: { updatedAt: true }
  }
)
~~~

`$set` creates a field if it is missing or changes its value. Dot notation can update part of an embedded document while keeping its other fields:

~~~javascript
db.learners.updateOne(
  { email: "mina@example.com" },
  { $set: { "contact.city": "Mysuru" } }
)
~~~

Setting the whole `contact` object would replace its previous fields. Use the path that matches the intended change.

## Use numeric and array operators

`$inc` adjusts a number atomically. `$push` appends an array item, while `$addToSet` appends only if an equal value is not already present:

~~~javascript
db.books.updateOne(
  { title: "Query Basics" },
  { $inc: { views: 1 }, $addToSet: { tags: "beginner" } }
)

db.learners.updateOne(
  { email: "mina@example.com" },
  { $push: { courses: "Aggregation" } }
)
~~~

Use `$pull` to remove array items that match a value or condition. Use `$unset` to remove a field. Choose `$push` when duplicates are meaningful and `$addToSet` when the array should contain unique values.

## Understand upsert and replacement

An upsert inserts a new document if the filter matches nothing. It is useful for idempotent update patterns when the filter identifies a stable key:

~~~javascript
db.settings.updateOne(
  { key: "theme" },
  { $set: { value: "dark" } },
  { upsert: true }
)
~~~

`replaceOne()` replaces the matched document with a new document, except for its `_id`. Fields not included in the replacement are removed. Use it when replacing the entire record is intentional. For a partial change, use an update operator.

## Preview before deleting

`deleteOne()` removes one matching document and `deleteMany()` removes every document matching the filter. Preview a filter before deleting:

~~~javascript
const oldDrafts = { status: "draft", createdAt: { $lt: new Date("2025-01-01") } }
db.articles.find(oldDrafts)
db.articles.deleteMany(oldDrafts)
~~~

An empty filter `{}` matches every document. Use it for a complete collection cleanup only when that is the explicit intent. `drop()` removes an entire collection and its indexes, so it is more destructive than deleting a selected set of documents.

## Read operation results

For an update, inspect `matchedCount`, `modifiedCount`, and, when using upsert, `upsertedId`. For a delete, inspect `deletedCount`. In application code, these values help you decide whether a record was found or whether the requested change happened.

~~~javascript
const result = db.learners.deleteOne({ email: "old@example.com" })
result.deletedCount
~~~

## Check what you learned

1. What is the difference between an update operator and a replacement document?
2. How can you change one nested field without replacing its whole parent object?
3. When is `$inc` useful?
4. How do `$push` and `$addToSet` differ?
5. What does `upsert: true` do when a filter matches no document?
6. Which fields disappear when `replaceOne()` replaces a document?
7. Why preview a filter with `find()` before calling `deleteMany()`?
8. What does an empty delete filter match?

## References

- [MongoDB update operations](https://www.mongodb.com/docs/manual/crud/#update-operations)
- [Update operators](https://www.mongodb.com/docs/manual/reference/operator/update/)
- [MongoDB delete operations](https://www.mongodb.com/docs/manual/crud/#delete-operations)
- [Node.js driver update documents](https://www.mongodb.com/docs/drivers/node/current/crud/update/modify/)
