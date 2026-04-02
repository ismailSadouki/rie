## Why they are important?
- it establishes a connection between a pair of tables that are logically related to each other.
- it helps to further refine table structures and minimize redundant data.
- it is the mechanism that enables you to draw data from multiple tables simultaneously.
### One-to-One Relationships
A pair of tables bears a one-to-one relationship when a single record in the first table is related to only one record in the second table and likewise for the second table.
In this type of relationship, one table servers as a parent table and the other serves as a child table.
A record must exist in the parent table before you can enter a related record in the child table.
### One-to-Many relationships
Exists between a pair of tables when a single record in the first table can be related to one or more records in the second table, but a single record in the second table can be related to only one record in the first table.


### Many-to-Many relationships
Exists when a single record in the first table can be related to one or more records in the second table and a single record in the second table can be related to one or more records in the first table.
You establish a many-to-many relationship with a linking table.
A linking table always contains a composite primary key.
### Problems with Many-to-Many relationships
How do easily associate record from the first table with records in the second table to establish the relationship? You will encounter problems such as these if you do no establish the relationship properly:
- Retrieving information from one of the tables will be tedious and somewhat difficult.
- One of the tables will contain a large amount of redundant data.
- Duplicate data will exist within both tables.
- Inserting, updating, and deleting data will be difficult.
### Solution: Self-Referencing Relationships
It does not exists between a pair of tables. Instead, a relationship that exists between the records within a table.
A table bears a self-referencing relationship (also known as recursive relationship) to itself when a given record in the table is related to other records within the table. a self-referencing relationship can be one-to-one, one-to-many, many-to-many.
#### One-to-One self-referencing
exists when a given record in the table can be related to only one other record within the table.
#### One-to-Many self-referencing
exists when a given record in the table can be related to one or more other records withing the table.

#### Many-to-Many self-referencing
exists when a given record in the table can be related to one or more other records within the table and one or more records can themselves be related to the given record.
You use a linking table to establish a self-referencing many-to-many relationship.


### Defining a Deletion Rule for Each  Relationship
This rule determines what your RDBMS should do when you place a request to delete a given record in the parent table of the relationship.
Deletion rules help guard against orphaned records, which are records in the child table that have no relationship whatsoever to any record in the parent table.
----The five types of deletion rules:
	1- Deny `(D)`: The RDBMS will not delete the record in the parent table, but will instead keep the record and designate it as 'inactive'.
	2- Restrict `(R)`: The RDBMS will not delete the record in the parent table if related records exist in the child table.
	3- Cascade `(C)`: The RDBMS will delete the record in the parent table, and it will also delete all related records in the child table.
	4- Nullify `(N)`: The RDBMS will delete the record in the parent table and it will then update the foreign key values of related record in child table to `Null`.
	5- Set Default `(S)`: The RDBMS will delete the record in the parent table  and it will then update the foreign key of related record in the child table to a specific default value.

You always set the deletion rule from the perspective of the parent table.

### Identifying the type of Participation for each table:
The type of participation you assign to a given table determines whether a record must exist in that table before you can enter records into the related table, The two types of participation are:
	1. Mandatory: At least one record must exist in this table before you can enter any records into the related table.
		`Use a Vertical line to represent a Mondatory type of participation`
    1. Optional: There is no requirement for any records to exist in this table before you can enter record into the related table.
	    `Use Circle to represent an Optional type of participation`

### Identifying the Degree of Participation for each table:
The degree of participation indicates the minimum number of records that a given table must have associated with a single record in the related table and the maximum number of records that the table is allowed to have associated with a single record in the related table.


### Relationship-Level integrity
- The connection between the two table in relationship is sound.
- You can insert new records into each table in a meaningful manner.
- You can delete an existing record without producing any adverse effects.
- A meaningful limit exists for the number of records that can be interrelated within the relationship.
































