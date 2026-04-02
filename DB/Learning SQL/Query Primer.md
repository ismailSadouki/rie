
### The select clause
it is one of the last clauses that the database server evaluates.

### Removing Duplicates
by using the keyword `DISTINCT` after the keyword `SELECT`.
NOTE: Keep in mind that generating distinct set of results requires the data to be sorted, which can be time consuming for large result sets, Don't fall into the trap of using distinct just to be sure there are duplicates.

### The form clause
The form defines the tables used by a query, along with the means of linking the table together.
	NOTE:
	Tables type:
	1. Permanent tables (created using the create table statement).
	2. Derived tables (rows returned by a subquery and held in memory)
	3. Temporary tables (volatile data held in memory)
	4. Virtual tables (created using the create view statement)
	Each of these tables may be included in a query's `from` clause.

2. Derived(subquery-generated table): 
	is a query contained within another query, Subqueries are surrounded by parentheses and can be found in various parts of `select` statement.
3. Temporary tables:
	`CREATE TEMPORARY actor_j (actor_id smallint(5), name varch...`
	these table will disappear after your session is closed.
4. Views:
	A view is a query that is stored in the data dictionary, It looks and acts like a table, but there is no data associated with a view(virtual table).
	When the view is created, no additional data is generated or stored, the server simply tucks away the select statement for future use.

### Sorting via Numeric Placeholders
If you are sorting using the columns in your select clause, you can opt to reference the columns by their position in the select clause rather than by name.
	`SELECT lname, fname FROM .. ORDER BY 2`
	this will sort the data by the `lname` column.











