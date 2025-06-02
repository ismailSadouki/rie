### Why it's important?
- Field specifications help establish and enforce field-level integrity.
- Defining field specifications for each field enhances overall data integrity.
- Defining field specifications compels you to acquire a complete understanding of the nature and purpose of the data in the database.
- Field specifications constitute the 'data dictionary' of the database.
### Field-level integrity
- The identity and purpose of a field are clear.
- Field definitions are consistent throughout the database.
- The values of a field are consistent and valid.
- The types of modifications that can be applied to the values in the field are clearly identified.

### The elements of the ideal field
- It represents a distinct characteristic of the subject of the table.
- It contains only a single value.
- It cannot be deconstructed into smaller components.
- It does not contain calculated or concatenated value.
- It is unique within the entire database structure.
- It retains a majority of its characteristic when it appears on one or more tables as a foreign key.

### Anatomy of  a field specification
	 - - Physical elements
		- Data Type:
		 1. Alphanumeric: this data type stores any combination letters, numbers, keyboard characters, special characters.
		 2. Numeric: this data type stores only whole numbers and real numbers, And it will accept numbers with leading zeros(e.g 0002).
		 3. DateTime: This data type stores dates, times, or a combination of bouth.
		- Length.
		- Decimal Places.
		- Character Support.