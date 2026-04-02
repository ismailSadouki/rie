
### Check constraint:
to constrains the allowable values for a particular column
	`->eye_color CHAR(2) CHECK (eye_color IN ('BR', 'BL', 'GR')),`
	MySQl does provide another data type called enum that merges the ckeck constraint into the data type definition:
	`->eye_color ENUM('BR', 'BL', 'GR'),`
	