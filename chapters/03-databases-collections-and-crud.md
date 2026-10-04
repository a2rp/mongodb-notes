# 3. Databases, collections, and CRUD overview

[Back to notes index](../README.md)

| [Previous: Install MongoDB and connect with mongosh](./02-install-and-connect-with-mongosh.md) | [Notes index](../README.md) | [Next: Insert documents and understand BSON types](./04-insert-documents-and-bson-types.md) |
| --- | --- | --- |

## Organize stored data

A MongoDB deployment can contain multiple databases. A database contains collections, and a collection contains documents. Use names that describe the data so the purpose of a command stays clear when you return to it later.

The shell variable `db` represents the database currently selected in `mongosh`. A collection can be selected as a property of that database:

~~~javascript
use study_notes

const learners = db.learners
learners.getName()
~~~

The `learners` variable is a shell convenience. It does not create the collection until an operation writes data or the collection is explicitly created.

## Create a collection when needed

MongoDB creates a collection automatically on its first write. Explicit creation is useful when you need collection options such as validation or capped behavior:

~~~javascript
db.createCollection("courses")
show collections
~~~

Collection names should be consistent. A common convention is lowercase plural nouns, such as `learners`, `courses`, and `orders`. The convention helps readability but is not required by MongoDB.

## CRUD means four actions

CRUD stands for create, read, update, and delete. MongoDB exposes methods for these operations on a collection:

| Goal | Common method | What it does |
| --- | --- | --- |
| Create | `insertOne()` or `insertMany()` | Adds one or more documents |
| Read | `findOne()` or `find()` | Finds matching documents |
| Update | `updateOne()` or `updateMany()` | Changes matching documents |
| Delete | `deleteOne()` or `deleteMany()` | Removes matching documents |

A CRUD cycle can be run against one practice collection:

~~~javascript
const courses = db.courses

courses.insertOne({ title: "MongoDB basics", level: "beginner", active: true })
courses.find({ active: true })
courses.updateOne(
  { title: "MongoDB basics" },
  { $set: { level: "foundation" } }
)
courses.deleteOne({ title: "MongoDB basics" })
~~~

The filter in each update or delete identifies the documents affected. For destructive operations, test the filter with `find()` first and prefer a specific identifier or condition.

## Understand operation results

Insert methods return an inserted identifier. Update methods report how many documents matched and how many changed. Delete methods report how many documents were removed. These results help an application distinguish a successful request from a filter that matched nothing.

~~~javascript
const result = db.courses.updateOne(
  { title: "Indexes" },
  { $set: { active: true } }
)

result.matchedCount
result.modifiedCount
~~~

A matched document may not be modified when the requested value already equals the stored value. Read the result fields according to what the operation needs to confirm.

## Work in a practice database

Use a separate database for experiments. Before running a delete, inspect the target set:

~~~javascript
const filter = { level: "temporary" }
db.courses.find(filter)
db.courses.deleteMany(filter)
~~~

If the filter is too broad, the preview makes that visible before data is removed. Do not use a production database for first attempts at unfamiliar commands.

## Check what you learned

1. Describe the relationship between a deployment, database, collection, and document.
2. When does MongoDB create a collection automatically?
3. What does CRUD stand for?
4. Which method returns a cursor for multiple matching documents?
5. What information do `matchedCount` and `modifiedCount` provide?
6. Why can an update match a document but report no modification?
7. What should you do before calling `deleteMany()`?
8. Why is a dedicated practice database useful?

## References

- [Databases and collections](https://www.mongodb.com/docs/manual/core/databases-and-collections/)
- [Insert documents](https://www.mongodb.com/docs/manual/crud/)
- [Query documents](https://www.mongodb.com/docs/manual/crud/)
- [Update documents](https://www.mongodb.com/docs/manual/crud/)
- [Delete documents](https://www.mongodb.com/docs/manual/crud/)

