An Index is simply a mechanism for finding a specific item within a recourse. a DB uses indexes to locate rows in a table.

### Index Creation
	`ALTER TABLE custmer ADD INDEX idx_email (email);`
	this statement create a B-tree index.

  To show indexes in MySQL in specific table:
	  `SHOW INDEX FROM table_name \G;`

### Multi-column indexes
	`ALTER TABLE customer 
	ADD INDEX idx_full_name (last_name, first_name);`
	this index will be useful for queries that specify first and last name or even only first name, but it will not be useful for queries that specify only the first name.

### Type of indexes
- B-tree Indexes.
- Bitmap indexes (In Oracle).
- Text indexes (search for words or phrases in the documents).

### The downside of indexes
1. every index is a table,and every time a row is modified, all indexes on the table must be modified, which tends to slow things down.
2. Indexes also require disk space as well as some amount or care from your administrators.
As a solution you could and indexes in any time you need them and then drop them when you finish your work.

### Constraints
- Primary key constraint.
- Foreign key constraint.
- Unique constraint: Restrict one or more column to contain unique values within a table.
- Check constrain: Restrict the allowable values for a column.