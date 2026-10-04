# MongoDB Study Notes

These are my personal study notes from learning MongoDB and working with document databases. They bring the core ideas, practical commands, and JavaScript examples together in one place so I can understand how the pieces fit and return to them while building applications.

## About these notes

This collection starts with documents, BSON, and local setup, then moves through CRUD operations, query design, data modeling, indexes, aggregation, validation, transactions, replication, sharding, security, and the official Node.js driver. Each chapter explains the reason behind a feature, shows a focused example, and includes questions for review.

Examples use `mongosh` and JavaScript with the official MongoDB Node.js driver. Commands that change or remove data are labeled so they can be tested against a local practice database first. Version-specific behavior should be checked against the current MongoDB Server and driver documentation linked below.

## Core topics

- Understand documents, collections, BSON, and MongoDB deployments
- Connect with `mongosh` and choose a database safely
- Insert, find, update, and delete documents
- Write filters, projections, sorts, and bounded pagination
- Model related data with embedded documents and references
- Build indexes and inspect query plans
- Transform data with aggregation pipelines
- Validate document shapes and handle atomic operations
- Learn transactions, replica sets, read and write concerns, and sharding
- Connect a JavaScript application with the official Node.js driver
- Apply basic security, backup, and operational practices

## How I use these notes

I work through the chapters in order and run the examples against a throwaway database named `study_notes`. I change one value at a time, inspect the result, and keep important data outside experiments. The final chapters collect code samples and answer the review questions in one place.

## Chapters

01. [Documents and MongoDB fundamentals](./chapters/01-documents-and-mongodb-fundamentals.md)
02. [Install MongoDB and connect with mongosh](./chapters/02-install-and-connect-with-mongosh.md)
03. [Databases, collections, and CRUD overview](./chapters/03-databases-collections-and-crud.md)
04. [Insert documents and understand BSON types](./chapters/04-insert-documents-and-bson-types.md)
05. [Find documents with query operators](./chapters/05-query-filters-and-operators.md)
06. [Projection, sorting, limits, and cursors](./chapters/06-projection-sorting-limits-and-cursors.md)
07. [Update and delete documents safely](./chapters/07-update-and-delete-documents.md)
08. [Data modeling with embedding and references](./chapters/08-data-modeling-embedding-and-references.md)
09. [Indexes and query plans](./chapters/09-indexes-and-query-plans.md)
10. [Aggregation pipelines](./chapters/10-aggregation-pipelines.md)
11. [Schema validation and data consistency](./chapters/11-schema-validation-and-consistency.md)
12. [Atomicity and transactions](./chapters/12-atomicity-and-transactions.md)
13. [Replica sets and read/write concerns](./chapters/13-replica-sets-and-read-write-concerns.md)
14. [Sharding and horizontal scaling](./chapters/14-sharding-and-horizontal-scaling.md)
15. [Security, backups, and operations](./chapters/15-security-backups-and-operations.md)
16. [MongoDB with the Node.js driver](./chapters/16-mongodb-with-nodejs-driver.md)
98. [All code samples](./chapters/98-all-code-samples.md)
99. [Complete questions and answers](./chapters/99-complete-q-and-a.md)

## Official references

- [MongoDB Manual](https://www.mongodb.com/docs/manual/)
- [MongoDB Shell](https://www.mongodb.com/docs/mongodb-shell/)
- [MongoDB Node.js Driver](https://www.mongodb.com/docs/drivers/node/current/)
- [CRUD operations](https://www.mongodb.com/docs/manual/crud/)
- [Data modeling](https://www.mongodb.com/docs/manual/data-modeling/)
- [Indexes](https://www.mongodb.com/docs/manual/indexes/)
- [Aggregation](https://www.mongodb.com/docs/manual/aggregation/)
- [Security](https://www.mongodb.com/docs/manual/security/)

## Links

- Portfolio: https://www.ashishranjan.net
- GitHub: https://github.com/a2rp
- CodePen: https://codepen.io/ash1198
- LinkedIn: https://www.linkedin.com/in/aashishranjan
- Facebook: https://www.facebook.com/theash.ashish/
- YouTube: https://www.youtube.com/@ashishranjan-ashz?sub_confirmation=1
- Email: mailto:ash.ranjan09@gmail.com

## Support

- Support: https://a2rp-donation-page.netlify.app/
- Buy Me a Coffee: https://buymeacoffee.com/ashishranjan
- Patreon: https://www.patreon.com/ashishranjan
