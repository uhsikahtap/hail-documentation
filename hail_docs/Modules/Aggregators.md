## Aggregators

  
The `aggregators` module is exposed as `hl.agg`, e.g. `hl.agg.sum`.

`collect`(expr) | Collect records into an array.  
---|---  
`collect_as_set`(expr) | Collect records into a set.  
`count`() | Count the number of records.  
`count_where`(condition) | Count the number of records where a predicate is `True`.  
`counter`(expr, *[, weight]) | Count the occurrences of each unique record and return a dictionary.  
`any`(condition) | Returns `True` if condition is `True` for any record.  
`all`(condition) | Returns `True` if condition is `True` for every record.  
`take`(expr, n[, ordering]) | Take n records of expr, optionally ordered by ordering.  
`min`(expr) | Compute the minimum expr.  
`max`(expr) | Compute the maximum expr.  
`sum`(expr) | Compute the sum of all records of expr.  
`array_sum`(expr) | Compute the coordinate-wise sum of all records of expr.  
`mean`(expr) | Compute the mean value of records of expr.  
`approx_quantiles`(expr, qs[, k]) | Compute an array of approximate quantiles.  
`approx_median`(expr[, k]) | Compute the approximate median.  
`stats`(expr) | Compute a number of useful statistics about expr.  
`product`(expr) | Compute the product of all records of expr.  
`fraction`(predicate) | Compute the fraction of records where predicate is `True`.  
`hardy_weinberg_test`(expr[, one_sided]) | Performs test of Hardy-Weinberg equilibrium.  
`explode`(f, array_agg_expr) | Explode an array or set expression to aggregate the elements of all records.  
`filter`(condition, aggregation) | Filter records according to a predicate.  
`inbreeding`(expr, prior) | Compute inbreeding statistics on calls.  
`call_stats`(call, alleles) | Compute useful call statistics.  
`info_score`(gp) | Compute the IMPUTE information score.  
`hist`(expr, start, end, bins) | Compute binned counts of a numeric expression.  
`linreg`(y, x[, nested_dim, weight]) | Compute multivariate linear regression statistics.  
`corr`(x, y) | Computes the [Pearson correlation coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient) between x and y.  
`group_by`(group, agg_expr) | Compute aggregation statistics stratified by one or more groups.  
`array_agg`(f, array) | Aggregate an array element-wise using a user-specified aggregation function.  
`downsample`(x, y[, label, n_divisions]) | Downsample (x, y) coordinate datapoints.  
`approx_cdf`(expr[, k, _raw]) | Produce a summary of the distribution of values.  
  
hail.expr.aggregators.collect(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#collect)

    

Collect records into an array.

Examples

Collect the ID field where HT is greater than 68:

    
    
    >>> table1.aggregate(hl.agg.filter(table1.HT > 68, hl.agg.collect(table1.ID)))
    [2, 3]
    

Notes

The element order of the resulting array is not guaranteed, and in some cases
is non-deterministic.

Use `collect_as_set()` to collect unique items.

Warning

Collecting a large number of items can cause out-of-memory exceptions.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression to collect.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of all expr records.

hail.expr.aggregators.collect_as_set(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#collect_as_set)

    

Collect records into a set.

Examples

Collect the unique ID field where HT is greater than 68:

    
    
    >>> table1.aggregate(hl.agg.filter(table1.HT > 68, hl.agg.collect_as_set(table1.ID)))
    {2, 3}
    

Note that when collecting a set-typed field with `collect_as_set()`, the
values become
[`frozenset`](https://docs.python.org/3.9/library/stdtypes.html#frozenset
"\(in Python v3.9\)") s because Python does not permit the keys of a
dictionary to be mutable:

    
    
    >>> table1.aggregate(hl.agg.filter(table1.HT > 68, hl.agg.collect_as_set(hl.set({table1.ID}))))
    {frozenset({3}), frozenset({2})}
    

Warning

Collecting a large number of items can cause out-of-memory exceptions.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression to collect.

Returns:

    

[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") – Set of unique expr records.

hail.expr.aggregators.count()[[source]](_modules/hail/expr/aggregators/aggregators.html#count)

    

Count the number of records.

Examples

Group by the SEX field and count the number of rows in each category:

    
    
    >>> (table1.group_by(table1.SEX)
    ...        .aggregate(n=hl.agg.count())
    ...        .show())
    +-----+-------+
    | SEX |     n |
    +-----+-------+
    | str | int64 |
    +-----+-------+
    | "F" |     2 |
    | "M" |     2 |
    +-----+-------+
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") – Total number of records.

hail.expr.aggregators.count_where(_condition_)[[source]](_modules/hail/expr/aggregators/aggregators.html#count_where)

    

Count the number of records where a predicate is `True`.

Examples

Count the number of individuals with HT greater than 68:

    
    
    >>> table1.aggregate(hl.agg.count_where(table1.HT > 68))
    2
    

Parameters:

    

**condition**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Criteria for inclusion.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") – Total number of records where condition is `True`.

hail.expr.aggregators.counter(_expr_ , _*_ , _weight
=None_)[[source]](_modules/hail/expr/aggregators/aggregators.html#counter)

    

Count the occurrences of each unique record and return a dictionary.

Examples

Count the number of individuals for each unique SEX value:

    
    
    >>> table1.aggregate(hl.agg.counter(table1.SEX))
    {'F': 2, 'M': 2}
    

Compute the total height for each unique SEX value:

    
    
    >>> table1.aggregate(hl.agg.counter(table1.SEX, weight=table1.HT))
    {'F': 130, 'M': 137}
    

Note that when counting a set-typed field, the values become
[`frozenset`](https://docs.python.org/3.9/library/stdtypes.html#frozenset
"\(in Python v3.9\)") s because Python does not permit the keys of a
dictionary to be mutable:

    
    
    >>> table1.aggregate(hl.agg.counter(hl.set({table1.SEX}), weight=table1.HT))
    {frozenset({'F'}): 130, frozenset({'M'}): 137}
    

Notes

If you need a more complex grouped aggregation than `counter()` supports, try
using `group_by()`.

This aggregator method returns a dict expression whose key type is the same
type as expr and whose value type is
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64"). This dict contains a key for each unique value of
expr, and the value is the number of times that key was observed.

Ensure that the result can be stored in memory on a single machine.

Warning

Using `counter()` with a large number of unique items can cause out-of-memory
exceptions.

Parameters:

    

  * **expr** (```Expression```) – Expression to count by key.

  * **weight** (```NumericExpression```, optional) – Expression by which to weight each occurence (when unspecified, it is effectively `1`)

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression") – Dictionary with the number of occurrences of
each unique record.

hail.expr.aggregators.any(_condition_)[[source]](_modules/hail/expr/aggregators/aggregators.html#any)

    

Returns `True` if condition is `True` for any record.

Examples

    
    
    >>> (table1.group_by(table1.SEX)
    ... .aggregate(any_over_70 = hl.agg.any(table1.HT > 70))
    ... .show())
    +-----+-------------+
    | SEX | any_over_70 |
    +-----+-------------+
    | str | bool        |
    +-----+-------------+
    | "F" | False       |
    | "M" | True        |
    +-----+-------------+
    

Notes

If there are no records to aggregate, the result is `False`.

Missing records are not considered. If every record is missing, the result is
also `False`.

Parameters:

    

**condition**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Condition to test.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.aggregators.all(_condition_)[[source]](_modules/hail/expr/aggregators/aggregators.html#all)

    

Returns `True` if condition is `True` for every record.

Examples

    
    
    >>> (table1.group_by(table1.SEX)
    ... .aggregate(all_under_70 = hl.agg.all(table1.HT < 70))
    ... .show())
    +-----+--------------+
    | SEX | all_under_70 |
    +-----+--------------+
    | str | bool         |
    +-----+--------------+
    | "F" | False        |
    | "M" | False        |
    +-----+--------------+
    

Notes

If there are no records to aggregate, the result is `True`.

Missing records are not considered. If every record is missing, the result is
also `True`.

Parameters:

    

**condition**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Condition to test.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.aggregators.take(_expr_ , _n_ , _ordering
=None_)[[source]](_modules/hail/expr/aggregators/aggregators.html#take)

    

Take n records of expr, optionally ordered by ordering.

Examples

Take 3 elements of field X:

    
    
    >>> table1.aggregate(hl.agg.take(table1.X, 3))
    [5, 6, 7]
    

Take the ID and HT fields, ordered by HT (descending):

    
    
    >>> table1.aggregate(hl.agg.take(hl.struct(ID=table1.ID, HT=table1.HT),
    ...                              3,
    ...                              ordering=-table1.HT))
    [Struct(ID=2, HT=72), Struct(ID=3, HT=70), Struct(ID=1, HT=65)]
    

Notes

The resulting array can include fewer than n elements if there are fewer than
n total records.

The ordering argument may be an expression, a function, or `None`.

If ordering is an expression, this expression’s type should be one with a
natural ordering (e.g. numeric).

If ordering is a function, it will be evaluated on each record of expr to
compute the value used for ordering. In the above example,
`ordering=-table1.HT` and `ordering=lambda x: -x.HT` would be equivalent.

If ordering is `None`, then there is no guaranteed ordering on the elements
taken, and and the results may be non-deterministic.

Missing values are always sorted **last**.

Parameters:

    

  * **expr** (```Expression```) – Expression to store.

  * **n** (```Expression``` of type ```tint32```) – Number of records to take.

  * **ordering** (```Expression``` or function ((arg) -> ```Expression```) or None) – Optional ordering on records.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of up to n records of expr.

hail.expr.aggregators.min(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#min)

    

Compute the minimum expr.

Examples

Compute the minimum value of HT:

    
    
    >>> table1.aggregate(hl.agg.min(table1.HT))
    60
    

Notes

This function returns the minimum non-missing value. If there are no non-
missing values, then the result is missing.

For back-compatibility reasons, this function also ignores NaN, in contrast
with [`functions.min()`](functions/numeric.html#hail.expr.functions.min
"hail.expr.functions.min"). The behavior is similar to
[`functions.nanmin()`](functions/numeric.html#hail.expr.functions.nanmin
"hail.expr.functions.nanmin").

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Minimum value of all expr records, same type
as expr.

hail.expr.aggregators.max(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#max)

    

Compute the maximum expr.

Examples

Compute the maximum value of HT:

    
    
    >>> table1.aggregate(hl.agg.max(table1.HT))
    72
    

Notes

This function returns the maximum non-missing value. If there are no non-
missing values, then the result is missing.

For back-compatibility reasons, this function also ignores NaN, in contrast
with [`functions.max()`](functions/numeric.html#hail.expr.functions.max
"hail.expr.functions.max"). The behavior is similar to
[`functions.nanmax()`](functions/numeric.html#hail.expr.functions.nanmax
"hail.expr.functions.nanmax").

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Maximum value of all expr records, same type
as expr.

hail.expr.aggregators.sum(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#sum)

    

Compute the sum of all records of expr.

Examples

Compute the sum of field C1:

    
    
    >>> table1.aggregate(hl.agg.sum(table1.C1))
    25
    

Notes

Missing values are ignored (treated as zero).

If expr is an expression of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32"), [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64"), or [`tbool`](types.html#hail.expr.types.tbool
"hail.expr.types.tbool"), then the result is an expression of type
```tint64```. If
expr is an expression of type [`tfloat32`](types.html#hail.expr.types.tfloat32
"hail.expr.types.tfloat32") or
```tfloat64```,
then the result is an expression of type
```tfloat64```.

Warning

Boolean values are cast to integers before computing the sum.

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") or [`tfloat64`](types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – Sum of records of expr.

hail.expr.aggregators.array_sum(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#array_sum)

    

Compute the coordinate-wise sum of all records of expr.

Examples

Compute the sum of C1 and C2:

    
    
    >>> table1.aggregate(hl.agg.array_sum([table1.C1, table1.C2]))
    [25, 282]
    

Notes

All records must have the same length. Each coordinate is summed independently
as described in `sum()`.

Parameters:

    

**expr**
([`ArrayNumericExpression`](hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression"))

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") with element type
```tint64``` or
```tfloat64```

hail.expr.aggregators.mean(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#mean)

    

Compute the mean value of records of expr.

Examples

Compute the mean of field HT:

    
    
    >>> table1.aggregate(hl.agg.mean(table1.HT))
    66.75
    

Notes

Missing values are ignored.

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Mean value of records of expr.

hail.expr.aggregators.approx_quantiles(_expr_ , _qs_ , _k
=100_)[[source]](_modules/hail/expr/aggregators/aggregators.html#approx_quantiles)

    

Compute an array of approximate quantiles.

Examples

Estimate the median of the HT field.

    
    
    >>> table1.aggregate(hl.agg.approx_quantiles(table1.HT, 0.5)) 
    64
    

Estimate the quartiles of the HT field.

    
    
    >>> table1.aggregate(hl.agg.approx_quantiles(table1.HT, [0, 0.25, 0.5, 0.75, 1])) 
    [50, 60, 64, 71, 86]
    

Warning

This is an approximate and nondeterministic method.

Parameters:

    

  * **expr** (```Expression```) – Expression to collect.

  * **qs** (```NumericExpression``` or ```ArrayNumericExpression```) – Number or array of numbers between 0 and 1.

  * **k** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Parameter controlling the accuracy vs. memory usage tradeoff. Increasing k increases both memory use and accuracy.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`ArrayNumericExpression`](hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") – If qs is a single number, returns the
estimated quantile. If qs is an array, returns the array of estimated
quantiles.

hail.expr.aggregators.approx_median(_expr_ , _k
=100_)[[source]](_modules/hail/expr/aggregators/aggregators.html#approx_median)

    

Compute the approximate median. This function is a shorthand for
approx_quantiles(expr, .5, k)

Examples

Estimate the median of the HT field.

    
    
    >>> table1.aggregate(hl.agg.approx_median(table1.HT)) 
    64
    

Warning

This is an approximate and nondeterministic method.

Parameters:

    

  * **expr** (```Expression```) – Expression to collect.

  * **k** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Parameter controlling the accuracy vs. memory usage tradeoff. Increasing k increases both memory use and accuracy.

See also

`approx_quantiles()`

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The estimated median.

hail.expr.aggregators.stats(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#stats)

    

Compute a number of useful statistics about expr.

Examples

Compute statistics about field HT:

    
    
    >>> table1.aggregate(hl.agg.stats(table1.HT))  
    Struct(mean=66.75, stdev=4.656984002549289, min=60.0, max=72.0, n=4, sum=267.0)
    

Notes

Computes a struct with the following fields:

  * min (```tfloat64```) - Minimum value.

  * max (```tfloat64```) - Maximum value.

  * mean (```tfloat64```) - Mean value,

  * stdev (```tfloat64```) - Standard deviation.

  * n (```tint64```) - Number of non-missing records.

  * sum (```tfloat64```) - Sum.

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct expression with fields mean, stdev,
min, max, n, and sum.

hail.expr.aggregators.product(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#product)

    

Compute the product of all records of expr.

Examples

Compute the product of field C1:

    
    
    >>> table1.aggregate(hl.agg.product(table1.C1))
    440
    

Notes

Missing values are ignored (treated as one).

If expr is an expression of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32"), [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") or [`tbool`](types.html#hail.expr.types.tbool
"hail.expr.types.tbool"), then the result is an expression of type
```tint64```. If
expr is an expression of type [`tfloat32`](types.html#hail.expr.types.tfloat32
"hail.expr.types.tfloat32") or
```tfloat64```,
then the result is an expression of type
```tfloat64```.

Warning

Boolean values are cast to integers before computing the product.

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") or [`tfloat64`](types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – Product of records of expr.

hail.expr.aggregators.fraction(_predicate_)[[source]](_modules/hail/expr/aggregators/aggregators.html#fraction)

    

Compute the fraction of records where predicate is `True`.

Examples

Compute the fraction of rows where SEX is “F” and HT > 65:

    
    
    >>> table1.aggregate(hl.agg.fraction((table1.SEX == 'F') & (table1.HT > 65)))
    0.25
    

Notes

Missing values for predicate are treated as `False`.

Parameters:

    

**predicate**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Boolean predicate.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Fraction of records where predicate is `True`.

hail.expr.aggregators.hardy_weinberg_test(_expr_ , _one_sided
=False_)[[source]](_modules/hail/expr/aggregators/aggregators.html#hardy_weinberg_test)

    

Performs test of Hardy-Weinberg equilibrium.

Examples

Test each row of a dataset:

    
    
    >>> dataset_result = dataset.annotate_rows(hwe = hl.agg.hardy_weinberg_test(dataset.GT))
    

Test each row on a sub-population:

    
    
    >>> dataset_result = dataset.annotate_rows(
    ...     hwe_eas = hl.agg.filter(dataset.pop == 'EAS',
    ...                             hl.agg.hardy_weinberg_test(dataset.GT)))
    

Notes

This method performs the test described in
[`functions.hardy_weinberg_test()`](functions/stats.html#hail.expr.functions.hardy_weinberg_test
"hail.expr.functions.hardy_weinberg_test") based solely on the counts of
homozygous reference, heterozygous, and homozygous variant calls.

The resulting struct expression has two fields:

  * het_freq_hwe (```tfloat64```) - Expected frequency of heterozygous calls under Hardy-Weinberg equilibrium.

  * p_value (```tfloat64```) - p-value from test of Hardy-Weinberg equilibrium.

By default, Hail computes the exact p-value with mid-p-value correction, i.e.
the probability of a less-likely outcome plus one-half the probability of an
equally-likely outcome. See this [document](_static/LeveneHaldane.pdf) for
details on the Levene-Haldane distribution and references.

To perform one-sided exact test of excess heterozygosity with mid-p-value
correction instead, set one_sided=True and the p-value returned will be from
the one-sided exact test.

Warning

Non-diploid calls (`ploidy != 2`) are ignored in the counts. While the counts
are defined for multiallelic variants, this test is only statistically
rigorous in the biallelic setting; use
[`split_multi()`](methods/genetics.html#hail.methods.split_multi
"hail.methods.split_multi") to split multiallelic variants beforehand.

Parameters:

    

  * **expr** (```CallExpression```) – Call to test for Hardy-Weinberg equilibrium.

  * **one_sided** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – `False` by default. When `True`, perform one-sided test for excess heterozygosity.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct expression with fields het_freq_hwe and
p_value.

hail.expr.aggregators.explode(_f_ ,
_array_agg_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#explode)

    

Explode an array or set expression to aggregate the elements of all records.

Examples

Compute the mean of all elements in fields C1, C2, and C3:

    
    
    >>> table1.aggregate(hl.agg.explode(lambda elt: hl.agg.mean(elt), [table1.C1, table1.C2, table1.C3]))
    24.833333333333332
    

Compute the set of all observed elements in the filters field (`Set[String]`):

    
    
    >>> dataset.aggregate_rows(hl.agg.explode(lambda elt: hl.agg.collect_as_set(elt), dataset.filters))
    set()
    

Notes

This method can be used with aggregator functions to aggregate the elements of
collection types ([`tarray`](types.html#hail.expr.types.tarray
"hail.expr.types.tarray") and [`tset`](types.html#hail.expr.types.tset
"hail.expr.types.tset")).

Parameters:

    

  * **f** (Function from ```Expression``` to ```Expression```) – Aggregation function to apply to each element of the exploded array.

  * **array_agg_expr** (```CollectionExpression```) – Expression of type ```tarray``` or ```tset```.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Aggregation expression.

hail.expr.aggregators.filter(_condition_ ,
_aggregation_)[[source]](_modules/hail/expr/aggregators/aggregators.html#filter)

    

Filter records according to a predicate.

Examples

Collect the ID field where HT >= 70:

    
    
    >>> table1.aggregate(hl.agg.filter(table1.HT >= 70, hl.agg.collect(table1.ID)))
    [2, 3]
    

Notes

This method can be used with aggregator functions to remove records from
aggregation.

Parameters:

    

  * **condition** (```BooleanExpression```) – Filter expression.

  * **aggregation** (```Expression```) – Aggregation expression to filter.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Aggregable expression.

hail.expr.aggregators.inbreeding(_expr_ ,
_prior_)[[source]](_modules/hail/expr/aggregators/aggregators.html#inbreeding)

    

Compute inbreeding statistics on calls.

Examples

Compute inbreeding statistics per column:

    
    
    >>> dataset_result = dataset.annotate_cols(IB = hl.agg.inbreeding(dataset.GT, dataset.variant_qc.AF[1]))
    >>> dataset_result.IB.show(width=100)
    +------------------+-----------+-------------+------------------+------------------+
    | s                | IB.f_stat | IB.n_called | IB.expected_homs | IB.observed_homs |
    +------------------+-----------+-------------+------------------+------------------+
    | str              |   float64 |       int64 |          float64 |            int64 |
    +------------------+-----------+-------------+------------------+------------------+
    | "C1046::HG02024" |  2.79e-01 |           9 |         7.61e+00 |                8 |
    | "C1046::HG02025" | -4.41e-01 |           9 |         7.61e+00 |                7 |
    | "C1046::HG02026" | -4.41e-01 |           9 |         7.61e+00 |                7 |
    | "C1047::HG00731" |  2.79e-01 |           9 |         7.61e+00 |                8 |
    | "C1047::HG00732" |  2.79e-01 |           9 |         7.61e+00 |                8 |
    | "C1047::HG00733" |  2.79e-01 |           9 |         7.61e+00 |                8 |
    | "C1048::HG02024" | -4.41e-01 |           9 |         7.61e+00 |                7 |
    | "C1048::HG02025" | -4.41e-01 |           9 |         7.61e+00 |                7 |
    | "C1048::HG02026" | -4.41e-01 |           9 |         7.61e+00 |                7 |
    | "C1049::HG00731" |  2.79e-01 |           9 |         7.61e+00 |                8 |
    +------------------+-----------+-------------+------------------+------------------+
    showing top 10 rows
    

Notes

`E` is total number of expected homozygous calls, given by the sum of `1 - 2.0
* prior * (1 - prior)` across records.

`O` is the observed number of homozygous calls across records.

`N` is the number of non-missing calls.

`F` is the inbreeding coefficient, and is computed by: `(O - E) / (N - E)`.

This method returns a struct expression with four fields:

>   * f_stat ([`tfloat64`](types.html#hail.expr.types.tfloat64
> "hail.expr.types.tfloat64")): `F`, the inbreeding coefficient.
>
>   * n_called ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")): `N`, the number of non-missing calls.
>
>   * expected_homs ([`tfloat64`](types.html#hail.expr.types.tfloat64
> "hail.expr.types.tfloat64")): `E`, the expected number of homozygotes.
>
>   * observed_homs ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")): `O`, the number of observed homozygotes.
>
>

Parameters:

    

  * **expr** (```CallExpression```) – Call expression.

  * **prior** (```Expression``` of type ```tfloat64```) – Alternate allele frequency prior.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct expression with fields f_stat,
n_called, expected_homs, observed_homs.

hail.expr.aggregators.call_stats(_call_ ,
_alleles_)[[source]](_modules/hail/expr/aggregators/aggregators.html#call_stats)

    

Compute useful call statistics.

Examples

Compute call statistics per row:

    
    
    >>> dataset_result = dataset.annotate_rows(gt_stats = hl.agg.call_stats(dataset.GT, dataset.alleles))
    >>> dataset_result.rows().key_by('locus').select('gt_stats').show(width=120)
    +---------------+--------------+---------------------+-------------+---------------------------+
    | locus         | gt_stats.AC  | gt_stats.AF         | gt_stats.AN | gt_stats.homozygote_count |
    +---------------+--------------+---------------------+-------------+---------------------------+
    | locus<GRCh37> | array<int32> | array<float64>      |       int32 | array<int32>              |
    +---------------+--------------+---------------------+-------------+---------------------------+
    | 20:10579373   | [199,1]      | [9.95e-01,5.00e-03] |         200 | [99,0]                    |
    | 20:10579398   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [99,1]                    |
    | 20:10627772   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [98,0]                    |
    | 20:10633237   | [108,92]     | [5.40e-01,4.60e-01] |         200 | [31,23]                   |
    | 20:10636995   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [98,0]                    |
    | 20:10639222   | [175,25]     | [8.75e-01,1.25e-01] |         200 | [78,3]                    |
    | 20:13763601   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [98,0]                    |
    | 20:16223922   | [87,101]     | [4.63e-01,5.37e-01] |         188 | [28,35]                   |
    | 20:17479617   | [191,9]      | [9.55e-01,4.50e-02] |         200 | [91,0]                    |
    +---------------+--------------+---------------------+-------------+---------------------------+
    

Notes

This method is meaningful for computing call metrics per variant, but not
especially meaningful for computing metrics per sample.

This method returns a struct expression with three fields:

>   * AC ([`tarray`](types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of [`tint32`](types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Allele counts. One element for each allele,
> including the reference.
>
>   * AF ([`tarray`](types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of
> [`tfloat64`](types.html#hail.expr.types.tfloat64
> "hail.expr.types.tfloat64")) - Allele frequencies. One element for each
> allele, including the reference.
>
>   * AN ([`tint32`](types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Allele number. The total number of called
> alleles, or the number of non-missing calls * 2.
>
>   * homozygote_count ([`tarray`](types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of [`tint32`](types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Homozygote genotype counts for each allele,
> including the reference. Only **diploid** genotype calls are counted.
>
>

Parameters:

    

  * **call** (```CallExpression```)

  * **alleles** (```ArrayExpression``` of strings or ```Int32Expression```) – Variant alleles array, or number of alleles (including reference).

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct expression with fields AC, AF, AN, and
homozygote_count.

hail.expr.aggregators.info_score(_gp_)[[source]](_modules/hail/expr/aggregators/aggregators.html#info_score)

    

Compute the IMPUTE information score.

Examples

Calculate the info score per variant:

    
    
    >>> gen_mt = hl.import_gen('data/example.gen', sample_file='data/example.sample')
    >>> gen_mt = gen_mt.annotate_rows(info_score = hl.agg.info_score(gen_mt.GP))
    

Calculate group-specific info scores per variant:

    
    
    >>> gen_mt = hl.import_gen('data/example.gen', sample_file='data/example.sample')
    >>> gen_mt = gen_mt.annotate_cols(is_case = hl.rand_bool(0.5))
    >>> gen_mt = gen_mt.annotate_rows(info_score = hl.agg.group_by(gen_mt.is_case, hl.agg.info_score(gen_mt.GP)))
    

Notes

The result of `info_score()` is a struct with two fields:

>   * score (`float64`) – Info score.
>
>   * n_included (`int32`) – Number of non-missing samples included in the
> calculation.
>
>

We implemented the IMPUTE info measure as described in the supplementary
information from [Marchini & Howie. Genotype imputation for genome-wide
association studies. Nature Reviews Genetics
(2010)](http://www.nature.com/nrg/journal/v11/n7/extref/nrg2796-s3.pdf). To
calculate the info score \\(I_{A}\\) for one SNP:

\\[I_{A} = \begin{cases} 1 - \frac{\sum_{i=1}^{N}(f_{i} -
e_{i}^2)}{2N\hat{\theta}(1 - \hat{\theta})} & \text{when } \hat{\theta} \in
(0, 1) \\\ 1 & \text{when } \hat{\theta} = 0, \hat{\theta} = 1\\\
\end{cases}\\]

  * \\(N\\) is the number of samples with imputed genotype probabilities [\\(p_{ik} = P(G_{i} = k)\\) where \\(k \in \\{0, 1, 2\\}\\)]

  * \\(e_{i} = p_{i1} + 2p_{i2}\\) is the expected genotype per sample

  * \\(f_{i} = p_{i1} + 4p_{i2}\\)

  * \\(\hat{\theta} = \frac{\sum_{i=1}^{N}e_{i}}{2N}\\) is the MLE for the population minor allele frequency

Hail will not generate identical results to
[QCTOOL](http://www.well.ox.ac.uk/~gav/qctool/#overview) for the following
reasons:

  * Hail automatically removes genotype probability distributions that do not meet certain requirements on data import with ```import_gen()``` and ```import_bgen()```.

  * Hail does not use the population frequency to impute genotype probabilities when a genotype probability distribution has been set to missing.

  * Hail calculates the same statistic for sex chromosomes as autosomes while QCTOOL incorporates sex information.

  * The floating point number Hail stores for each genotype probability is slightly different than the original data due to rounding and normalization of probabilities.

Warning

  * The info score Hail reports will be extremely different from QCTOOL when a SNP has a high missing rate.

  * If the gp array must contain 3 elements, and its elements may not be missing.

  * If the genotype data was not imported using the ```import_gen()``` or ```import_bgen()``` functions, then the results for all variants will be `score = NA` and `n_included = 0`.

  * It only makes semantic sense to compute the info score per variant. While the aggregator will run in any context if its arguments are the right type, the results are only meaningful in a narrow context.

Parameters:

    

**gp**
([`ArrayNumericExpression`](hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression")) – Genotype probability array. Must have 3
elements, all of which must be defined.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct with fields score and n_included.

hail.expr.aggregators.hist(_expr_ , _start_ , _end_ ,
_bins_)[[source]](_modules/hail/expr/aggregators/aggregators.html#hist)

    

Compute binned counts of a numeric expression.

Examples

Compute a histogram of field GQ:

    
    
    >>> dataset.aggregate_entries(hl.agg.hist(dataset.GQ, 0, 100, 10))  
    Struct(bin_edges=[0.0, 10.0, 20.0, 30.0, 40.0, 50.0, 60.0, 70.0, 80.0, 90.0, 100.0],
           bin_freq=[2194L, 637L, 2450L, 1081L, 518L, 402L, 11168L, 1918L, 1379L, 11973L]),
           n_smaller=0,
           n_greater=0)
    

Notes

This method returns a struct expression with four fields:

>   * bin_edges ([`tarray`](types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of
> [`tfloat64`](types.html#hail.expr.types.tfloat64
> "hail.expr.types.tfloat64")): Bin edges. Bin i contains values in the left-
> inclusive, right-exclusive range `[ bin_edges[i], bin_edges[i+1] )`.
>
>   * bin_freq ([`tarray`](types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of [`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")): Bin frequencies. The number of records found in
> each bin.
>
>   * n_smaller ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")): The number of records smaller than the start of
> the first bin.
>
>   * n_larger ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")): The number of records larger than the end of the
> last bin.
>
>

Parameters:

    

  * **expr** (```NumericExpression```) – Target numeric expression.

  * **start** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)") or [`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Start of histogram range.

  * **end** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)") or [`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – End of histogram range.

  * **bins** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Number of bins.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct expression with fields bin_edges,
bin_freq, n_smaller, and n_larger.

hail.expr.aggregators.linreg(_y_ , _x_ , _nested_dim =1_, _weight
=None_)[[source]](_modules/hail/expr/aggregators/aggregators.html#linreg)

    

Compute multivariate linear regression statistics.

Examples

Regress HT against an intercept (1), SEX, and C1:

    
    
    >>> table1.aggregate(hl.agg.linreg(table1.HT, [1, table1.SEX == 'F', table1.C1]))  
    Struct(beta=[88.50000000000014, 81.50000000000057, -10.000000000000068],
           standard_error=[14.430869689661844, 59.70552738231206, 7.000000000000016],
           t_stat=[6.132686518775844, 1.365032746099571, -1.428571428571435],
           p_value=[0.10290201427537926, 0.40250974549499974, 0.3888002244284281],
           multiple_standard_error=4.949747468305833,
           multiple_r_squared=0.7175792507204611,
           adjusted_r_squared=0.1527377521613834,
           f_stat=1.2704081632653061,
           multiple_p_value=0.5314327326007864,
           n=4)
    

Regress blood pressure against an intercept (1), genotype, age, and the
interaction of genotype and age:

    
    
    >>> ds_ann = ds.annotate_rows(linreg =
    ...     hl.agg.linreg(ds.pheno.blood_pressure,
    ...                   [1,
    ...                    ds.GT.n_alt_alleles(),
    ...                    ds.pheno.age,
    ...                    ds.GT.n_alt_alleles() * ds.pheno.age]))
    

Warning

As in the example, the intercept covariate `1` must be included **explicitly**
if desired.

Notes

In relation to
[lm.summary](https://stat.ethz.ch/R-manual/R-devel/library/stats/html/summary.lm.html)
in R, `linreg(y, x = [1, mt.x1, mt.x2])` computes `summary(lm(y ~ x1 + x2))`
and `linreg(y, x = [mt.x1, mt.x2], nested_dim=0)` computes `summary(lm(y ~ x1
+ x2 - 1))`.

More generally, nested_dim defines the number of effects to fit in the nested
(null) model, with the effects on the remaining covariates fixed to zero.

The returned struct has ten fields:

    

  * beta (```tarray``` of ```tfloat64```): Estimated regression coefficient for each covariate.

  * standard_error (```tarray``` of ```tfloat64```): Estimated standard error for each covariate.

  * t_stat (```tarray``` of ```tfloat64```): t-statistic for each covariate.

  * p_value (```tarray``` of ```tfloat64```): p-value for each covariate.

  * multiple_standard_error (```tfloat64```): Estimated standard deviation of the random error.

  * multiple_r_squared (```tfloat64```): Coefficient of determination for nested models.

  * adjusted_r_squared (```tfloat64```): Adjusted multiple_r_squared taking into account degrees of freedom.

  * f_stat (```tfloat64```): F-statistic for nested models.

  * multiple_p_value (```tfloat64```): p-value for the [F-test](https://en.wikipedia.org/wiki/F-test#Regression_problems) of nested models.

  * n (```tint64```): Number of samples included in the regression. A sample is included if and only if y, all elements of x, and weight (if set) are non-missing.

All but the last field are missing if n is less than or equal to the number of
covariates or if the covariates are linearly dependent.

If set, the weight parameter generalizes the model to [weighted least
squares](https://en.wikipedia.org/wiki/Weighted_least_squares), useful for
heteroscedastic (diagonal but non-constant) variance.

Warning

If any weight is negative, the resulting statistics will be `nan`.

Parameters:

    

  * **y** (```Float64Expression```) – Response (dependent variable).

  * **x** (```Float64Expression``` or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of ```Float64Expression```) – Covariates (independent variables).

  * **nested_dim** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – The null model includes the first nested_dim covariates. Must be between 0 and k (the length of x).

  * **weight** (```Float64Expression```, optional) – Non-negative weight for weighted least squares.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of regression results.

hail.expr.aggregators.corr(_x_ ,
_y_)[[source]](_modules/hail/expr/aggregators/aggregators.html#corr)

    

Computes the [Pearson correlation
coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)
between x and y.

Examples

    
    
    >>> ds.aggregate_cols(hl.agg.corr(ds.pheno.age, ds.pheno.blood_pressure))  
    0.16592876044845484
    

Notes

Only records where both x and y are non-missing will be included in the
calculation.

In the case that there are no non-missing pairs, the result will be missing.

See also

`linreg()`

Parameters:

    

  * **x** (```Expression``` of type `tfloat64`)

  * **y** (```Expression``` of type `tfloat64`)

Returns:

    

[`Float64Expression`](hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression")

hail.expr.aggregators.group_by(_group_ ,
_agg_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#group_by)

    

Compute aggregation statistics stratified by one or more groups.

Examples

Compute linear regression statistics stratified by SEX:

    
    
    >>> table1.aggregate(hl.agg.group_by(table1.SEX,
    ...                                  hl.agg.linreg(table1.HT, table1.C1, nested_dim=0)))  
    {
    'F': Struct(beta=[6.153846153846154],
                standard_error=[0.7692307692307685],
                t_stat=[8.000000000000009],
                p_value=[0.07916684832113098],
                multiple_standard_error=11.4354374979373,
                multiple_r_squared=0.9846153846153847,
                adjusted_r_squared=0.9692307692307693,
                f_stat=64.00000000000014,
                multiple_p_value=0.07916684832113098,
                n=2),
    'M': Struct(beta=[34.25],
                standard_error=[1.75],
                t_stat=[19.571428571428573],
                p_value=[0.03249975499062629],
                multiple_standard_error=4.949747468305833,
                multiple_r_squared=0.9973961101073441,
                adjusted_r_squared=0.9947922202146882,
                f_stat=383.0408163265306,
                multiple_p_value=0.03249975499062629,
                n=2)
    }
    

Compute call statistics stratified by population group and case status:

    
    
    >>> ann = ds.annotate_rows(call_stats=hl.agg.group_by(hl.struct(pop=ds.pop, is_case=ds.is_case),
    ...                                                   hl.agg.call_stats(ds.GT, ds.alleles)))
    

Parameters:

    

  * **group** (```Expression``` or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of ```Expression```) – Group to stratify the result by.

  * **agg_expr** (```Expression```) – Aggregation or scan expression to compute per grouping.

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression") – Dictionary where the keys are group and the
values are the result of computing agg_expr for each unique value of group.

hail.expr.aggregators.array_agg(_f_ ,
_array_)[[source]](_modules/hail/expr/aggregators/aggregators.html#array_agg)

    

Aggregate an array element-wise using a user-specified aggregation function.

Examples

Start with a range table with an array of random boolean values:

    
    
    >>> ht = hl.utils.range_table(100)
    >>> ht = ht.annotate(arr = hl.range(0, 5).map(lambda _: hl.rand_bool(0.5)))
    

Aggregate to compute the fraction `True` per element:

    
    
    >>> ht.aggregate(hl.agg.array_agg(lambda element: hl.agg.fraction(element), ht.arr))  
    [0.54, 0.55, 0.46, 0.52, 0.48]
    

Notes

This function requires that all values of array have the same length. If two
values have different lengths, then an exception will be thrown.

The f argument should be a function taking one argument, an expression of the
element type of array, and returning an expression including aggregation(s).
The type of the aggregated expression returned by `array_agg()` is an array of
elements of the return type of f.

Parameters:

    

  * **f** – Aggregation function to apply to each element of the exploded array.

  * **array** (```ArrayExpression```) – Array to aggregate.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

hail.expr.aggregators.downsample(_x_ , _y_ , _label =None_, _n_divisions
=500_)[[source]](_modules/hail/expr/aggregators/aggregators.html#downsample)

    

Downsample (x, y) coordinate datapoints.

Parameters:

    

  * **x** (```NumericExpression```) – X-values to be downsampled.

  * **y** (```NumericExpression```) – Y-values to be downsampled.

  * **label** (```StringExpression``` or ```ArrayExpression```) – Additional data for each (x, y) coordinate. Can pass in multiple fields in an ```ArrayExpression```.

  * **n_divisions** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Factor by which to downsample (default value = 500). A lower input results in fewer output datapoints.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Expression for downsampled coordinate points
(x, y). The element type of the array is
```ttuple``` of
```tfloat64```,
```tfloat64```,
and ```tarray``` of
```tstr```

hail.expr.aggregators.approx_cdf(_expr_ , _k =100_, _*_ , __raw
=False_)[[source]](_modules/hail/expr/aggregators/aggregators.html#approx_cdf)

    

Produce a summary of the distribution of values.

Notes

This method returns a struct containing two arrays: values and ranks. The
values array contains an ordered sample of values seen. The ranks array is one
longer, and contains the approximate ranks for the corresponding values.

These represent a summary of the CDF of the distribution of values. In
particular, for any value x = values(i) in the summary, we estimate that there
are ranks(i) values strictly less than x, and that there are ranks(i+1) values
less than or equal to x. For any value y (not necessarily in the summary), we
estimate CDF(y) to be ranks(i), where i is such that values(i-1) < y ≤
values(i).

An alternative intuition is that the summary encodes a compressed
approximation to the sorted list of values. For example, values=[0,2,5,6,9]
and ranks=[0,3,4,5,8,10] represents the approximation [0,0,0,2,5,6,6,6,9,9],
with the value values(i) occupying indices ranks(i) (inclusive) to ranks(i+1)
(exclusive).

The returned struct also contains an array _compaction_counts, which is used
internally to support downstream error estimation.

Warning

This is an approximate and nondeterministic method.

Parameters:

    

  * **expr** (```Expression```) – Expression to collect.

  * **k** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Parameter controlling the accuracy vs. memory usage tradeoff.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct containing values and ranks arrays.

---

