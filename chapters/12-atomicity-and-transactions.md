# 12. Atomicity and transactions

[Back to notes index](../README.md)

| [Previous: Schema validation and data consistency](./11-schema-validation-and-consistency.md) | [Notes index](../README.md) | [Next: Replica sets and read/write concerns](./13-replica-sets-and-read-write-concerns.md) |
| --- | --- | --- |

## Single-document writes are atomic

MongoDB applies a write to one document atomically. Other operations do not observe only part of that document update. This is one reason to keep values that must change together in the same document when the data model permits it.

A conditional update can check a rule and change the value in one operation:

~~~javascript
db.accounts.updateOne(
  { _id: accountId, balanceCents: { $gte: amountCents } },
  { $inc: { balanceCents: -amountCents } }
)
~~~

The filter prevents the balance from becoming negative when the update runs. Check `matchedCount` to learn whether an account satisfied the condition.

## When a transaction is needed

A multi-document transaction groups several reads and writes so they commit together or abort together. It is useful when a business operation truly spans multiple documents and partial completion would be incorrect.

Transactions have more coordination and performance cost than a single-document write. First check whether a document model can keep the required invariant within one atomic document update. Use a transaction when the consistency requirement still spans records.

## Run a transaction with the Node.js driver

Transactions use a session. This example transfers integer cents between two accounts and rejects the operation if either account is missing or the source has insufficient funds:

~~~javascript
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
~~~

Validate the amount and account identifiers before starting the transaction. Avoid sending an email or charging an external service inside a transaction callback because the driver may retry transaction work. Perform external side effects after a confirmed commit, or use an application pattern designed to coordinate them.

## Know transaction boundaries

A transaction must use the same session for every participating operation. Keep it short, handle errors, and allow the session to end. MongoDB supports transactions on replica sets and sharded clusters, while a standalone server does not provide multi-document transactions.

Set read concern, write concern, and read preference at the transaction or client level according to the application's consistency needs. Use documented defaults unless a requirement calls for a different level. Transactions are not a substitute for modeling data around its normal access patterns.

## Check what you learned

1. What does atomicity mean for a single-document write?
2. How can a filter enforce a balance condition in the same operation as an update?
3. When is a multi-document transaction useful?
4. What happens if an operation in a transaction throws before commit?
5. Why should all transaction operations use the same session?
6. Why should external side effects stay outside retryable transaction work?
7. Which deployment types support multi-document transactions?
8. Why should a transaction not replace careful document modeling?

## References

- [Transactions](https://www.mongodb.com/docs/manual/core/transactions/)
- [Transaction production considerations](https://www.mongodb.com/docs/manual/core/transactions-production-consideration/)
- [Node.js driver transactions](https://www.mongodb.com/docs/drivers/node/current/fundamentals/transactions/)
- [Atomicity and transactions](https://www.mongodb.com/docs/manual/core/write-operations-atomicity/)
