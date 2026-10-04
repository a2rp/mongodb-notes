# 6. Projection, sorting, limits, and cursors

[Back to notes index](../README.md)

| [Previous: Find documents with query operators](./05-query-filters-and-operators.md) | [Notes index](../README.md) | [Next: Update and delete documents safely](./07-update-and-delete-documents.md) |
| --- | --- | --- |

## Return only the fields the caller needs

A projection controls which fields appear in query results. Include fields with `1`, or exclude fields with `0`. The `_id` field is included by default unless it is explicitly excluded:

~~~javascript
db.books.find(
  { available: true },
  { title: 1, author: 1, _id: 0 }
)
~~~

An inclusion projection and an exclusion projection cannot normally be mixed, except for excluding `_id`. Projection can reduce the amount of data sent to an application and prevent unrelated fields from being returned.

## Sort and limit results

A sort document uses `1` for ascending and `-1` for descending order. Add a unique tie-breaker when multiple documents may have the same primary sort value:

~~~javascript
db.books.find({ available: true })
  .sort({ title: 1, _id: 1 })
  .limit(10)
~~~

Sorting by `_id` as a tie-breaker makes the order stable when titles repeat. A `limit()` bounds the number of returned documents. Without a sort, the order should not be treated as a meaningful sequence.

## Work with a cursor

`find()` returns a cursor, not an array containing every matching record. A cursor lets the driver retrieve results in batches. In `mongosh`, you can chain cursor options before iterating:

~~~javascript
const cursor = db.books.find({ available: true })
  .project({ title: 1, author: 1 })
  .sort({ title: 1, _id: 1 })
  .limit(10)

cursor.forEach((book) => print(book.title))
~~~

In a Node.js application, the official driver can convert a bounded cursor to an array. Avoid calling `toArray()` for an unbounded result set because it loads all returned documents into application memory.

~~~javascript
const books = await collection.find({ available: true })
  .project({ title: 1 })
  .sort({ title: 1, _id: 1 })
  .limit(20)
  .toArray()
~~~

## Paginate with a stable order

`skip()` is simple for small offsets:

~~~javascript
db.books.find({ available: true })
  .sort({ _id: 1 })
  .skip(20)
  .limit(10)
~~~

For deep pages, the server may need to scan past many earlier results. Range pagination uses the last value from the previous page as a boundary. With ObjectIds sorted in ascending order:

~~~javascript
const lastSeenId = ObjectId("66f1a47f5e7a123456789012")

db.books.find({
  available: true,
  _id: { $gt: lastSeenId }
})
  .sort({ _id: 1 })
  .limit(10)
~~~

The application returns the final `_id` from the current page as a cursor token for the next request. If sorting by a non-unique field, include a unique tie-breaker and encode both values in the token.

## Understand cursor resource use

A cursor can hold server resources. Consume it promptly, limit the result set, and close it when iteration stops early in application code. Set a maximum page size in APIs so a client cannot request an unexpectedly large response.

## Check what you learned

1. What is a projection used for?
2. Which projection field is included by default?
3. Why add `_id` as a tie-breaker when sorting by a non-unique field?
4. Why is `toArray()` risky for an unbounded result set?
5. How does `limit()` help control a query response?
6. What is the main drawback of large `skip()` offsets?
7. How does range pagination identify where the next page begins?
8. What should an application do if cursor iteration stops early?

## References

- [Project fields from query results](https://www.mongodb.com/docs/manual/tutorial/project-fields-from-query-results/)
- [Sort query results](https://www.mongodb.com/docs/manual/tutorial/sort-results-with-indexes/)
- [Limit query results](https://www.mongodb.com/docs/manual/reference/method/cursor.limit/)
- [Node.js driver cursors](https://www.mongodb.com/docs/drivers/node/current/crud/query/cursor/)
