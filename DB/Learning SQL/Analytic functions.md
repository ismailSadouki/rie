### Data Windows
Analytic functions include the ability to  group rows into windows,which partition data for use by the analytic function without changing the overall result set.
Windows are defined using the `over` clause combined with an optional `partition by` sub-clause.
NOTE: different analytic functions  can define different data windows.
	`SELECT quarter(payment_date) quarter,
	`monthname(payment_date) month_nm,
	`sum(amount) monthly_sales,
	`max(sum(amount)) over () max_overall_sales,
	``max(sum(amount)) over (partition by `quarter(payment_date)) max_qrtr_sales
	`FROM payment
	`WHERE year(payment_date) = 2005
	``GROUP BY quarter(payment_date), `monthname(payment_date);

	the `over` cuase with an empty perenthesis `()` means that the function will be done over the entire set.
	the `partition by` clause specifies that the result set will split into multiple data windows.
### Localized sorting
Along with partitioning your result set into data  windows for your analytic functions, you may also specify a sort order.
	`rank() over (ORDER BY sum(amount) DESC) sales_rank`
if you want to use both `partition by and order clauses`
	`rank() over (partition by quarter(payment_date) ORDER BY sum(amount) DESC) qtr_sales_rank`

### Ranking Functions
- `row_number`
	Returns a unique number for each row,with  rankings arbitrarily assigned in case of a tie.
- `rank`
	Returns the same ranking in case of a tie, with gaps in the rankings.
- `dense_rank`
	Returns the same ranking in case of a tie, with no gaps in the rankings.

### Window Frames
What if you need to control over which  rows to include in a data window?
For example, Here's a query that sums payments for each week and includes a reporting function to calculate the rolling sum:
	`SELECT yearweek(payment_date) payment_week,
	`sum(amount) week_total,
	`sum(sum(amount))	over (order by yearweek(payment_date) rows unbounded preceding) rolling_sum`
	`FROM payment
	`GROUP BY yearweek(payment_date)
	`ORDER BY 1;
You even specify range or interval!

### Lag and Lead
another common reporting task involves comparing value from one row to another.
The `lag` function retrieve a column value from a prior row in the result set, and the `lead` function retrieve from a following row.
	`lag(sum(amount), 1) over (order by yearweek(payment_date)) prev_wk_tot`

### Column Value Concatenation
`group_concat` function is used to pivot a set of column values into a single delimited string, it works with group of rows within a data window.
	`group_concat(a.lname order by a.lname separator ',') actors`









































