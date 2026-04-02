# The NumPy ndarray: A Multidimensional Array Object
One of the key features of NumPy is its N-dimensional array object, or ndarray,
which is a fast, flexible container for large datasets in Python.
An ndarray is a generic multidimensional container for homogeneous data; that is, all
of the elements must be the same type. Every array has a shape , a tuple indicating the
size of each dimension, and a dtype , an object describing the data type of the array

## Creating ndarrays
The easiest way to create an array is to use the array function.
	`arr1 = np.array(data1)
arrays with zeros or ones...
	`np.zeros(10, 5)
## Arithmetic with NumPy Arrays
Arrays are important because they enable you to express batch operations on data
without writing any for loops. NumPy users call this vectorization.
array slices are views on the original array, This means that the data is not copied, and any modifications to the view will be
reflected in the source array.

## Fancy Indexing
Fancy indexing is a term adopted by NumPy to describe indexing using integer arrays.
To select out a subset of the rows in a particular order, you can simply pass a list or
ndarray of integers specifying the desired order:
	`arr[[4, 3, 0,6]]`
# Array-Oriented Programming with Arrays


## Expressing Conditional Logic as Array Operations
The numpy.where function is a vectorized version of the ternary expression x if con
dition else y .
	`xarr = np.array([1.1, 1.2, 1.3, 1.4, 1.5])
	`yarr = np.array([2.1, 2.2, 2.3, 2.4, 2.5])
	`cond = np.array([True, False, True, True, False])
	`result = np.where(cond, xarr, yarr) # if cond True assign xarr, else assign yarr