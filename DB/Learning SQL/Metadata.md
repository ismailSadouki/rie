Metadata is essentially data about data.Every you create a database object, the database server needs to record various pieces  of information such as table name, storage engine, column names primary keys....ect, this data is collectively known as the `data dictionary` or `system catalog`.

### Information_schema
All of the object available within the  `information_schema` database are views.
this table gave you the ability to retrieve information about your schema objects via SQL queries, for example: 
	`SELECT * FROM information_schema.tables WHERE table_schema = 'sakila';`


### Dynamic SQL Generation
most relational db servers allow SQL statements to be submitted to the server as strings.and it's know as `dynamic sql execution` .
MySQL provides `prepare, execute, and deallocate` to allow for dynamic SQL execution.
	`SET @qry = 'SELECT customer_id, first_name, last_name FROM customer';
	`PREPARE dynsql1 FROM @qry;
	`EXECUTE dynsql1;
	`DEALLOCATE PREPARE dynsql1;
	NOTE: You could use placeholder/variables:`?`
	