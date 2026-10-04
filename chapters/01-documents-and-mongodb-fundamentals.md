# 1. Documents and MongoDB fundamentals

[Back to notes index](../README.md)

| [Previous: Notes index](../README.md) | [Notes index](../README.md) | [Next: Install MongoDB and connect with mongosh](02-install-and-connect-with-mongosh.md) |
| --- | --- | --- |

## What MongoDB stores

MongoDB is a document database. It stores related records as documents grouped into collections. A document is made of field and value pairs, much like a JavaScript object. The shape can vary between documents in the same collection, although applications usually benefit from a consistent shape for related records.

A relational database commonly organizes data into tables and rows. MongoDB organizes it into databases, collections, and documents:

| MongoDB term | Meaning | Familiar comparison |
| --- | --- | --- |
| Database | A named container for collections | A database containing related tables |
| Collection | A group of documents | A table, with a flexible document shape |
| Document | One stored record | A row represented as field and value pairs |
| Field | A named value inside a document | A column value |

The comparison helps at first, but documents can contain nested objects and arrays. That lets one record hold data that is commonly read together.

## Read a document shape

Here is a learner record with an embedded contact object and a list of enrolled courses:

~~~javascript
{
  _id: ObjectId("66f1a47f5e7a123456789012"),
  name: "Mina",
  email: "mina@example.com",
  contact: {
    city: "Bengaluru",
    country: "India"
  },
  courses: ["MongoDB basics", "JavaScript"]
}
~~~

`_id` identifies the document within its collection. MongoDB creates an ObjectId when an inserted document does not provide `_id`. The nested `contact` value is an embedded document. `courses` is an array. Fields can hold strings, numbers, booleans, dates, arrays, nested documents, and other BSON values.

## BSON and JSON

BSON is the binary representation MongoDB uses for documents. Its types include the familiar JSON values and additional types such as dates, binary data, ObjectId, and decimal values. JSON is useful for exchanging data, but plain JSON cannot represent every BSON type without a convention.

In `mongosh`, a date can be created with JavaScript's `Date` constructor:

~~~javascript
{
  name: "Mina",
  enrolledAt: new Date("2026-10-04T00:00:00Z"),
  active: true
}
~~~

A date value is stored as a BSON Date, rather than as the literal text shown in the source. This matters when sorting or filtering by time. Store a value using the type that matches how the application will use it.

## Create a practice database

Run these commands in `mongosh`. They switch to a database named `study_notes` and insert two small records into a `learners` collection:

~~~javascript
use study_notes

db.learners.insertMany([
  {
    name: "Mina",
    level: "beginner",
    active: true,
    courses: ["MongoDB basics", "JavaScript"],
    contact: { city: "Bengaluru", country: "India" }
  },
  {
    name: "Ravi",
    level: "intermediate",
    active: true,
    courses: ["Indexes"],
    contact: { city: "Pune", country: "India" }
  }
])

db.learners.find()
~~~

MongoDB creates the database and collection when data is first written. Merely switching to a new database name does not necessarily create a stored database. `find()` returns a cursor that displays matching documents in the shell.

## Choose a useful document boundary

A document should represent a useful unit that the application creates, reads, or updates. For a blog, a post may contain its title, body, author identifier, and a short list of tags. If comments are always loaded with a post and their number is limited, embedding a comments array may fit. If a post can have an unbounded number of comments, keeping comments in a separate collection avoids a document that grows without a practical limit.

Embedding can make a common read simple and lets MongoDB update the fields in one document atomically. References can suit data that is shared, independently updated, or too large or unbounded to embed. These are design choices based on access patterns, not a rule to embed every relationship.

MongoDB documents have a maximum BSON size of 16 MiB. This is one reason to keep unbounded histories and large files out of a single document. GridFS is available for files larger than the document limit.

## Check what you learned

1. Explain the relationship between a database, collection, and document.
2. In the learner example, identify one embedded document and one array.
3. What does the `_id` field do?
4. Why is BSON more expressive than plain JSON?
5. What happens when `mongosh` switches to a database that has no stored data yet?
6. Give one case where embedding related values could simplify a read.
7. Give one case where a reference or separate collection could be a better fit.
8. Why should an unbounded list not grow inside one document?

## References

- [MongoDB documents](https://www.mongodb.com/docs/manual/core/document/)
- [BSON types](https://www.mongodb.com/docs/manual/reference/bson-types/)
- [MongoDB data modeling](https://www.mongodb.com/docs/manual/data-modeling/)
- [MongoDB limits](https://www.mongodb.com/docs/manual/reference/limits/)
