
## Returning Multiple Values
	`def f():
		`a = 5
		`b = 6
		`c` = 7
		`return a, b, c`
	`a, b, c = f()`
## Anonymous (Lambda) Functions
	def short_function(x):
		return x * 2
	
	equiv_anon = lambda x: x * 2
## Generators
An iterator is any object that will yield objects to the Python interpreter when used in
a context like a for loop.
	`dict_iterator = iter(some_dict)`
A generator is a concise way to construct a new iterable object.generators return a sequence of
multiple results lazily, pausing after each one until the next one is requested.
	`def squares(n=10):
		`print('Generating squares from 1 to {0}'.format(n ** 2))
		`for i in range(1, n + 1):
			`yield i ** 2
		`gen = squares()
		`for x in gen:
			`print(x, end=' ')