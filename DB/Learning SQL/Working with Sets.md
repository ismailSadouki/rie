when performing set operations on two data sets, the following guidelines must apply;
- Both data sets must have the same number of columns.
- The data types of each column across the two data sets must be the same (or the server must be able to convert one to the other).
You perform a set operation by placing a set operator between two select statements:
	`SELECT 1 num, 'abc' str UNION SELECT 9 num, 'xyz' str;
	this query known as a compound query because it comprises multiple queries.

### The Union operator
The `UNION` sorts the combined set and removes duplicates, wheres the `UNION ALL` does not.

### Set Operation Precedence
if your compound query contains more than two queries using different set operator, you need to think about the order in which to place the queries in your compound statement to achieve the desired results.
Because compound queries are evaluated from top to bottom, but with the following caveats:
	- The ANSI SQL specification calls for intersect operator to have precedence over the other set operator.
	- You may dictate the order in which queries are combined by enclosing multiple queries in parentheses.