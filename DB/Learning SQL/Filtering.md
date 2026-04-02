
### Matching Conditions
For example, you want to find all customers whose lase_name column begins with 'Q'
	`WHERE left(last_name, 1) = 'Q'`
But it's batter to use WILD-CARDS with the `LIKE` operator
	`_` Exactly one character matches
	`%` Any number of characters matches (including 0)

### NULL
when working with null, you should remember:
	- An expression can be `NULL`, but it can never equal null.
	- Two null are never equal to each other.
to test whether an expression is null, you need to use the `IS NULL or IS NOT NULL` operator
NOTE: if you preform some condition in data that is NULL, those columns who contain NULL value will not be returned.
