# 19.2 Nominal and Ordinal Variables
A numerical variable can be converted to an ordinal variable by dividing
the range of the numerical variable into bins and assigning values to each bin.
For example, a numerical variable between 1 and 10 can be divided into an ordinal variable with 5 labels with an ordinal relationship: 1-2, 3-4, 5-6, 7-8, 9-10. This is called **discretization**.
## 19.3.1 Ordinal Encoding
In ordinal encoding, each unique category value is assigned an integer value. For example, red is 1, green is 2, and blue is 3. This is called an ordinal encoding or an integer encoding and is easily reversible.
An integer ordinal encoding is a natural encoding for ordinal variables. For categorical variables, it imposes an ordinal relationship where no such relationship may exist. This can cause problems and a one hot encoding may be used instead.
in sklearn use `OrdinalEncoder` class

## 19.3.2 One Hot Encoding

# 19.3.3 Dummy Variable Encoding
The one hot encoding creates one binary variable for each category. The problem is that this representation includes redundancy. For example, if we know that [1, 0, 0] represents blue and [0, 1, 0] represents green we don’t need another binary variable to represent red, instead we could use 0 values alone, e.g. [0, 0]. This is called a dummy variable encoding, and always represents C categories with C − 1 binary variables.
```
When there are C possible values of the predictor and only C − 1 dummy variables are used, the matrix inverse can be computed and the contrast method is said to be a full rank parameterization
```
```
If the model includes an intercept and contains dummy variables [...], then the [...] columns would add up (row-wise) to the intercept and this linear combination would prevent the matrix inverse from being computed (as it is singular).
```
We can use the OneHotEncoder class to implement a dummy encoding as well as a one hot encoding. The drop argument can be set to indicate which category will become the one that is assigned all zero values, called the baseline. We can set this to ‘first’ so that the first category is used. When the labels are sorted alphabetically, the blue label will be the first and will become the baseline.

##### Q. What if I have a mixture of numeric and categorical data?
Or, what if I have a mixture of categorical and ordinal data? You will need to prepare or encode
each variable (column) in your dataset separately, then concatenate all of the prepared variables
back together into a single array for fitting or evaluating the model. Alternately, you can use the
ColumnTransformer to conditionally apply different data transforms to different input variables