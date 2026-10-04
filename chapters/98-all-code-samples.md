# 98. All code samples

[Back to notes index](../README.md)

| [Previous: MongoDB with the Node.js driver](./16-mongodb-with-nodejs-driver.md) | [Notes index](../README.md) | [Next: Complete questions and answers](./99-complete-q-and-a.md) |
| --- | --- | --- |

## Documents and MongoDB fundamentals

Source: [Open chapter](./01-documents-and-mongodb-fundamentals.md)

### Example 1

~~~~javascript
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
~~~~

### Example 2

~~~~javascript
{
  name: "Mina",
  enrolledAt: new Date("2026-10-04T00:00:00Z"),
  active: true
}
~~~~

### Example 3

~~~~javascript
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
~~~~

## Install MongoDB and connect with mongosh

Source: [Open chapter](./02-install-and-connect-with-mongosh.md)

### Example 1

~~~~sh
mongosh --version
~~~~

### Example 2

~~~~sh
mongosh "mongodb://127.0.0.1:27017"
~~~~

### Example 3

~~~~javascript
 db.runCommand({ ping: 1 })
 use study_notes
 db.getName()
~~~~

### Example 4

~~~~text
mongodb+srv://<username>:<password>@<cluster-host>/<database>?retryWrites=true&w=majority
~~~~

### Example 5

~~~~powershell
$env:MONGODB_URI = "mongodb+srv://<username>:<password>@<cluster-host>/study_notes"
mongosh "$env:MONGODB_URI"
~~~~

### Example 6

~~~~sh
export MONGODB_URI='mongodb+srv://<username>:<password>@<cluster-host>/study_notes'
mongosh "$MONGODB_URI"
~~~~

## Databases, collections, and CRUD overview

Source: [Open chapter](./03-databases-collections-and-crud.md)

### Example 1

~~~~javascript
use study_notes

const learners = db.learners
learners.getName()
~~~~

### Example 2

~~~~javascript
db.createCollection("courses")
show collections
~~~~

### Example 3

~~~~javascript
const courses = db.courses

courses.insertOne({ title: "MongoDB basics", level: "beginner", active: true })
courses.find({ active: true })
courses.updateOne(
  { title: "MongoDB basics" },
  { $set: { level: "foundation" } }
)
courses.deleteOne({ title: "MongoDB basics" })
~~~~

### Example 4

~~~~javascript
const result = db.courses.updateOne(
  { title: "Indexes" },
  { $set: { active: true } }
)

result.matchedCount
result.modifiedCount
~~~~

### Example 5

~~~~javascript
const filter = { level: "temporary" }
db.courses.find(filter)
db.courses.deleteMany(filter)
~~~~

## Insert documents and understand BSON types

Source: [Open chapter](./04-insert-documents-and-bson-types.md)

### Example 1

~~~~javascript
const result = db.books.insertOne({
  title: "A Practical Database",
  author: "Mina Rao",
  pages: 240,
  available: true
})

result.insertedId
db.books.findOne({ _id: result.insertedId })
~~~~

### Example 2

~~~~javascript
db.books.insertMany([
  { title: "Query Basics", pages: 180, tags: ["queries", "beginner"] },
  { title: "Index Notes", pages: 210, tags: ["indexes"] },
  { title: "Aggregation Notes", pages: 260, tags: ["aggregation"] }
])
~~~~

### Example 3

~~~~javascript
db.products.insertOne({
  name: "Notebook",
  quantity: 12,
  price: Decimal128("19.95"),
  active: true,
  createdAt: new Date("2026-10-04T12:00:00Z"),
  tags: ["paper", "stationery"],
  dimensions: { widthMm: 148, heightMm: 210 }
})
~~~~

### Example 4

~~~~javascript
db.products.find(
  { name: "Notebook" },
  { name: 1, quantity: 1, price: 1, createdAt: 1 }
)

db.products.aggregate([
  { $match: { name: "Notebook" } },
  { $project: { name: 1, priceType: { $type: "$price" } } }
])
~~~~

## Find documents with query operators

Source: [Open chapter](./05-query-filters-and-operators.md)

### Example 1

~~~~javascript
db.books.find({ available: true })
db.books.find({ author: "Mina Rao" })
~~~~

### Example 2

~~~~javascript
db.books.find({ available: true, pages: 240 })
~~~~

### Example 3

~~~~javascript
db.books.find({ pages: { $gte: 200, $lt: 300 } })
db.books.find({ pages: { $ne: 240 } })
db.books.find({ title: { $in: ["Query Basics", "Index Notes"] } })
~~~~

### Example 4

~~~~javascript
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
~~~~

### Example 5

~~~~javascript
db.books.find({ tags: "beginner" })
db.books.find({ tags: { $all: ["beginner", "queries"] } })

db.courses.find({
  lessons: {
    $elemMatch: { title: "Indexes", minutes: { $gte: 20 } }
  }
})
~~~~

### Example 6

~~~~javascript
db.learners.find({ "contact.city": "Bengaluru" })
~~~~

### Example 7

~~~~javascript
db.learners.find({ phone: { $exists: true } })
db.products.find({ price: { $type: "decimal" } })
~~~~

### Example 8

~~~~javascript
const filter = { active: false, updatedAt: { $lt: new Date("2026-01-01") } }
db.learners.find(filter)
~~~~

## Projection, sorting, limits, and cursors

Source: [Open chapter](./06-projection-sorting-limits-and-cursors.md)

### Example 1

~~~~javascript
db.books.find(
  { available: true },
  { title: 1, author: 1, _id: 0 }
)
~~~~

### Example 2

~~~~javascript
db.books.find({ available: true })
  .sort({ title: 1, _id: 1 })
  .limit(10)
~~~~

### Example 3

~~~~javascript
const cursor = db.books.find({ available: true })
  .project({ title: 1, author: 1 })
  .sort({ title: 1, _id: 1 })
  .limit(10)

cursor.forEach((book) => print(book.title))
~~~~

### Example 4

~~~~javascript
const books = await collection.find({ available: true })
  .project({ title: 1 })
  .sort({ title: 1, _id: 1 })
  .limit(20)
  .toArray()
~~~~

### Example 5

~~~~javascript
db.books.find({ available: true })
  .sort({ _id: 1 })
  .skip(20)
  .limit(10)
~~~~

### Example 6

~~~~javascript
const lastSeenId = ObjectId("66f1a47f5e7a123456789012")

db.books.find({
  available: true,
  _id: { $gt: lastSeenId }
})
  .sort({ _id: 1 })
  .limit(10)
~~~~

## Update and delete documents safely

Source: [Open chapter](./07-update-and-delete-documents.md)

### Example 1

~~~~javascript
db.learners.updateOne(
  { email: "mina@example.com" },
  {
    $set: { level: "intermediate" },
    $currentDate: { updatedAt: true }
  }
)
~~~~

### Example 2

~~~~javascript
db.learners.updateOne(
  { email: "mina@example.com" },
  { $set: { "contact.city": "Mysuru" } }
)
~~~~

### Example 3

~~~~javascript
db.books.updateOne(
  { title: "Query Basics" },
  { $inc: { views: 1 }, $addToSet: { tags: "beginner" } }
)

db.learners.updateOne(
  { email: "mina@example.com" },
  { $push: { courses: "Aggregation" } }
)
~~~~

### Example 4

~~~~javascript
db.settings.updateOne(
  { key: "theme" },
  { $set: { value: "dark" } },
  { upsert: true }
)
~~~~

### Example 5

~~~~javascript
const oldDrafts = { status: "draft", createdAt: { $lt: new Date("2025-01-01") } }
db.articles.find(oldDrafts)
db.articles.deleteMany(oldDrafts)
~~~~

### Example 6

~~~~javascript
const result = db.learners.deleteOne({ email: "old@example.com" })
result.deletedCount
~~~~

## Data modeling with embedding and references

Source: [Open chapter](./08-data-modeling-embedding-and-references.md)

### Example 1

~~~~javascript
{
  _id: ObjectId("66f1a47f5e7a123456789012"),
  customerId: ObjectId("66f1a47f5e7a123456789013"),
  shippingAddress: {
    city: "Bengaluru",
    postalCode: "560001"
  },
  items: [
    { productId: "notebook-1", name: "Notebook", unitPrice: Decimal128("19.95"), quantity: 2 },
    { productId: "pen-2", name: "Pen", unitPrice: Decimal128("2.50"), quantity: 3 }
  ],
  status: "placed"
}
~~~~

### Example 2

~~~~javascript
const customer = await db.customers.findOne({ _id: customerId })
const orders = await db.orders.find({ customerId }).sort({ placedAt: -1 }).toArray()
~~~~

## Indexes and query plans

Source: [Open chapter](./09-indexes-and-query-plans.md)

### Example 1

~~~~javascript
db.orders.createIndex({ customerId: 1 })
db.orders.find({ customerId: ObjectId("66f1a47f5e7a123456789013") })
~~~~

### Example 2

~~~~javascript
db.orders.createIndex({ customerId: 1, placedAt: -1 })

db.orders.find({ customerId: ObjectId("66f1a47f5e7a123456789013") })
  .sort({ placedAt: -1 })
  .limit(20)
~~~~

### Example 3

~~~~javascript
db.orders.find({ customerId: ObjectId("66f1a47f5e7a123456789013") })
  .sort({ placedAt: -1 })
  .limit(20)
  .explain("executionStats")
~~~~

### Example 4

~~~~javascript
db.accounts.createIndex({ email: 1 }, { unique: true })
db.sessions.createIndex({ expiresAt: 1 }, { expireAfterSeconds: 0 })
~~~~

## Aggregation pipelines

Source: [Open chapter](./10-aggregation-pipelines.md)

### Example 1

~~~~javascript
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
~~~~

### Example 2

~~~~javascript
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
~~~~

## Schema validation and data consistency

Source: [Open chapter](./11-schema-validation-and-consistency.md)

### Example 1

~~~~javascript
db.createCollection("tasks", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["title", "status"],
      properties: {
        title: {
          bsonType: "string",
          minLength: 1,
          description: "A task needs a non-empty title"
        },
        status: {
          enum: ["open", "in_progress", "done"],
          description: "Use one of the supported task states"
        },
        priority: {
          bsonType: "int",
          minimum: 1,
          maximum: 5
        }
      }
    }
  },
  validationAction: "error"
})
~~~~

### Example 2

~~~~javascript
db.tasks.insertOne({
  title: "Review indexes",
  status: "open",
  priority: 2
})
~~~~

### Example 3

~~~~javascript
db.tasks.insertOne({ title: "", status: "waiting" })
~~~~

### Example 4

~~~~javascript
db.runCommand({
  collMod: "tasks",
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["title", "status"],
      properties: {
        title: { bsonType: "string", minLength: 1 },
        status: { enum: ["open", "in_progress", "done"] }
      }
    }
  },
  validationAction: "error"
})
~~~~

## Atomicity and transactions

Source: [Open chapter](./12-atomicity-and-transactions.md)

### Example 1

~~~~javascript
db.accounts.updateOne(
  { _id: accountId, balanceCents: { $gte: amountCents } },
  { $inc: { balanceCents: -amountCents } }
)
~~~~

### Example 2

~~~~javascript
const session = client.startSession()

try {
  await session.withTransaction(async () => {
    const debit = await accounts.updateOne(
      { _id: fromId, balanceCents: { $gte: amountCents } },
      { $inc: { balanceCents: -amountCents } },
      { session }
    )

    if (debit.matchedCount !== 1) {
      throw new Error("Source account is missing or has insufficient funds")
    }

    const credit = await accounts.updateOne(
      { _id: toId },
      { $inc: { balanceCents: amountCents } },
      { session }
    )

    if (credit.matchedCount !== 1) {
      throw new Error("Destination account was not found")
    }
  })
} finally {
  await session.endSession()
}
~~~~

## Replica sets and read/write concerns

Source: [Open chapter](./13-replica-sets-and-read-write-concerns.md)

### Example 1

~~~~javascript
rs.status()
~~~~

### Example 2

~~~~javascript
db.orders.insertOne(
  { orderNumber: "ORD-1001", status: "placed" },
  { writeConcern: { w: "majority" } }
)
~~~~

### Example 3

~~~~javascript
const client = new MongoClient(uri, {
  readPreference: "primary",
  readConcern: { level: "majority" },
  writeConcern: { w: "majority" }
})
~~~~

## Sharding and horizontal scaling

Source: [Open chapter](./14-sharding-and-horizontal-scaling.md)

### Example 1

~~~~javascript
{ tenantId: 1, eventId: 1 }
~~~~

## Security, backups, and operations

Source: [Open chapter](./15-security-backups-and-operations.md)

### Example 1

~~~~sh
export MONGODB_URI='mongodb://127.0.0.1:27017'
mongodump --uri="$MONGODB_URI" --db=study_notes --out=./backup
~~~~

### Example 2

~~~~sh
mongorestore \
  --uri="$MONGODB_URI" \
  --nsFrom='study_notes.*' \
  --nsTo='study_notes_restore.*' \
  ./backup/study_notes
~~~~

## MongoDB with the Node.js driver

Source: [Open chapter](./16-mongodb-with-nodejs-driver.md)

### Example 1

~~~~sh
npm install mongodb
~~~~

### Example 2

~~~~json
{
  "type": "module",
  "scripts": {
    "start": "node app.js"
  }
}
~~~~

### Example 3

~~~~javascript
import { MongoClient } from "mongodb"

const uri = process.env.MONGODB_URI

if (!uri) {
  throw new Error("Set MONGODB_URI before starting the application")
}

const client = new MongoClient(uri)
~~~~

### Example 4

~~~~javascript
import { MongoClient } from "mongodb"

async function main() {
  const uri = process.env.MONGODB_URI

  if (!uri) {
    throw new Error("Set MONGODB_URI before starting the application")
  }

  const client = new MongoClient(uri)

  try {
    await client.connect()
    const db = client.db("study_notes")
    const tasks = db.collection("tasks")

    const inserted = await tasks.insertOne({
      title: "Read the driver notes",
      status: "open",
      createdAt: new Date()
    })

    const task = await tasks.findOne({ _id: inserted.insertedId })
    console.log(task)

    const cursor = tasks.find({ status: "open" })
      .project({ title: 1, createdAt: 1 })
      .sort({ createdAt: -1, _id: 1 })
      .limit(20)

    for await (const item of cursor) {
      console.log(item.title)
    }
  } finally {
    await client.close()
  }
}

main().catch((error) => {
  console.error("Database operation failed", error)
  process.exitCode = 1
})
~~~~

### Example 5

~~~~javascript
const filter = { _id: taskId, status: "open" }
const update = { $set: { status: "done", completedAt: new Date() } }
const result = await tasks.updateOne(filter, update)

if (result.matchedCount === 0) {
  console.log("No open task matched")
}
~~~~

