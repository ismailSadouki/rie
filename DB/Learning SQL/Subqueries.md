
A subquery is a query contained within another SQL statement.

## Subquery Types
### Noncorrelated subqueries
It may be executed alone and does not reference anything from the containing statement.

 NOTE: 
 - scalar subquery is a noncorrelated subquery who returns a single row and column, and they can appear on either side of a condition.
 - if you use a subquery in an equality condition but the subquery returns more than one row, you will receive an error.

### Multiple-Row, Single-Column Subqueries
- The `IN` and `NOT IN` operators:
	While you can't equate a single value to a set of values, you can check whether a single value can be found within the set of values.
 - The `ALL` operator:
	It allows you to make comparisons between a single value and every value in a set.
	NOTE: you must ensure that the set of values does not contain `NULL` values.
	The `ANY` operator


### Correlated Subqueries
It's a subquery that depends on its containing statement from which it references one or more columns.
A correlated subquery is not executed once prior to execution of the containing statement, instead, its executed one for each candidate row.
#### The exists Operator
You may use the `EXISTS` operator when you want to identify whether a relationship exists without regard for the quantity.
In other words, It check whether a subquery return one or more rows.


NOTE:
- When using correlated subqueries with delete statements in MySql, Keep in mind that table aliases are not allowed when using `delete`.

### Subqueries as Data Sources
Subqueries used in the `from` clause must be noncorrelated, they are executed first, and the data is held in memory until  the containing query finishes execution.

### Common table expressions (a.k.a CTEs)
A CTE is a named subquery that appears at the top of a query in a `with` clause, which can contain multiple subqueries separated by commas.
	`WITH actors_s AS 
	`(SELECT actor_id..),
	`actors_s_pg AS (SELECT ...);`

























