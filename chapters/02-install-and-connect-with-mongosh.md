# 2. Install MongoDB and connect with mongosh

[Back to notes index](../README.md)

| [Previous: Documents and MongoDB fundamentals](./01-documents-and-mongodb-fundamentals.md) | [Notes index](../README.md) | [Next: Databases, collections, and CRUD overview](./03-databases-collections-and-crud.md) |
| --- | --- | --- |

## Know which parts you need

A local MongoDB setup has two separate parts:

- **MongoDB Server** stores data and accepts database connections.
- **MongoDB Shell (`mongosh`)** is a command-line client that connects to a server and lets you inspect or change data.

Installing the shell alone does not start a database server. You can connect the shell to a local server or to a hosted deployment such as MongoDB Atlas. Follow the installation page for your operating system because package names and supported platforms change over time.

## Install and check the tools

Install MongoDB Community Server using the official installation instructions for your operating system. Install `mongosh` if the server package did not include it. Start the server using the service or command documented for that installation.

Check that the shell is available:

~~~sh
mongosh --version
~~~

Connect to a local server using the default local URI:

~~~sh
mongosh "mongodb://127.0.0.1:27017"
~~~

`127.0.0.1` means this computer. Port `27017` is MongoDB's default server port. A connection error often means the server is not running, the URI or port is wrong, or a local firewall is blocking the connection.

## Select a database and verify the connection

Once connected, check the server and switch to the practice database:

~~~javascript
 db.runCommand({ ping: 1 })
 use study_notes
 db.getName()
~~~

The `ping` command asks the server to respond. `use study_notes` changes the current database context. A database is persisted after data or metadata is created in it. The shell prompt shows the active database name.

In shell examples throughout these notes, commands are written without a leading space. They can be entered one at a time in `mongosh`.

## Connect to MongoDB Atlas

For an Atlas deployment, create a database user and allow the client network address in the project's network access settings. Copy the connection string from Atlas and replace its placeholders with the database username and password. The string often looks like this:

~~~text
mongodb+srv://<username>:<password>@<cluster-host>/<database>?retryWrites=true&w=majority
~~~

Do not put a real password into a note, source file, screenshot, or public repository. Use an environment variable in a local terminal instead. The exact syntax depends on the shell. In PowerShell:

~~~powershell
$env:MONGODB_URI = "mongodb+srv://<username>:<password>@<cluster-host>/study_notes"
mongosh "$env:MONGODB_URI"
~~~

In a POSIX shell such as Bash:

~~~sh
export MONGODB_URI='mongodb+srv://<username>:<password>@<cluster-host>/study_notes'
mongosh "$MONGODB_URI"
~~~

Avoid committing `.env` files. If a credential is accidentally published, rotate it in the deployment rather than relying only on deleting the file from the latest commit.

## Diagnose common connection problems

- **Connection refused:** check that the server process is running and that the host and port are correct.
- **Authentication failed:** verify the database username, password, and authentication database. URL-encode special characters in credentials when required by the connection URI.
- **Atlas connection times out:** confirm the client address is allowed and that the network can reach the deployment.
- **Name resolution error:** check the hostname and DNS connection. An SRV URI also requires working DNS SRV lookup.
- **Connected to the wrong database:** run `db.getName()` and switch with `use databaseName`.

## Check what you learned

1. What is the difference between MongoDB Server and `mongosh`?
2. Does installing `mongosh` by itself create a database server?
3. What does `127.0.0.1` mean in a local connection URI?
4. What does the `ping` command help verify?
5. Why may a database name not appear after only running `use name`?
6. Name two checks for an Atlas connection timeout.
7. Why should credentials be supplied outside committed source files?
8. What should be done if a database password is accidentally published?

## References

- [Install MongoDB Community Edition](https://www.mongodb.com/docs/manual/installation/)
- [Install `mongosh`](https://www.mongodb.com/docs/mongodb-shell/install/)
- [Connect to MongoDB](https://www.mongodb.com/docs/manual/reference/connection-string/)
- [Atlas connection troubleshooting](https://www.mongodb.com/docs/atlas/troubleshoot-connection/)
