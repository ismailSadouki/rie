To check which mode you are in you could use the following statement:
	`SELECT @@session.sql_mode;`
	And you can change the mode using `SET`
	`SET sql_mode='ansi';`

To find the location of a sub-string within a string you could use `POSITION()` statement, if the sub-string cannot be found, the `POSITION()` function returns `0`.
If you want to start the search from something other then the first character of your target string, you could use `LOCATE()` statement instead of `POSITION()`.

### The insert() function
It takes four arguments: the original string, the position at which to start, the number of characters to replace, and the replacement string
	`SELECT INSERT('GODDBY WORLD', 9, 0, 'ISMAIL'); => 'GODDBY ISMAIL WORLD'`
	if the third argument is `0`, the trailing characters are pushed to the right.

### Extract sub-string from a string
 `SELECT SUBSTRING('goodby creal world', 9, 5); > cruel`
	

### Handling Signed Data
You could use the `SIGN()` function to return only the sign of a column that contain numeric values.
or the `ABS()` function to return all the values in positive sign.

### Function for generating dates
`str_to_date()` function provide you format string along with the date string
	`STR_TO_DATE('September 17, 2020', '%M %d, %Y');`


### Temporal functions that return numbers
to determine the number of intervals(days,weeks, years..) between two dates you could use the `DATEDIFF()` function.











