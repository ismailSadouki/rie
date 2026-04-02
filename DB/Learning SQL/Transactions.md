
### Multiuser Databases
Database management systems allow a single user to query and modify data.

### Locking 
Locks are the mechanism the database server uses to control simultaneous use of data resources, When some portion of the database is locked, any other users wishing to modify or read the data must wait until the lock has been released.
There is two locking strategies:
- Database writers must request and receive  from the server a `write lock` to modify data, and db readers must request  and receive the server a `read lock` to query data.
    While multiple users can read data simultaneously, only one `write lock` is given out at a time for each table, and read requests are blocked until the `write lock` is released.
 - Database writers must request and receive from the server a `write lock` to modify data, but readers do not need any type of lock to query data.
 The first approach  can lead to long wait times if there are many concurrent read and write requests, and the second can be problematic if there are long-running queries while data is being modified.
### Lock Granularities
There are also a number of different  strategies that you may employ when deciding how to lock a resource.
	- Table Locks:
		Keep multiple users from modifying data in the same table simultaneously.
	- Page Locks:
		Keep multiple users from modifying data on the same page of a table simultaneously.
	 - Row locks:
		 Keep multiple users from modifying the same row in a table simultaneously.

### What is a Transaction?
It is a device fro grouping together multiple SQL statements such that all or none of the statements will succeed (atomicity).

### Starting a Transaction
DB server handle transaction creation in one of two ways:
1. An active transaction is always associated with a db session.
2. Unless you explicitly begin a transaction , individual SQL statements are automatically committed independently of one another.To begin a transaction you must first issue a command.
NOTE: MySQL uses the second approach, so if you deleted or updated something you cant undo that.
until you explicitly begin a transaction, you are in what known as `autocommit mode`, and to disable `autocommit mode` in MySQL
	`SET AUTOCOMMIT=0`
	Once you have left autocommit mode, all SQL commands take place within the scope of a transaction and must be explicitly committed or rolled back.