# 5. Find documents with query operators

[Back to notes index](../README.md)

| [Previous: Insert documents and understand BSON types](./04-insert-documents-and-bson-types.md) | [Notes index](../README.md) | [Next: Projection, sorting, limits, and cursors](./06-projection-sorting-limits-and-cursors.md) |
| --- | --- | --- |

## Match exact values

`find()` accepts a query document. A field with a plain value matches documents whose field has that value:

~~~javascript
db.books.find({ available: true })
db.books.find({ author: "Mina Rao" })
~~~

When a query includes more than one field, MongoDB requires all of those conditions to match. This is an implicit AND:

~~~javascript
db.books.find({ available: true, pages: 240 })
~~~

## Compare values

Comparison operators begin with `$`. They support ranges, inequality, and membership checks:

~~~javascript
db.books.find({ pages: { $gte: 200, $lt: 300 } })
db.books.find({ pages: { $ne: 240 } })
db.books.find({ title: { $in: ["Query Basics", "Index Notes"] } })
~~~

Common operators include `$eq`, `$ne`, `$gt`, `$gte`, `$lt`, `$lte`, `$in`, and `$nin`. Match the BSON type used by the stored value. A numeric query should not be written as a string unless the data is stored as text.

## Combine conditions

Use `$or` when either condition may match. Use `$and` when explicit grouping makes the intended logic clearer:

~~~javascript
db.books.find({
  $or: [
    { pages: { $lt: 200 } },
    { tags: "beginner" }
  ]
})

db.books.find({
  $and: [
    { available: true },
    { pages: { $gte: 200 } }
  ]
})
~~~

Most simple AND queries are clearer as several fields in one query document. Use explicit `$and` when the same field needs multiple separate expressions that cannot be expressed in one field condition.

## Query arrays and embedded documents

An array query can match a value contained in the array. `$all` requires every listed value to occur. `$elemMatch` ensures multiple conditions apply to the same array element:

~~~javascript
db.books.find({ tags: "beginner" })
db.books.find({ tags: { $all: ["beginner", "queries"] } })

db.courses.find({
  lessons: {
    $elemMatch: { title: "Indexes", minutes: { $gte: 20 } }
  }
})
~~~

Use dot notation to query a nested field:

~~~javascript
db.learners.find({ "contact.city": "Bengaluru" })
~~~

The field name is a string containing the path. It does not mean that the document has a literal field named `contact.city`.

## Check field presence and types

`$exists` checks whether a field is present. `$type` checks a stored BSON type:

~~~javascript
db.learners.find({ phone: { $exists: true } })
db.products.find({ price: { $type: "decimal" } })
~~~

A field set to `null` and a field that is missing are different states. Queries for `null` can match both null-valued and missing fields, so pair the query with `$exists` when the distinction matters.

## Test filters before using them to write

Use the same filter with `find()` before an update or delete. This shows which documents it currently selects:

~~~javascript
const filter = { active: false, updatedAt: { $lt: new Date("2026-01-01") } }
db.learners.find(filter)
~~~

A filter `{}` matches every document. That can be useful for a deliberate read, but it is dangerous in an update or delete when you intended to target only a subset.

## Check what you learned

1. How does MongoDB combine two ordinary fields in one query document?
2. Which operator finds values greater than or equal to a threshold?
3. How does `$in` differ from `$all` when matching an array?
4. What problem does `$elemMatch` solve?
5. How do you query a nested field such as `contact.city`?
6. How can a query distinguish a missing field from a field set to `null`?
7. Why should the stored BSON type match the query value's type?
8. What does an empty filter match?

## References

- [Specify a query](https://www.mongodb.com/docs/manual/tutorial/query-documents/)
- [Query and projection operators](https://www.mongodb.com/docs/manual/reference/operator/query/)
- [Query arrays](https://www.mongodb.com/docs/manual/tutorial/query-arrays/)
- [Query embedded documents](https://www.mongodb.com/docs/manual/tutorial/query-embedded-documents/)
