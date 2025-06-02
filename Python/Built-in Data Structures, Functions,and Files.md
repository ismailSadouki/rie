## Tuple
A tuple is a fixed-length, immutable sequence of Python objects.
	 > tup = 4, 5, 6
You can convert any sequence or iterator to a tuple by invoking tuple :
	> tuple([4, 0, 2])
You can concatenate tuples using the + operator to produce longer tuples:
	 > (4, None, 'foo') + (6, 0) + ('bar',)
Unpacking tuples 
	> a, b, c = tup
if you may want to “pluck” a few elements from the beginning of
a tuple
	> a, b, *rest = tup
## List
In contrast with tuples, lists are variable-length and their contents can be modified
in-place.
	a_list = [2, 3, 7, None]
Check if a list contains a value using the in keyword. (Checking whether a list contains a value is a lot slower than doing so with dicts and
sets):
	> 'dwarf' in b_list
adding two lists together with + or extend method: (concatenation by addition is a comparatively expensive operation)
	> [4, None, 'foo'] + [7, 8, (2, 3)]
	> a_list.extend([7, 8, (2, 3)])
enumerate: It’s common when iterating over a sequence to want to keep track of the index of the
current item.
	> for i, value in enumerate(collection):
	
## zip
zip “pairs” up the elements of a number of lists, tuples, or other sequences to create a
list of tuples:
	seq1 = ['foo', 'bar', 'baz']
	seq2 = ['one', 'two', 'three']
	zipped = zip(seq1, seq2)
	list(zipped)
	OUT:[('foo', 'one'), ('bar', 'two'), ('baz', 'three')]
## dict (hash map)
	d1 = {'a' : 'some value', 'b' : [1, 2, 3, 4]}
You can merge one dict into another using the update method
	`d1.update({'b' : 'foo', 'c' : 12})`
It’s common to occasionally end up with two sequences that you want to pair up
element-wise in a dict
	`mapping = dict(zip(range(5), reversed(range(5))))
	`OUT: {0: 4, 1: 3, 2: 2, 3: 1, 4: 0}
## Set
A set is an unordered collection of unique elements. You can think of them like dicts,
but keys only, no values.
	`set([2, 2, 2, 1, 3, 3])
Sets support mathematical set operations like union, intersection, difference, and
symmetric difference.
Like dicts, set elements generally must be immutable. To have list-like elements, you
must convert it to a tuple.
You can also check if a set is a subset of (is contained in) or a superset of (contains all
elements of) another set:
	`{1, 2, 3}.issubset(a_set)
Sets are equal if and only if their contents are equal.

## List, Set, and Dict Comprehensions
List Comprehension:
	`[expr for val in collection if condition]`
Dict Comprehension:
	`dict_comp = {key-expr : value-expr for value in collection
	`if condition}
Set Comprehension:
	`set_comp = {expr for value in collection if condition}`

## 
