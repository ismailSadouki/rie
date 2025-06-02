
### JOIN
The operation of allowing columns from two tables to be included in the query's result set.

### Inner join
suppose that you have a table of customer contains an address_id who is related to the address table, and suppose that you have a customer with address_id 999, but there is no row in the address table with id 999, then that costumer row will not be included in the result set.
NOTE: if the names of the columns used to join the two tables are identical, you can use the `USIN` subclause instead of the `ON` subclause.
	`JOIN address USIN (address_id)`

### The ANSI Join Syntax
The older method of joining tables.
	`SELECT c.fname, c.lname, a.address FROM customer c, address a WHERE c.addres_id = a.address_id;`


### Does Join order matter?
Keep in mind that SQL is nonprocedural language , meaning that you describe what you want to retrieve and which database objects need to be involved, but it is up to the database server to determine how to execute your query.


### Self-Joins
Not only can you include the same table more than once in the same query, but you can actually join a table to itself. for example for self-referencing foreign key.