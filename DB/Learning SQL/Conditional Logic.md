Conditional logic is simply the ability to take one of several path during program execution.

### The Case Expression Types
- Searched Case Expressions:
	`CASE
		`WHEN C1 THEN E1
		`WHEN C2 THEN E2
		`...
		`[ELSE ...]
	`END
	C1 C2 ... represent conditions.
	E1 E2 ... represent expressions to be returned.
- Simple case Expressions:
	`CASE V0
		`WHEN V1 THEN E1
		`WHEN V2 THEN E2
		`...
		`[ELSE ...]
	`END
	V# represent values.
Simple case expressions are less flexible than searched  case expressions because you can't specify your own conditions, whereas searched case expressions may include any type of conditions.




### Division-by-Zero Errors
Some database will throw an error when a zero denominator is encountered, MySQL simply sets the result of the calculation to `null`.
To avoid dividing by Zero you should always wrap all denominators in conditional logic.


NOTE:
For calculation it's batter to translate `NULL` values into `0 or 1` to yield a non-null value.







