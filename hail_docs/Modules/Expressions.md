## Expressions


`eval`(expression) | Evaluate a Hail expression, returning the result.  
---|---  
```Expression``` | Base class for Hail expressions.  
---|---  
```ArrayExpression``` | Expression of type ```tarray```.  
```ArrayNumericExpression``` | Expression of type ```tarray``` with a numeric type.  
```BooleanExpression``` | Expression of type ```tbool```.  
```CallExpression``` | Expression of type ```tcall```.  
```CollectionExpression``` | Expression of type ```tarray``` or ```tset```  
```DictExpression``` | Expression of type ```tdict```.  
```IntervalExpression``` | Expression of type ```tinterval```.  
```LocusExpression``` | Expression of type ```tlocus```.  
```NumericExpression``` | Expression of numeric type.  
```Int32Expression``` | Expression of type ```tint32```.  
```Int64Expression``` | Expression of type ```tint64```.  
```Float32Expression``` | Expression of type ```tfloat32```.  
```Float64Expression``` | Expression of type ```tfloat64```.  
```SetExpression``` | Expression of type ```tset```.  
```StringExpression``` | Expression of type ```tstr```.  
```StructExpression``` | Expression of type ```tstruct```.  
```TupleExpression``` | Expression of type ```ttuple```.  
```NDArrayExpression``` | Expression of type ```tndarray```.  
```NDArrayNumericExpression``` | Expression of type ```tndarray``` with a numeric element type.  
  
hail.expr.eval(_expression_)[[source]](_modules/hail/expr/expressions/expression_utils.html#eval)

    

Evaluate a Hail expression, returning the result.

This method is extremely useful for learning about Hail expressions and
understanding how to compose them.

The expression must have no indices, but can refer to the globals of a
```Table``` or
```MatrixTable```.

Examples

Evaluate a conditional:

    
    
    >>> x = 6
    >>> hl.eval(hl.if_else(x % 2 == 0, 'Even', 'Odd'))
    'Even'
    

Parameters:

    

**expression** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Any expression, or a Python value that can be
implicitly interpreted as an expression.

Returns:

    

_Any_

---



### Expression

_class
_hail.expr.Expression[[source]](_modules/hail/expr/expressions/base_expression.html#Expression)

    

Base class for Hail expressions.

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`collect` | Collect all records of an expression into a local list.  
---|---  
`describe` | Print information about type, index, and dependencies.  
`export` | Export a field to a text file.  
`show` | Print the first few records of the expression to the console.  
`summarize` | Compute and print summary information about the expression.  
`take` | Collect the first n records of an expression.  
  
__eq__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__eq__)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** (`Expression`) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__ge__)

    

Return self>=value.

__gt__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__gt__)

    

Return self>value.

__le__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__le__)

    

Return self<=value.

__lt__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__lt__)

    

Return self<value.

__ne__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__ne__)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** (`Expression`) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize
=True_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.collect)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function
print>_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.describe)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header
=True_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.export)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols
=None_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.show)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler
=None_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.summarize)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize
=True_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.take)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### BooleanExpression

_class
_hail.expr.BooleanExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#BooleanExpression)

    

Expression of type [`tbool`](types.html#hail.expr.types.tbool
"hail.expr.types.tbool").

    
    
    >>> t = hl.literal(True)
    >>> f = hl.literal(False)
    >>> na = hl.missing(hl.tbool)
    
    
    
    >>> hl.eval(t)
    True
    
    
    
    >>> hl.eval(f)
    False
    
    
    
    >>> hl.eval(na)
    None
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to add.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Sum of the two numbers.

__and__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#BooleanExpression.__and__)

    

Return `True` if the left and right arguments are `True`.

Examples

    
    
    >>> hl.eval(t & f)
    False
    
    
    
    >>> hl.eval(t & na)
    None
    
    
    
    >>> hl.eval(f & na)
    False
    

The `&` and `|` operators have higher priority than comparison operators like
`==`, `<`, or `>`. Parentheses are often necessary:

    
    
    >>> x = hl.literal(5)
    
    
    
    >>> hl.eval((x < 10) & (x > 2))
    True
    

Parameters:

    

**other** (`BooleanExpression`) – Right-side operand.

Returns:

    

`BooleanExpression` – `True` if both left and right are `True`.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

`BooleanExpression` – `True` if the two expressions are equal.

__floordiv__(_other_)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The floor of the left number divided by the
right.

__ge__(_other_)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

`BooleanExpression` – `True` if the left side is greater than or equal to the
right side.

__gt__(_other_)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

`BooleanExpression` – `True` if the left side is greater than the right side.

__invert__()[[source]](_modules/hail/expr/expressions/typed_expressions.html#BooleanExpression.__invert__)

    

Return the boolean negation.

Examples

    
    
    >>> hl.eval(~t)
    False
    
    
    
    >>> hl.eval(~f)
    True
    
    
    
    >>> hl.eval(~na)
    None
    

Returns:

    

`BooleanExpression` – Boolean negation.

__le__(_other_)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

`BooleanExpression` – `True` if the left side is smaller than or equal to the
right side.

__lt__(_other_)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

`BooleanExpression` – `True` if the left side is smaller than the right side.

__mod__(_other_)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Remainder after dividing the left by the
right.

__mul__(_other_)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to multiply.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

`BooleanExpression` – `True` if the two expressions are not equal.

__neg__()

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Negated number.

__or__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#BooleanExpression.__or__)

    

Return `True` if at least one of the left and right arguments is `True`.

Examples

    
    
    >>> hl.eval(t | f)
    True
    
    
    
    >>> hl.eval(t | na)
    True
    
    
    
    >>> hl.eval(f | na)
    None
    

The `&` and `|` operators have higher priority than comparison operators like
`==`, `<`, or `>`. Parentheses are often necessary:

    
    
    >>> x = hl.literal(5)
    
    
    
    >>> hl.eval((x < 10) | (x > 20))
    True
    

Parameters:

    

**other** (`BooleanExpression`) – Right-side operand.

Returns:

    

`BooleanExpression` – `True` if either left or right is `True`.

__pow__(_power_ , _modulo =None_)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (```NumericExpression```)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to subtract.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Difference of the two numbers.

__truediv__(_other_)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### StructExpression

_class
_hail.expr.StructExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression)

    

Expression of type [`tstruct`](types.html#hail.expr.types.tstruct
"hail.expr.types.tstruct").

    
    
    >>> struct = hl.struct(a=5, b='Foo')
    

Struct fields are accessible as attributes and keys. It is therefore possible
to access field a of struct s with dot syntax:

    
    
    >>> hl.eval(struct.a)
    5
    

However, it is recommended to use square brackets to select fields:

    
    
    >>> hl.eval(struct['a'])
    5
    

The latter syntax is safer, because fields that share their name with an
existing attribute of `StructExpression` (keys, values, annotate, drop, etc.)
will only be accessible using the `StructExpression.__getitem__()` syntax.
This is also the only way to access fields that are not valid Python
identifiers, like fields with spaces or symbols.

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`annotate` | Add new fields or recompute existing fields.  
---|---  
`drop` | Drop fields from the struct.  
`flatten` | Recursively eliminate struct fields by adding their fields to this struct.  
`get` | See `StructExpression.__getitem__()`  
`items` | A list of pairs of field name and expression for said field.  
`keys` | The list of field names.  
`rename` | Rename fields of the struct.  
`select` | Select existing fields and compute new ones.  
`values` | A list of expressions for each field.  
  
__class_getitem___ = <bound method GenericAlias of <class
'hail.expr.expressions.typed_expressions.StructExpression'>>_

    

__eq__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.__eq__)

    

Check each field for equality.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – An expression of the same type.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.__getitem__)

    

Access a field of the struct by name or index.

Examples

    
    
    >>> hl.eval(struct['a'])
    5
    
    
    
    >>> hl.eval(struct[1])
    'Foo'
    

Parameters:

    

**item** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Field name.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Struct field.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.__ne__)

    

Return self!=value.

annotate(_**
named_exprs_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.annotate)

    

Add new fields or recompute existing fields.

Examples

    
    
    >>> hl.eval(struct.annotate(a=10, c=2*2*2))
    Struct(a=10, b='Foo', c=8)
    

Notes

If an expression in named_exprs shares a name with a field of the struct, then
that field will be replaced but keep its position in the struct. New fields
will be appended to the end of the struct.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Fields to add.

Returns:

    

`StructExpression` – Struct with new or updated fields.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

drop(_*
fields_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.drop)

    

Drop fields from the struct.

Examples

    
    
    >>> hl.eval(struct.drop('b'))
    Struct(a=5)
    

Parameters:

    

**fields** (varargs of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – Fields to drop.

Returns:

    

`StructExpression` – Struct without certain fields.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

flatten()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.flatten)

    

Recursively eliminate struct fields by adding their fields to this struct.

get(_k_ , _default
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.get)

    

See `StructExpression.__getitem__()`

items()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.items)

    

A list of pairs of field name and expression for said field.

keys()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.keys)

    

The list of field names.

rename(_mapping_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.rename)

    

Rename fields of the struct.

Examples

    
    
    >>> s = hl.struct(x='hello', y='goodbye', a='dogs')
    >>> s.rename({'x' : 'y', 'y' : 'z'}).show()
    +----------+----------+-----------+
    | <expr>.a | <expr>.y | <expr>.z  |
    +----------+----------+-----------+
    | str      | str      | str       |
    +----------+----------+-----------+
    | "dogs"   | "hello"  | "goodbye" |
    +----------+----------+-----------+
    

Parameters:

    

**mapping** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict
"\(in Python v3.9\)") of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)"), [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Mapping from old field names to new field names.

Notes

Any field that does not appear as a key in mapping will not be renamed.

Returns:

    

`StructExpression` – Struct with renamed fields.

select(_* fields_, _**
named_exprs_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.select)

    

Select existing fields and compute new ones.

Examples

    
    
    >>> hl.eval(struct.select('a', c=['bar', 'baz']))
    Struct(a=5, c=['bar', 'baz'])
    

Notes

The fields argument is a list of field names to keep. These fields will appear
in the resulting struct in the order they appear in fields.

The named_exprs arguments are new field expressions.

Parameters:

    

  * **fields** (varargs of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Field names to keep.

  * **named_exprs** (keyword args of ```Expression```) – New field expressions.

Returns:

    

`StructExpression` – Struct containing specified existing fields and computed
fields.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

values()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.values)

    

A list of expressions for each field.

---


### ArrayExpression

_class
_hail.expr.ArrayExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression)

    

Expression of type [`tarray`](types.html#hail.expr.types.tarray
"hail.expr.types.tarray").

    
    
    >>> names = hl.literal(['Alice', 'Bob', 'Charlie'])
    

See also

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression")

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`aggregate` | Uses the aggregator library to compute a summary from an array.  
---|---  
`append` | Append an element to the array and return the result.  
`contains` | Returns a boolean indicating whether item is found in the array.  
`extend` | Concatenate two arrays and return the result.  
`first` | Returns the first element of the array, or missing if empty.  
`grouped` | Partition an array into fixed size subarrays.  
`head` | Deprecated in favor of `first()`.  
`index` | Returns the first index of x, or missing.  
`last` | Returns the last element of the array, or missing if empty.  
`scan` | Map each element of the array to cumulative value of function f, with initial value zero.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.__getitem__)

    

Index into or slice the array.

Examples

Index with a single integer:

    
    
    >>> hl.eval(names[1])
    'Bob'
    
    
    
    >>> hl.eval(names[-1])
    'Charlie'
    

Slicing is also supported:

    
    
    >>> hl.eval(names[1:])
    ['Bob', 'Charlie']
    

Parameters:

    

**item** (slice or
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")) – Index or slice.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element or array slice.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

aggregate(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.aggregate)

    

Uses the aggregator library to compute a summary from an array.

This method is useful for accessing functionality that exists in the
aggregator library but not the basic expression library, for instance,
[`call_stats()`](aggregators.html#hail.expr.aggregators.call_stats
"hail.expr.aggregators.call_stats").

Parameters:

    

**f** – Aggregation function

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

all(_f_)

    

Returns `True` if f returns `True` for every element.

Examples

    
    
    >>> hl.eval(a.all(lambda x: x < 10))
    True
    

Notes

This method returns `True` if the collection is empty.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for every
element, `False` otherwise.

any(_f_)

    

Returns `True` if f returns `True` for any element.

Examples

    
    
    >>> hl.eval(a.any(lambda x: x % 2 == 0))
    True
    
    
    
    >>> hl.eval(s3.any(lambda x: x[0] == 'D'))
    False
    

Notes

This method always returns `False` for empty collections.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for any element,
`False` otherwise.

append(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.append)

    

Append an element to the array and return the result.

Examples

    
    
    >>> hl.eval(names.append('Dan'))
    ['Alice', 'Bob', 'Charlie', 'Dan']
    

Note

This method does not mutate the caller, but instead returns a new array by
copying the caller and adding item.

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Element to append, same type as the array element
type.

Returns:

    

`ArrayExpression`

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.contains)

    

Returns a boolean indicating whether item is found in the array.

Examples

    
    
    >>> hl.eval(names.contains('Charlie'))
    True
    
    
    
    >>> hl.eval(names.contains('Helen'))
    False
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Item for inclusion test.

Warning

This method takes time proportional to the length of the array. If a pipeline
uses this method on the same array several times, it may be more efficient to
convert the array to a set first early in the script
([`set()`](functions/constructors.html#hail.expr.functions.set
"hail.expr.functions.set")).

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the element is found in the array,
`False` otherwise.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

extend(_a_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.extend)

    

Concatenate two arrays and return the result.

Examples

    
    
    >>> hl.eval(names.extend(['Dan', 'Edith']))
    ['Alice', 'Bob', 'Charlie', 'Dan', 'Edith']
    

Parameters:

    

**a** (`ArrayExpression`) – Array to concatenate, same type as the callee.

Returns:

    

`ArrayExpression`

filter(_f_)

    

Returns a new collection containing elements where f returns `True`.

Examples

    
    
    >>> hl.eval(a.filter(lambda x: x % 2 == 0))
    [2, 4]
    
    
    
    >>> hl.eval(s3.filter(lambda x: ~(x[-1] == 'e')))  
    {'Bob'}
    

Notes

Returns a same-type expression; evaluated on a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"), returns a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). Evaluated on an `ArrayExpression`, returns an
`ArrayExpression`.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression") – Expression of the same type as the callee.

find(_f_)

    

Returns the first element where f returns `True`.

Examples

    
    
    >>> hl.eval(a.find(lambda x: x ** 2 > 20))
    5
    
    
    
    >>> hl.eval(s3.find(lambda x: x[0] == 'D'))
    None
    

Notes

If f returns `False` for every element, then the result is missing.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Expression whose type is the element type of the
collection.

first()[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.first)

    

Returns the first element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.first())
    'Alice'
    

If the array has no elements, then the result is missing: >>>
hl.eval(names.filter(lambda x: x.startswith(‘D’)).first()) None

flatmap(_f_)

    

Map each element of the collection to a new collection, and flatten the
results.

Examples

    
    
    >>> hl.eval(a.flatmap(lambda x: hl.range(0, x)))
    [0, 0, 1, 0, 1, 2, 0, 1, 2, 3, 0, 1, 2, 3, 4]
    
    
    
    >>> hl.eval(s3.flatmap(lambda x: hl.set(hl.range(0, x.length()).map(lambda i: x[i]))))  
    {'A', 'B', 'C', 'a', 'b', 'c', 'e', 'h', 'i', 'l', 'o', 'r'}
    

Parameters:

    

**f** (function ( (arg) ->
[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"))) – Function from the element type of the
collection to the type of the collection. For instance, flatmap on a
`set<str>` should take a `str` and return a `set`.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression")

fold(_f_ , _zero_)

    

Reduces the collection with the given function f, provided the initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.fold(lambda i, j: i + j, 0, a))
    3
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression").

group_by(_f_)

    

Group elements into a dict according to a lambda function.

Examples

    
    
    >>> hl.eval(a.group_by(lambda x: x % 2 == 0))  
    {False: [1, 3, 5], True: [2, 4]}
    
    
    
    >>> hl.eval(s3.group_by(lambda x: x.length()))  
    {3: {'Bob'}, 5: {'Alice'}, 7: {'Charlie'}}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to evaluate for each element of the
collection to produce a key for the resulting dictionary.

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"). – Dictionary keyed by results of f.

grouped(_group_size_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.grouped)

    

Partition an array into fixed size subarrays.

Examples

    
    
    >>> a = hl.array([0, 1, 2, 3, 4])
    
    
    
    >>> hl.eval(a.grouped(2))
    [[0, 1], [2, 3], [4]]
    

Parameters:

    

**group_size**
([`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")) – The number of elements per group.

Returns:

    

`ArrayExpression`.

head()[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.head)

    

Deprecated in favor of `first()`.

Returns the first element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.head())
    'Alice'
    

If the array has no elements, then the result is missing:

    
    
    >>> hl.eval(names.filter(lambda x: x.startswith('D')).head())
    None
    

index(_x_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.index)

    

Returns the first index of x, or missing.

Parameters:

    

**x** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") or
[`typing.Callable`](https://docs.python.org/3.9/library/typing.html#typing.Callable
"\(in Python v3.9\)")) – Value to find, or function from element to Boolean
expression.

Returns:

    

[`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

Examples

    
    
    >>> hl.eval(names.index('Bob'))
    1
    
    
    
    >>> hl.eval(names.index('Beth'))
    None
    
    
    
    >>> hl.eval(names.index(lambda x: x.endswith('e')))
    0
    
    
    
    >>> hl.eval(names.index(lambda x: x.endswith('h')))
    None
    

last()[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.last)

    

Returns the last element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.last())
    'Charlie'
    

If the array has no elements, then the result is missing: >>>
hl.eval(names.filter(lambda x: x.startswith(‘D’)).last()) None

length()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.length())
    5
    
    
    
    >>> hl.eval(s3.length())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

map(_f_)

    

Transform each element of a collection.

Examples

    
    
    >>> hl.eval(a.map(lambda x: x ** 3))
    [1.0, 8.0, 27.0, 64.0, 125.0]
    
    
    
    >>> hl.eval(s3.map(lambda x: x.length()))
    {3, 5, 7}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

scan(_f_ ,
_zero_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.scan)

    

Map each element of the array to cumulative value of function f, with initial
value zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.array_scan(lambda i, j: i + j, 0, a))
    [0, 0, 1, 3]
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

`ArrayExpression`.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

size()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.size())
    5
    
    
    
    >>> hl.eval(s3.size())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

starmap(_f_)

    

Transform each element of a collection of tuples.

Examples

    
    
    >>> hl.eval(hl.array([(1, 2), (2, 3)]).starmap(lambda x, y: x+y))
    [3, 5]
    

Parameters:

    

**f** (function ( (*args) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### ArrayNumericExpression

_class
_hail.expr.ArrayNumericExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression)

    

Expression of type [`tarray`](types.html#hail.expr.types.tarray
"hail.expr.types.tarray") with a numeric type.

Numeric arrays support arithmetic both with scalar values and other arrays.
Arithmetic between two numeric arrays requires that the length of each array
is identical, and will apply the operation positionally (`a1 * a2` will
multiply the first element of `a1` by the first element of `a2`, the second
element of `a1` by the second element of `a2`, and so on). Arithmetic with a
scalar will apply the operation to each element of the array.

    
    
    >>> a1 = hl.literal([0, 1, 2, 3, 4, 5])
    
    
    
    >>> a2 = hl.literal([1, -1, 1, -1, 1, -1])
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__add__)

    

Positionally add an array or a scalar.

Examples

    
    
    >>> hl.eval(a1 + 5)
    [5, 6, 7, 8, 9, 10]
    
    
    
    >>> hl.eval(a1 + a2)
    [1, 0, 3, 2, 5, 4]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`) – Value or array
to add.

Returns:

    

`ArrayNumericExpression` – Array of positional sums.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__floordiv__)

    

Positionally divide by an array or a scalar using floor division.

Examples

    
    
    >>> hl.eval(a1 // 2)
    [0, 0, 1, 1, 2, 2]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`)

Returns:

    

`ArrayNumericExpression`

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)

    

Index into or slice the array.

Examples

Index with a single integer:

    
    
    >>> hl.eval(names[1])
    'Bob'
    
    
    
    >>> hl.eval(names[-1])
    'Charlie'
    

Slicing is also supported:

    
    
    >>> hl.eval(names[1:])
    ['Bob', 'Charlie']
    

Parameters:

    

**item** (slice or
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")) – Index or slice.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element or array slice.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__mod__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__mod__)

    

Positionally compute the left modulo the right.

Examples

    
    
    >>> hl.eval(a1 % 2)
    [0, 1, 0, 1, 0, 1]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`)

Returns:

    

`ArrayNumericExpression`

__mul__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__mul__)

    

Positionally multiply by an array or a scalar.

Examples

    
    
    >>> hl.eval(a2 * 5)
    [5, -5, 5, -5, 5, -5]
    
    
    
    >>> hl.eval(a1 * a2)
    [0, -1, 2, -3, 4, -5]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`) – Value or array
to multiply by.

Returns:

    

`ArrayNumericExpression` – Array of positional products.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__neg__)

    

Negate elements of the array.

Examples

    
    
    >>> hl.eval(-a1)
    [0, -1, -2, -3, -4, -5]
    

Returns:

    

`ArrayNumericExpression` – Array expression of the same type.

__pow__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__pow__)

    

Positionally raise to the power of an array or a scalar.

Examples

    
    
    >>> hl.eval(a1 ** 2)
    [0.0, 1.0, 4.0, 9.0, 16.0, 25.0]
    
    
    
    >>> hl.eval(a1 ** a2)
    [0.0, 1.0, 2.0, 0.3333333333333333, 4.0, 0.2]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`)

Returns:

    

`ArrayNumericExpression`

__sub__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__sub__)

    

Positionally subtract an array or a scalar.

Examples

    
    
    >>> hl.eval(a2 - 1)
    [0, -2, 0, -2, 0, -2]
    
    
    
    >>> hl.eval(a1 - a2)
    [-1, 2, 1, 4, 3, 6]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`) – Value or array
to subtract.

Returns:

    

`ArrayNumericExpression` – Array of positional differences.

__truediv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__truediv__)

    

Positionally divide by an array or a scalar.

Examples

    
    
    >>> hl.eval(a1 / 10)  
    [0.0, 0.1, 0.2, 0.3, 0.4, 0.5]
    
    
    
    >>> hl.eval(a2 / a1)  
    [inf, -1.0, 0.5, -0.3333333333333333, 0.25, -0.2]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`) – Value or array
to divide by.

Returns:

    

`ArrayNumericExpression` – Array of positional quotients.

aggregate(_f_)

    

Uses the aggregator library to compute a summary from an array.

This method is useful for accessing functionality that exists in the
aggregator library but not the basic expression library, for instance,
[`call_stats()`](aggregators.html#hail.expr.aggregators.call_stats
"hail.expr.aggregators.call_stats").

Parameters:

    

**f** – Aggregation function

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

all(_f_)

    

Returns `True` if f returns `True` for every element.

Examples

    
    
    >>> hl.eval(a.all(lambda x: x < 10))
    True
    

Notes

This method returns `True` if the collection is empty.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for every
element, `False` otherwise.

any(_f_)

    

Returns `True` if f returns `True` for any element.

Examples

    
    
    >>> hl.eval(a.any(lambda x: x % 2 == 0))
    True
    
    
    
    >>> hl.eval(s3.any(lambda x: x[0] == 'D'))
    False
    

Notes

This method always returns `False` for empty collections.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for any element,
`False` otherwise.

append(_item_)

    

Append an element to the array and return the result.

Examples

    
    
    >>> hl.eval(names.append('Dan'))
    ['Alice', 'Bob', 'Charlie', 'Dan']
    

Note

This method does not mutate the caller, but instead returns a new array by
copying the caller and adding item.

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Element to append, same type as the array element
type.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_item_)

    

Returns a boolean indicating whether item is found in the array.

Examples

    
    
    >>> hl.eval(names.contains('Charlie'))
    True
    
    
    
    >>> hl.eval(names.contains('Helen'))
    False
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Item for inclusion test.

Warning

This method takes time proportional to the length of the array. If a pipeline
uses this method on the same array several times, it may be more efficient to
convert the array to a set first early in the script
([`set()`](functions/constructors.html#hail.expr.functions.set
"hail.expr.functions.set")).

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the element is found in the array,
`False` otherwise.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

extend(_a_)

    

Concatenate two arrays and return the result.

Examples

    
    
    >>> hl.eval(names.extend(['Dan', 'Edith']))
    ['Alice', 'Bob', 'Charlie', 'Dan', 'Edith']
    

Parameters:

    

**a**
([`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")) – Array to concatenate, same type as the callee.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

filter(_f_)

    

Returns a new collection containing elements where f returns `True`.

Examples

    
    
    >>> hl.eval(a.filter(lambda x: x % 2 == 0))
    [2, 4]
    
    
    
    >>> hl.eval(s3.filter(lambda x: ~(x[-1] == 'e')))  
    {'Bob'}
    

Notes

Returns a same-type expression; evaluated on a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"), returns a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). Evaluated on an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression"), returns an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression") – Expression of the same type as the callee.

find(_f_)

    

Returns the first element where f returns `True`.

Examples

    
    
    >>> hl.eval(a.find(lambda x: x ** 2 > 20))
    5
    
    
    
    >>> hl.eval(s3.find(lambda x: x[0] == 'D'))
    None
    

Notes

If f returns `False` for every element, then the result is missing.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Expression whose type is the element type of the
collection.

first()

    

Returns the first element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.first())
    'Alice'
    

If the array has no elements, then the result is missing: >>>
hl.eval(names.filter(lambda x: x.startswith(‘D’)).first()) None

flatmap(_f_)

    

Map each element of the collection to a new collection, and flatten the
results.

Examples

    
    
    >>> hl.eval(a.flatmap(lambda x: hl.range(0, x)))
    [0, 0, 1, 0, 1, 2, 0, 1, 2, 3, 0, 1, 2, 3, 4]
    
    
    
    >>> hl.eval(s3.flatmap(lambda x: hl.set(hl.range(0, x.length()).map(lambda i: x[i]))))  
    {'A', 'B', 'C', 'a', 'b', 'c', 'e', 'h', 'i', 'l', 'o', 'r'}
    

Parameters:

    

**f** (function ( (arg) ->
[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"))) – Function from the element type of the
collection to the type of the collection. For instance, flatmap on a
`set<str>` should take a `str` and return a `set`.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression")

fold(_f_ , _zero_)

    

Reduces the collection with the given function f, provided the initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.fold(lambda i, j: i + j, 0, a))
    3
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression").

group_by(_f_)

    

Group elements into a dict according to a lambda function.

Examples

    
    
    >>> hl.eval(a.group_by(lambda x: x % 2 == 0))  
    {False: [1, 3, 5], True: [2, 4]}
    
    
    
    >>> hl.eval(s3.group_by(lambda x: x.length()))  
    {3: {'Bob'}, 5: {'Alice'}, 7: {'Charlie'}}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to evaluate for each element of the
collection to produce a key for the resulting dictionary.

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"). – Dictionary keyed by results of f.

grouped(_group_size_)

    

Partition an array into fixed size subarrays.

Examples

    
    
    >>> a = hl.array([0, 1, 2, 3, 4])
    
    
    
    >>> hl.eval(a.grouped(2))
    [[0, 1], [2, 3], [4]]
    

Parameters:

    

**group_size**
([`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")) – The number of elements per group.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

head()

    

Deprecated in favor of
[`first()`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression.first
"hail.expr.ArrayExpression.first").

Returns the first element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.head())
    'Alice'
    

If the array has no elements, then the result is missing:

    
    
    >>> hl.eval(names.filter(lambda x: x.startswith('D')).head())
    None
    

index(_x_)

    

Returns the first index of x, or missing.

Parameters:

    

**x** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") or
[`typing.Callable`](https://docs.python.org/3.9/library/typing.html#typing.Callable
"\(in Python v3.9\)")) – Value to find, or function from element to Boolean
expression.

Returns:

    

[`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

Examples

    
    
    >>> hl.eval(names.index('Bob'))
    1
    
    
    
    >>> hl.eval(names.index('Beth'))
    None
    
    
    
    >>> hl.eval(names.index(lambda x: x.endswith('e')))
    0
    
    
    
    >>> hl.eval(names.index(lambda x: x.endswith('h')))
    None
    

last()

    

Returns the last element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.last())
    'Charlie'
    

If the array has no elements, then the result is missing: >>>
hl.eval(names.filter(lambda x: x.startswith(‘D’)).last()) None

length()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.length())
    5
    
    
    
    >>> hl.eval(s3.length())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

map(_f_)

    

Transform each element of a collection.

Examples

    
    
    >>> hl.eval(a.map(lambda x: x ** 3))
    [1.0, 8.0, 27.0, 64.0, 125.0]
    
    
    
    >>> hl.eval(s3.map(lambda x: x.length()))
    {3, 5, 7}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

scan(_f_ , _zero_)

    

Map each element of the array to cumulative value of function f, with initial
value zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.array_scan(lambda i, j: i + j, 0, a))
    [0, 0, 1, 3]
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

size()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.size())
    5
    
    
    
    >>> hl.eval(s3.size())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

starmap(_f_)

    

Transform each element of a collection of tuples.

Examples

    
    
    >>> hl.eval(hl.array([(1, 2), (2, 3)]).starmap(lambda x, y: x+y))
    [3, 5]
    

Parameters:

    

**f** (function ( (*args) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### CallExpression

_class
_hail.expr.CallExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression)

    

Expression of type [`tcall`](types.html#hail.expr.types.tcall
"hail.expr.types.tcall").

    
    
    >>> call = hl.call(0, 1, phased=False)
    

Attributes

`dtype` | The data type of the expression.  
---|---  
`phased` | True if the call is phased.  
`ploidy` | Return the number of alleles of this call.  
  
Methods

`contains_allele` | Returns true if the call has one or more called alleles of the given index.  
---|---  
`is_diploid` | True if the call has ploidy equal to 2.  
`is_haploid` | True if the call has ploidy equal to 1.  
`is_het` | Evaluate whether the call includes two different alleles.  
`is_het_non_ref` | Evaluate whether the call includes two different alleles, neither of which is reference.  
`is_het_ref` | Evaluate whether the call includes two different alleles, one of which is reference.  
`is_hom_ref` | Evaluate whether the call includes two reference alleles.  
`is_hom_var` | Evaluate whether the call includes two identical alternate alleles.  
`is_non_ref` | Evaluate whether the call includes one or more non-reference alleles.  
`n_alt_alleles` | Returns the number of non-reference alleles.  
`one_hot_alleles` | Returns an array containing the summed one-hot encoding of the alleles.  
`unphase` | Returns an unphased version of this call.  
`unphased_diploid_gt_index` | Return the genotype index for unphased, diploid calls.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.__getitem__)

    

Get the i*th* allele.

Examples

Index with a single integer:

    
    
    >>> hl.eval(call[0])
    0
    
    
    
    >>> hl.eval(call[1])
    1
    

Parameters:

    

**item** (int or [`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")) – Allele index.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains_allele(_allele_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.contains_allele)

    

Returns true if the call has one or more called alleles of the given index.

    
    
    >>> c = hl.call(0, 3)
    
    
    
    >>> hl.eval(c.contains_allele(3))
    True
    
    
    
    >>> hl.eval(c.contains_allele(1))
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

is_diploid()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_diploid)

    

True if the call has ploidy equal to 2.

Examples

    
    
    >>> hl.eval(call.is_diploid())
    True
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

is_haploid()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_haploid)

    

True if the call has ploidy equal to 1.

Examples

    
    
    >>> hl.eval(call.is_haploid())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

is_het()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_het)

    

Evaluate whether the call includes two different alleles.

Examples

    
    
    >>> hl.eval(call.is_het())
    True
    

Notes

In the diploid biallelic case, a `0/1` call will return `True`, and `0/0` and
`1/1` will return `False`.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two alleles are different,
`False` if they are the same.

is_het_non_ref()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_het_non_ref)

    

Evaluate whether the call includes two different alleles, neither of which is
reference.

Examples

    
    
    >>> hl.eval(call.is_het_non_ref())
    False
    

Notes

A biallelic variant may never have a het-non-ref call. Examples of these calls
are `1/2` and `2/4`.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the call includes two different
alternate alleles, `False` otherwise.

is_het_ref()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_het_ref)

    

Evaluate whether the call includes two different alleles, one of which is
reference.

Examples

    
    
    >>> hl.eval(call.is_het_ref())
    True
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the call includes one reference and
one alternate allele, `False` otherwise.

is_hom_ref()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_hom_ref)

    

Evaluate whether the call includes two reference alleles.

Examples

    
    
    >>> hl.eval(call.is_hom_ref())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the call includes two reference
alleles, `False` otherwise.

is_hom_var()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_hom_var)

    

Evaluate whether the call includes two identical alternate alleles.

Examples

    
    
    >>> hl.eval(call.is_hom_var())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the call includes two identical
alternate alleles, `False` otherwise.

is_non_ref()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_non_ref)

    

Evaluate whether the call includes one or more non-reference alleles.

Examples

    
    
    >>> hl.eval(call.is_non_ref())
    True
    

Notes

In the diploid biallelic case, a `0/0` call will return `False`, and `0/1` and
`1/1` will return `True`.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if at least one allele is non-
reference, `False` otherwise.

n_alt_alleles()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.n_alt_alleles)

    

Returns the number of non-reference alleles.

Examples

    
    
    >>> hl.eval(call.n_alt_alleles())
    1
    

Notes

For diploid biallelic calls, this method is equivalent to the alternate allele
dosage. For instance, `0/0` will return `0`, `0/1` will return `1`, and `1/1`
will return `2`.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of non-reference alleles.

one_hot_alleles(_alleles_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.one_hot_alleles)

    

Returns an array containing the summed one-hot encoding of the alleles.

Examples

Compute one-hot encoding when number of total alleles is 2.

    
    
    >>> hl.eval(call.one_hot_alleles(2))
    [1, 1]
    

**DEPRECATED** : Compute one-hot encoding based on length of list of alleles.

    
    
    >>> hl.eval(call.one_hot_alleles(['A', 'T']))
    [1, 1]
    

This one-hot representation is the positional sum of the one-hot encoding for
each called allele. For a biallelic variant, the one-hot encoding for a
reference allele is `[1, 0]` and the one-hot encoding for an alternate allele
is `[0, 1]`. Diploid calls would produce the following arrays: `[2, 0]` for
homozygous reference, `[1, 1]` for heterozygous, and `[0, 2]` for homozygous
alternate.

Parameters:

    

**alleles**
([`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") of [`tstr`](types.html#hail.expr.types.tstr
"hail.expr.types.tstr").) – Number of total alleles, including the reference,
or array of variant alleles.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") of [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – An array of summed one-hot encodings of allele
indices.

_property _phased

    

True if the call is phased.

Examples

    
    
    >>> hl.eval(call.phased)
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

_property _ploidy

    

Return the number of alleles of this call.

Examples

    
    
    >>> hl.eval(call.ploidy)
    2
    

Notes

Currently only ploidy 1 and 2 are supported.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

unphase()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.unphase)

    

Returns an unphased version of this call.

Returns:

    

`CallExpression`

unphased_diploid_gt_index()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.unphased_diploid_gt_index)

    

Return the genotype index for unphased, diploid calls.

Examples

    
    
    >>> hl.eval(call.unphased_diploid_gt_index())
    1
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")

---

### CollectionExpression

_class
_hail.expr.CollectionExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression)

    

Expression of type [`tarray`](types.html#hail.expr.types.tarray
"hail.expr.types.tarray") or [`tset`](types.html#hail.expr.types.tset
"hail.expr.types.tset")

    
    
    >>> a = hl.literal([1, 2, 3, 4, 5])
    
    
    
    >>> s3 = hl.literal({'Alice', 'Bob', 'Charlie'})
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`all` | Returns `True` if f returns `True` for every element.  
---|---  
`any` | Returns `True` if f returns `True` for any element.  
`filter` | Returns a new collection containing elements where f returns `True`.  
`find` | Returns the first element where f returns `True`.  
`flatmap` | Map each element of the collection to a new collection, and flatten the results.  
`fold` | Reduces the collection with the given function f, provided the initial value zero.  
`group_by` | Group elements into a dict according to a lambda function.  
`length` | Returns the size of a collection.  
`map` | Transform each element of a collection.  
`size` | Returns the size of a collection.  
`starmap` | Transform each element of a collection of tuples.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

all(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.all)

    

Returns `True` if f returns `True` for every element.

Examples

    
    
    >>> hl.eval(a.all(lambda x: x < 10))
    True
    

Notes

This method returns `True` if the collection is empty.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for every
element, `False` otherwise.

any(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.any)

    

Returns `True` if f returns `True` for any element.

Examples

    
    
    >>> hl.eval(a.any(lambda x: x % 2 == 0))
    True
    
    
    
    >>> hl.eval(s3.any(lambda x: x[0] == 'D'))
    False
    

Notes

This method always returns `False` for empty collections.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for any element,
`False` otherwise.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

filter(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.filter)

    

Returns a new collection containing elements where f returns `True`.

Examples

    
    
    >>> hl.eval(a.filter(lambda x: x % 2 == 0))
    [2, 4]
    
    
    
    >>> hl.eval(s3.filter(lambda x: ~(x[-1] == 'e')))  
    {'Bob'}
    

Notes

Returns a same-type expression; evaluated on a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"), returns a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). Evaluated on an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression"), returns an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

`CollectionExpression` – Expression of the same type as the callee.

find(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.find)

    

Returns the first element where f returns `True`.

Examples

    
    
    >>> hl.eval(a.find(lambda x: x ** 2 > 20))
    5
    
    
    
    >>> hl.eval(s3.find(lambda x: x[0] == 'D'))
    None
    

Notes

If f returns `False` for every element, then the result is missing.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Expression whose type is the element type of the
collection.

flatmap(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.flatmap)

    

Map each element of the collection to a new collection, and flatten the
results.

Examples

    
    
    >>> hl.eval(a.flatmap(lambda x: hl.range(0, x)))
    [0, 0, 1, 0, 1, 2, 0, 1, 2, 3, 0, 1, 2, 3, 4]
    
    
    
    >>> hl.eval(s3.flatmap(lambda x: hl.set(hl.range(0, x.length()).map(lambda i: x[i]))))  
    {'A', 'B', 'C', 'a', 'b', 'c', 'e', 'h', 'i', 'l', 'o', 'r'}
    

Parameters:

    

**f** (function ( (arg) -> `CollectionExpression`)) – Function from the
element type of the collection to the type of the collection. For instance,
flatmap on a `set<str>` should take a `str` and return a `set`.

Returns:

    

`CollectionExpression`

fold(_f_ ,
_zero_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.fold)

    

Reduces the collection with the given function f, provided the initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.fold(lambda i, j: i + j, 0, a))
    3
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression").

group_by(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.group_by)

    

Group elements into a dict according to a lambda function.

Examples

    
    
    >>> hl.eval(a.group_by(lambda x: x % 2 == 0))  
    {False: [1, 3, 5], True: [2, 4]}
    
    
    
    >>> hl.eval(s3.group_by(lambda x: x.length()))  
    {3: {'Bob'}, 5: {'Alice'}, 7: {'Charlie'}}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to evaluate for each element of the
collection to produce a key for the resulting dictionary.

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"). – Dictionary keyed by results of f.

length()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.length)

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.length())
    5
    
    
    
    >>> hl.eval(s3.length())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

map(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.map)

    

Transform each element of a collection.

Examples

    
    
    >>> hl.eval(a.map(lambda x: x ** 3))
    [1.0, 8.0, 27.0, 64.0, 125.0]
    
    
    
    >>> hl.eval(s3.map(lambda x: x.length()))
    {3, 5, 7}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

`CollectionExpression`. – Collection where each element has been transformed
according to f.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

size()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.size)

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.size())
    5
    
    
    
    >>> hl.eval(s3.size())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

starmap(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.starmap)

    

Transform each element of a collection of tuples.

Examples

    
    
    >>> hl.eval(hl.array([(1, 2), (2, 3)]).starmap(lambda x, y: x+y))
    [3, 5]
    

Parameters:

    

**f** (function ( (*args) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

`CollectionExpression`. – Collection where each element has been transformed
according to f.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### DictExpression

_class
_hail.expr.DictExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression)

    

Expression of type [`tdict`](types.html#hail.expr.types.tdict
"hail.expr.types.tdict").

    
    
    >>> d = hl.literal({'Alice': 43, 'Bob': 33, 'Charles': 44})
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`contains` | Returns whether a given key is present in the dictionary.  
---|---  
`get` | Returns the value associated with key k or a default value if that key is not present.  
`items` | Returns an array of tuples containing key/value pairs in the dictionary.  
`key_set` | Returns the set of keys in the dictionary.  
`keys` | Returns an array with all keys in the dictionary.  
`map_values` | Transform values of the dictionary according to a function.  
`size` | Returns the size of the dictionary.  
`values` | Returns an array with all values in the dictionary.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.__getitem__)

    

Get the value associated with key item.

Examples

    
    
    >>> hl.eval(d['Alice'])
    43
    

Notes

Raises an error if item is not a key of the dictionary. Use
`DictExpression.get()` to return missing instead of an error.

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Key expression.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Value associated with key item.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.contains)

    

Returns whether a given key is present in the dictionary.

Examples

    
    
    >>> hl.eval(d.contains('Alice'))
    True
    
    
    
    >>> hl.eval(d.contains('Anne'))
    False
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Key to test for inclusion.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if item is a key of the dictionary,
`False` otherwise.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

get(_item_ , _default
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.get)

    

Returns the value associated with key k or a default value if that key is not
present.

Examples

    
    
    >>> hl.eval(d.get('Alice'))
    43
    
    
    
    >>> hl.eval(d.get('Anne'))
    None
    
    
    
    >>> hl.eval(d.get('Anne', 0))
    0
    

Parameters:

    

  * **item** (```Expression```) – Key.

  * **default** (```Expression```) – Default value. Must be same type as dictionary values.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – The value associated with item, or default.

items()[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.items)

    

Returns an array of tuples containing key/value pairs in the dictionary.

Examples

    
    
    >>> hl.eval(d.items())  
    [('Alice', 430), ('Bob', 330), ('Charles', 440)]
    

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – All key/value pairs in the dictionary.

key_set()[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.key_set)

    

Returns the set of keys in the dictionary.

Examples

    
    
    >>> hl.eval(d.key_set())  
    {'Alice', 'Bob', 'Charles'}
    

Returns:

    

[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") – Set of all keys.

keys()[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.keys)

    

Returns an array with all keys in the dictionary.

Examples

    
    
    >>> hl.eval(d.keys())  
    ['Bob', 'Charles', 'Alice']
    

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of all keys.

map_values(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.map_values)

    

Transform values of the dictionary according to a function.

Examples

    
    
    >>> hl.eval(d.map_values(lambda x: x * 10))  
    {'Alice': 430, 'Bob': 330, 'Charles': 440}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to apply to each value.

Returns:

    

`DictExpression` – Dictionary with transformed values.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

size()[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.size)

    

Returns the size of the dictionary.

Examples

    
    
    >>> hl.eval(d.size())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – Size of the dictionary.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

values()[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.values)

    

Returns an array with all values in the dictionary.

Examples

    
    
    >>> hl.eval(d.values())  
    [33, 44, 43]
    

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – All values in the dictionary.

---

### IntervalExpression

_class
_hail.expr.IntervalExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#IntervalExpression)

    

Expression of type [`tinterval`](types.html#hail.expr.types.tinterval
"hail.expr.types.tinterval").

    
    
    >>> interval = hl.interval(3, 11)
    >>> locus_interval = hl.parse_locus_interval("1:53242-90543")
    

Attributes

`dtype` | The data type of the expression.  
---|---  
`end` | Returns the end point.  
`includes_end` | True if the interval includes the end point.  
`includes_start` | True if the interval includes the start point.  
`start` | Returns the start point.  
  
Methods

`contains` | Tests whether a value is contained in the interval.  
---|---  
`overlaps` | True if the the supplied interval contains any value in common with this one.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_value_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#IntervalExpression.contains)

    

Tests whether a value is contained in the interval.

Examples

    
    
    >>> hl.eval(interval.contains(3))
    True
    
    
    
    >>> hl.eval(interval.contains(11))
    False
    

Parameters:

    

**value** – Object with type matching the interval point type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if value is contained in the interval,
`False` otherwise.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

_property _end

    

Returns the end point.

Examples

    
    
    >>> hl.eval(interval.end)
    11
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

_property _includes_end

    

True if the interval includes the end point.

Examples

    
    
    >>> hl.eval(interval.includes_end)
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

_property _includes_start

    

True if the interval includes the start point.

Examples

    
    
    >>> hl.eval(interval.includes_start)
    True
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

overlaps(_interval_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#IntervalExpression.overlaps)

    

True if the the supplied interval contains any value in common with this one.

Examples

    
    
    >>> hl.eval(interval.overlaps(hl.interval(5, 9)))
    True
    
    
    
    >>> hl.eval(interval.overlaps(hl.interval(11, 20)))
    False
    

Parameters:

    

**interval** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") with type
[`tinterval`](types.html#hail.expr.types.tinterval
"hail.expr.types.tinterval")) – Interval object with the same point type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

_property _start

    

Returns the start point.

Examples

    
    
    >>> hl.eval(interval.start)
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### LocusExpression

_class
_hail.expr.LocusExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression)

    

Expression of type [`tlocus`](types.html#hail.expr.types.tlocus
"hail.expr.types.tlocus").

    
    
    >>> locus = hl.locus('1', 1034245)
    

Attributes

`contig` | Returns the chromosome.  
---|---  
`contig_idx` | Returns the chromosome.  
`dtype` | The data type of the expression.  
`position` | Returns the position along the chromosome.  
  
Methods

`global_position` | Returns a zero-indexed absolute position along the reference genome.  
---|---  
`in_autosome` | Returns `True` if the locus is on an autosome.  
`in_autosome_or_par` | Returns `True` if the locus is on an autosome or a pseudoautosomal region of chromosome X or Y.  
`in_mito` | Returns `True` if the locus is on mitochondrial DNA.  
`in_x_nonpar` | Returns `True` if the locus is in a non-pseudoautosomal region of chromosome X.  
`in_x_par` | Returns `True` if the locus is in a pseudoautosomal region of chromosome X.  
`in_y_nonpar` | Returns `True` if the locus is in a non-pseudoautosomal region of chromosome Y.  
`in_y_par` | Returns `True` if the locus is in a pseudoautosomal region of chromosome Y.  
`sequence_context` | Return the reference genome sequence at the locus.  
`window` | Returns an interval of a specified number of bases around the locus.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

_property _contig

    

Returns the chromosome.

Examples

    
    
    >>> hl.eval(locus.contig)
    '1'
    

Returns:

    

[`StringExpression`](hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression") – The chromosome for this locus.

_property _contig_idx

    

Returns the chromosome.

Examples

    
    
    >>> hl.eval(locus.contig_idx)
    0
    

Returns:

    

[`StringExpression`](hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression") – The index of the chromosome for this locus.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

global_position()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.global_position)

    

Returns a zero-indexed absolute position along the reference genome.

The global position is computed as `position` \- 1 plus the sum of the lengths
of all the contigs that precede this locus’s `contig` in the reference
genome’s ordering of contigs.

See also
[`locus_from_global_position()`](functions/genetics.html#hail.expr.functions.locus_from_global_position
"hail.expr.functions.locus_from_global_position").

Examples

A locus with position 1 along chromosome 1 will have a global position of 0
along the reference genome GRCh37.

    
    
    >>> hl.eval(hl.locus('1', 1).global_position())
    0
    

A locus with position 1 along chromosome 2 will have a global position of
(1-1) + 249250621, where 249250621 is the length of chromosome 1 on GRCh37.

    
    
    >>> hl.eval(hl.locus('2', 1).global_position())
    249250621
    

A different reference genome than the default results in a different global
position.

    
    
    >>> hl.eval(hl.locus('chr2', 1, 'GRCh38').global_position())
    248956422
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") – Global base position of locus along the reference
genome.

in_autosome()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_autosome)

    

Returns `True` if the locus is on an autosome.

Notes

All contigs are considered autosomal except those designated as X, Y, or MT by
[`ReferenceGenome`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome").

Examples

    
    
    >>> hl.eval(locus.in_autosome())
    True
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_autosome_or_par()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_autosome_or_par)

    

Returns `True` if the locus is on an autosome or a pseudoautosomal region of
chromosome X or Y.

Examples

    
    
    >>> hl.eval(locus.in_autosome_or_par())
    True
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_mito()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_mito)

    

Returns `True` if the locus is on mitochondrial DNA.

Examples

    
    
    >>> hl.eval(locus.in_mito())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_x_nonpar()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_x_nonpar)

    

Returns `True` if the locus is in a non-pseudoautosomal region of chromosome
X.

Examples

    
    
    >>> hl.eval(locus.in_x_nonpar())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_x_par()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_x_par)

    

Returns `True` if the locus is in a pseudoautosomal region of chromosome X.

Examples

    
    
    >>> hl.eval(locus.in_x_par())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_y_nonpar()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_y_nonpar)

    

Returns `True` if the locus is in a non-pseudoautosomal region of chromosome
Y.

Examples

    
    
    >>> hl.eval(locus.in_y_nonpar())
    False
    

Note

Many variant callers only generate variants on chromosome X for the
pseudoautosomal region. In this case, all loci mapped to chromosome Y are non-
pseudoautosomal.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_y_par()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_y_par)

    

Returns `True` if the locus is in a pseudoautosomal region of chromosome Y.

Examples

    
    
    >>> hl.eval(locus.in_y_par())
    False
    

Note

Many variant callers only generate variants on chromosome X for the
pseudoautosomal region. In this case, all loci mapped to chromosome Y are non-
pseudoautosomal.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

_property _position

    

Returns the position along the chromosome.

Examples

    
    
    >>> hl.eval(locus.position)
    1034245
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – This locus’s position along its chromosome.

sequence_context(_before =0_, _after
=0_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.sequence_context)

    

Return the reference genome sequence at the locus.

Examples

Get the reference allele at a locus:

    
    
    >>> hl.eval(locus.sequence_context()) 
    "G"
    

Get the reference sequence at a locus including the previous 5 bases:

    
    
    >>> hl.eval(locus.sequence_context(before=5)) 
    "ACTCGG"
    

Notes

This function requires that this locus’ reference genome has an attached
reference sequence. Use
[`ReferenceGenome.add_sequence()`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome.add_sequence
"hail.genetics.ReferenceGenome.add_sequence") to load and attach a reference
sequence to a reference genome.

Parameters:

    

  * **before** (```Expression``` of type ```tint32```, optional) – Number of bases to include before the locus. Truncates at contig boundary.

  * **after** (```Expression``` of type ```tint32```, optional) – Number of bases to include after the locus. Truncates at contig boundary.

Returns:

    

[`StringExpression`](hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

window(_before_ ,
_after_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.window)

    

Returns an interval of a specified number of bases around the locus.

Examples

Create a window of two megabases centered at a locus:

    
    
    >>> locus = hl.locus('16', 29_500_000)
    >>> window = locus.window(1_000_000, 1_000_000)
    >>> hl.eval(window)
    Interval(start=Locus(contig=16, position=28500000, reference_genome=GRCh37), end=Locus(contig=16, position=30500000, reference_genome=GRCh37), includes_start=True, includes_end=True)
    

Notes

The returned interval is inclusive of both the start and end endpoints.

Parameters:

    

  * **before** (```Expression``` of type ```tint32```) – Number of bases to include before the locus. Truncates at 1.

  * **after** (```Expression``` of type ```tint32```) – Number of bases to include after the locus. Truncates at contig length.

Returns:

    

[`IntervalExpression`](hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression")

---

### NumericExpression

_class
_hail.expr.NumericExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression)

    

Expression of numeric type.

    
    
    >>> x = hl.literal(3)
    
    
    
    >>> y = hl.literal(4.5)
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__add__)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other** (`NumericExpression`) – Number to add.

Returns:

    

`NumericExpression` – Sum of the two numbers.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__floordiv__)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other** (`NumericExpression`) – Dividend.

Returns:

    

`NumericExpression` – The floor of the left number divided by the right.

__ge__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__ge__)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other** (`NumericExpression`) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than or
equal to the right side.

__gt__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__gt__)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other** (`NumericExpression`) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than the
right side.

__le__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__le__)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other** (`NumericExpression`) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than or
equal to the right side.

__lt__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__lt__)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other** (`NumericExpression`) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than the
right side.

__mod__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__mod__)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other** (`NumericExpression`) – Dividend.

Returns:

    

`NumericExpression` – Remainder after dividing the left by the right.

__mul__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__mul__)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other** (`NumericExpression`) – Number to multiply.

Returns:

    

`NumericExpression` – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__neg__)

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

`NumericExpression` – Negated number.

__pow__(_power_ , _modulo
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__pow__)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (`NumericExpression`)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__sub__)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other** (`NumericExpression`) – Number to subtract.

Returns:

    

`NumericExpression` – Difference of the two numbers.

__truediv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__truediv__)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other** (`NumericExpression`) – Dividend.

Returns:

    

`NumericExpression` – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### Int32Expression

_class
_hail.expr.Int32Expression[[source]](_modules/hail/expr/expressions/typed_expressions.html#Int32Expression)

    

Expression of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32").

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to add.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Sum of the two numbers.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The floor of the left number divided by the
right.

__ge__(_other_)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than or
equal to the right side.

__gt__(_other_)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than the
right side.

__le__(_other_)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than or
equal to the right side.

__lt__(_other_)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than the
right side.

__mod__(_other_)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Remainder after dividing the left by the
right.

__mul__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#Int32Expression.__mul__)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to multiply.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Negated number.

__pow__(_power_ , _modulo =None_)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (```NumericExpression```)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to subtract.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Difference of the two numbers.

__truediv__(_other_)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### Int64Expression

_class
_hail.expr.Int64Expression[[source]](_modules/hail/expr/expressions/typed_expressions.html#Int64Expression)

    

Expression of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64").

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to add.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Sum of the two numbers.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The floor of the left number divided by the
right.

__ge__(_other_)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than or
equal to the right side.

__gt__(_other_)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than the
right side.

__le__(_other_)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than or
equal to the right side.

__lt__(_other_)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than the
right side.

__mod__(_other_)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Remainder after dividing the left by the
right.

__mul__(_other_)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to multiply.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Negated number.

__pow__(_power_ , _modulo =None_)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (```NumericExpression```)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to subtract.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Difference of the two numbers.

__truediv__(_other_)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### Float32Expression

_class
_hail.expr.Float32Expression[[source]](_modules/hail/expr/expressions/typed_expressions.html#Float32Expression)

    

Expression of type [`tfloat32`](types.html#hail.expr.types.tfloat32
"hail.expr.types.tfloat32").

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to add.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Sum of the two numbers.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The floor of the left number divided by the
right.

__ge__(_other_)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than or
equal to the right side.

__gt__(_other_)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than the
right side.

__le__(_other_)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than or
equal to the right side.

__lt__(_other_)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than the
right side.

__mod__(_other_)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Remainder after dividing the left by the
right.

__mul__(_other_)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to multiply.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Negated number.

__pow__(_power_ , _modulo =None_)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (```NumericExpression```)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to subtract.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Difference of the two numbers.

__truediv__(_other_)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### Float64Expression

_class
_hail.expr.Float64Expression[[source]](_modules/hail/expr/expressions/typed_expressions.html#Float64Expression)

    

Expression of type [`tfloat64`](types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64").

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to add.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Sum of the two numbers.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The floor of the left number divided by the
right.

__ge__(_other_)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than or
equal to the right side.

__gt__(_other_)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than the
right side.

__le__(_other_)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than or
equal to the right side.

__lt__(_other_)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than the
right side.

__mod__(_other_)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Remainder after dividing the left by the
right.

__mul__(_other_)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to multiply.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Negated number.

__pow__(_power_ , _modulo =None_)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (```NumericExpression```)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to subtract.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Difference of the two numbers.

__truediv__(_other_)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### SetExpression

_class
_hail.expr.SetExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression)

    

Expression of type [`tset`](types.html#hail.expr.types.tset
"hail.expr.types.tset").

    
    
    >>> s1 = hl.literal({1, 2, 3})
    >>> s2 = hl.literal({1, 3, 5})
    

See also

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression")

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`add` | Returns a new set including item.  
---|---  
`contains` | Returns `True` if item is in the set.  
`difference` | Return the set of elements in the set that are not present in set s.  
`intersection` | Return the intersection of the set and set s.  
`is_subset` | Returns `True` if every element is contained in set s.  
`remove` | Returns a new set excluding item.  
`union` | Return the union of the set and set s.  
  
__and__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__and__)

    

Return the intersection of the set and other.

Examples

    
    
    >>> hl.eval(s1 & s2)
    {1, 3}
    

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements present in both the set and other.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__ge__)

    

Test whether every element in other is in the set.

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if every element in other is in the
set. `False` otherwise.

__gt__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__gt__)

    

Test whether other is a proper subset of the set (`other <= set and other !=
set`).

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if other is a proper subset of the
set. `False` otherwise.

__le__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__le__)

    

Test whether every element in the set is in other.

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if every element in the set is in
other. `False` otherwise.

__lt__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__lt__)

    

Test whether the set is a proper subset of other (`set <= other and set !=
other`).

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the set is a proper subset of
other. `False` otherwise.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__or__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__or__)

    

Return the union of the set and other.

Examples

    
    
    >>> hl.eval(s1 | s2)
    {1, 2, 3, 5}
    

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements present in either set.

__sub__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__sub__)

    

Return the difference of the set and other.

Examples

    
    
    >>> hl.eval(s1 - s2)
    {2}
    
    
    
    >>> hl.eval(s2 - s1)
    {5}
    

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements in the set that are not in other.

__xor__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__xor__)

    

Return the symmetric difference of the set and other.

Examples

    
    
    >>> hl.eval(s1 ^ s2)
    {2, 5}
    

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements present in either the set or other but not
both.

add(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.add)

    

Returns a new set including item.

Examples

    
    
    >>> hl.eval(s1.add(10))  
    {1, 2, 3, 10}
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Value to add.

Returns:

    

`SetExpression` – Set with item added.

all(_f_)

    

Returns `True` if f returns `True` for every element.

Examples

    
    
    >>> hl.eval(a.all(lambda x: x < 10))
    True
    

Notes

This method returns `True` if the collection is empty.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for every
element, `False` otherwise.

any(_f_)

    

Returns `True` if f returns `True` for any element.

Examples

    
    
    >>> hl.eval(a.any(lambda x: x % 2 == 0))
    True
    
    
    
    >>> hl.eval(s3.any(lambda x: x[0] == 'D'))
    False
    

Notes

This method always returns `False` for empty collections.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for any element,
`False` otherwise.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.contains)

    

Returns `True` if item is in the set.

Examples

    
    
    >>> hl.eval(s1.contains(1))
    True
    
    
    
    >>> hl.eval(s1.contains(10))
    False
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Value for inclusion test.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if item is in the set.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

difference(_s_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.difference)

    

Return the set of elements in the set that are not present in set s.

Examples

    
    
    >>> hl.eval(s1.difference(s2))
    {2}
    
    
    
    >>> hl.eval(s2.difference(s1))
    {5}
    

Parameters:

    

**s** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements not in s.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

filter(_f_)

    

Returns a new collection containing elements where f returns `True`.

Examples

    
    
    >>> hl.eval(a.filter(lambda x: x % 2 == 0))
    [2, 4]
    
    
    
    >>> hl.eval(s3.filter(lambda x: ~(x[-1] == 'e')))  
    {'Bob'}
    

Notes

Returns a same-type expression; evaluated on a `SetExpression`, returns a
`SetExpression`. Evaluated on an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression"), returns an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression") – Expression of the same type as the callee.

find(_f_)

    

Returns the first element where f returns `True`.

Examples

    
    
    >>> hl.eval(a.find(lambda x: x ** 2 > 20))
    5
    
    
    
    >>> hl.eval(s3.find(lambda x: x[0] == 'D'))
    None
    

Notes

If f returns `False` for every element, then the result is missing.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Expression whose type is the element type of the
collection.

flatmap(_f_)

    

Map each element of the collection to a new collection, and flatten the
results.

Examples

    
    
    >>> hl.eval(a.flatmap(lambda x: hl.range(0, x)))
    [0, 0, 1, 0, 1, 2, 0, 1, 2, 3, 0, 1, 2, 3, 4]
    
    
    
    >>> hl.eval(s3.flatmap(lambda x: hl.set(hl.range(0, x.length()).map(lambda i: x[i]))))  
    {'A', 'B', 'C', 'a', 'b', 'c', 'e', 'h', 'i', 'l', 'o', 'r'}
    

Parameters:

    

**f** (function ( (arg) ->
[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"))) – Function from the element type of the
collection to the type of the collection. For instance, flatmap on a
`set<str>` should take a `str` and return a `set`.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression")

fold(_f_ , _zero_)

    

Reduces the collection with the given function f, provided the initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.fold(lambda i, j: i + j, 0, a))
    3
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression").

group_by(_f_)

    

Group elements into a dict according to a lambda function.

Examples

    
    
    >>> hl.eval(a.group_by(lambda x: x % 2 == 0))  
    {False: [1, 3, 5], True: [2, 4]}
    
    
    
    >>> hl.eval(s3.group_by(lambda x: x.length()))  
    {3: {'Bob'}, 5: {'Alice'}, 7: {'Charlie'}}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to evaluate for each element of the
collection to produce a key for the resulting dictionary.

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"). – Dictionary keyed by results of f.

intersection(_s_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.intersection)

    

Return the intersection of the set and set s.

Examples

    
    
    >>> hl.eval(s1.intersection(s2))
    {1, 3}
    

Parameters:

    

**s** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements present in s.

is_subset(_s_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.is_subset)

    

Returns `True` if every element is contained in set s.

Examples

    
    
    >>> hl.eval(s1.is_subset(s2))
    False
    
    
    
    >>> hl.eval(s1.remove(2).is_subset(s2))
    True
    

Parameters:

    

**s** (`SetExpression`) – Set expression of the same type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if every element is contained in set
s.

length()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.length())
    5
    
    
    
    >>> hl.eval(s3.length())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

map(_f_)

    

Transform each element of a collection.

Examples

    
    
    >>> hl.eval(a.map(lambda x: x ** 3))
    [1.0, 8.0, 27.0, 64.0, 125.0]
    
    
    
    >>> hl.eval(s3.map(lambda x: x.length()))
    {3, 5, 7}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

remove(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.remove)

    

Returns a new set excluding item.

Examples

    
    
    >>> hl.eval(s1.remove(1))
    {2, 3}
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Value to remove.

Returns:

    

`SetExpression` – Set with item removed.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

size()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.size())
    5
    
    
    
    >>> hl.eval(s3.size())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

starmap(_f_)

    

Transform each element of a collection of tuples.

Examples

    
    
    >>> hl.eval(hl.array([(1, 2), (2, 3)]).starmap(lambda x, y: x+y))
    [3, 5]
    

Parameters:

    

**f** (function ( (*args) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

union(_s_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.union)

    

Return the union of the set and set s.

Examples

    
    
    >>> hl.eval(s1.union(s2))
    {1, 2, 3, 5}
    

Parameters:

    

**s** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements present in either set.

---

### StringExpression

_class
_hail.expr.StringExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression)

    

Expression of type [`tstr`](types.html#hail.expr.types.tstr
"hail.expr.types.tstr").

    
    
    >>> s = hl.literal('The quick brown fox')
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`contains` | Returns whether substr is contained in the string.  
---|---  
`endswith` | Returns whether substr is a suffix of the string.  
`find` | Return the lowest index in the string where substring sub is found within the slice s[start:end].  
`first_match_in` | Returns an array containing the capture groups of the first match of regex in the given character sequence.  
`join` | Returns a string which is the concatenation of the strings in collection separated by the string providing this method.  
`length` | Returns the length of the string.  
`lower` | Returns a copy of the string, but with upper case letters converted to lower case.  
`matches` | Returns `True` if the string contains any match for the given regex if full_match is false.  
`replace` | Replace substrings matching pattern1 with pattern2 using regex.  
`reverse` | Returns the reversed value.  
`split` | Returns an array of strings generated by splitting the string at delim.  
`startswith` | Returns whether substr is a prefix of the string.  
`strip` | Returns a copy of the string with whitespace removed from the start and end.  
`translate` | Translates characters of the string using mapping.  
`upper` | Returns a copy of the string, but with lower case letters converted to upper case.  
  
__add__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.__add__)

    

Concatenate strings.

Examples

    
    
    >>> hl.eval(s + ' jumped over the lazy dog')
    'The quick brown fox jumped over the lazy dog'
    

Parameters:

    

**other** (`StringExpression`) – String to concatenate.

Returns:

    

`StringExpression` – Concatenated string.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.__getitem__)

    

Slice or index into the string.

Examples

    
    
    >>> hl.eval(s[:15])
    'The quick brown'
    
    
    
    >>> hl.eval(s[0])
    'T'
    

Parameters:

    

**item** (slice or
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")) – Slice or character index.

Returns:

    

`StringExpression` – Substring or character at index item.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_substr_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.contains)

    

Returns whether substr is contained in the string.

Examples

    
    
    >>> hl.eval(s.contains('fox'))
    True
    
    
    
    >>> hl.eval(s.contains('dog'))
    False
    

Note

This method is case-sensitive.

Parameters:

    

**substr** (`StringExpression`)

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

endswith(_substr_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.endswith)

    

Returns whether substr is a suffix of the string.

Examples

    
    
    >>> hl.eval(s.endswith('fox'))
    True
    

Note

This method is case-sensitive.

Parameters:

    

**substr** (`StringExpression`)

Returns:

    

`StringExpression`

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

find(_sub_ , _start =None_, _end
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.find)

    

Return the lowest index in the string where substring sub is found within the
slice s[start:end]. Optional arguments start and end are interpreted as in
slice notation. Evaluates to -1 if sub is not found.

Examples

    
    
    >>> a = hl.str('hello, world')
    >>> hl.eval(a.find('world'))
    7
    
    
    
    >>> hl.eval(a.find('hail'))
    -1
    

Parameters:

    

  * **sub** (`StringExpression`) – substring to find

  * **start** (```Int32Expression```) – optional slice start index

  * **end** (```Int32Expression```) – optional slice end index

Returns:

    

[`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") – lowest index in the string where substring sub
is found or -1.

first_match_in(_regex_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.first_match_in)

    

Returns an array containing the capture groups of the first match of regex in
the given character sequence.

Examples

    
    
    >>> hl.eval(s.first_match_in("The quick (\w+) fox"))
    ['brown']
    
    
    
    >>> hl.eval(s.first_match_in("The (\w+) (\w+) (\w+)"))
    ['quick', 'brown', 'fox']
    
    
    
    >>> hl.eval(s.first_match_in("(\w+) (\w+)"))
    ['The', 'quick']
    

Parameters:

    

**regex** (`StringExpression`)

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") with element type
```tstr```

join(_collection_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.join)

    

Returns a string which is the concatenation of the strings in collection
separated by the string providing this method. Raises
[`TypeError`](https://docs.python.org/3.9/library/exceptions.html#TypeError
"\(in Python v3.9\)") if the element type of collection is not
```tstr```.

Examples

    
    
    >>> a = ['Bob', 'Charlie', 'Alice', 'Bob', 'Bob']
    
    
    
    >>> hl.eval(hl.str(',').join(a))
    'Bob,Charlie,Alice,Bob,Bob'
    

Parameters:

    

**collection**
([`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression")) – Collection.

Returns:

    

`StringExpression` – Joined string expression.

length()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.length)

    

Returns the length of the string.

Examples

    
    
    >>> hl.eval(s.length())
    19
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – Length of the string.

lower()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.lower)

    

Returns a copy of the string, but with upper case letters converted to lower
case.

Examples

    
    
    >>> hl.eval(s.lower())
    'the quick brown fox'
    

Returns:

    

`StringExpression`

matches(_regex_ , _full_match
=False_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.matches)

    

Returns `True` if the string contains any match for the given regex if
full_match is false. Returns `True` if the whole string matches the given
regex if full_match is true.

Examples

The regex parameter does not need to match the entire string if full_match is
`False`:

    
    
    >>> string = hl.literal('NA12878')
    >>> hl.eval(string.matches('12'))
    True
    

The regex parameter needs to match the entire string if full_match is `True`:

    
    
    >>> string = hl.literal('NA12878')
    >>> hl.eval(string.matches('12', True))
    False
    
    
    
    >>> string = hl.literal('3412878')
    >>> hl.eval(string.matches('^[0-9]*$'))
    True
    

Regex motifs can be used to match sequences of characters:

    
    
    >>> string = hl.literal('NA12878')
    >>> hl.eval(string.matches(r'NA\d+'))
    True
    
    
    
    >>> string = hl.literal('3412878')
    >>> hl.eval(string.matches('^[0-9]*$'))
    True
    

Notes

The regex argument is a [regular
expression](https://en.wikipedia.org/wiki/Regular_expression), and uses [Java
regex
syntax](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/regex/Pattern.html).

Parameters:

    

  * **regex** (`StringExpression`) – Pattern to match.

  * **full_match** (:obj: bool) – If `True`, the function considers whether the whole string matches the regex. If `False`, the function considers whether the string has a partial match for that regex

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – If full_match is `False`,``True`` if the
string contains any match for the regex, otherwise `False`. If full_match is
`True`,``True`` if the whole string matches the regex, otherwise `False`.

replace(_pattern1_ ,
_pattern2_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.replace)

    

Replace substrings matching pattern1 with pattern2 using regex.

Examples

Replace spaces with underscores in a Hail string:

    
    
    >>> hl.eval(hl.str("The quick  brown fox").replace(' ', '_'))
    'The_quick__brown_fox'
    

Remove the leading zero in contigs in variant strings in a table:

    
    
    >>> t = hl.import_table('data/leading-zero-variants.txt')
    >>> t.show()
    +----------------+
    | variant        |
    +----------------+
    | str            |
    +----------------+
    | "01:1000:A:T"  |
    | "01:10001:T:G" |
    | "02:99:A:C"    |
    | "02:893:G:C"   |
    | "22:100:A:T"   |
    | "X:10:C:A"     |
    +----------------+
    
    >>> t = t.annotate(variant = t.variant.replace("^0([0-9])", "$1"))
    >>> t.show()
    +---------------+
    | variant       |
    +---------------+
    | str           |
    +---------------+
    | "1:1000:A:T"  |
    | "1:10001:T:G" |
    | "2:99:A:C"    |
    | "2:893:G:C"   |
    | "22:100:A:T"  |
    | "X:10:C:A"    |
    +---------------+
    

Notes

The regex expressions used should follow [Java regex
syntax](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/regex/Pattern.html).
In the Java regular expression syntax, a dollar sign, `$1`, refers to the
first group, not the canonical `\1`.

Parameters:

    

  * **pattern1** (str or `StringExpression`)

  * **pattern2** (str or `StringExpression`)

reverse()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.reverse)

    

Returns the reversed value. .. rubric:: Examples

    
    
    >>> string = hl.literal('ATGCC')
    >>> hl.eval(string.reverse())
    'CCGTA'
    

Returns:

    

`StringExpression`

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

split(_delim_ , _n
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.split)

    

Returns an array of strings generated by splitting the string at delim.

Examples

    
    
    >>> hl.eval(s.split('\s+'))
    ['The', 'quick', 'brown', 'fox']
    
    
    
    >>> hl.eval(s.split('\s+', 2))
    ['The', 'quick brown fox']
    

Notes

The delimiter is a regex using the [Java regex
syntax](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/regex/Pattern.html)
delimiter. To split on special characters, escape them with double backslash
(`\\`).

Parameters:

    

  * **delim** (str or `StringExpression`) – Delimiter regex.

  * **n** (```Expression``` of type ```tint32```, optional) – Maximum number of splits.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of split strings.

startswith(_substr_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.startswith)

    

Returns whether substr is a prefix of the string.

Examples

    
    
    >>> hl.eval(s.startswith('The'))
    True
    
    
    
    >>> hl.eval(s.startswith('the'))
    False
    

Note

This method is case-sensitive.

Parameters:

    

**substr** (`StringExpression`)

Returns:

    

`StringExpression`

strip()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.strip)

    

Returns a copy of the string with whitespace removed from the start and end.

Examples

    
    
    >>> s2 = hl.str('  once upon a time\n')
    >>> hl.eval(s2.strip())
    'once upon a time'
    

Returns:

    

`StringExpression`

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

translate(_mapping_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.translate)

    

Translates characters of the string using mapping.

Examples

    
    
    >>> string = hl.literal('ATTTGCA')
    >>> hl.eval(string.translate({'T': 'U'}))
    'AUUUGCA'
    

Parameters:

    

**mapping**
([`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression")) – Dictionary of character-character translations.

Returns:

    

`StringExpression`

See also

`replace()`

upper()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.upper)

    

Returns a copy of the string, but with lower case letters converted to upper
case.

Examples

    
    
    >>> hl.eval(s.upper())
    'THE QUICK BROWN FOX'
    

Returns:

    

`StringExpression`

---

### TupleExpression

_class
_hail.expr.TupleExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#TupleExpression)

    

Expression of type [`ttuple`](types.html#hail.expr.types.ttuple
"hail.expr.types.ttuple").

    
    
    >>> tup = hl.literal(("a", 1, [1, 2, 3]))
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`count` | Do not use this method.  
---|---  
`index` | Do not use this method.  
  
__class_getitem___ = <bound method GenericAlias of <class
'hail.expr.expressions.typed_expressions.TupleExpression'>>_

    

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#TupleExpression.__getitem__)

    

Index into the tuple.

Examples

    
    
    >>> hl.eval(tup[1])
    1
    

Parameters:

    

**item** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in
Python v3.9\)")) – Element index.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__len__()[[source]](_modules/hail/expr/expressions/typed_expressions.html#TupleExpression.__len__)

    

Returns the length of the tuple.

Examples

    
    
    >>> len(tup)
    3
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

count(_value_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#TupleExpression.count)

    

Do not use this method.

This only exists for compatibility with the Python Sequence abstract base
class.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

index(_value_ , _start =0_, _stop
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#TupleExpression.index)

    

Do not use this method.

This only exists for compatibility with the Python Sequence abstract base
class.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

### NDArrayExpression

_class
_hail.expr.NDArrayExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayExpression)

    

Expression of type [`tndarray`](types.html#hail.expr.types.tndarray
"hail.expr.types.tndarray").

    
    
    >>> nd = hl.nd.array([[1, 2], [3, 4]])
    

Attributes

`T` | Reverse the dimensions of this ndarray.  
---|---  
`dtype` | The data type of the expression.  
`ndim` | The number of dimensions of this ndarray.  
`shape` | The shape of this ndarray.  
  
Methods

`map` | Applies an element-wise operation on an NDArray.  
---|---  
`map2` | Applies an element-wise binary operation on two NDArrays.  
`reshape` | Reshape this ndarray to a new shape.  
`transpose` | Permute the dimensions of this ndarray according to the ordering of axes.  
  
_property _T

    

Reverse the dimensions of this ndarray. For an n-dimensional array a, a[i_0,
…, i_n-1, i_n] = a.T[i_n, i_n-1, …, i_0]. Same as self.transpose().

See also `transpose()`.

Returns:

    

`NDArrayExpression`.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

map(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayExpression.map)

    

Applies an element-wise operation on an NDArray.

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the NDArray.

Returns:

    

`NDArrayExpression`. – NDArray where each element has been transformed
according to f.

map2(_other_ ,
_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayExpression.map2)

    

Applies an element-wise binary operation on two NDArrays.

Parameters:

    

  * **other** (class:.NDArrayExpression, ```ArrayExpression```, numpy NDarray,) – or nested python list/tuples. Both NDArrays must be the same shape or broadcastable into common shape.

  * **f** (function ((arg1, arg2)-> ```Expression```)) – Function to be applied to each element of both NDArrays.

Returns:

    

`NDArrayExpression`. – Element-wise result of applying f to each index in
NDArrays.

_property _ndim

    

The number of dimensions of this ndarray.

Examples

    
    
    >>> nd.ndim
    2
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

reshape(_*
shape_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayExpression.reshape)

    

Reshape this ndarray to a new shape.

Parameters:

    

**shape** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") or) – :obj: tuple of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64")

Examples

    
    
    >>> v = hl.nd.array([1, 2, 3, 4]) 
    >>> m = v.reshape((2, 2)) 
    

Returns:

    

`NDArrayExpression`.

_property _shape

    

The shape of this ndarray.

Examples

    
    
    >>> hl.eval(nd.shape)
    (2, 2)
    

Returns:

    

[`TupleExpression`](hail.expr.TupleExpression.html#hail.expr.TupleExpression
"hail.expr.TupleExpression")

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

transpose(_axes
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayExpression.transpose)

    

Permute the dimensions of this ndarray according to the ordering of axes. Axis
j in the ith index of axes maps the jth dimension of the ndarray to the ith
dimension of the output ndarray.

Parameters:

    

**axes** ([`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple
"\(in Python v3.9\)") of
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)"), optional) – The new ordering of the ndarray’s dimensions.

Notes

Does nothing on ndarrays of dimensionality 0 or 1.

Returns:

    

`NDArrayExpression`.

---

### NDArrayNumericExpression

_class
_hail.expr.NDArrayNumericExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression)

    

Expression of type [`tndarray`](types.html#hail.expr.types.tndarray
"hail.expr.types.tndarray") with a numeric element type.

Numeric ndarrays support arithmetic both with scalar values and other arrays.
Arithmetic between two numeric ndarrays requires that the shapes of each
ndarray be either identical or compatible for broadcasting. Operations are
applied positionally (`nd1 * nd2` will multiply the first element of `nd1` by
the first element of `nd2`, the second element of `nd1` by the second element
of `nd2`, and so on). Arithmetic with a scalar will apply the operation to
each element of the ndarray.

Attributes

`T` | Reverse the dimensions of this ndarray.  
---|---  
`dtype` | The data type of the expression.  
`ndim` | The number of dimensions of this ndarray.  
`shape` | The shape of this ndarray.  
  
Methods

`sum` | Sum out one or more axes of an ndarray.  
---|---  
  
_property _T

    

Reverse the dimensions of this ndarray. For an n-dimensional array a, a[i_0,
…, i_n-1, i_n] = a.T[i_n, i_n-1, …, i_0]. Same as self.transpose().

See also `transpose()`.

Returns:

    

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression").

__add__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__add__)

    

Positionally add an array or a scalar.

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `NDArrayNumericExpression`) – Value or
ndarray to add.

Returns:

    

`NDArrayNumericExpression` – NDArray of positional sums.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__floordiv__)

    

Positionally divide by a ndarray or a scalar using floor division.

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `NDArrayNumericExpression`)

Returns:

    

`NDArrayNumericExpression`

__ge__(_other_)

    

Return self>=value.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__matmul__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__matmul__)

    

Matrix multiplication: a @ b, semantically equivalent to NumPy matmul. If a
and b are vectors, the vector dot product is performed, returning a
NumericExpression. If a and b are both 2-dimensional matrices, this performs
normal matrix multiplication. If a and b have more than 2 dimensions, they are
treated as multi-dimensional stacks of 2-dimensional matrices. Matrix
multiplication is applied element-wise across the higher dimensions. E.g. if a
has shape (3, 4, 5) and b has shape (3, 5, 6), a is treated as a stack of
three matrices of shape (4, 5) and b as a stack of three matrices of shape (5,
6). a @ b would then have shape (3, 4, 6).

Notes

The last dimension of a and the second to last dimension of b (or only
dimension if b is a vector) must have the same length. The dimensions to the
left of the last two dimensions of a and b (for NDArrays of dimensionality >
2) must be equal or be compatible for broadcasting. Number of dimensions of
both NDArrays must be at least 1.

Parameters:

    

**other**
([`numpy.ndarray`](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray
"\(in NumPy v2.3\)") `NDArrayNumericExpression`)

Returns:

    

`NDArrayNumericExpression` or
[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

__mul__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__mul__)

    

Positionally multiply by a ndarray or a scalar.

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `NDArrayNumericExpression`) – Value or
ndarray to multiply by.

Returns:

    

`NDArrayNumericExpression` – NDArray of positional products.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__neg__)

    

Negate elements of the ndarray.

Returns:

    

`NDArrayNumericExpression` – Array expression of the same type.

__sub__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__sub__)

    

Positionally subtract a ndarray or a scalar.

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `NDArrayNumericExpression`) – Value or
ndarray to subtract.

Returns:

    

`NDArrayNumericExpression` – NDArray of positional differences.

__truediv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__truediv__)

    

Positionally divide by a ndarray or a scalar.

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `NDArrayNumericExpression`) – Value or
ndarray to divide by.

Returns:

    

`NDArrayNumericExpression` – NDArray of positional quotients.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

map(_f_)

    

Applies an element-wise operation on an NDArray.

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the NDArray.

Returns:

    

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression"). – NDArray where each element has been
transformed according to f.

map2(_other_ , _f_)

    

Applies an element-wise binary operation on two NDArrays.

Parameters:

    

  * **other** (class:.NDArrayExpression, ```ArrayExpression```, numpy NDarray,) – or nested python list/tuples. Both NDArrays must be the same shape or broadcastable into common shape.

  * **f** (function ((arg1, arg2)-> ```Expression```)) – Function to be applied to each element of both NDArrays.

Returns:

    

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression"). – Element-wise result of applying f to each
index in NDArrays.

_property _ndim

    

The number of dimensions of this ndarray.

Examples

    
    
    >>> nd.ndim
    2
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

reshape(_* shape_)

    

Reshape this ndarray to a new shape.

Parameters:

    

**shape** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") or) – :obj: tuple of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64")

Examples

    
    
    >>> v = hl.nd.array([1, 2, 3, 4]) 
    >>> m = v.reshape((2, 2)) 
    

Returns:

    

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression").

_property _shape

    

The shape of this ndarray.

Examples

    
    
    >>> hl.eval(nd.shape)
    (2, 2)
    

Returns:

    

[`TupleExpression`](hail.expr.TupleExpression.html#hail.expr.TupleExpression
"hail.expr.TupleExpression")

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

sum(_axis
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.sum)

    

Sum out one or more axes of an ndarray.

Parameters:

    

**axis** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in
Python v3.9\)")
[`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python
v3.9\)")) – The axis or axes to sum out.

Returns:

    

`NDArrayNumericExpression` or
[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

transpose(_axes =None_)

    

Permute the dimensions of this ndarray according to the ordering of axes. Axis
j in the ith index of axes maps the jth dimension of the ndarray to the ith
dimension of the output ndarray.

Parameters:

    

**axes** ([`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple
"\(in Python v3.9\)") of
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)"), optional) – The new ordering of the ndarray’s dimensions.

Notes

Does nothing on ndarrays of dimensionality 0 or 1.

Returns:

    

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression").

---

