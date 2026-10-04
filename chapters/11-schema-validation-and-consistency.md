# 11. Schema validation and data consistency

[Back to notes index](../README.md)

| [Previous: Aggregation pipelines](./10-aggregation-pipelines.md) | [Notes index](../README.md) | [Next: Atomicity and transactions](./12-atomicity-and-transactions.md) |
| --- | --- | --- |

## Keep a flexible shape predictable

A collection does not require every document to have an identical shape. Still, applications often need required fields and known types. MongoDB schema validation can reject or warn about writes that do not match a declared rule.

Create a collection whose tasks require a string title and a known status:

~~~javascript
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
~~~

The validator runs when documents are inserted or updated. The `required` list names fields that must exist. `properties` describes constraints for fields when they are present. Use a required rule when a field must exist in every document.

## Test valid and invalid writes

A valid task can be inserted:

~~~javascript
db.tasks.insertOne({
  title: "Review indexes",
  status: "open",
  priority: 2
})
~~~

A misspelled status or a missing title fails the validator when `validationAction` is `error`. The application should catch and report a validation error in a useful way, without exposing internal connection details.

~~~javascript
db.tasks.insertOne({ title: "", status: "waiting" })
~~~

This fails because the title is too short and `waiting` is not an allowed status. During a migration, `validationAction: "warn"` can help reveal older records that do not yet match the new rules. Warnings do not replace a plan for fixing existing data.

## Validation does not replace every constraint

Schema validation checks a document's shape and field rules. Use a unique index for collection-wide uniqueness, such as a user email. Use application checks for rules that depend on permissions or external systems. A validation rule should complement, not duplicate blindly, every piece of application logic.

Validation applies to normal writes. Privileged operations can be configured to bypass validation, so protect those paths and use them only for a deliberate repair or migration.

## Change a validator over time

Validators can be added or adjusted with `collMod`. Plan a migration in stages: inspect current documents, repair invalid data, apply the validator, and monitor rejected writes. A strict rule introduced before existing data and write paths are ready can interrupt normal operations.

~~~javascript
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
~~~

## Check what you learned

1. Why can a flexible collection still benefit from schema validation?
2. What is the purpose of the `required` list?
3. How does `validationAction: "error"` differ from `"warn"`?
4. What happens when an invalid write is rejected?
5. What kind of rule is better enforced with a unique index?
6. Why inspect existing documents before tightening a validator?
7. How can a validator be changed after a collection exists?
8. Why should bypassing validation be limited to deliberate operations?

## References

- [Schema validation](https://www.mongodb.com/docs/manual/core/schema-validation/)
- [JSON Schema validation](https://www.mongodb.com/docs/manual/core/schema-validation/specify-json-schema/)
- [Validation actions](https://www.mongodb.com/docs/manual/core/schema-validation/handle-invalid-documents/)
- [Modify a collection](https://www.mongodb.com/docs/manual/reference/command/collMod/)
