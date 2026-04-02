A view is simply a mechanism for querying data. 
You create a view by assigning a name to a select statement and then storing the query for others to use.
This can keep your tables private and allow your users  to access data only through a set of views.
fore example let's say you want to partially obscure the email address in the customer table.
	`CREATE VIEW customer_vw
	``(customer_id, fname, lname, email) AS SELECT customer_id, fname, lname, concat(substr(email,1,2), '****', substr(email, -4)) email FROM customer;
when the `CREATE VIEW` statement is executed, the database server simply stores the view definition for future use; the view is not executed, and no data is retrieved or stored, Once the view has been created, users can query it just like they would do a table.
If you want to know what columns are available in view, you can use the `DESCRIVE` statement.

### Why use views?
- ##### Data Security.
- ##### Data Aggregation.
- ##### Hiding Complexity.
- ##### Joining Partitioned Data.
- #### Updatable Views:
	in MySQL Views are updatable if the following conditions are met:
	- No aggregate functions are used.
	- The view does not employ group by or having clauses.
	- No sub-query exists in the select or form clause, and any sub-query in the where clause do not refer to tables in the from clause.
	- The does not utilize union, union all, distinct.
	- The from clause includes at least one table or updatable view.
	- The from clause uses only inner joins if there is more than one table or view.
