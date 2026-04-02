### Why keys are important ?
- They ensure that each table in the database is identified.
- They help establish and enforce various types of integrity.
- They serve to establish table relationship.

### Types of keys :
- Candidate keys: it's a field or set of fields that uniquely identifies a single record of the table's subject.
	## Elements of a candidate key:
	- it cannot be a multipart field.
	- it must contain unique values.
	- it cannot contain Nulls.
	- it's value cannot cause a breach of the organization's security or privacy rules.
	- it's value can be modified only in rare or extreme cases.
	`Candidate key (CK)`
	A candidate key composed of two or more fields is knows as a `composite candidate key (CCK)`
	If you have two or more composite keys, write `CCK1 CCK2...etc`
- Artificial (surrogate) candidate keys: When you determine that a table does not contain a candidate key, you can create an artificial candidate key.
- Primary Keys:
	- `primary key (PK)`
	- `composed primary key (CPK)`
 - Alternate Keys: after you select a candidate key to serve as the primary key for particular table, you'll designate the remaining candidate primary keys as `alternate keys (AK or CAK)`.
### Table-Level Integrity
it ensures the fallowing:
- There are no duplicate records in a table.
- The primary key exclusively identify each record in a table.
- Every primary key value is unique.
- Primary key values are not null.