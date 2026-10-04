# 8. Data modeling with embedding and references

[Back to notes index](../README.md)

| [Previous: Update and delete documents safely](./07-update-and-delete-documents.md) | [Notes index](../README.md) | [Next: Indexes and query plans](./09-indexes-and-query-plans.md) |
| --- | --- | --- |

## Start with application questions

Data modeling is the work of deciding which values belong together and how a query will find them. Begin by listing the important reads and writes:

- Which screen or operation needs this data?
- Which fields are usually read together?
- Which values change independently?
- Can a related list grow without a practical bound?
- Must one update change several records as one unit?

MongoDB lets a document hold nested documents and arrays. It also lets documents refer to records in other collections using identifiers. These choices support different access patterns.

## Embed data that is read together

An order can keep a snapshot of the shipping address and purchased item details. The order remains useful even if the customer's current address or a product's name later changes:

~~~javascript
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
~~~

A read can retrieve the order and its item snapshot in one operation. A single-document write is atomic, so changing the order status and a related field in this document is handled together.

## Reference independently managed data

A customer can be stored separately because many orders may belong to the same customer and the profile can change independently. The order stores the customer's identifier:

~~~javascript
const customer = await db.customers.findOne({ _id: customerId })
const orders = await db.orders.find({ customerId }).sort({ placedAt: -1 }).toArray()
~~~

The application performs two reads here. A reference is an identifier convention, not a foreign-key constraint enforced automatically by MongoDB. The application must decide what should happen if the referenced record is missing.

## Balance duplication and growth

Embedding a small snapshot duplicates values, but can make common reads simpler and preserve historical facts. A reference reduces duplication when records are shared or independently changed. A hybrid model can store both an identifier and a small read-optimized snapshot.

Avoid arrays that can grow without a practical limit, such as every event ever recorded for one account. Put those records in a separate collection with an owner identifier and an index. Then retrieve the needed range with a filter and limit.

## One-to-one, one-to-many, and many-to-many

- **One-to-one:** embed a small dependent object or use a separate collection if it has its own access rules or lifecycle.
- **One-to-many:** embed a bounded list used with the parent, or store child records separately when they can grow or are queried independently.
- **Many-to-many:** store references on one or both sides based on the common lookup direction. For large or changing relationships, use a linking collection.

There is no universal rule that every relation must be embedded or referenced. Measure the common reads, document size, update patterns, and consistency needs before choosing.

## Check what you learned

1. What questions should be answered before choosing a document shape?
2. Why can an order store a snapshot of item names and prices?
3. What is one benefit of embedding values that are read together?
4. What responsibility stays with the application when documents store references?
5. Give an example of a list that should not grow without a bound inside a document.
6. When can duplicated values make sense?
7. How might a many-to-many relationship use a linking collection?
8. Why is there no single modeling rule that suits every relationship?

## References

- [MongoDB data modeling](https://www.mongodb.com/docs/manual/data-modeling/)
- [Embedded data](https://www.mongodb.com/docs/manual/data-modeling/embedding/)
- [Referenced data](https://www.mongodb.com/docs/manual/data-modeling/referencing/)
- [Model one-to-many relationships](https://www.mongodb.com/docs/manual/tutorial/model-referenced-one-to-many-relationships-between-documents/)
