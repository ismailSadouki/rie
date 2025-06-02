	
## Series
A Series is a one-dimensional array-like object containing a sequence of values (of
similar types to NumPy types) and an associated array of data labels, called its index.
	`obj = pd.Series([4, 7, -5, 3])
Often it will be desirable to create a Series with an index identifying each data point
with a label
	`obj2 = pd.Series([4, 7, -5, 3], index=['d', 'b', 'a', 'c'])`
Should you have data contained in a Python dict, you can create a Series from it by
passing the dict:
	`sdata = {'Ohio': 35000, 'Texas': 71000, 'Oregon': 16000, 'Utah': 5000}`
	`obj3 = pd.Series(sdata)`
You can  passing the dict keys in the order you
want them to appear in the resulting Series:
	`states = ['California', 'Ohio', 'Oregon', 'Texas']
	`obj4 = pd.Series(sdata, index=states)
Both the Series object itself and its index have a name attribute, which integrates with
other key areas of pandas functionality:
	`obj4.name = 'population'
	`obj4.index.name = 'state'
A Series’s index can be altered in-place by assignment:
	`obj.index = ['Bob', 'Steve', 'Jeff', 'Ryan']`

## DataFrame
There are many ways to construct a DataFrame, though one of the most common is
from a dict of equal-length lists or NumPy arrays:
	`data = {'state': ['Ohio', 'Ohio', 'Ohio', 'Nevada', 'Nevada', 'Nevada'],
		`'year': [2000, 2001, 2002, 2001, 2002, 2003],
		`'pop': [1.5, 1.7, 3.6, 2.4, 2.9, 3.2]}`
	`frame = pd.DataFrame(data)
Another common form of data is a nested dict of dicts:
	`pop = {'Nevada': {2001: 2.4, 2002: 2.9},
			`'Ohio': {2000: 1.5, 2001: 1.7, 2002: 3.6}}
		If the nested dict is passed to the DataFrame, pandas will interpret the outer dict keys
		as the columns and the inner keys as the row indices.
If a DataFrame’s index and columns have their name attributes set, these will also be
displayed:
		`frame3.index.name = 'year'; frame3.columns.name = 'state'
As with Series, the values attribute returns the data contained in the DataFrame as a
two-dimensional ndarray:
	`frame3.values
		If the DataFrame’s columns are different dtypes, the dtype of the values array will be
		chosen to accommodate all of the columns.


## Index Objects
pandas’s Index objects are responsible for holding the axis labels and other metadata
(like the axis name or names).
	`obj = pd.Series(range(3), index=['a', 'b', 'c'])`
	`obj.index`
	`OUT:Index(['a', 'b', 'c'], dtype='object')`
Index objects are immutable and thus can’t be modified by the user.
Unlike Python sets, a pandas Index can contain duplicate labels:
	`pd.Index(['foo', 'foo', 'bar', 'bar'])`
	`OUT: Index(['foo', 'foo', 'bar', 'bar'], dtype='object')`
	Selections with duplicate labels will select all occurrences of that label.

## Essential Functionality

### Reindexing
An important method on pandas objects is reindex , which means to create a new
object with the data conformed to a new index.
	`obj = pd.Series([4.5, 7.2, -5.3, 3.6], index=['d', 'b', 'a', 'c'])`
	`obj`
	`OUT: d 4.5;b 7.2;a -5.3
	`obj.reindex(['a', 'b', 'd', 'e'])
	`OUT: a -5.3;b 7.2;d 4.5;e NaN`
### Selection with loc and iloc

### Integer Indexes
if you have an axis index containing integers, data selection will always be label-oriented. For more precise handling, use loc (for labels) or iloc (for integers).
### Arithmetic methods with fill values
In arithmetic operations between differently indexed objects, you might want to fill
with a special value, like 0, when an axis label is found in one object but not the other:
	`df1.add(df2, fill_value=0)`
Relatedly, when reindexing a Series or DataFrame, you can also specify a different fill
value:
	`df1.reindex(columns=df2.columns, fill_value=0)
### Operations between DataFrame and Series
- By default, arithmetic between DataFrame and Series matches the index of the Series
on the DataFrame’s columns, broadcasting down the rows.
- If an index value is not found in either the DataFrame’s columns or the Series’s index,
the objects will be reindexed to form the union.
- If you want to instead broadcast over the columns, matching on the rows, you have to
use one of the arithmetic methods. For example:
	`frame.sub(series3, axis='index')`

### Sorting and Ranking
use the sort_index method, which returns a new, sorted object:
	`pd.Series(range(4), index=['d', 'a', 'b', 'c']).sort_index()`
With a DataFrame, you can sort by index on either axis:
	`frame.sort_index()`
	`frame.sort_index(axis=1)`
### Axis Indexes with Duplicate Labels
The index’s is_unique property can tell you whether its labels are unique or not:
		`obj.index.is_unique`



## Handling Missing Data

### Filtering Out Missing Data
the dropna can be helpful. On a Series, it returns the Series with only the non-null data and index values:
	`data.dropna()`
		this is equivalent to `data[data.notnull()]`
- `dropna` by default drops any row containing a missing value

## Data Transformation

### Renaming Axis Indexes
Like a Series, the axis indexes have a map method:
	`ransform = lambda x: x[:4].upper()
	`data.index.map(transform)
### Discretization and Binning
Suppose you have data about a group of people in a study, and you want to group
them into discrete age buckets:
	`cats = pd.cut(ages, bins)`
	You can change which side is closed by passing `right=False`
	You can also pass your own bin names by passing a list or array to the `labels` option.




## Computing Indicator/Dummy Variables
If a column in a DataFrame has k distinct values, you would derive a matrix or Data‐
Frame with k columns containing all 1s and 0s. pandas has a get_dummies function
for doing this, though devising one yourself is not difficult. Let’s return to an earlier
example DataFrame:
	`df = pd.DataFrame({'key': ['b', 'b', 'a', 'c', 'a', 'b'], 'data': range(6)})`
	`pd.get_dummies(df['key'])`
In some cases, you may want to add a prefix to the columns in the indicator Data‐Frame,
	`dummies = pd.get_dummies(df['key'], prefix='key')`
	`df_with_dummy = df[['data1']].join(dummies)`




# Data Wrangling: Join, Combine, and Reshape

### Hierarchical Indexing
Hierarchical indexing is an important feature of pandas that enables you to have mul‐
tiple (two or more) index levels on an axis.
create a Series with a list of lists (or arrays) as the index:
	`data = pd.Series(np.random.randn(9), index=[['a', 'a', 'a', 'b', 'b', 'c', 'c', 'd', 'd'], [1, 2, 3, 1, 3, 1, 2, 2, 3]])`
Hierarchical indexing plays an important role in reshaping data and group-based
operations like forming a pivot table. For example, you could rearrange the data into
a DataFrame using its unstack method:
	`data.unstack()`
- The inverse operation of unstack is stack:
With a DataFrame, either axis can have a hierarchical index:
	`frame = pd.DataFrame(np.arange(12).reshape((4, 3)), 
		`index=[['a', 'a', 'b', 'b'], [1, 2, 1, 2]],
		`columns=[['Ohio', 'Ohio', 'Colorado'], ['Green', 'Red', 'Green']])`
The hierarchical levels can have names (as strings or any Python objects). If so, these
will show up in the console output:
	`frame.index.names = ['key1', 'key2']
	`frame.columns.names = ['state', 'color']
A MultiIndex can be created by itself and then reused; the columns in the preceding
DataFrame with level names could be created like this:
	`MultiIndex.from_arrays([['Ohio', 'Ohio', 'Colorado'], ['Green', 'Red', 'Green']], names=['state', 'color'])`

### Reordering and Sorting Levels
At times you will need to rearrange the order of the levels on an axis or sort the data
by the values in one specific level.\
	`frame.swaplevel('key1', 'key2')`
sort_index , on the other hand, sorts the data using only the values in a single level.
	`frame.sort_index(level=1)`
### Indexing with a DataFrame’s columns





## Combining and Merging Datasets

### Databise-style Dataframe Joins
Merge or join operations combine datasets by linking rows using one or more keys.
many-to-one join: using the merge func: `pd.merge(df1,df2, on='key')` . Note: if the column names are different in each object : `pd.merge(df1, df2, left_on='lkey', right_on='rkey')`
By default merge does an 'inner' join. the keys are the intersection. you could change that by using the attribute `how='inner/outer/right/left'`
- to merge with multiple keys: `on=['key1', 'key2']`

combine together many DataFrame objects having the same or similar indexes but
non-overlapping columns: `left1.join(right1, on='key')`