# 15. Security, backups, and operations

[Back to notes index](../README.md)

| [Previous: Sharding and horizontal scaling](./14-sharding-and-horizontal-scaling.md) | [Notes index](../README.md) | [Next: MongoDB with the Node.js driver](./16-mongodb-with-nodejs-driver.md) |
| --- | --- | --- |

## Protect access to the deployment

A database deployment should be reachable only by the people and applications that need it. Apply these basics:

- Enable authentication and create named users for applications and operators.
- Give each user only the roles needed for its work. Avoid using an administrator account from an application.
- Restrict network access to approved clients. Do not expose a database port to every address.
- Use TLS when traffic crosses a network you do not fully control.
- Store credentials in a secrets manager or protected environment configuration, not in source control.
- Rotate credentials when access changes or a secret may have been exposed.

Atlas and self-managed deployments use different configuration screens and controls. Check the security guidance for the deployment type and confirm the effective settings rather than assuming a default.

## Make backups that can be restored

A backup is useful only if it can be restored. `mongodump` and `mongorestore` are MongoDB Database Tools that can export and restore BSON data. Install the tools appropriate for the deployment before running these commands.

For a local practice database, set a connection URI in the terminal without writing the credential into a script:

~~~sh
export MONGODB_URI='mongodb://127.0.0.1:27017'
mongodump --uri="$MONGODB_URI" --db=study_notes --out=./backup
~~~

Restore into a separate practice database so the source remains untouched:

~~~sh
mongorestore \
  --uri="$MONGODB_URI" \
  --nsFrom='study_notes.*' \
  --nsTo='study_notes_restore.*' \
  ./backup/study_notes
~~~

Backup behavior and consistency depend on deployment type, topology, and write activity. Use a backup method designed for the deployment, keep copies protected and separate, and test recovery on an isolated target. A replica set alone is not a backup.

## Monitor a database in normal operation

Watch the signals that relate to the application's workload:

- Connection count and connection failures
- Operation latency and slow queries
- CPU, memory, disk capacity, and disk I/O
- Replication lag and replica-set health
- Index use and collection growth
- Application errors and timeout rates

Use the deployment's monitoring tools and logs. Investigate a slow query with its filter, sort, projection, and `explain()` output. Avoid enabling detailed profiling broadly without understanding its storage and performance impact.

## Prepare for recovery

Write down who can restore a backup, where the backup is stored, and how long recovery should take. Test the process periodically with a non-production copy. Confirm that the restored database has the expected collections, indexes, validation rules, and application access.

## Check what you learned

1. Why should an application use a restricted database role?
2. What is the purpose of limiting network access?
3. Where should connection credentials be kept?
4. What tools can create and restore a BSON backup?
5. Why restore a practice backup into a separate database?
6. Why is a replica set not a historical backup?
7. Name four useful operational signals to watch.
8. What should a recovery exercise verify beyond whether a command exits successfully?

## References

- [MongoDB security](https://www.mongodb.com/docs/manual/security/)
- [Access control](https://www.mongodb.com/docs/manual/core/authorization/)
- [Network security](https://www.mongodb.com/docs/manual/core/security-network/)
- [MongoDB Database Tools](https://www.mongodb.com/docs/database-tools/)
- [Back up and restore with Database Tools](https://www.mongodb.com/docs/database-tools/)
- [Monitoring](https://www.mongodb.com/docs/manual/administration/monitoring/)
