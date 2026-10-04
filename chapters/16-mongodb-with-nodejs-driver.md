# 16. MongoDB with the Node.js driver

[Back to notes index](../README.md)

| [Previous: Security, backups, and operations](./15-security-backups-and-operations.md) | [Notes index](../README.md) | [Next: All code samples](./98-all-code-samples.md) |
| --- | --- | --- |

## Install the official driver

The official MongoDB Node.js driver lets a JavaScript application use MongoDB without manually speaking the database wire protocol. Add it to an existing Node.js project:

~~~sh
npm install mongodb
~~~

For an ECMAScript module project, a minimal `package.json` can declare module syntax:

~~~json
{
  "type": "module",
  "scripts": {
    "start": "node app.js"
  }
}
~~~

Keep the project's existing package metadata and scripts when adding these fields. Do not commit the lockfile only if the project intentionally tracks lockfiles; most applications should commit their package manager's lockfile.

## Create and reuse a client

Create one `MongoClient` for an application process and reuse it. The client manages connection pools. Read the URI from the environment and fail early if it is missing:

~~~javascript
import { MongoClient } from "mongodb"

const uri = process.env.MONGODB_URI

if (!uri) {
  throw new Error("Set MONGODB_URI before starting the application")
}

const client = new MongoClient(uri)
~~~

Do not print the URI in logs because it can contain credentials. For a local experiment, set `MONGODB_URI` in the terminal before running the process. In a deployed application, use the platform's secret configuration.

## Connect and perform a bounded read

This small script connects, inserts one practice record, reads it back, then closes the client even if an operation throws:

~~~javascript
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
~~~

A long-running web server usually connects during startup and reuses the same client for incoming requests. Close it when the process shuts down. Avoid creating a new client for every HTTP request because that needlessly creates connection pools.

## Use filters and update operators

Pass values as filter fields and update operators. Do not build query strings by concatenating user input:

~~~javascript
const filter = { _id: taskId, status: "open" }
const update = { $set: { status: "done", completedAt: new Date() } }
const result = await tasks.updateOne(filter, update)

if (result.matchedCount === 0) {
  console.log("No open task matched")
}
~~~

The driver returns result counts and identifiers for writes. Convert and validate request values before constructing a database filter. Apply authorization in the application before accessing records that belong to a user.

## Handle errors and close resources

Catch errors at an application boundary where they can be logged safely and returned as an appropriate response. Do not send raw database errors or stack traces to a public client. Avoid logging passwords, connection strings, or sensitive documents.

Use `try` and `finally` so sessions and cursors can be closed when a function exits early. Give API reads a sensible filter and maximum result size. Add indexes based on the query patterns that the application actually runs.

## Check what you learned

1. Which package provides the official MongoDB driver for Node.js?
2. Why create and reuse one `MongoClient` for an application process?
3. Where should a connection URI with credentials be stored?
4. Why close the client in a `finally` block in a one-off script?
5. Why should a web server avoid creating one client per request?
6. How can a filter and update object safely change a task status?
7. Why should an API bound its result size?
8. What should an application avoid including in public error responses and logs?

## References

- [MongoDB Node.js driver](https://www.mongodb.com/docs/drivers/node/current/)
- [Install the Node.js driver](https://www.mongodb.com/docs/drivers/node/current/get-started/)
- [Connect to MongoDB](https://www.mongodb.com/docs/drivers/node/current/connect/)
- [CRUD operations](https://www.mongodb.com/docs/drivers/node/current/crud/)
- [Node.js driver cursors](https://www.mongodb.com/docs/drivers/node/current/crud/query/cursor/)
