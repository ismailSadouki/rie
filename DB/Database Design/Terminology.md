
## Value-related Terms

#### The difference between data and information
		Data is what you store; imformation is what you retrieve
### The Problem with NULL

		- "An operation involving a NULL evaluates to NULL."
The issues of missing values, unknown values, and whether values will be used
in mathematical expression or aggregator function are all taken in consideration
in the database design process 

### Keys:
Keys are special fields that plays specific roles within  a table, and the type of key determines its purpose within the table.
### The difference between key and Index
		keys are logical structures you use to identify record within a table,
		and indexes are physical strurtures you use to optimize data processing.

## Relationship-Related Terms

### Relationships
A relationship exists between two tables when you can in some way associate the records of the first table with those of the second. You can establish the relationship via a set of primary and foreign keys or through a third table known as a linking table.
### Type of relationships
* One-to-one relationship:
	when a single record in the first table related to zero or one and only one record in second table, 
	and a single record in the second table related to one and only one record in the first table.
	You establish the relationship by taking a copy of the parent table's primary key and incorporating it within the structure of the child table, where it becomes a foreign key ant it's primary key.
* One-to-Many relationship:
	when a single record in the first table can be related to zero, one, or many records in the second table, but a single record in the second table can be related to only one record in the first table.
* Many-to-Many relationship:
	when a single record in the first table can be related to zero, one, or many record in the second table and vise versa.
	You establish this relationship with a linking table.

## Integrity-Related Terms

### Field Specification
represents all the elements of the field, and each field specification incorporates three types of elements:
* General elements such as Field Name, Desc, and Parent Table.
* Physical elements such as Data Type, Length, and Character Support.
* Logical elements such as Required Value, Range of Value, and Null Support.
### Data integrity
Data integrity refers to the validity, consistency, and accuracy  of the data in a database.
* Table-level integrity (entity integrity): ensures that no duplicate record exists, and the field that identifies each record within the table is unique and never NULL.
* Field-level integrity.
* Relationship-level integrity.
* Business rules.














