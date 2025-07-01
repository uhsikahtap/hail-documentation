## Functions


These functions are exposed at the top level of the module, e.g. `hl.case`.

  * ``Core language functions``
    * ```literal()```
    * ```cond()```
    * ```if_else()```
    * ```switch()```
    * ```case()```
    * ```bind()```
    * ```rbind()```
    * ```missing()```
    * ```null()```
    * ```str()```
    * ```is_missing()```
    * ```is_defined()```
    * ```coalesce()```
    * ```or_else()```
    * ```or_missing()```
    * ```range()```
    * ```query_table()```
    * ``ConditionalBuilder``
    * ``CaseBuilder``
    * ``SwitchBuilder``
  * ``Constructor functions``
    * ```bool()```
    * ```float()```
    * ```float32()```
    * ```float64()```
    * ```int()```
    * ```int32()```
    * ```int64()```
    * ```interval()```
    * ```struct()```
    * ```tuple()```
    * ```array()```
    * ```empty_array()```
    * ```set()```
    * ```empty_set()```
    * ```dict()```
    * ```empty_dict()```
  * ``Collection functions``
    * ```len()```
    * ```map()```
    * ```flatmap()```
    * ```starmap()```
    * ```zip()```
    * ```enumerate()```
    * ```zip_with_index()```
    * ```flatten()```
    * ```any()```
    * ```all()```
    * ```filter()```
    * ```sorted()```
    * ```find()```
    * ```group_by()```
    * ```fold()```
    * ```array_scan()```
    * ```reversed()```
    * ```keyed_intersection()```
    * ```keyed_union()```
  * ``Numeric functions``
    * ```abs()```
    * ```approx_equal()```
    * ```bit_and()```
    * ```bit_or()```
    * ```bit_xor()```
    * ```bit_lshift()```
    * ```bit_rshift()```
    * ```bit_not()```
    * ```bit_count()```
    * ```exp()```
    * ```expit()```
    * ```is_nan()```
    * ```is_finite()```
    * ```is_infinite()```
    * ```log()```
    * ```log10()```
    * ```logit()```
    * ```floor()```
    * ```ceil()```
    * ```sqrt()```
    * ```sign()```
    * ```min()```
    * ```nanmin()```
    * ```max()```
    * ```nanmax()```
    * ```mean()```
    * ```median()```
    * ```product()```
    * ```sum()```
    * ```cumulative_sum()```
    * ```argmin()```
    * ```argmax()```
    * ```corr()```
    * ```uniroot()```
    * ```binary_search()```
  * ``String functions``
    * ```format()```
    * ```json()```
    * ```parse_json()```
    * ```hamming()```
    * ```delimit()```
    * ```entropy()```
    * ```parse_int()```
    * ```parse_int32()```
    * ```parse_int64()```
    * ```parse_float()```
    * ```parse_float32()```
    * ```parse_float64()```
  * ``Statistical functions``
    * ```chi_squared_test()```
    * ```fisher_exact_test()```
    * ```contingency_table_test()```
    * ```cochran_mantel_haenszel_test()```
    * ```dbeta()```
    * ```dchisq()```
    * ```dnorm()```
    * ```dpois()```
    * ```hardy_weinberg_test()```
    * ```binom_test()```
    * ```pchisqtail()```
    * ```pgenchisq()```
    * ```pnorm()```
    * ```pT()```
    * ```pF()```
    * ```ppois()```
    * ```qchisqtail()```
    * ```qnorm()```
    * ```qpois()```
  * ``Random functions``
    * ``Setting a seed``
    * ``Reproducibility across sessions``
  * ``Genetics functions``
    * ```locus()```
    * ```locus_from_global_position()```
    * ```locus_interval()```
    * ```parse_locus()```
    * ```parse_variant()```
    * ```parse_locus_interval()```
    * ```variant_str()```
    * ```call()```
    * ```unphased_diploid_gt_index_call()```
    * ```parse_call()```
    * ```downcode()```
    * ```triangle()```
    * ```is_snp()```
    * ```is_mnp()```
    * ```is_transition()```
    * ```is_transversion()```
    * ```is_insertion()```
    * ```is_deletion()```
    * ```is_indel()```
    * ```is_star()```
    * ```is_complex()```
    * ```is_strand_ambiguous()```
    * ```is_valid_contig()```
    * ```is_valid_locus()```
    * ```contig_length()```
    * ```allele_type()```
    * ```numeric_allele_type()```
    * ```pl_dosage()```
    * ```gp_dosage()```
    * ```get_sequence()```
    * ```mendel_error_code()```
    * ```liftover()```
    * ```min_rep()```
    * ```reverse_complement()```

Core language functions

```literal```(x[, dtype]) | Captures and broadcasts a Python variable or object as an expression.  
---|---  
```cond```(condition, consequent, alternate[, ...]) | Deprecated in favor of ```if_else()```.  
```if_else```(condition, consequent, alternate[, ...]) | Expression for an if/else statement; tests a condition and returns one of two options based on the result.  
```switch```(expr) | Build a conditional tree on the value of an expression.  
```case```([missing_false]) | Chain multiple if-else statements with a ```CaseBuilder```.  
```bind```(f, *exprs[, _ctx]) | Bind a temporary variable and use it in a function.  
```rbind```(*exprs[, _ctx]) | Bind a temporary variable and use it in a function.  
```null```(t) | Deprecated in favor of ```missing()```.  
```is_missing```(expression) | Returns `True` if the argument is missing.  
```is_defined```(expression) | Returns `True` if the argument is not missing.  
```coalesce```(*args) | Returns the first non-missing value of args.  
```or_else```(a, b) | If a is missing, return b.  
```or_missing```(predicate, value) | Returns value if predicate is `True`, otherwise returns missing.  
```range```(start[, stop, step]) | Returns an array of integers from start to stop by step.  
```query_table```(path, point_or_interval) | Query records from a table corresponding to a given point or range of keys.  
  
Constructors

```bool```(x) | Convert to a Boolean expression.  
---|---  
```float```(x) | Convert to a 64-bit floating point expression.  
```float32```(x) | Convert to a 32-bit floating point expression.  
```float64```(x) | Convert to a 64-bit floating point expression.  
```int```(x) | Convert to a 32-bit integer expression.  
```int32```(x) | Convert to a 32-bit integer expression.  
```int64```(x) | Convert to a 64-bit integer expression.  
```interval```(start, end[, includes_start, ...]) | Construct an interval expression.  
```str```(x) | Returns the string representation of x.  
```struct```(**kwargs) | Construct a struct expression.  
```tuple```(iterable) | Construct a tuple expression.  
  
Collection constructors

```array```(collection) | Construct an array expression.  
---|---  
```empty_array```(t) | Returns an empty array of elements of a type t.  
```set```(collection) | Convert a set expression.  
```empty_set```(t) | Returns an empty set of elements of a type t.  
```dict```(collection) | Creates a dictionary.  
```empty_dict```(key_type, value_type) | Returns an empty dictionary with key type key_type and value type value_type.  
  
Collection functions

```len```(x) | Returns the size of a collection or string.  
---|---  
```map```(f, *collections) | Transform each element of a collection.  
```flatmap```(f, collection) | Map each element of the collection to a new collection, and flatten the results.  
```zip```(*arrays[, fill_missing]) | Zip together arrays into a single array.  
```enumerate```(a[, start, index_first]) | Returns an array of (index, element) tuples.  
```zip_with_index```(a[, index_first]) | Deprecated in favor of ```enumerate()```.  
```flatten```(collection) | Flatten a nested collection by concatenating sub-collections.  
```any```(*args) | Check for any `True` in boolean expressions or collections of booleans.  
```all```(*args) | Check for all `True` in boolean expressions or collections of booleans.  
```filter```(f, collection) | Returns a new collection containing elements where f returns `True`.  
```sorted```(collection[, key, reverse]) | Returns a sorted array.  
```find```(f, collection) | Returns the first element where f returns `True`.  
```group_by```(f, collection) | Group collection elements into a dict according to a lambda function.  
```fold```(f, zero, collection) | Reduces a collection with the given function f, provided the initial value zero.  
```array_scan```(f, zero, a) | Map each element of a to cumulative value of function f, with initial value zero.  
```reversed```(x) | Reverses the elements of a collection.  
```keyed_intersection```(*arrays, key) | Compute the intersection of sorted arrays on a given key.  
```keyed_union```(*arrays, key) | Compute the distinct union of sorted arrays on a given key.  
  
Numeric functions

```abs```(x) | Take the absolute value of a numeric value, array or ndarray.  
---|---  
```approx_equal```(x, y[, tolerance, absolute, ...]) | Tests whether two numbers are approximately equal.  
```bit_and```(x, y) | Bitwise and x and y.  
```bit_or```(x, y) | Bitwise or x and y.  
```bit_xor```(x, y) | Bitwise exclusive-or x and y.  
```bit_lshift```(x, y) | Bitwise left-shift x by y.  
```bit_rshift```(x, y[, logical]) | Bitwise right-shift x by y.  
```bit_not```(x) | Bitwise invert x.  
```bit_count```(x) | Count the number of 1s in the in the [two's complement](https://en.wikipedia.org/wiki/Two%27s_complement) binary representation of x.  
```exp```(x) |   
```expit```(x) |   
```is_nan```(x) |   
```is_finite```(x) |   
```is_infinite```(x) |   
```log```(x[, base]) | Take the logarithm of the x with base base.  
```log10```(x) |   
```logit```(x) |   
```sign```(x) | Returns the sign of a numeric value, array or ndarray.  
```sqrt```(x) |   
```int```(x) | Convert to a 32-bit integer expression.  
```int32```(x) | Convert to a 32-bit integer expression.  
```int64```(x) | Convert to a 64-bit integer expression.  
```float```(x) | Convert to a 64-bit floating point expression.  
```float32```(x) | Convert to a 32-bit floating point expression.  
```float64```(x) | Convert to a 64-bit floating point expression.  
```floor```(x) |   
```ceil```(x) |   
```uniroot```(f, min, max, *[, max_iter, epsilon, ...]) | Finds a root of the function f within the interval [min, max].  
  
Numeric collection functions

```min```(*exprs[, filter_missing]) | Returns the minimum element of a collection or of given numeric expressions.  
---|---  
```nanmin```(*exprs[, filter_missing]) | Returns the minimum value of a collection or of given arguments, excluding NaN.  
```max```(*exprs[, filter_missing]) | Returns the maximum element of a collection or of given numeric expressions.  
```nanmax```(*exprs[, filter_missing]) | Returns the maximum value of a collection or of given arguments, excluding NaN.  
```mean```(collection[, filter_missing]) | Returns the mean of all values in the collection.  
```median```(collection) | Returns the median value in the collection.  
```product```(collection[, filter_missing]) | Returns the product of values in the collection.  
```sum```(collection[, filter_missing]) | Returns the sum of values in the collection.  
```cumulative_sum```(a[, filter_missing]) | Returns an array of the cumulative sum of values in the array.  
```argmin```(array[, unique]) | Return the index of the minimum value in the array.  
```argmax```(array[, unique]) | Return the index of the maximum value in the array.  
```corr```(x, y) | Compute the [Pearson correlation coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient) between x and y.  
```binary_search```(array, elem) | Binary search array for the insertion point of elem.  
  
String functions

```format```(f, *args) | Returns a formatted string using a specified format string and arguments.  
---|---  
```json```(x) | Convert an expression to a JSON string expression.  
```parse_json```(x, dtype) | Convert a JSON string to a structured expression.  
```hamming```(s1, s2) | Returns the Hamming distance between the two strings.  
```delimit```(collection[, delimiter]) | Joins elements of collection into single string delimited by delimiter.  
```entropy```(s) | Returns the [Shannon entropy](https://en.wikipedia.org/wiki/Entropy_\(information_theory\)) of the character distribution defined by the string.  
```parse_int```(x) | Parse a string as a 32-bit integer.  
```parse_int32```(x) | Parse a string as a 32-bit integer.  
```parse_int64```(x) | Parse a string as a 64-bit integer.  
```parse_float```(x) | Parse a string as a 64-bit floating point number.  
```parse_float32```(x) | Parse a string as a 32-bit floating point number.  
```parse_float64```(x) | Parse a string as a 64-bit floating point number.  
  
Statistical functions

```chi_squared_test```(c1, c2, c3, c4) | Performs chi-squared test of independence on a 2x2 contingency table.  
---|---  
```fisher_exact_test```(c1, c2, c3, c4) | Calculates the p-value, odds ratio, and 95% confidence interval using Fisher's exact test for a 2x2 table.  
```contingency_table_test```(c1, c2, c3, c4, ...) | Performs chi-squared or Fisher's exact test of independence on a 2x2 contingency table.  
```cochran_mantel_haenszel_test```(a, b, c, d) | Perform the Cochran-Mantel-Haenszel test for association.  
```dbeta```(x, a, b) | Returns the probability density at x of a [beta distribution](https://en.wikipedia.org/wiki/Beta_distribution) with parameters a (alpha) and b (beta).  
```dpois```(x, lamb[, log_p]) | Compute the (log) probability density at x of a Poisson distribution with rate parameter lamb.  
```hardy_weinberg_test```(n_hom_ref, n_het, n_hom_var) | Performs test of Hardy-Weinberg equilibrium.  
```pchisqtail```(x, df[, ncp, lower_tail, log_p]) | Returns the probability under the right-tail starting at x for a chi-squared distribution with df degrees of freedom.  
```pnorm```(x[, mu, sigma, lower_tail, log_p]) | The cumulative probability function of a normal distribution with mean mu and standard deviation sigma.  
```ppois```(x, lamb[, lower_tail, log_p]) | The cumulative probability function of a Poisson distribution.  
```qchisqtail```(p, df[, ncp, lower_tail, log_p]) | The quantile function of a chi-squared distribution with df degrees of freedom, inverts ```pchisqtail()```.  
```qnorm```(p[, mu, sigma, lower_tail, log_p]) | The quantile function of a normal distribution with mean mu and standard deviation sigma, inverts ```pnorm()```.  
```qpois```(p, lamb[, lower_tail, log_p]) | The quantile function of a Poisson distribution with rate parameter lamb, inverts ```ppois()```.  
  
Randomness

```rand_bool```(p[, seed]) | Returns `True` with probability p.  
---|---  
```rand_beta```(a, b[, lower, upper, seed]) | Samples from a [beta distribution](https://en.wikipedia.org/wiki/Beta_distribution) with parameters a (alpha) and b (beta).  
```rand_cat```(prob[, seed]) | Samples from a [categorical distribution](https://en.wikipedia.org/wiki/Categorical_distribution).  
```rand_dirichlet```(a[, seed]) | Samples from a [Dirichlet distribution](https://en.wikipedia.org/wiki/Dirichlet_distribution).  
```rand_gamma```(shape, scale[, seed]) | Samples from a [gamma distribution](https://en.wikipedia.org/wiki/Gamma_distribution) with parameters shape and scale.  
```rand_norm```([mean, sd, seed, size]) | Samples from a normal distribution with mean mean and standard deviation sd.  
```rand_pois```(lamb[, seed]) | Samples from a [Poisson distribution](https://en.wikipedia.org/wiki/Poisson_distribution) with rate parameter lamb.  
```rand_unif```([lower, upper, seed, size]) | Samples from a uniform distribution within the interval [lower, upper].  
```rand_int32```(a[, b, seed]) | Samples from a uniform distribution of 32-bit integers.  
```rand_int64```([a, b, seed]) | Samples from a uniform distribution of 64-bit integers.  
```shuffle```(a[, seed]) | Randomly permute an array  
  
Genetics functions

```locus```(contig, pos[, reference_genome]) | Construct a locus expression from a chromosome and position.  
---|---  
```locus_from_global_position```(global_pos[, ...]) | Constructs a locus expression from a global position and a reference genome.  
```locus_interval```(contig, start, end[, ...]) | Construct a locus interval expression.  
```parse_locus```(s[, reference_genome]) | Construct a locus expression by parsing a string or string expression.  
```parse_variant```(s[, reference_genome]) | Construct a struct with a locus and alleles by parsing a string.  
```parse_locus_interval```(s[, reference_genome, ...]) | Construct a locus interval expression by parsing a string or string expression.  
```variant_str```(*args) | Create a variant colon-delimited string.  
```call```(*alleles[, phased]) | Construct a call expression.  
```unphased_diploid_gt_index_call```(gt_index) | Construct an unphased, diploid call from a genotype index.  
```parse_call```(s) | Construct a call expression by parsing a string or string expression.  
```downcode```(c, i) | Create a new call by setting all alleles other than i to ref  
```triangle```(n) | Returns the triangle number of n.  
```is_snp```(ref, alt) | Returns `True` if the alleles constitute a single nucleotide polymorphism.  
```is_mnp```(ref, alt) | Returns `True` if the alleles constitute a multiple nucleotide polymorphism.  
```is_transition```(ref, alt) | Returns `True` if the alleles constitute a transition.  
```is_transversion```(ref, alt) | Returns `True` if the alleles constitute a transversion.  
```is_insertion```(ref, alt) | Returns `True` if the alleles constitute an insertion.  
```is_deletion```(ref, alt) | Returns `True` if the alleles constitute a deletion.  
```is_indel```(ref, alt) | Returns `True` if the alleles constitute an insertion or deletion.  
```is_star```(ref, alt) | Returns `True` if the alleles constitute an upstream deletion.  
```is_complex```(ref, alt) | Returns `True` if the alleles constitute a complex polymorphism.  
```is_valid_contig```(contig[, reference_genome]) | Returns `True` if contig is a valid contig name in reference_genome.  
```is_valid_locus```(contig, position[, ...]) | Returns `True` if contig and position is a valid site in reference_genome.  
```contig_length```(contig[, reference_genome]) | Returns the length of contig in reference_genome.  
```allele_type```(ref, alt) | Returns the type of the polymorphism as a string.  
```pl_dosage```(pl) | Return expected genotype dosage from array of Phred-scaled genotype likelihoods with uniform prior.  
```gp_dosage```(gp) | Return expected genotype dosage from array of genotype probabilities.  
```get_sequence```(contig, position[, before, ...]) | Return the reference sequence at a given locus.  
```mendel_error_code```(locus, is_female, father, ...) | Compute a Mendelian violation code for genotypes.  
```liftover```(x, dest_reference_genome[, ...]) | Lift over coordinates to a different reference genome.  
```min_rep```(locus, alleles) | Computes the minimal representation of a (locus, alleles) polymorphism.  
```reverse_complement```(s[, rna]) | Reverses the string and translates base pairs into their complements .

### Core language functions

`literal`(x[, dtype]) | Captures and broadcasts a Python variable or object as an expression.  
---|---  
`cond`(condition, consequent, alternate[, ...]) | Deprecated in favor of `if_else()`.  
`if_else`(condition, consequent, alternate[, ...]) | Expression for an if/else statement; tests a condition and returns one of two options based on the result.  
`switch`(expr) | Build a conditional tree on the value of an expression.  
`case`([missing_false]) | Chain multiple if-else statements with a ```CaseBuilder```.  
`bind`(f, *exprs[, _ctx]) | Bind a temporary variable and use it in a function.  
`rbind`(*exprs[, _ctx]) | Bind a temporary variable and use it in a function.  
`missing`(t) | Creates an expression representing a missing value of a specified type.  
`null`(t) | Deprecated in favor of `missing()`.  
`str`(x) | Returns the string representation of x.  
`is_missing`(expression) | Returns `True` if the argument is missing.  
`is_defined`(expression) | Returns `True` if the argument is not missing.  
`coalesce`(*args) | Returns the first non-missing value of args.  
`or_else`(a, b) | If a is missing, return b.  
`or_missing`(predicate, value) | Returns value if predicate is `True`, otherwise returns missing.  
`range`(start[, stop, step]) | Returns an array of integers from start to stop by step.  
`query_table`(path, point_or_interval) | Query records from a table corresponding to a given point or range of keys.  
  
hail.expr.functions.literal(_x_ , _dtype
=None_)[[source]](../_modules/hail/expr/functions.html#literal)

    

Captures and broadcasts a Python variable or object as an expression.

Examples

    
    
    >>> table = hl.utils.range_table(8)
    >>> greetings = hl.literal({1: 'Good morning', 4: 'Good afternoon', 6 : 'Good evening'})
    >>> table.annotate(greeting = greetings.get(table.idx)).show()
    +-------+------------------+
    |   idx | greeting         |
    +-------+------------------+
    | int32 | str              |
    +-------+------------------+
    |     0 | NA               |
    |     1 | "Good morning"   |
    |     2 | NA               |
    |     3 | NA               |
    |     4 | "Good afternoon" |
    |     5 | NA               |
    |     6 | "Good evening"   |
    |     7 | NA               |
    +-------+------------------+
    

Notes

Use this function to capture large Python objects for use in expressions. This
function provides an alternative to adding an object as a global annotation on
a ```Table``` or
```MatrixTable```.

Parameters:

    

**x** – Object to capture and broadcast as an expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

hail.expr.functions.cond(_condition_ , _consequent_ , _alternate_ ,
_missing_false =False_)[[source]](../_modules/hail/expr/functions.html#cond)

    

Deprecated in favor of `if_else()`.

Expression for an if/else statement; tests a condition and returns one of two
options based on the result.

Examples

    
    
    >>> x = 5
    >>> hl.eval(hl.cond(x < 2, 'Hi', 'Bye'))
    'Bye'
    
    
    
    >>> a = hl.literal([1, 2, 3, 4])
    >>> hl.eval(hl.cond(hl.len(a) > 0, 2.0 * a, a / 2.0))
    [2.0, 4.0, 6.0, 8.0]
    

Notes

If condition evaluates to `True`, returns consequent. If condition evaluates
to `False`, returns alternate. If predicate is missing, returns missing.

Note

The type of consequent and alternate must be the same.

Parameters:

    

  * **condition** (```BooleanExpression```) – Condition to test.

  * **consequent** (```Expression```) – Branch to return if the condition is `True`.

  * **alternate** (```Expression```) – Branch to return if the condition is `False`.

  * **missing_false** (```bool```) – If `True`, treat missing condition as `False`.

See also

`case()`, `switch()`, `if_else()`

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – One of consequent, alternate, or missing, based on
condition.

hail.expr.functions.if_else(_condition_ , _consequent_ , _alternate_ ,
_missing_false
=False_)[[source]](../_modules/hail/expr/functions.html#if_else)

    

Expression for an if/else statement; tests a condition and returns one of two
options based on the result.

Examples

    
    
    >>> x = 5
    >>> hl.eval(hl.if_else(x < 2, 'Hi', 'Bye'))
    'Bye'
    
    
    
    >>> a = hl.literal([1, 2, 3, 4])
    >>> hl.eval(hl.if_else(hl.len(a) > 0, 2.0 * a, a / 2.0))
    [2.0, 4.0, 6.0, 8.0]
    

Notes

If condition evaluates to `True`, returns consequent. If condition evaluates
to `False`, returns alternate. If predicate is missing, returns missing.

Note

The type of consequent and alternate must be the same.

Parameters:

    

  * **condition** (```BooleanExpression```) – Condition to test.

  * **consequent** (```Expression```) – Branch to return if the condition is `True`.

  * **alternate** (```Expression```) – Branch to return if the condition is `False`.

  * **missing_false** (```bool```) – If `True`, treat missing condition as `False`.

See also

`case()`, `switch()`

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – One of consequent, alternate, or missing, based on
condition.

hail.expr.functions.switch(_expr_)[[source]](../_modules/hail/expr/functions.html#switch)

    

Build a conditional tree on the value of an expression.

Examples

    
    
    >>> csq = hl.literal('loss of function')
    >>> expr = (hl.switch(csq)
    ...                  .when('synonymous', 1)
    ...                  .when('SYN', 1)
    ...                  .when('missense', 2)
    ...                  .when('MIS', 2)
    ...                  .when('loss of function', 3)
    ...                  .when('LOF', 3)
    ...                  .or_missing())
    >>> hl.eval(expr)
    3
    

See also

[`SwitchBuilder`](hail.expr.builders.SwitchBuilder.html#hail.expr.builders.SwitchBuilder
"hail.expr.builders.SwitchBuilder"), `case()`, `cond()`

Parameters:

    

**expr** ([`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Value to match against.

Returns:

    

[`SwitchBuilder`](hail.expr.builders.SwitchBuilder.html#hail.expr.builders.SwitchBuilder
"hail.expr.builders.SwitchBuilder")

hail.expr.functions.case(_missing_false
=False_)[[source]](../_modules/hail/expr/functions.html#case)

    

Chain multiple if-else statements with a
[`CaseBuilder`](hail.expr.builders.CaseBuilder.html#hail.expr.builders.CaseBuilder
"hail.expr.builders.CaseBuilder").

Examples

    
    
    >>> x = hl.literal('foo bar baz')
    >>> expr = (hl.case()
    ...                  .when(x[:3] == 'FOO', 1)
    ...                  .when(hl.len(x) == 11, 2)
    ...                  .when(x == 'secret phrase', 3)
    ...                  .default(0))
    >>> hl.eval(expr)
    2
    

Parameters:

    

**missing_false** ([`bool`](constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")) – Treat missing predicates as `False`.

See also

[`CaseBuilder`](hail.expr.builders.CaseBuilder.html#hail.expr.builders.CaseBuilder
"hail.expr.builders.CaseBuilder"), `switch()`, `cond()`

Returns:

    

[`CaseBuilder`](hail.expr.builders.CaseBuilder.html#hail.expr.builders.CaseBuilder
"hail.expr.builders.CaseBuilder").

hail.expr.functions.bind(_f_ , _* exprs_, __ctx
=None_)[[source]](../_modules/hail/expr/functions.html#bind)

    

Bind a temporary variable and use it in a function.

Examples

    
    
    >>> hl.eval(hl.bind(lambda x: x + 1, 1))
    2
    

`bind()` also can take multiple arguments:

    
    
    >>> hl.eval(hl.bind(lambda x, y: x / y, x, x))
    1.0
    

Parameters:

    

  * **f** (function ( (args) -> ```Expression```)) – Function of exprs.

  * **exprs** (variable-length args of ```Expression```) – Expressions to bind.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Result of evaluating f with exprs as arguments.

hail.expr.functions.rbind(_* exprs_, __ctx
=None_)[[source]](../_modules/hail/expr/functions.html#rbind)

    

Bind a temporary variable and use it in a function.

This is `bind()` with flipped argument order.

Examples

    
    
    >>> hl.eval(hl.rbind(1, lambda x: x + 1))
    2
    

`rbind()` also can take multiple arguments:

    
    
    >>> hl.eval(hl.rbind(4.0, 2.0, lambda x, y: x / y))
    2.0
    

Parameters:

    

  * **exprs** (variable-length args of ```Expression```) – Expressions to bind.

  * **f** (function ( (args) -> ```Expression```)) – Function of exprs.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Result of evaluating f with exprs as arguments.

hail.expr.functions.missing(_t_)[[source]](../_modules/hail/expr/functions.html#missing)

    

Creates an expression representing a missing value of a specified type.

Examples

    
    
    >>> hl.eval(hl.missing(hl.tarray(hl.tstr)))
    None
    
    
    
    >>> hl.eval(hl.missing('array<str>'))
    None
    

Notes

This method is useful for constructing an expression that includes missing
values, since [`None`](https://docs.python.org/3.9/library/constants.html#None
"\(in Python v3.9\)") cannot be interpreted as an expression.

Parameters:

    

**t** (`str` or [`HailType`](../types.html#hail.expr.types.HailType
"hail.expr.types.HailType")) – Type of the missing expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – A missing expression of type t.

hail.expr.functions.null(_t_)[[source]](../_modules/hail/expr/functions.html#null)

    

Deprecated in favor of `missing()`.

Creates an expression representing a missing value of a specified type.

Examples

    
    
    >>> hl.eval(hl.null(hl.tarray(hl.tstr)))
    None
    
    
    
    >>> hl.eval(hl.null('array<str>'))
    None
    

Notes

This method is useful for constructing an expression that includes missing
values, since [`None`](https://docs.python.org/3.9/library/constants.html#None
"\(in Python v3.9\)") cannot be interpreted as an expression.

Parameters:

    

**t** (`str` or [`HailType`](../types.html#hail.expr.types.HailType
"hail.expr.types.HailType")) – Type of the missing expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – A missing expression of type t.

hail.expr.functions.str(_x_)[[source]](../_modules/hail/expr/functions.html#str)

    

Returns the string representation of x.

Examples

    
    
    >>> hl.eval(hl.str(hl.struct(a=5, b=7)))
    '{"a":5,"b":7}'
    

Parameters:

    

**x**

Returns:

    

[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

hail.expr.functions.is_missing(_expression_)[[source]](../_modules/hail/expr/functions.html#is_missing)

    

Returns `True` if the argument is missing.

Examples

    
    
    >>> hl.eval(hl.is_missing(5))
    False
    
    
    
    >>> hl.eval(hl.is_missing(hl.missing(hl.tstr)))
    True
    
    
    
    >>> hl.eval(hl.is_missing(hl.missing(hl.tbool) & True))
    True
    

Parameters:

    

**expression** – Expression to test.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if expression is missing, `False`
otherwise.

hail.expr.functions.is_defined(_expression_)[[source]](../_modules/hail/expr/functions.html#is_defined)

    

Returns `True` if the argument is not missing.

Examples

    
    
    >>> hl.eval(hl.is_defined(5))
    True
    
    
    
    >>> hl.eval(hl.is_defined(hl.missing(hl.tstr)))
    False
    
    
    
    >>> hl.eval(hl.is_defined(hl.missing(hl.tbool) & True))
    False
    

Parameters:

    

**expression** – Expression to test.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if expression is not missing, `False`
otherwise.

hail.expr.functions.coalesce(_*
args_)[[source]](../_modules/hail/expr/functions.html#coalesce)

    

Returns the first non-missing value of args.

Examples

    
    
    >>> x1 = hl.missing('int')
    >>> x2 = 2
    >>> hl.eval(hl.coalesce(x1, x2))
    2
    

Notes

All arguments must have the same type, or must be convertible to a common type
(all numeric, for instance).

See also

`or_else()`

Parameters:

    

**args** (variable-length args of
[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

hail.expr.functions.or_else(_a_ ,
_b_)[[source]](../_modules/hail/expr/functions.html#or_else)

    

If a is missing, return b.

Examples

    
    
    >>> hl.eval(hl.or_else(5, 7))
    5
    
    
    
    >>> hl.eval(hl.or_else(hl.missing(hl.tint32), 7))
    7
    

See also

`coalesce()`

Parameters:

    

  * **a** (```Expression```)

  * **b** (```Expression```)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

hail.expr.functions.or_missing(_predicate_ ,
_value_)[[source]](../_modules/hail/expr/functions.html#or_missing)

    

Returns value if predicate is `True`, otherwise returns missing.

Examples

    
    
    >>> hl.eval(hl.or_missing(True, 5))
    5
    
    
    
    >>> hl.eval(hl.or_missing(False, 5))
    None
    

Parameters:

    

  * **predicate** (```BooleanExpression```)

  * **value** (```Expression```) – Value to return if predicate is `True`.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – This expression has the same type as b.

hail.expr.functions.range(_start_ , _stop =None_, _step
=1_)[[source]](../_modules/hail/expr/functions.html#range)

    

Returns an array of integers from start to stop by step.

Examples

    
    
    >>> hl.eval(hl.range(10))
    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    
    
    
    >>> hl.eval(hl.range(3, 10))
    [3, 4, 5, 6, 7, 8, 9]
    
    
    
    >>> hl.eval(hl.range(0, 10, step=3))
    [0, 3, 6, 9]
    

Notes

The range includes start, but excludes stop.

If provided exactly one argument, the argument is interpreted as stop and
start is set to zero. This matches the behavior of Python’s `range`.

Parameters:

    

  * **start** (int or ```Expression``` of type ```tint32```) – Start of range.

  * **stop** (int or ```Expression``` of type ```tint32```) – End of range.

  * **step** (int or ```Expression``` of type ```tint32```) – Step of range.

Returns:

    

[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression")

hail.expr.functions.query_table(_path_ ,
_point_or_interval_)[[source]](../_modules/hail/expr/functions.html#query_table)

    

Query records from a table corresponding to a given point or range of keys.

Notes

This function does not dispatch to a distributed runtime; it can be used
inside already-distributed queries such as in
[`Table.annotate()`](../hail.Table.html#hail.Table.annotate
"hail.Table.annotate").

Warning

This function contains no safeguards against reading large amounts of data
using a single thread.

Parameters:

    

  * **path** (`str`) – Table path.

  * **point_or_interval** – Point or interval to query.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

```ConditionalBuilder``` | Base class for ```SwitchBuilder``` and ```CaseBuilder```  
---|---  
```CaseBuilder``` | Class for chaining multiple if-else statements.  
```SwitchBuilder``` | Class for generating conditional trees based on value of an expression.

---

### Constructor functions  
  
Constructors

`bool`(x) | Convert to a Boolean expression.  
---|---  
`float`(x) | Convert to a 64-bit floating point expression.  
`float32`(x) | Convert to a 32-bit floating point expression.  
`float64`(x) | Convert to a 64-bit floating point expression.  
`int`(x) | Convert to a 32-bit integer expression.  
`int32`(x) | Convert to a 32-bit integer expression.  
`int64`(x) | Convert to a 64-bit integer expression.  
`interval`(start, end[, includes_start, ...]) | Construct an interval expression.  
`struct`(**kwargs) | Construct a struct expression.  
`tuple`(iterable) | Construct a tuple expression.  
  
Collection constructors

`array`(collection) | Construct an array expression.  
---|---  
`empty_array`(t) | Returns an empty array of elements of a type t.  
`set`(collection) | Convert a set expression.  
`empty_set`(t) | Returns an empty set of elements of a type t.  
`dict`(collection) | Creates a dictionary.  
`empty_dict`(key_type, value_type) | Returns an empty dictionary with key type key_type and value type value_type.  
  
hail.expr.functions.bool(_x_)[[source]](../_modules/hail/expr/functions.html#bool)

    

Convert to a Boolean expression.

Examples

    
    
    >>> hl.eval(hl.bool('TRUE'))
    True
    
    
    
    >>> hl.eval(hl.bool(1.5))
    True
    

Notes

Numeric expressions return `True` if they are non-zero, and `False` if they
are zero.

Acceptable string values are: `'True'`, `'true'`, `'TRUE'`, `'False'`,
`'false'`, and `'FALSE'`.

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.float(_x_)[[source]](../_modules/hail/expr/functions.html#float)

    

Convert to a 64-bit floating point expression.

Examples

    
    
    >>> hl.eval(hl.float('1.1'))
    1.1
    
    
    
    >>> hl.eval(hl.float(1))
    1.0
    
    
    
    >>> hl.eval(hl.float(True))
    1.0
    

Note

Alias for `float64()`.

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.float32(_x_)[[source]](../_modules/hail/expr/functions.html#float32)

    

Convert to a 32-bit floating point expression.

Examples

    
    
    >>> hl.eval(hl.float32('1.1'))
    1.100000023841858
    
    
    
    >>> hl.eval(hl.float32(1))
    1.0
    
    
    
    >>> hl.eval(hl.float32(True))
    1.0
    

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
[`tfloat32`](../types.html#hail.expr.types.tfloat32
"hail.expr.types.tfloat32")

hail.expr.functions.float64(_x_)[[source]](../_modules/hail/expr/functions.html#float64)

    

Convert to a 64-bit floating point expression.

Examples

    
    
    >>> hl.eval(hl.float64('1.1'))
    1.1
    
    
    
    >>> hl.eval(hl.float64(1))
    1.0
    
    
    
    >>> hl.eval(hl.float64(True))
    1.0
    

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.int(_x_)[[source]](../_modules/hail/expr/functions.html#int)

    

Convert to a 32-bit integer expression.

Examples

    
    
    >>> hl.eval(hl.int('1'))
    1
    
    
    
    >>> hl.eval(hl.int(1.5))
    1
    
    
    
    >>> hl.eval(hl.int(True))
    1
    

Note

Alias for `int32()`.

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
```tint32```

hail.expr.functions.int32(_x_)[[source]](../_modules/hail/expr/functions.html#int32)

    

Convert to a 32-bit integer expression.

Examples

    
    
    >>> hl.eval(hl.int32('1'))
    1
    
    
    
    >>> hl.eval(hl.int32(1.5))
    1
    
    
    
    >>> hl.eval(hl.int32(True))
    1
    

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
```tint32```

hail.expr.functions.int64(_x_)[[source]](../_modules/hail/expr/functions.html#int64)

    

Convert to a 64-bit integer expression.

Examples

    
    
    >>> hl.eval(hl.int64('1'))
    1
    
    
    
    >>> hl.eval(hl.int64(1.5))
    1
    
    
    
    >>> hl.eval(hl.int64(True))
    1
    

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
```tint64```

hail.expr.functions.interval(_start_ , _end_ , _includes_start =True_,
_includes_end
=False_)[[source]](../_modules/hail/expr/functions.html#interval)

    

Construct an interval expression.

Examples

    
    
    >>> hl.eval(hl.interval(5, 100))
    Interval(start=5, end=100, includes_start=True, includes_end=False)
    
    
    
    >>> hl.eval(hl.interval(hl.locus("1", 100), hl.locus("1", 1000)))
        Interval(start=Locus(contig=1, position=100, reference_genome=GRCh37),
                 end=Locus(contig=1, position=1000, reference_genome=GRCh37),
                 includes_start=True,
                 includes_end=False)
    

Notes

start and end must have the same type.

Parameters:

    

  * **start** (```Expression```) – Start point.

  * **end** (```Expression```) – End point.

  * **includes_start** (```BooleanExpression```) – If `True`, interval includes start point.

  * **includes_end** (```BooleanExpression```) – If `True`, interval includes end point.

Returns:

    

[`IntervalExpression`](../hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression")

hail.expr.functions.struct(_**
kwargs_)[[source]](../_modules/hail/expr/functions.html#struct)

    

Construct a struct expression.

Examples

    
    
    >>> s = hl.struct(a=5, b='Foo')
    >>> hl.eval(s.a)
    5
    

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Keyword arguments as a struct.

hail.expr.functions.tuple(_iterable_)[[source]](../_modules/hail/expr/functions.html#tuple)

    

Construct a tuple expression.

Examples

    
    
    >>> t = hl.tuple([1, 2, '3'])
    >>> hl.eval(t)
    (1, 2, '3')
    
    
    
    >>> hl.eval(t[2])
    '3'
    

Parameters:

    

**iterable** (an iterable of
[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Tuple elements.

Returns:

    

[`TupleExpression`](../hail.expr.TupleExpression.html#hail.expr.TupleExpression
"hail.expr.TupleExpression")

hail.expr.functions.array(_collection_)[[source]](../_modules/hail/expr/functions.html#array)

    

Construct an array expression.

Examples

    
    
    >>> s = {'Bob', 'Charlie', 'Alice'}
    
    
    
    >>> hl.eval(hl.array(s))
    ['Alice', 'Bob', 'Charlie']
    

Parameters:

    

**collection**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") or
[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"))

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

hail.expr.functions.empty_array(_t_)[[source]](../_modules/hail/expr/functions.html#empty_array)

    

Returns an empty array of elements of a type t.

Examples

    
    
    >>> hl.eval(hl.empty_array(hl.tint32))
    []
    

Parameters:

    

**t** (```str``` or
[`HailType`](../types.html#hail.expr.types.HailType
"hail.expr.types.HailType")) – Type of the array elements.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

hail.expr.functions.set(_collection_)[[source]](../_modules/hail/expr/functions.html#set)

    

Convert a set expression.

Examples

    
    
    >>> s = hl.set(['Bob', 'Charlie', 'Alice', 'Bob', 'Bob'])
    >>> hl.eval(s) 
    {'Alice', 'Bob', 'Charlie'}
    

Returns:

    

[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") – Set of all unique elements.

hail.expr.functions.empty_set(_t_)[[source]](../_modules/hail/expr/functions.html#empty_set)

    

Returns an empty set of elements of a type t.

Examples

    
    
    >>> hl.eval(hl.empty_set(hl.tstr))
    set()
    

Parameters:

    

**t** (```str``` or
[`HailType`](../types.html#hail.expr.types.HailType
"hail.expr.types.HailType")) – Type of the set elements.

Returns:

    

[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression")

hail.expr.functions.dict(_collection_)[[source]](../_modules/hail/expr/functions.html#dict)

    

Creates a dictionary.

Examples

    
    
    >>> hl.eval(hl.dict([('foo', 1), ('bar', 2), ('baz', 3)]))
    {'bar': 2, 'baz': 3, 'foo': 1}
    

Notes

This method expects arrays or sets with elements of type
```ttuple``` with
2 fields. The first field of the tuple becomes the key, and the second field
becomes the value.

Parameters:

    

**collection**
([`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression") or
[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"))

Returns:

    

[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression")

hail.expr.functions.empty_dict(_key_type_ ,
_value_type_)[[source]](../_modules/hail/expr/functions.html#empty_dict)

    

Returns an empty dictionary with key type key_type and value type value_type.

Examples

    
    
    >>> hl.eval(hl.empty_dict(hl.tstr, hl.tint32))
    {}
    

Parameters:

    

  * **key_type** (```str``` or ```HailType```) – Type of the keys.

  * **value_type** (```str``` or ```HailType```) – Type of the values.

Returns:

    

[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression")

---

---


### Collection functions

Collection constructors

```dict```(collection) | Creates a dictionary.  
---|---  
```empty_dict```(key_type, value_type) | Returns an empty dictionary with key type key_type and value type value_type.  
```array```(collection) | Construct an array expression.  
```empty_array```(t) | Returns an empty array of elements of a type t.  
```set```(collection) | Convert a set expression.  
```empty_set```(t) | Returns an empty set of elements of a type t.  
  
Collection functions

`len`(x) | Returns the size of a collection or string.  
---|---  
`map`(f, *collections) | Transform each element of a collection.  
`flatmap`(f, collection) | Map each element of the collection to a new collection, and flatten the results.  
`starmap`(f, collection) | Transform each element of a collection of tuples.  
`zip`(*arrays[, fill_missing]) | Zip together arrays into a single array.  
`enumerate`(a[, start, index_first]) | Returns an array of (index, element) tuples.  
`zip_with_index`(a[, index_first]) | Deprecated in favor of `enumerate()`.  
`flatten`(collection) | Flatten a nested collection by concatenating sub-collections.  
`any`(*args) | Check for any `True` in boolean expressions or collections of booleans.  
`all`(*args) | Check for all `True` in boolean expressions or collections of booleans.  
`filter`(f, collection) | Returns a new collection containing elements where f returns `True`.  
`sorted`(collection[, key, reverse]) | Returns a sorted array.  
`find`(f, collection) | Returns the first element where f returns `True`.  
`group_by`(f, collection) | Group collection elements into a dict according to a lambda function.  
`fold`(f, zero, collection) | Reduces a collection with the given function f, provided the initial value zero.  
`array_scan`(f, zero, a) | Map each element of a to cumulative value of function f, with initial value zero.  
`reversed`(x) | Reverses the elements of a collection.  
`keyed_intersection`(*arrays, key) | Compute the intersection of sorted arrays on a given key.  
`keyed_union`(*arrays, key) | Compute the distinct union of sorted arrays on a given key.  
  
hail.expr.functions.len(_x_)[[source]](../_modules/hail/expr/functions.html#len)

    

Returns the size of a collection or string.

Examples

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> s = {1, 3, 5, 6, 7, 9}
    
    
    
    >>> hl.eval(hl.len(a))
    4
    
    
    
    >>> hl.eval(hl.len(s))
    6
    
    
    
    >>> hl.eval(hl.len("12345"))
    5
    

Parameters:

    

**x**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") or
[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")) – String or collection expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```

hail.expr.functions.map(_f_ , _*
collections_)[[source]](../_modules/hail/expr/functions.html#map)

    

Transform each element of a collection.

Examples

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> b = [2, 4, 6, 8]
    
    
    
    >>> hl.eval(hl.map(lambda x: hl.len(x), a))
    [3, 5, 5, 3]
    
    
    
    >>> hl.eval(hl.map(lambda s, n: hl.len(s) + n, a, b))
    [5, 9, 11, 11]
    

Parameters:

    

  * **f** (function ( (*arg) -> ```Expression```)) – Function to transform each element of the collection.

  * ***collections** (```ArrayExpression``` or ```SetExpression```) – A single collection expression or multiple array expressions.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). – Collection where each element has been
transformed by f.

hail.expr.functions.flatmap(_f_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#flatmap)

    

Map each element of the collection to a new collection, and flatten the
results.

Examples

    
    
    >>> a = [[0, 1], [1, 2], [4, 5, 6, 7]]
    
    
    
    >>> hl.eval(hl.flatmap(lambda x: x[1:], a))
    [1, 2, 5, 6, 7]
    

Parameters:

    

  * **f** (function ( (arg) -> ```CollectionExpression```)) – Function from the element type of the collection to the type of the collection. For instance, flatmap on a `set<str>` should take a `str` and return a `set`.

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression")

hail.expr.functions.starmap(_f_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#starmap)

    

Transform each element of a collection of tuples.

Examples

    
    
    >>> a = [(1, 5), (3, 2), (7, 8)]
    
    
    
    >>> hl.eval(hl.starmap(lambda x, y: hl.if_else(x < y, x, y), a))
    [1, 2, 7]
    

Parameters:

    

  * **f** (function ( (*args) -> ```Expression```)) – Function to transform each element of the collection.

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). – Collection where each element has been
transformed by f.

hail.expr.functions.zip(_* arrays_, _fill_missing
=False_)[[source]](../_modules/hail/expr/functions.html#zip)

    

Zip together arrays into a single array.

Examples

    
    
    >>> hl.eval(hl.zip([1, 2, 3], [4, 5, 6]))
    [(1, 4), (2, 5), (3, 6)]
    

If the arrays are different lengths, the behavior is decided by the
fill_missing parameter.

    
    
    >>> hl.eval(hl.zip([1], [10, 20], [100, 200, 300]))
    [(1, 10, 100)]
    
    
    
    >>> hl.eval(hl.zip([1], [10, 20], [100, 200, 300], fill_missing=True))
    [(1, 10, 100), (None, 20, 200), (None, None, 300)]
    

Notes

The element type of the resulting array is a
```ttuple``` with
a field for each array.

Parameters:

    

  * **arrays** (: variable-length args of ```ArrayExpression```) – Array expressions.

  * **fill_missing** (```bool```) – If `False`, return an array with length equal to the shortest length of the arrays. If `True`, return an array equal to the longest length of the arrays, by extending the shorter arrays with missing values.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

hail.expr.functions.enumerate(_a_ , _start =0_, _*_ , _index_first
=True_)[[source]](../_modules/hail/expr/functions.html#enumerate)

    

Returns an array of (index, element) tuples.

Examples

    
    
    >>> hl.eval(hl.enumerate(['A', 'B', 'C']))
    [(0, 'A'), (1, 'B'), (2, 'C')]
    
    
    
    >>> hl.eval(hl.enumerate(['A', 'B', 'C'], start=3))
    [(3, 'A'), (4, 'B'), (5, 'C')]
    
    
    
    >>> hl.eval(hl.enumerate(['A', 'B', 'C'], index_first=False))
    [('A', 0), ('B', 1), ('C', 2)]
    

Parameters:

    

  * **a** (```ArrayExpression```)

  * **start** (```Int32Expression```) – The index value from which the counter is started, 0 by default.

  * **index_first** (```bool```) – If `True`, the index is the first value of the element tuples. If `False`, the index is the second value.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of (index, element) or (element, index)
tuples.

hail.expr.functions.zip_with_index(_a_ , _index_first
=True_)[[source]](../_modules/hail/expr/functions.html#zip_with_index)

    

Deprecated in favor of `enumerate()`.

Returns an array of (index, element) tuples.

Examples

    
    
    >>> hl.eval(hl.zip_with_index(['A', 'B', 'C']))
    [(0, 'A'), (1, 'B'), (2, 'C')]
    
    
    
    >>> hl.eval(hl.zip_with_index(['A', 'B', 'C'], index_first=False))
    [('A', 0), ('B', 1), ('C', 2)]
    

Parameters:

    

  * **a** (```ArrayExpression```)

  * **index_first** (```bool```) – If `True`, the index is the first value of the element tuples. If `False`, the index is the second value.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of (index, element) or (element, index)
tuples.

hail.expr.functions.flatten(_collection_)[[source]](../_modules/hail/expr/functions.html#flatten)

    

Flatten a nested collection by concatenating sub-collections.

Examples

    
    
    >>> a = [[1, 2], [2, 3]]
    
    
    
    >>> hl.eval(hl.flatten(a))
    [1, 2, 2, 3]
    

Parameters:

    

**collection**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression")) – Collection with element type
```tarray``` or
```tset```.

Returns:

    

**collection**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"))

hail.expr.functions.any(_*
args_)[[source]](../_modules/hail/expr/functions.html#any)

    

Check for any `True` in boolean expressions or collections of booleans.

`any()` comes in three forms:

  1. `hl.any(boolean, ...)`. Is at least one argument `True`?

  2. `hl.any(collection)`. Is at least one element of this collection `True`?

  3. `hl.any(function, collection)`. Does `function` return `True` for at least one value in this collection?

Examples

The first form:

    
    
    >>> hl.eval(hl.any())
    False
    
    
    
    >>> hl.eval(hl.any(True))
    True
    
    
    
    >>> hl.eval(hl.any(False))
    False
    
    
    
    >>> hl.eval(hl.any(False, False, True, False))
    True
    

The second form:

    
    
    >>> hl.eval(hl.any([False, True, False]))
    True
    
    
    
    >>> hl.eval(hl.any([False, False, False]))
    False
    

The third form:

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> s = {1, 3, 5, 6, 7, 9}
    
    
    
    >>> hl.eval(hl.any(lambda x: x[-1] == 'x', a))
    True
    
    
    
    >>> hl.eval(hl.any(lambda x: x % 4 == 0, s))
    False
    

Notes

`any()` returns `False` when given an empty array or empty argument list.

hail.expr.functions.all(_*
args_)[[source]](../_modules/hail/expr/functions.html#all)

    

Check for all `True` in boolean expressions or collections of booleans.

`all()` comes in three forms:

  1. `hl.all(boolean, ...)`. Are all arguments `True`?

  2. `hl.all(collection)`. Are all elements of the collection `True`?

  3. `hl.all(function, collection)`. Does `function` return `True` for all values in this collection?

Examples

The first form:

    
    
    >>> hl.eval(hl.all())
    True
    
    
    
    >>> hl.eval(hl.all(True))
    True
    
    
    
    >>> hl.eval(hl.all(False))
    False
    
    
    
    >>> hl.eval(hl.all(True, True, True))
    True
    
    
    
    >>> hl.eval(hl.all(False, False, True, False))
    False
    

The second form:

    
    
    >>> hl.eval(hl.all([False, True, False]))
    False
    
    
    
    >>> hl.eval(hl.all([True, True, True]))
    True
    

The third form:

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> s = {1, 3, 5, 6, 7, 9}
    
    
    
    >>> hl.eval(hl.all(lambda x: hl.len(x) > 3, a))
    False
    
    
    
    >>> hl.eval(hl.all(lambda x: x < 10, s))
    True
    

Notes

`all()` returns `True` when given an empty array or empty argument list.

hail.expr.functions.filter(_f_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#filter)

    

Returns a new collection containing elements where f returns `True`.

Examples

    
    
    >>> a = [1, 2, 3, 4]
    >>> s = {'Alice', 'Bob', 'Charlie'}
    
    
    
    >>> hl.eval(hl.filter(lambda x: x % 2 == 0, a))
    [2, 4]
    
    
    
    >>> hl.eval(hl.filter(lambda x: ~(x[-1] == 'e'), s))
    {'Bob'}
    

Notes

Returns a same-type expression; evaluated on a
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"), returns a
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). Evaluated on an
[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression"), returns an
[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

Parameters:

    

  * **f** (function ( (arg) -> ```BooleanExpression```)) – Function to evaluate for each element of the collection. Must return a ```BooleanExpression```.

  * **collection** (```ArrayExpression``` or ```SetExpression```.) – Array or set expression to filter.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") – Expression of the same type as collection.

hail.expr.functions.sorted(_collection_ , _key =None_, _reverse
=False_)[[source]](../_modules/hail/expr/functions.html#sorted)

    

Returns a sorted array.

Examples

    
    
    >>> a = ['Charlie', 'Alice', 'Bob']
    
    
    
    >>> hl.eval(hl.sorted(a))
    ['Alice', 'Bob', 'Charlie']
    
    
    
    >>> hl.eval(hl.sorted(a, reverse=True))
    ['Charlie', 'Bob', 'Alice']
    
    
    
    >>> hl.eval(hl.sorted(a, key=lambda x: hl.len(x)))
    ['Bob', 'Alice', 'Charlie']
    

Notes

The ordered types are [`tstr`](../types.html#hail.expr.types.tstr
"hail.expr.types.tstr") and numeric types.

Parameters:

    

  * **collection** (```ArrayExpression``` or ```SetExpression``` or ```DictExpression```) – Collection to sort.

  * **key** (function ( (arg) -> ```Expression```), optional) – Function to evaluate for each element to compute sort key.

  * **reverse** (```BooleanExpression```) – Sort in descending order.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Sorted array.

hail.expr.functions.find(_f_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#find)

    

Returns the first element where f returns `True`.

Examples

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> s = {1, 3, 5, 6, 7, 9}
    
    
    
    >>> hl.eval(hl.find(lambda x: x[-1] == 'x', a))
    'fox'
    
    
    
    >>> hl.eval(hl.find(lambda x: x % 4 == 0, s))
    None
    

Notes

If f returns `False` for every element, then the result is missing.

Sets are unordered. If collection is of type
```tset```, then the
element returned comes from no guaranteed ordering.

Parameters:

    

  * **f** (function ( (arg) -> ```BooleanExpression```)) – Function to evaluate for each element of the collection. Must return a ```BooleanExpression```.

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Expression whose type is the element type of the
collection.

hail.expr.functions.group_by(_f_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#group_by)

    

Group collection elements into a dict according to a lambda function.

Examples

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> hl.eval(hl.group_by(lambda x: hl.len(x), a))
    {3: ['The', 'fox'], 5: ['quick', 'brown']}
    

Parameters:

    

  * **f** (function ( (arg) -> ```Expression```)) – Function to evaluate for each element of the collection to produce a key for the resulting dictionary.

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression.

Returns:

    

[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"). – Dictionary keyed by results of f.

hail.expr.functions.fold(_f_ , _zero_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#fold)

    

Reduces a collection with the given function f, provided the initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.fold(lambda i, j: i + j, 0, a))
    3
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

  * **collection** (```ArrayExpression``` or ```SetExpression```)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

hail.expr.functions.array_scan(_f_ , _zero_ ,
_a_)[[source]](../_modules/hail/expr/functions.html#array_scan)

    

Map each element of a to cumulative value of function f, with initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.array_scan(lambda i, j: i + j, 0, a))
    [0, 0, 1, 3]
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

  * **a** (```ArrayExpression```)

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

hail.expr.functions.reversed(_x_)[[source]](../_modules/hail/expr/functions.html#reversed)

    

Reverses the elements of a collection.

Examples

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> hl.eval(hl.reversed(a))
    ['fox', 'brown', 'quick', 'The']
    

Parameters:

    

**x**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")) – Array or string expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

hail.expr.functions.keyed_intersection(_* arrays_,
_key_)[[source]](../_modules/hail/expr/functions.html#keyed_intersection)

    

Compute the intersection of sorted arrays on a given key.

Requires sorted arrays with distinct keys.

Warning

Experimental. Does not support downstream randomness.

Parameters:

    

  * **arrays**

  * **key**

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

hail.expr.functions.keyed_union(_* arrays_,
_key_)[[source]](../_modules/hail/expr/functions.html#keyed_union)

    

Compute the distinct union of sorted arrays on a given key.

Requires sorted arrays with distinct keys.

Warning

Experimental. Does not support downstream randomness.

Parameters:

    

  * **exprs**

  * **key**

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

---



### Numeric functions

Numeric functions

`abs`(x) | Take the absolute value of a numeric value, array or ndarray.  
---|---  
`approx_equal`(x, y[, tolerance, absolute, ...]) | Tests whether two numbers are approximately equal.  
`bit_and`(x, y) | Bitwise and x and y.  
`bit_or`(x, y) | Bitwise or x and y.  
`bit_xor`(x, y) | Bitwise exclusive-or x and y.  
`bit_lshift`(x, y) | Bitwise left-shift x by y.  
`bit_rshift`(x, y[, logical]) | Bitwise right-shift x by y.  
`bit_not`(x) | Bitwise invert x.  
`bit_count`(x) | Count the number of 1s in the in the [two's complement](https://en.wikipedia.org/wiki/Two%27s_complement) binary representation of x.  
`exp`(x) |   
`expit`(x) |   
`is_nan`(x) |   
`is_finite`(x) |   
`is_infinite`(x) |   
`log`(x[, base]) | Take the logarithm of the x with base base.  
`log10`(x) |   
`logit`(x) |   
`sign`(x) | Returns the sign of a numeric value, array or ndarray.  
`sqrt`(x) |   
```int```(x) | Convert to a 32-bit integer expression.  
```int32```(x) | Convert to a 32-bit integer expression.  
```int64```(x) | Convert to a 64-bit integer expression.  
```float```(x) | Convert to a 64-bit floating point expression.  
```float32```(x) | Convert to a 32-bit floating point expression.  
```float64```(x) | Convert to a 64-bit floating point expression.  
`floor`(x) |   
`ceil`(x) |   
`uniroot`(f, min, max, *[, max_iter, epsilon, ...]) | Finds a root of the function f within the interval [min, max].  
  
Numeric collection functions

`min`(*exprs[, filter_missing]) | Returns the minimum element of a collection or of given numeric expressions.  
---|---  
`nanmin`(*exprs[, filter_missing]) | Returns the minimum value of a collection or of given arguments, excluding NaN.  
`max`(*exprs[, filter_missing]) | Returns the maximum element of a collection or of given numeric expressions.  
`nanmax`(*exprs[, filter_missing]) | Returns the maximum value of a collection or of given arguments, excluding NaN.  
`mean`(collection[, filter_missing]) | Returns the mean of all values in the collection.  
`median`(collection) | Returns the median value in the collection.  
`product`(collection[, filter_missing]) | Returns the product of values in the collection.  
`sum`(collection[, filter_missing]) | Returns the sum of values in the collection.  
`cumulative_sum`(a[, filter_missing]) | Returns an array of the cumulative sum of values in the array.  
`argmin`(array[, unique]) | Return the index of the minimum value in the array.  
`argmax`(array[, unique]) | Return the index of the maximum value in the array.  
`corr`(x, y) | Compute the [Pearson correlation coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient) between x and y.  
`binary_search`(array, elem) | Binary search array for the insertion point of elem.  
  
hail.expr.functions.abs(_x_)[[source]](../_modules/hail/expr/functions.html#abs)

    

Take the absolute value of a numeric value, array or ndarray.

Examples

    
    
    >>> hl.eval(hl.abs(-5))
    5
    
    
    
    >>> hl.eval(hl.abs([1.0, -2.5, -5.1]))
    [1.0, 2.5, 5.1]
    

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression"),
[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") or
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression"),
[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") or
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression").

hail.expr.functions.approx_equal(_x_ , _y_ , _tolerance =1e-06_, _absolute
=False_, _nan_same
=False_)[[source]](../_modules/hail/expr/functions.html#approx_equal)

    

Tests whether two numbers are approximately equal.

Examples

    
    
    >>> hl.eval(hl.approx_equal(0.25, 0.2500001))
    True
    
    
    
    >>> hl.eval(hl.approx_equal(0.25, 0.251, tolerance=1e-3, absolute=True))
    False
    

Parameters:

    

  * **x** (```NumericExpression```)

  * **y** (```NumericExpression```)

  * **tolerance** (```NumericExpression```)

  * **absolute** (```BooleanExpression```) – If True, compute `abs(x - y) <= tolerance`. Otherwise, compute `abs(x - y) <= max(tolerance * max(abs(x), abs(y)), 2 ** -1022)`.

  * **nan_same** (```BooleanExpression```) – If True, then `NaN == NaN` will evaluate to True. Otherwise, it will return False.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.bit_and(_x_ ,
_y_)[[source]](../_modules/hail/expr/functions.html#bit_and)

    

Bitwise and x and y.

Examples

    
    
    >>> hl.eval(hl.bit_and(5, 3))
    1
    

Notes

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

  * **x** (```Int32Expression``` or ```Int64Expression```)

  * **y** (```Int32Expression``` or ```Int64Expression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_or(_x_ ,
_y_)[[source]](../_modules/hail/expr/functions.html#bit_or)

    

Bitwise or x and y.

Examples

    
    
    >>> hl.eval(hl.bit_or(5, 3))
    7
    

Notes

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

  * **x** (```Int32Expression``` or ```Int64Expression```)

  * **y** (```Int32Expression``` or ```Int64Expression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_xor(_x_ ,
_y_)[[source]](../_modules/hail/expr/functions.html#bit_xor)

    

Bitwise exclusive-or x and y.

Examples

    
    
    >>> hl.eval(hl.bit_xor(5, 3))
    6
    

Notes

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

  * **x** (```Int32Expression``` or ```Int64Expression```)

  * **y** (```Int32Expression``` or ```Int64Expression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_lshift(_x_ ,
_y_)[[source]](../_modules/hail/expr/functions.html#bit_lshift)

    

Bitwise left-shift x by y.

Examples

    
    
    >>> hl.eval(hl.bit_lshift(5, 3))
    40
    
    
    
    >>> hl.eval(hl.bit_lshift(1, 8))
    256
    

Unlike Python, Hail integers are fixed-size (32 or 64 bits), and bits extended
beyond will be ignored:

    
    
    >>> hl.eval(hl.bit_lshift(1, 31))
    -2147483648
    
    
    
    >>> hl.eval(hl.bit_lshift(1, 32))
    0
    
    
    
    >>> hl.eval(hl.bit_lshift(hl.int64(1), 32))
    4294967296
    
    
    
    >>> hl.eval(hl.bit_lshift(hl.int64(1), 64))
    0
    

Notes

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

  * **x** (```Int32Expression``` or ```Int64Expression```)

  * **y** (```Int32Expression``` or ```Int64Expression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_rshift(_x_ , _y_ , _logical
=False_)[[source]](../_modules/hail/expr/functions.html#bit_rshift)

    

Bitwise right-shift x by y.

Examples

    
    
    >>> hl.eval(hl.bit_rshift(256, 3))
    32
    

With `logical=False` (default), the sign is preserved:

    
    
    >>> hl.eval(hl.bit_rshift(-1, 1))
    -1
    

With `logical=True`, the sign bit is treated as any other:

    
    
    >>> hl.eval(hl.bit_rshift(-1, 1, logical=True))
    2147483647
    

Notes

If logical is `False`, then the shift is a sign-preserving right shift. If
logical is `True`, then the shift is logical, with the sign bit treated as any
other bit.

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

  * **x** (```Int32Expression``` or ```Int64Expression```)

  * **y** (```Int32Expression``` or ```Int64Expression```)

  * **logical** (```bool```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_not(_x_)[[source]](../_modules/hail/expr/functions.html#bit_not)

    

Bitwise invert x.

Examples

    
    
    >>> hl.eval(hl.bit_not(0))
    -1
    

Notes

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

**x**
([`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression"))

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_count(_x_)[[source]](../_modules/hail/expr/functions.html#bit_count)

    

Count the number of 1s in the in the [two’s
complement](https://en.wikipedia.org/wiki/Two%27s_complement) binary
representation of x.

Examples

The binary representation of 7 is 111, so:

    
    
    >>> hl.eval(hl.bit_count(7))
    3
    

Parameters:

    

**x**
([`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression"))

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

hail.expr.functions.exp(_x_)[[source]](../_modules/hail/expr/functions.html#exp)

    

hail.expr.functions.expit(_x_)[[source]](../_modules/hail/expr/functions.html#expit)

    

hail.expr.functions.is_nan(_x_)[[source]](../_modules/hail/expr/functions.html#is_nan)

    

hail.expr.functions.is_finite(_x_)[[source]](../_modules/hail/expr/functions.html#is_finite)

    

hail.expr.functions.is_infinite(_x_)[[source]](../_modules/hail/expr/functions.html#is_infinite)

    

hail.expr.functions.log(_x_ , _base
=None_)[[source]](../_modules/hail/expr/functions.html#log)

    

Take the logarithm of the x with base base.

Examples

    
    
    >>> hl.eval(hl.log(10))
    2.302585092994046
    
    
    
    >>> hl.eval(hl.log(10, 10))
    1.0
    
    
    
    >>> hl.eval(hl.log(1024, 2))
    10.0
    

Notes

If the base argument is not supplied, then the natural logarithm is used.

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```)

  * **base** (float or ```Expression``` of type ```tfloat64```)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.log10(_x_)[[source]](../_modules/hail/expr/functions.html#log10)

    

hail.expr.functions.logit(_x_)[[source]](../_modules/hail/expr/functions.html#logit)

    

hail.expr.functions.floor(_x_)[[source]](../_modules/hail/expr/functions.html#floor)

    

hail.expr.functions.ceil(_x_)[[source]](../_modules/hail/expr/functions.html#ceil)

    

hail.expr.functions.sqrt(_x_)[[source]](../_modules/hail/expr/functions.html#sqrt)

    

hail.expr.functions.sign(_x_)[[source]](../_modules/hail/expr/functions.html#sign)

    

Returns the sign of a numeric value, array or ndarray.

Examples

    
    
    >>> hl.eval(hl.sign(-1.23))
    -1.0
    
    
    
    >>> hl.eval(hl.sign([-4, 0, 5]))
    [-1, 0, 1]
    
    
    
    >>> hl.eval(hl.sign([0.0, 3.14]))
    [0.0, 1.0]
    
    
    
    >>> hl.eval(hl.sign(float('nan')))
    nan
    

Notes

The sign function preserves type and maps `nan` to `nan`.

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression"),
[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") or
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression"),
[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") or
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression").

hail.expr.functions.min(_* exprs_, _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#min)

    

Returns the minimum element of a collection or of given numeric expressions.

Examples

Take the minimum value of an array:

    
    
    >>> hl.eval(hl.min([1, 3, 5, 6, 7, 9]))
    1
    

Take the minimum value of arguments:

    
    
    >>> hl.eval(hl.min(1, 50, 2))
    1
    

Notes

Like the Python builtin `min` function, this function can either take a single
iterable expression (an array or set of numeric elements), or variable-length
arguments of numeric expressions.

Note

If filter_missing is `True`, then the result is the minimum of non-missing
arguments or elements. If filter_missing is `False`, then any missing argument
or element causes the result to be missing.

If any element or argument is NaN, then the result is NaN.

See also

`nanmin()`, `max()`, `nanmax()`

Parameters:

    

  * **exprs** (```ArrayExpression``` or ```SetExpression``` or varargs of ```NumericExpression```) – Single numeric array or set, or multiple numeric values.

  * **filter_missing** (```bool```) – Remove missing arguments/elements before computing minimum.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.nanmin(_* exprs_, _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#nanmin)

    

Returns the minimum value of a collection or of given arguments, excluding
NaN.

Examples

Compute the minimum value of an array:

    
    
    >>> hl.eval(hl.nanmin([1.1, 50.1, float('nan')]))
    1.1
    

Take the minimum value of arguments:

    
    
    >>> hl.eval(hl.nanmin(1.1, 50.1, float('nan')))
    1.1
    

Notes

Like the Python builtin `min` function, this function can either take a single
iterable expression (an array or set of numeric elements), or variable-length
arguments of numeric expressions.

Note

If filter_missing is `True`, then the result is the minimum of non-missing
arguments or elements. If filter_missing is `False`, then any missing argument
or element causes the result to be missing.

NaN arguments / array elements are ignored; the minimum value of NaN and any
non-NaN value x is x.

See also

`min()`, `max()`, `nanmax()`

Parameters:

    

  * **exprs** (```ArrayExpression``` or ```SetExpression``` or varargs of ```NumericExpression```) – Single numeric array or set, or multiple numeric values.

  * **filter_missing** (```bool```) – Remove missing arguments/elements before computing minimum.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.max(_* exprs_, _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#max)

    

Returns the maximum element of a collection or of given numeric expressions.

Examples

Take the maximum value of an array:

    
    
    >>> hl.eval(hl.max([1, 3, 5, 6, 7, 9]))
    9
    

Take the maximum value of values:

    
    
    >>> hl.eval(hl.max(1, 50, 2))
    50
    

Notes

Like the Python builtin `max` function, this function can either take a single
iterable expression (an array or set of numeric elements), or variable-length
arguments of numeric expressions.

Note

If filter_missing is `True`, then the result is the maximum of non-missing
arguments or elements. If filter_missing is `False`, then any missing argument
or element causes the result to be missing.

If any element or argument is NaN, then the result is NaN.

See also

`nanmax()`, `min()`, `nanmin()`

Parameters:

    

  * **exprs** (```ArrayExpression``` or ```SetExpression``` or varargs of ```NumericExpression```) – Single numeric array or set, or multiple numeric values.

  * **filter_missing** (```bool```) – Remove missing arguments/elements before computing maximum.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.nanmax(_* exprs_, _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#nanmax)

    

Returns the maximum value of a collection or of given arguments, excluding
NaN.

Examples

Compute the maximum value of an array:

    
    
    >>> hl.eval(hl.nanmax([1.1, 50.1, float('nan')]))
    50.1
    

Take the maximum value of arguments:

    
    
    >>> hl.eval(hl.nanmax(1.1, 50.1, float('nan')))
    50.1
    

Notes

Like the Python builtin `max` function, this function can either take a single
iterable expression (an array or set of numeric elements), or variable-length
arguments of numeric expressions.

Note

If filter_missing is `True`, then the result is the maximum of non-missing
arguments or elements. If filter_missing is `False`, then any missing argument
or element causes the result to be missing.

NaN arguments / array elements are ignored; the maximum value of NaN and any
non-NaN value x is x.

See also

`max()`, `min()`, `nanmin()`

Parameters:

    

  * **exprs** (```ArrayExpression``` or ```SetExpression``` or varargs of ```NumericExpression```) – Single numeric array or set, or multiple numeric values.

  * **filter_missing** (```bool```) – Remove missing arguments/elements before computing maximum.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.mean(_collection_ , _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#mean)

    

Returns the mean of all values in the collection.

Examples

    
    
    >>> a = [1, 3, 5, 6, 7, 9]
    
    
    
    >>> hl.eval(hl.mean(a))
    5.166666666666667
    

Note

Missing elements are ignored if filter_missing is `True`. If filter_missing is
`False`, then any missing element causes the result to be missing.

Parameters:

    

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression with numeric element type.

  * **filter_missing** (```bool```) – Remove missing elements from the collection before computing product.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.median(_collection_)[[source]](../_modules/hail/expr/functions.html#median)

    

Returns the median value in the collection.

Examples

    
    
    >>> a = [1, 3, 5, 6, 7, 9]
    
    
    
    >>> hl.eval(hl.median(a))
    5
    

Note

Missing elements are ignored.

Parameters:

    

**collection**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression")) – Collection expression with numeric element type.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.product(_collection_ , _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#product)

    

Returns the product of values in the collection.

Examples

    
    
    >>> a = [1, 3, 5, 6, 7, 9]
    
    
    
    >>> hl.eval(hl.product(a))
    5670
    

Note

Missing elements are ignored if filter_missing is `True`. If filter_missing is
`False`, then any missing element causes the result to be missing.

Parameters:

    

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression with numeric element type.

  * **filter_missing** (```bool```) – Remove missing elements from the collection before computing product.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.sum(_collection_ , _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#sum)

    

Returns the sum of values in the collection.

Examples

    
    
    >>> a = [1, 3, 5, 6, 7, 9]
    
    
    
    >>> hl.eval(hl.sum(a))
    31
    

Note

Missing elements are ignored if filter_missing is `True`. If filter_missing is
`False`, then any missing element causes the result to be missing.

Parameters:

    

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression with numeric element type.

  * **filter_missing** (```bool```) – Remove missing elements from the collection before computing product.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.cumulative_sum(_a_ , _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#cumulative_sum)

    

Returns an array of the cumulative sum of values in the array.

Examples

    
    
    >>> a = [1, 3, 5, 6, 7, 9]
    
    
    
    >>> hl.eval(hl.cumulative_sum(a))
    [1, 4, 9, 15, 22, 31]
    

Note

Missing elements are ignored if filter_missing is `True`. If filter_missing is
`False`, then any missing element causes the result to be missing.

Parameters:

    

  * **a** (```ArrayNumericExpression```) – Array expression with numeric element type.

  * **filter_missing** (```bool```) – Remove missing elements from the collection before computing product.

Returns:

    

[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression")

hail.expr.functions.argmin(_array_ , _unique
=False_)[[source]](../_modules/hail/expr/functions.html#argmin)

    

Return the index of the minimum value in the array.

Examples

    
    
    >>> hl.eval(hl.argmin([0.2, 0.3, 0.6]))
    0
    
    
    
    >>> hl.eval(hl.argmin([0.4, 0.2, 0.2]))
    1
    
    
    
    >>> hl.eval(hl.argmin([0.4, 0.2, 0.2], unique=True))
    None
    

Notes

Returns the index of the minimum value in the array.

If two or more elements are tied for minimum, then the unique parameter will
determine the result. If unique is `False`, then the first index will be
returned. If unique is `True`, then the result is missing.

If the array is empty, then the result is missing.

Note

Missing elements are ignored.

Parameters:

    

  * **array** (```ArrayNumericExpression```)

  * **unique** (_bool_)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```

hail.expr.functions.argmax(_array_ , _unique
=False_)[[source]](../_modules/hail/expr/functions.html#argmax)

    

Return the index of the maximum value in the array.

Examples

    
    
    >>> hl.eval(hl.argmax([0.2, 0.2, 0.6]))
    2
    
    
    
    >>> hl.eval(hl.argmax([0.4, 0.4, 0.2]))
    0
    
    
    
    >>> hl.eval(hl.argmax([0.4, 0.4, 0.2], unique=True))
    None
    

Notes

Returns the index of the maximum value in the array.

If two or more elements are tied for maximum, then the unique parameter will
determine the result. If unique is `False`, then the first index will be
returned. If unique is `True`, then the result is missing.

If the array is empty, then the result is missing.

Note

Missing elements are ignored.

Parameters:

    

  * **array** (```ArrayNumericExpression```)

  * **unique** (_bool_)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```

hail.expr.functions.corr(_x_ ,
_y_)[[source]](../_modules/hail/expr/functions.html#corr)

    

Compute the [Pearson correlation
coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)
between x and y.

Examples

    
    
    >>> hl.eval(hl.corr([1, 2, 4], [2, 3, 1]))
    -0.6546536707079772
    

Notes

Only indices where both x and y are non-missing will be included in the
calculation.

If x and y have length zero, then the result is missing.

Parameters:

    

  * **x** (```Expression``` of type `array<tfloat64>`)

  * **y** (```Expression``` of type `array<tfloat64>`)

Returns:

    

[`Float64Expression`](../hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression")

hail.expr.functions.uniroot(_f_ , _min_ , _max_ , _*_ , _max_iter =1000_,
_epsilon =2.220446049250313e-16_, _tolerance
=0.0001220703_)[[source]](../_modules/hail/expr/functions.html#uniroot)

    

Finds a root of the function f within the interval [min, max].

Examples

    
    
    >>> hl.eval(hl.uniroot(lambda x: x - 1, -5, 5))
    1.0
    

Notes

f(min) and f(max) must not have the same sign.

If no root can be found, the result of this call will be NA (missing).

`uniroot()` returns an estimate for a root with accuracy 4 * epsilon * abs(x)
+ tolerance.

4*EPSILON*abs(x) + tol

Parameters:

    

  * **f** (function ( (arg) -> ```Float64Expression```)) – Must return a ```Float64Expression```.

  * **min** (```Float64Expression```)

  * **max** (```Float64Expression```)

  * **max_iter** (int) – The maximum number of iterations before giving up.

  * **epsilon** (float) – The scaling factor in the accuracy of the root found.

  * **tolerance** (float) – The constant factor in approximate accuracy of the root found.

Returns:

    

[`Float64Expression`](../hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression") – The root of the function f.

hail.expr.functions.binary_search(_array_ ,
_elem_)[[source]](../_modules/hail/expr/functions.html#binary_search)

    

Binary search array for the insertion point of elem.

Parameters:

    

  * **array** (```Expression``` of type ```tarray```)

  * **elem** (```Expression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

Notes

This function assumes that array is sorted in ascending order, and does not
perform any sortedness check. Missing values sort last.

The returned index is the lower bound on the insertion point of elem into the
ordered array, or the index of the first element in array not smaller than
elem. This is a value between 0 and the length of array, inclusive (if all
elements in array are smaller than elem, the returned value is the length of
array or the index of the first missing value, if one exists).

If either elem or array is missing, the result is missing.

Examples

    
    
    >>> a = hl.array([0, 2, 4, 8])
    
    
    
    >>> hl.eval(hl.binary_search(a, -1))
    0
    
    
    
    >>> hl.eval(hl.binary_search(a, 1))
    1
    
    
    
    >>> hl.eval(hl.binary_search(a, 10))
    4

---

### String functions

*   `format(f, *args)`: Returns a formatted string using a specified format string and arguments.
*   `json(x)`: Convert an expression to a JSON string expression.
*   `parse_json(x, dtype)`: Convert a JSON string to a structured expression.
*   `hamming(s1, s2)`: Returns the Hamming distance between the two strings.
*   `delimit(collection[, delimiter])`: Joins elements of collection into single string delimited by delimiter.
*   `entropy(s)`: Returns the Shannon entropy of the character distribution defined by the string.
*   `parse_int(x)`: Parse a string as a 32-bit integer.
*   `parse_int32(x)`: Parse a string as a 32-bit integer.
*   `parse_int64(x)`: Parse a string as a 64-bit integer.
*   `parse_float(x)`: Parse a string as a 64-bit floating point number.
*   `parse_float32(x)`: Parse a string as a 32-bit floating point number.
*   `parse_float64(x)`: Parse a string as a 64-bit floating point number.

---

**`hail.expr.functions.format(f, *args)`**

Returns a formatted string using a specified format string and arguments.

**Examples**
```python
>>> hl.eval(hl.format('%.3e', 0.09345332))
'9.345e-02'
>>> hl.eval(hl.format('%.4f', hl.missing(hl.tfloat64)))
'null'
>>> hl.eval(hl.format('%s %s %s', 'hello', hl.tuple([3, hl.locus('1', 2453)]), True))
'hello (3, 1:2453) true'
```

**Notes**

See the Java documentation for valid format specifiers and arguments.

Missing values are printed as 'null' except when using the format flags ‘b’ and ‘B’ (printed as 'false' instead).

**Parameters**
*   **`f`** (`StringExpression`): Java format string.
*   **`args`** (variable-length arguments of `Expression`): Arguments to format.

**Returns**
`StringExpression`

---

**`hail.expr.functions.json(x)`**

Convert an expression to a JSON string expression.

**Examples**
```python
>>> hl.eval(hl.json([1,2,3,4,5]))
'[1,2,3,4,5]'
>>> hl.eval(hl.json(hl.struct(a='Hello', b=0.12345, c=[1,2], d={'hi', 'bye'})))
'{"a":"Hello","b":0.12345,"c":[1,2],"d":["bye","hi"]}'
```

**Parameters**
*   **`x`**: Expression to convert.

**Returns**
`StringExpression`: String expression with JSON representation of x.

---

**`hail.expr.functions.parse_json(x, dtype)`**

Convert a JSON string to a structured expression.

**Examples**
```python
>>> json_str = '{"a": 5, "b": 1.1, "c": "foo"}'
>>> parsed = hl.parse_json(json_str, dtype='struct{a: int32, b: float64, c: str}')
>>> hl.eval(parsed.a)
5
```

**Parameters**
*   **`x`** (`StringExpression`): JSON string.
*   **`dtype`**: Type of value to parse.

**Returns**
`Expression`

---

**`hail.expr.functions.hamming(s1, s2)`**

Returns the Hamming distance between the two strings.

**Examples**
```python
>>> hl.eval(hl.hamming('ATATA', 'ATGCA'))
2
>>> hl.eval(hl.hamming('abcdefg', 'zzcdefz'))
3
```

**Notes**

This method will fail if the two strings have different length.

**Parameters**
*   **`s1`** (`StringExpression`): First string.
*   **`s2`** (`StringExpression`): Second string.

**Returns**
`Expression` of type `tint32`

---

**`hail.expr.functions.delimit(collection, delimiter=',')`**

Joins elements of collection into single string delimited by delimiter.

**Examples**
```python
>>> a = ['Bob', 'Charlie', 'Alice', 'Bob', 'Bob']
>>> hl.eval(hl.delimit(a))
'Bob,Charlie,Alice,Bob,Bob'
```

**Notes**

If the element type of `collection` is not `tstr`, then the `str()` function will be called on each element before joining with the delimiter.

**Parameters**
*   **`collection`** (`ArrayExpression` or `SetExpression`): Collection.
*   **`delimiter`** (`str` or `StringExpression`): Field delimiter.

**Returns**
`StringExpression`: Joined string expression.

---

**`hail.expr.functions.entropy(s)`**

Returns the Shannon entropy of the character distribution defined by the string.

**Examples**
```python
>>> hl.eval(hl.entropy('ac'))
1.0
>>> hl.eval(hl.entropy('accctg'))
1.7924812503605778
```

**Notes**

For a string of length *n* with *k* unique characters {*c*₁, ..., *c*ₖ}, let *p*ᵢ be the probability that a randomly chosen character is *c*ᵢ, e.g. the number of instances of *c*ᵢ divided by *n*. Then the base-2 Shannon entropy is given by:

$H = - \sum_{i=1}^{k} p_i \log_2(p_i)$

**Parameters**
*   **`s`** (`StringExpression`)

**Returns**
`Expression` of type `tfloat64`

---

**`hail.expr.functions.parse_int(x)`**

Parse a string as a 32-bit integer.

**Examples**
```python
>>> hl.eval(hl.parse_int('154'))
154
>>> hl.eval(hl.parse_int('15.4'))
None
>>> hl.eval(hl.parse_int('asdf'))
None
```

**Notes**

If the input is an invalid integer, then result of this call will be missing.

**Parameters**
*   **`x`** (`StringExpression`)

**Returns**
`NumericExpression` of type `tint32`

---

**`hail.expr.functions.parse_int32(x)`**

Parse a string as a 32-bit integer.

**Examples**
```python
>>> hl.eval(hl.parse_int32('154'))
154
>>> hl.eval(hl.parse_int32('15.4'))
None
>>> hl.eval(hl.parse_int32('asdf'))
None
```

**Notes**

If the input is an invalid integer, then result of this call will be missing.

**Parameters**
*   **`x`** (`StringExpression`)

**Returns**
`NumericExpression` of type `tint32`

---

**`hail.expr.functions.parse_int64(x)`**

Parse a string as a 64-bit integer.

**Examples**
```python
>>> hl.eval(hl.parse_int64('154'))
154
>>> hl.eval(hl.parse_int64('15.4'))
None
>>> hl.eval(hl.parse_int64('asdf'))
None
```

**Notes**

If the input is an invalid integer, then result of this call will be missing.

**Parameters**
*   **`x`** (`StringExpression`)

**Returns**
`NumericExpression` of type `tint64`

---

**`hail.expr.functions.parse_float(x)`**

Parse a string as a 64-bit floating point number.

**Examples**
```python
>>> hl.eval(hl.parse_float('1.1'))
1.1
>>> hl.eval(hl.parse_float('asdf'))
None
```

**Notes**

If the input is an invalid floating point number, then result of this call will be missing.

**Parameters**
*   **`x`** (`StringExpression`)

**Returns**
`NumericExpression` of type `tfloat64`

---

**`hail.expr.functions.parse_float32(x)`**

Parse a string as a 32-bit floating point number.

**Examples**
```python
>>> hl.eval(hl.parse_float32('1.1'))
1.100000023841858
>>> hl.eval(hl.parse_float32('asdf'))
None
```

**Notes**

If the input is an invalid floating point number, then result of this call will be missing.

**Parameters**
*   **`x`** (`StringExpression`)

**Returns**
`NumericExpression` of type `tfloat32`

---

**`hail.expr.functions.parse_float64(x)`**

Parse a string as a 64-bit floating point number.

**Examples**
```python
>>> hl.eval(hl.parse_float64('1.1'))
1.1
>>> hl.eval(hl.parse_float64('asdf'))
None
```

**Notes**

If the input is an invalid floating point number, then result of this call will be missing.

**Parameters**
*   **`x`** (`StringExpression`)

**Returns**
`NumericExpression` of type `tfloat64`

### Statistical functions

`chi_squared_test`(c1, c2, c3, c4) | Performs chi-squared test of independence on a 2x2 contingency table.  
---|---  
`fisher_exact_test`(c1, c2, c3, c4) | Calculates the p-value, odds ratio, and 95% confidence interval using Fisher's exact test for a 2x2 table.  
`contingency_table_test`(c1, c2, c3, c4, ...) | Performs chi-squared or Fisher's exact test of independence on a 2x2 contingency table.  
`cochran_mantel_haenszel_test`(a, b, c, d) | Perform the Cochran-Mantel-Haenszel test for association.  
`dbeta`(x, a, b) | Returns the probability density at x of a [beta distribution](https://en.wikipedia.org/wiki/Beta_distribution) with parameters a (alpha) and b (beta).  
`dchisq`(x, df[, ncp, log_p]) | Compute the probability density at x of a chi-squared distribution with df degrees of freedom.  
`dnorm`(x[, mu, sigma, log_p]) | Compute the probability density at x of a normal distribution with mean mu and standard deviation sigma.  
`dpois`(x, lamb[, log_p]) | Compute the (log) probability density at x of a Poisson distribution with rate parameter lamb.  
`hardy_weinberg_test`(n_hom_ref, n_het, n_hom_var) | Performs test of Hardy-Weinberg equilibrium.  
`binom_test`(x, n, p, alternative) | Performs a binomial test on p given x successes in n trials.  
`pchisqtail`(x, df[, ncp, lower_tail, log_p]) | Returns the probability under the right-tail starting at x for a chi-squared distribution with df degrees of freedom.  
`pgenchisq`(x, w, k, lam, mu, sigma, *[, ...]) | The cumulative probability function of a [generalized chi-squared distribution](https://en.wikipedia.org/wiki/Generalized_chi-squared_distribution).  
`pnorm`(x[, mu, sigma, lower_tail, log_p]) | The cumulative probability function of a normal distribution with mean mu and standard deviation sigma.  
`pT`(x, n[, lower_tail, log_p]) | The cumulative probability function of a [t-distribution](https://en.wikipedia.org/wiki/Student%27s_t-distribution) with n degrees of freedom.  
`pF`(x, df1, df2[, lower_tail, log_p]) | The cumulative probability function of a [F-distribution](https://en.wikipedia.org/wiki/F-distribution) with parameters df1 and df2.  
`ppois`(x, lamb[, lower_tail, log_p]) | The cumulative probability function of a Poisson distribution.  
`qchisqtail`(p, df[, ncp, lower_tail, log_p]) | The quantile function of a chi-squared distribution with df degrees of freedom, inverts `pchisqtail()`.  
`qnorm`(p[, mu, sigma, lower_tail, log_p]) | The quantile function of a normal distribution with mean mu and standard deviation sigma, inverts `pnorm()`.  
`qpois`(p, lamb[, lower_tail, log_p]) | The quantile function of a Poisson distribution with rate parameter lamb, inverts `ppois()`.  
  
hail.expr.functions.chi_squared_test(_c1_ , _c2_ , _c3_ ,
_c4_)[[source]](../_modules/hail/expr/functions.html#chi_squared_test)

    

Performs chi-squared test of independence on a 2x2 contingency table.

Examples

    
    
    >>> hl.eval(hl.chi_squared_test(10, 10, 10, 10))
    Struct(p_value=1.0, odds_ratio=1.0)
    
    
    
    >>> hl.eval(hl.chi_squared_test(51, 43, 22, 92))
    Struct(p_value=1.4626257805267089e-07, odds_ratio=4.959830866807611)
    

Notes

The odds ratio is given by `(c1 / c2) / (c3 / c4)`.

Returned fields may be `nan` or `inf`.

Parameters:

    

  * **c1** (int or ```Expression``` of type ```tint32```) – Value for cell 1.

  * **c2** (int or ```Expression``` of type ```tint32```) – Value for cell 2.

  * **c3** (int or ```Expression``` of type ```tint32```) – Value for cell 3.

  * **c4** (int or ```Expression``` of type ```tint32```) – Value for cell 4.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A
```tstruct```
expression with two fields, p_value
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")) and odds_ratio
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")).

hail.expr.functions.fisher_exact_test(_c1_ , _c2_ , _c3_ ,
_c4_)[[source]](../_modules/hail/expr/functions.html#fisher_exact_test)

    

Calculates the p-value, odds ratio, and 95% confidence interval using Fisher’s
exact test for a 2x2 table.

Examples

    
    
    >>> hl.eval(hl.fisher_exact_test(10, 10, 10, 10))
    Struct(p_value=1.0000000000000002, odds_ratio=1.0,
           ci_95_lower=0.24385796914260355, ci_95_upper=4.100747675033819)
    
    
    
    >>> hl.eval(hl.fisher_exact_test(51, 43, 22, 92))
    Struct(p_value=2.1564999740157304e-07, odds_ratio=4.918058171469967,
           ci_95_lower=2.5659373368248444, ci_95_upper=9.677929632035475)
    

Notes

This method is identical to the version implemented in
[R](https://stat.ethz.ch/R-manual/R-devel/library/stats/html/fisher.test.html)
with default parameters (two-sided, alpha = 0.05, null hypothesis that the
odds ratio equals 1).

Returned fields may be `nan` or `inf`.

Parameters:

    

  * **c1** (int or ```Expression``` of type ```tint32```) – Value for cell 1.

  * **c2** (int or ```Expression``` of type ```tint32```) – Value for cell 2.

  * **c3** (int or ```Expression``` of type ```tint32```) – Value for cell 3.

  * **c4** (int or ```Expression``` of type ```tint32```) – Value for cell 4.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A
```tstruct```
expression with four fields, p_value
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")), odds_ratio
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")), ci_95_lower (:py:data:.tfloat64`), and
ci_95_upper ([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")).

hail.expr.functions.contingency_table_test(_c1_ , _c2_ , _c3_ , _c4_ ,
_min_cell_count_)[[source]](../_modules/hail/expr/functions.html#contingency_table_test)

    

Performs chi-squared or Fisher’s exact test of independence on a 2x2
contingency table.

Examples

    
    
    >>> hl.eval(hl.contingency_table_test(51, 43, 22, 92, min_cell_count=22))
    Struct(p_value=1.4626257805267089e-07, odds_ratio=4.959830866807611)
    
    
    
    >>> hl.eval(hl.contingency_table_test(51, 43, 22, 92, min_cell_count=23))
    Struct(p_value=2.1564999740157304e-07, odds_ratio=4.918058171469967)
    

Notes

If all cell counts are at least min_cell_count, the chi-squared test is used.
Otherwise, Fisher’s exact test is used.

Returned fields may be `nan` or `inf`.

Parameters:

    

  * **c1** (int or ```Expression``` of type ```tint32```) – Value for cell 1.

  * **c2** (int or ```Expression``` of type ```tint32```) – Value for cell 2.

  * **c3** (int or ```Expression``` of type ```tint32```) – Value for cell 3.

  * **c4** (int or ```Expression``` of type ```tint32```) – Value for cell 4.

  * **min_cell_count** (int or ```Expression``` of type ```tint32```) – Minimum count in every cell to use the chi-squared test.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A
```tstruct```
expression with two fields, p_value
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")) and odds_ratio
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")).

hail.expr.functions.cochran_mantel_haenszel_test(_a_ , _b_ , _c_ ,
_d_)[[source]](../_modules/hail/expr/functions.html#cochran_mantel_haenszel_test)

    

Perform the Cochran-Mantel-Haenszel test for association.

Examples

    
    
    >>> a = [56, 61, 73, 71]
    >>> b = [69, 257, 65, 48]
    >>> c = [40, 57, 71, 55]
    >>> d = [77, 301, 79, 48]
    >>> hl.eval(hl.cochran_mantel_haenszel_test(a, b, c, d))
    Struct(test_statistic=5.0496881823306765, p_value=0.024630370456863417)
    
    
    
    >>> mt = ds.filter_rows(mt.locus == hl.Locus(20, 10633237))
    >>> mt.count_rows()
    1
    >>> a, b, c, d = mt.aggregate_entries(
    ...     hl.tuple([
    ...         hl.array([hl.agg.count_where(mt.GT.is_non_ref() & mt.pheno.is_case & mt.pheno.is_female), hl.agg.count_where(mt.GT.is_non_ref() & mt.pheno.is_case & ~mt.pheno.is_female)]),
    ...         hl.array([hl.agg.count_where(mt.GT.is_non_ref() & ~mt.pheno.is_case & mt.pheno.is_female), hl.agg.count_where(mt.GT.is_non_ref() & ~mt.pheno.is_case & ~mt.pheno.is_female)]),
    ...         hl.array([hl.agg.count_where(~mt.GT.is_non_ref() & mt.pheno.is_case & mt.pheno.is_female), hl.agg.count_where(~mt.GT.is_non_ref() & mt.pheno.is_case & ~mt.pheno.is_female)]),
    ...         hl.array([hl.agg.count_where(~mt.GT.is_non_ref() & ~mt.pheno.is_case & mt.pheno.is_female), hl.agg.count_where(~mt.GT.is_non_ref() & ~mt.pheno.is_case & ~mt.pheno.is_female)])
    ...     ])
    ... )
    >>> hl.eval(hl.cochran_mantel_haenszel_test(a, b, c, d))
    Struct(test_statistic=0.2188830334629822, p_value=0.6398923118508772)
    

Notes

See the [Wikipedia
article](https://en.m.wikipedia.org/wiki/Cochran%E2%80%93Mantel%E2%80%93Haenszel_statistics)
for more details.

Parameters:

    

  * **a** (```ArrayExpression``` of type ```tint64```) – Values for the upper-left cell in the contingency tables.

  * **b** (```ArrayExpression``` of type ```tint64```) – Values for the upper-right cell in the contingency tables.

  * **c** (```ArrayExpression``` of type ```tint64```) – Values for the lower-left cell in the contingency tables.

  * **d** (```ArrayExpression``` of type ```tint64```) – Values for the lower-right cell in the contingency tables.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A
```tstruct```
expression with two fields, test_statistic
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")) and p_value
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")).

hail.expr.functions.dbeta(_x_ , _a_ ,
_b_)[[source]](../_modules/hail/expr/functions.html#dbeta)

    

Returns the probability density at x of a [beta
distribution](https://en.wikipedia.org/wiki/Beta_distribution) with parameters
a (alpha) and b (beta).

Examples

    
    
    >>> hl.eval(hl.dbeta(.2, 5, 20))
    4.900377563180943
    

Parameters:

    

  * **x** (```float``` or ```Expression``` of type ```tfloat64```) – Point in [0,1] at which to sample. If a < 1 then x must be positive. If b < 1 then x must be less than 1.

  * **a** (```float``` or ```Expression``` of type ```tfloat64```) – The alpha parameter in the beta distribution. The result is undefined for non-positive a.

  * **b** (```float``` or ```Expression``` of type ```tfloat64```) – The beta parameter in the beta distribution. The result is undefined for non-positive b.

Returns:

    

[`Float64Expression`](../hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression")

hail.expr.functions.dchisq(_x_ , _df_ , _ncp =None_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#dchisq)

    

Compute the probability density at x of a chi-squared distribution with df
degrees of freedom.

Examples

    
    
    >>> hl.eval(hl.dchisq(1, 2))
    0.3032653298563167
    
    
    
    >>> hl.eval(hl.dchisq(1, 2, ncp=2))
    0.17472016746112667
    
    
    
    >>> hl.eval(hl.dchisq(1, 2, log_p=True))
    -1.1931471805599454
    

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```) – Non-negative number at which to compute the probability density.

  * **df** (float or ```Expression``` of type ```tfloat64```) – Degrees of freedom.

  * **ncp** (float or ```Expression``` of type ```tfloat64```) – Noncentrality parameter, defaults to 0 if unspecified.

  * **log_p** (bool or ```BooleanExpression```) – If `True`, the natural logarithm of the probability density is returned.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – The probability density.

hail.expr.functions.dnorm(_x_ , _mu =0_, _sigma =1_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#dnorm)

    

Compute the probability density at x of a normal distribution with mean mu and
standard deviation sigma. Returns density of standard normal distribution by
default.

Examples

    
    
    >>> hl.eval(hl.dnorm(1))
    0.24197072451914337
    
    
    
    >>> hl.eval(hl.dnorm(1, mu=1, sigma=2))
    0.19947114020071635
    
    
    
    >>> hl.eval(hl.dnorm(1, log_p=True))
    -1.4189385332046727
    

Parameters:

    

  * **x** (```float``` or ```Expression``` of type ```tfloat64```) – Real number at which to compute the probability density.

  * **mu** (float or ```Expression``` of type ```tfloat64```) – Mean (default = 0).

  * **sigma** (float or ```Expression``` of type ```tfloat64```) – Standard deviation (default = 1).

  * **log_p** (```bool``` or ```BooleanExpression```) – If `True`, the natural logarithm of the probability density is returned.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – The probability density.

hail.expr.functions.dpois(_x_ , _lamb_ , _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#dpois)

    

Compute the (log) probability density at x of a Poisson distribution with rate
parameter lamb.

Examples

    
    
    >>> hl.eval(hl.dpois(5, 3))
    0.10081881344492458
    

Parameters:

    

  * **x** (```float``` or ```Expression``` of type ```tfloat64```) – Non-negative number at which to compute the probability density.

  * **lamb** (```float``` or ```Expression``` of type ```tfloat64```) – Poisson rate parameter. Must be non-negative.

  * **log_p** (```bool``` or ```BooleanExpression```) – If `True`, the natural logarithm of the probability density is returned.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – The (log) probability density.

hail.expr.functions.hardy_weinberg_test(_n_hom_ref_ , _n_het_ , _n_hom_var_ ,
_one_sided
=False_)[[source]](../_modules/hail/expr/functions.html#hardy_weinberg_test)

    

Performs test of Hardy-Weinberg equilibrium.

Examples

    
    
    >>> hl.eval(hl.hardy_weinberg_test(250, 500, 250))
    Struct(het_freq_hwe=0.5002501250625313, p_value=0.9747844394217698)
    
    
    
    >>> hl.eval(hl.hardy_weinberg_test(37, 200, 85))
    Struct(het_freq_hwe=0.48964964307448583, p_value=1.1337210383168987e-06)
    

Notes

By default, this method performs a two-sided exact test with mid-p-value
correction of [Hardy-Weinberg
equilibrium](https://en.wikipedia.org/wiki/Hardy%E2%80%93Weinberg_principle)
via an efficient implementation of the [Levene-Haldane
distribution](../_static/LeveneHaldane.pdf), which models the number of
heterozygous individuals under equilibrium.

The mean of this distribution is `(n_ref * n_var) / (2n - 1)`, where `n_ref =
2*n_hom_ref + n_het` is the number of reference alleles, `n_var = 2*n_hom_var
+ n_het` is the number of variant alleles, and `n = n_hom_ref + n_het +
n_hom_var` is the number of individuals. So the expected frequency of
heterozygotes under equilibrium, het_freq_hwe, is this mean divided by `n`.

To perform one-sided exact test of excess heterozygosity with mid-p-value
correction instead, set one_sided=True and the p-value returned will be from
the one-sided exact test.

Parameters:

    

  * **n_hom_ref** (int or ```Expression``` of type ```tint32```) – Number of homozygous reference genotypes.

  * **n_het** (int or ```Expression``` of type ```tint32```) – Number of heterozygous genotypes.

  * **n_hom_var** (int or ```Expression``` of type ```tint32```) – Number of homozygous variant genotypes.

  * **one_sided** (```bool```) – `False` by default. When `True`, perform one-sided test for excess heterozygosity.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A struct expression with two fields,
het_freq_hwe ([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")) and p_value
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")).

hail.expr.functions.binom_test(_x_ , _n_ , _p_ ,
_alternative_)[[source]](../_modules/hail/expr/functions.html#binom_test)

    

Performs a binomial test on p given x successes in n trials.

Returns the p-value from the [exact binomial
test](https://en.wikipedia.org/wiki/Binomial_test) of the null hypothesis that
success has probability p, given x successes in n trials.

The alternatives are interpreted as follows: \- `'less'`: a one-tailed test of
the significance of x or fewer successes, \- `'greater'`: a one-tailed test of
the significance of x or more successes, and \- `'two-sided'`: a two-tailed
test of the significance of x or any equivalent or more unlikely outcome.

Examples

All the examples below use a fair coin as the null hypothesis. Zero is
interpreted as tail and one as heads.

Test if a coin is biased towards heads or tails after observing two heads out
of ten flips:

    
    
    >>> hl.eval(hl.binom_test(2, 10, 0.5, 'two-sided'))
    0.10937499999999994
    

Test if a coin is biased towards tails after observing four heads out of ten
flips:

    
    
    >>> hl.eval(hl.binom_test(4, 10, 0.5, 'less'))
    0.3769531250000001
    

Test if a coin is biased towards heads after observing thirty-two heads out of
fifty flips:

    
    
    >>> hl.eval(hl.binom_test(32, 50, 0.5, 'greater'))
    0.03245432353613613
    

Parameters:

    

  * **x** (int or ```Expression``` of type ```tint32```) – Number of successes.

  * **n** (int or ```Expression``` of type ```tint32```) – Number of trials.

  * **p** (float or ```Expression``` of type ```tfloat64```) – Probability of success, between 0 and 1.

  * **alternative** – : One of, “two-sided”, “greater”, “less”, (deprecated: “two.sided”).

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – p-value.

hail.expr.functions.pchisqtail(_x_ , _df_ , _ncp =None_, _lower_tail =False_,
_log_p =False_)[[source]](../_modules/hail/expr/functions.html#pchisqtail)

    

Returns the probability under the right-tail starting at x for a chi-squared
distribution with df degrees of freedom.

Examples

    
    
    >>> hl.eval(hl.pchisqtail(5, 1))
    0.025347318677468304
    
    
    
    >>> hl.eval(hl.pchisqtail(5, 1, ncp=2))
    0.20571085634347097
    
    
    
    >>> hl.eval(hl.pchisqtail(5, 1, lower_tail=True))
    0.9746526813225317
    
    
    
    >>> hl.eval(hl.pchisqtail(5, 1, log_p=True))
    -3.6750823266311876
    

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```) – The value at which to evaluate the CDF.

  * **df** (float or ```Expression``` of type ```tfloat64```) – Degrees of freedom.

  * **ncp** (float or ```Expression``` of type ```tfloat64```) – Noncentrality parameter, defaults to 0 if unspecified.

  * **lower_tail** (bool or ```BooleanExpression```) – If `True`, compute the probability of an outcome at or below x, otherwise greater than x.

  * **log_p** (bool or ```BooleanExpression```) – Return the natural logarithm of the probability.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.pgenchisq(_x_ , _w_ , _k_ , _lam_ , _mu_ , _sigma_ , _*_ ,
_max_iterations =None_, _min_accuracy
=None_)[[source]](../_modules/hail/expr/functions.html#pgenchisq)

    

The cumulative probability function of a [generalized chi-squared
distribution](https://en.wikipedia.org/wiki/Generalized_chi-
squared_distribution).

The generalized chi-squared distribution has many interpretations. We share
here four interpretations of the values of this distribution:

  1. A linear combination of normal variables and squares of normal variables.

  2. A weighted sum of sums of squares of normally distributed values plus a normally distributed value.

  3. A weighted sum of chi-squared distributed values plus a normally distributed value.

  4. A [“quadratic form”](https://en.wikipedia.org/wiki/Quadratic_form_\(statistics\)) in a vector of uncorrelated [standard normal](https://en.wikipedia.org/wiki/Normal_distribution#Standard_normal_distribution) values.

The parameters of this function correspond to the parameters of the third
interpretation.

\\[\begin{aligned} w &: R^n \quad k : Z^n \quad lam : R^n \quad mu : R \quad
sigma : R \\\ \\\ x &\sim N(mu, sigma^2) \\\ y_i &\sim
\mathrm{NonCentralChiSquared}(k_i, lam_i) \\\ \\\ Z &= x + w y^T \\\ &= x +
\sum_i w_i y_i \\\ Z &\sim \mathrm{GeneralizedNonCentralChiSquared}(w, k, lam,
mu, sigma) \end{aligned}\\]

The generalized chi-squared distribution often arises when working on linear
models with standard normal noise because the sum of the squares of the
residuals should follow a generalized chi-squared distribution.

Examples

The following plot shows three examples of the generalized chi-squared
cumulative distribution function.

[![Plots of examples of the generalized chi-square cumulative distribution
function. Created by
Dvidby0.](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/Generalized_chi-
square_cumulative_distribution_function.svg/1280px-Generalized_chi-
square_cumulative_distribution_function.svg.png)](https://commons.wikimedia.org/wiki/File:Generalized_chi-
square_cumulative_distribution_function.svg)

The following examples are chosen from the three instances shown above. The
curves appear in the same order as the legend of the plot: blue, red, yellow.

    
    
    >>> hl.eval(hl.pgenchisq(-80, w=[1, 2], k=[1, 4], lam=[1, 1], mu=0, sigma=0).value)
    0.0
    >>> hl.eval(hl.pgenchisq(-20, w=[1, 2], k=[1, 4], lam=[1, 1], mu=0, sigma=0).value)
    0.0
    >>> hl.eval(hl.pgenchisq(10 , w=[1, 2], k=[1, 4], lam=[1, 1], mu=0, sigma=0).value)
    0.4670012373599629
    >>> hl.eval(hl.pgenchisq(40 , w=[1, 2], k=[1, 4], lam=[1, 1], mu=0, sigma=0).value)
    0.9958803111156718
    
    
    
    >>> hl.eval(hl.pgenchisq(-80, w=[-2, -1], k=[5, 2], lam=[3, 1], mu=-3, sigma=0).value)
    9.227056966837344e-05
    >>> hl.eval(hl.pgenchisq(-20, w=[-2, -1], k=[5, 2], lam=[3, 1], mu=-3, sigma=0).value)
    0.516439358616939
    >>> hl.eval(hl.pgenchisq(10 , w=[-2, -1], k=[5, 2], lam=[3, 1], mu=-3, sigma=0).value)
    1.0
    >>> hl.eval(hl.pgenchisq(40 , w=[-2, -1], k=[5, 2], lam=[3, 1], mu=-3, sigma=0).value)
    1.0
    
    
    
    >>> hl.eval(hl.pgenchisq(-80, w=[1, -10, 2], k=[1, 2, 3], lam=[2, 3, 7], mu=-10, sigma=0).value)
    0.14284718767288906
    >>> hl.eval(hl.pgenchisq(-20, w=[1, -10, 2], k=[1, 2, 3], lam=[2, 3, 7], mu=-10, sigma=0).value)
    0.5950150356303258
    >>> hl.eval(hl.pgenchisq(10 , w=[1, -10, 2], k=[1, 2, 3], lam=[2, 3, 7], mu=-10, sigma=0).value)
    0.923219534175858
    >>> hl.eval(hl.pgenchisq(40 , w=[1, -10, 2], k=[1, 2, 3], lam=[2, 3, 7], mu=-10, sigma=0).value)
    0.9971746768781656
    

Notes

We follow Wikipedia’s notational conventions. Some texts refer to the weight
vector (our w) as \\(\lambda\\) or lb and the non-centrality vector (our lam)
as nc.

We use the Davies’ algorithm which was published as:

> [Davies, Robert. “The distribution of a linear combination of chi-squared
> random variables.” Applied Statistics 29 323-333.
> 1980.](http://www.robertnz.net/pdf/lc_chisq.pdf)

Davies included Fortran source code in the original publication. Davies also
released a [C language port](http://www.robertnz.net/QF.htm). Hail’s
implementation is a fairly direct port of the C implementation to Scala.
Davies provides 39 test cases with the source code. The Hail tests include all
39 test cases as well as a few additional tests.

Davies’ website cautions:

> The method works well in most situations if you want only modest accuracy,
> say 0.0001. But problems may arise if the sum is dominated by one or two
> terms with a total of only one or two degrees of freedom and x is small.

For an accessible introduction the Generalized Chi-Squared Distribution, we
strongly recommend the introduction of this paper:

> [Das, Abhranil; Geisler, Wilson (2020). “A method to integrate and classify
> normal distributions”.](https://arxiv.org/abs/2012.14331)

Parameters:

    

  * **x** (```float``` or ```Expression``` of type ```tfloat64```) – The value at which to evaluate the cumulative distribution function (CDF).

  * **w** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of ```float``` or ```Expression``` of type ```tarray``` of ```tfloat64```) – A weight for each non-central chi-square term.

  * **k** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of ```int``` or ```Expression``` of type ```tarray``` of ```tint32```) – A degrees of freedom parameter for each non-central chi-square term.

  * **lam** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of ```float``` or ```Expression``` of type ```tarray``` of ```tfloat64```) – A non-centrality parameter for each non-central chi-square term. We use lam instead of lambda because the latter is a reserved word in Python.

  * **mu** (```float``` or ```Expression``` of type ```tfloat64```) – The standard deviation of the normal term.

  * **sigma** (```float``` or ```Expression``` of type ```tfloat64```) – The standard deviation of the normal term.

  * **max_iterations** (```int``` or ```Expression``` of type ```tint32```) – The maximum number of iterations of the numerical integration before raising an error. The default maximum number of iterations is `1e5`.

  * **min_accuracy** (```int``` or ```Expression``` of type ```tint32```) – The minimum accuracy of the returned value. If the minimum accuracy is not achieved, this function will raise an error. The default minimum accuracy is `1e-5`.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – This method returns a structure with the value
as well as information about the numerical integration.

  * value : ```Float64Expression```. If converged is true, the value of the CDF evaluated at x. Otherwise, this is the last value the integration evaluated before aborting.

  * n_iterations : ```Int32Expression```. The number of iterations before stopping.

  * converged : ```BooleanExpression```. True if the min_accuracy was achieved and round off error is not likely significant.

  * fault : ```Int32Expression```. If converged is true, fault is zero. If converged is false, fault is either one or two. One indicates that the requried accuracy was not achieved. Two indicates the round-off error is possibly significant.

hail.expr.functions.pnorm(_x_ , _mu =0_, _sigma =1_, _lower_tail =True_,
_log_p =False_)[[source]](../_modules/hail/expr/functions.html#pnorm)

    

The cumulative probability function of a normal distribution with mean mu and
standard deviation sigma. Returns cumulative probability of standard normal
distribution by default.

Examples

    
    
    >>> hl.eval(hl.pnorm(0))
    0.5
    
    
    
    >>> hl.eval(hl.pnorm(1, mu=2, sigma=2))
    0.30853753872598694
    
    
    
    >>> hl.eval(hl.pnorm(2, lower_tail=False))
    0.022750131948179212
    
    
    
    >>> hl.eval(hl.pnorm(2, log_p=True))
    -0.023012909328963493
    

Notes

Returns the left-tail probability p = Prob(\\(Z < x\\)) with \\(Z\\) a normal
random variable. Defaults to a standard normal random variable.

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```)

  * **mu** (float or ```Expression``` of type ```tfloat64```) – Mean (default = 0).

  * **sigma** (float or ```Expression``` of type ```tfloat64```) – Standard deviation (default = 1).

  * **lower_tail** (bool or ```BooleanExpression```) – If `True`, compute the probability of an outcome at or below x, otherwise greater than x.

  * **log_p** (bool or ```BooleanExpression```) – Return the natural logarithm of the probability.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.pT(_x_ , _n_ , _lower_tail =True_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#pT)

    

The cumulative probability function of a
[t-distribution](https://en.wikipedia.org/wiki/Student%27s_t-distribution)
with n degrees of freedom.

Examples

    
    
    >>> hl.eval(hl.pT(0, 10))
    0.5
    
    
    
    >>> hl.eval(hl.pT(1, 10))
    0.82955343384897
    
    
    
    >>> hl.eval(hl.pT(1, 10, lower_tail=False))
    0.17044656615103004
    
    
    
    >>> hl.eval(hl.pT(1, 10, log_p=True))
    -0.186867754489647
    

Notes

If lower_tail is true, returns Prob(\\(X \leq\\) x) where \\(X\\) is a
t-distributed random variable with n degrees of freedom. If lower_tail is
false, returns Prob(\\(X\\) > x).

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```)

  * **n** (float or ```Expression``` of type ```tfloat64```) – Degrees of freedom of the t-distribution.

  * **lower_tail** (bool or ```BooleanExpression```) – If `True`, compute the probability of an outcome at or below x, otherwise greater than x.

  * **log_p** (bool or ```BooleanExpression```) – Return the natural logarithm of the probability.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.pF(_x_ , _df1_ , _df2_ , _lower_tail =True_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#pF)

    

The cumulative probability function of a
[F-distribution](https://en.wikipedia.org/wiki/F-distribution) with parameters
df1 and df2.

Examples

    
    
    >>> hl.eval(hl.pF(0, 3, 10))
    0.0
    
    
    
    >>> hl.eval(hl.pF(1, 3, 10))
    0.5676627969783028
    
    
    
    >>> hl.eval(hl.pF(1, 3, 10, lower_tail=False))
    0.4323372030216972
    
    
    
    >>> hl.eval(hl.pF(1, 3, 10, log_p=True))
    -0.566227703842908
    

Notes

If lower_tail is true, returns Prob(\\(X \leq\\) x) where \\(X\\) is a random
variable with distribution \\(F`(df1, df2). If `lower_tail\\) is false,
returns Prob(\\(X\\) > x).

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```)

  * **df1** (float or ```Expression``` of type ```tfloat64```) – Parameter of the F-distribution

  * **df2** (float or ```Expression``` of type ```tfloat64```) – Parameter of the F-distribution

  * **lower_tail** (bool or ```BooleanExpression```) – If `True`, compute the probability of an outcome at or below x, otherwise greater than x.

  * **log_p** (bool or ```BooleanExpression```) – Return the natural logarithm of the probability.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.ppois(_x_ , _lamb_ , _lower_tail =True_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#ppois)

    

The cumulative probability function of a Poisson distribution.

Examples

    
    
    >>> hl.eval(hl.ppois(2, 1))
    0.9196986029286058
    

Notes

If lower_tail is true, returns Prob(\\(X \leq\\) x) where \\(X\\) is a Poisson
random variable with rate parameter lamb. If lower_tail is false, returns
Prob(\\(X\\) > x).

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```)

  * **lamb** (float or ```Expression``` of type ```tfloat64```) – Rate parameter of Poisson distribution.

  * **lower_tail** (bool or ```BooleanExpression```) – If `True`, compute the probability of an outcome at or below x, otherwise greater than x.

  * **log_p** (bool or ```BooleanExpression```) – Return the natural logarithm of the probability.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.qchisqtail(_p_ , _df_ , _ncp =None_, _lower_tail =False_,
_log_p =False_)[[source]](../_modules/hail/expr/functions.html#qchisqtail)

    

The quantile function of a chi-squared distribution with df degrees of
freedom, inverts `pchisqtail()`.

Examples

    
    
    >>> hl.eval(hl.qchisqtail(0.05, 2))
    5.991464547107979
    
    
    
    >>> hl.eval(hl.qchisqtail(0.05, 2, ncp=2))
    10.838131614372958
    
    
    
    >>> hl.eval(hl.qchisqtail(0.05, 2, lower_tail=True))
    0.10258658877510107
    
    
    
    >>> hl.eval(hl.qchisqtail(hl.log(0.05), 2, log_p=True))
    5.991464547107979
    

Notes

Returns right-quantile x for which p = Prob(\\(Z^2\\) > x) with \\(Z^2\\) a
chi-squared random variable with degrees of freedom specified by df. The
probability p must satisfy 0 < p < 1.

Parameters:

    

  * **p** (float or ```Expression``` of type ```tfloat64```) – Probability.

  * **df** (float or ```Expression``` of type ```tfloat64```) – Degrees of freedom.

  * **ncp** (float or ```Expression``` of type ```tfloat64```) – Corresponds to ncp parameter in `pchisqtail()`.

  * **lower_tail** (bool or ```BooleanExpression```) – Corresponds to lower_tail parameter in `pchisqtail()`.

  * **log_p** (bool or ```BooleanExpression```) – Exponentiate p, corresponds to log_p parameter in `pchisqtail()`.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.qnorm(_p_ , _mu =0_, _sigma =1_, _lower_tail =True_,
_log_p =False_)[[source]](../_modules/hail/expr/functions.html#qnorm)

    

The quantile function of a normal distribution with mean mu and standard
deviation sigma, inverts `pnorm()`. Returns quantile of standard normal
distribution by default.

Examples

    
    
    >>> hl.eval(hl.qnorm(0.90))
    1.2815515655446008
    
    
    
    >>> hl.eval(hl.qnorm(0.90, mu=1, sigma=2))
    3.5631031310892016
    
    
    
    >>> hl.eval(hl.qnorm(0.90, lower_tail=False))
    -1.2815515655446008
    
    
    
    >>> hl.eval(hl.qnorm(hl.log(0.90), log_p=True))
    1.2815515655446008
    

Notes

Returns left-quantile x for which p = Prob(\\(Z\\) < x) with \\(Z\\) a normal
random variable with mean mu and standard deviation sigma. Defaults to a
standard normal random variable, and the probability p must satisfy 0 < p < 1.

Parameters:

    

  * **p** (float or ```Expression``` of type ```tfloat64```) – Probability.

  * **mu** (float or ```Expression``` of type ```tfloat64```) – Mean (default = 0).

  * **sigma** (float or ```Expression``` of type ```tfloat64```) – Standard deviation (default = 1).

  * **lower_tail** (bool or ```BooleanExpression```) – Corresponds to lower_tail parameter in `pnorm()`.

  * **log_p** (bool or ```BooleanExpression```) – Exponentiate p, corresponds to log_p parameter in `pnorm()`.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.qpois(_p_ , _lamb_ , _lower_tail =True_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#qpois)

    

The quantile function of a Poisson distribution with rate parameter lamb,
inverts `ppois()`.

Examples

    
    
    >>> hl.eval(hl.qpois(0.99, 1))
    4
    

Notes

Returns the smallest integer \\(x\\) such that Prob(\\(X \leq x\\)) \\(\geq\\)
p where \\(X\\) is a Poisson random variable with rate parameter lambda.

Parameters:

    

  * **p** (float or ```Expression``` of type ```tfloat64```)

  * **lamb** (float or ```Expression``` of type ```tfloat64```) – Rate parameter of Poisson distribution.

  * **lower_tail** (bool or ```BooleanExpression```) – Corresponds to lower_tail parameter in inverse `ppois()`.

  * **log_p** (bool or ```BooleanExpression```) – Exponentiate p before testing.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")


---
### Genetics functions

`locus`(contig, pos[, reference_genome]) | Construct a locus expression from a chromosome and position.  
---|---  
`locus_from_global_position`(global_pos[, ...]) | Constructs a locus expression from a global position and a reference genome.  
`locus_interval`(contig, start, end[, ...]) | Construct a locus interval expression.  
`parse_locus`(s[, reference_genome]) | Construct a locus expression by parsing a string or string expression.  
`parse_variant`(s[, reference_genome]) | Construct a struct with a locus and alleles by parsing a string.  
`parse_locus_interval`(s[, reference_genome, ...]) | Construct a locus interval expression by parsing a string or string expression.  
`variant_str`(*args) | Create a variant colon-delimited string.  
`call`(*alleles[, phased]) | Construct a call expression.  
`unphased_diploid_gt_index_call`(gt_index) | Construct an unphased, diploid call from a genotype index.  
`parse_call`(s) | Construct a call expression by parsing a string or string expression.  
`downcode`(c, i) | Create a new call by setting all alleles other than i to ref  
`triangle`(n) | Returns the triangle number of n.  
`is_snp`(ref, alt) | Returns `True` if the alleles constitute a single nucleotide polymorphism.  
`is_mnp`(ref, alt) | Returns `True` if the alleles constitute a multiple nucleotide polymorphism.  
`is_transition`(ref, alt) | Returns `True` if the alleles constitute a transition.  
`is_transversion`(ref, alt) | Returns `True` if the alleles constitute a transversion.  
`is_insertion`(ref, alt) | Returns `True` if the alleles constitute an insertion.  
`is_deletion`(ref, alt) | Returns `True` if the alleles constitute a deletion.  
`is_indel`(ref, alt) | Returns `True` if the alleles constitute an insertion or deletion.  
`is_star`(ref, alt) | Returns `True` if the alleles constitute an upstream deletion.  
`is_complex`(ref, alt) | Returns `True` if the alleles constitute a complex polymorphism.  
`is_strand_ambiguous`(ref, alt) | Returns `True` if the alleles are strand ambiguous.  
`is_valid_contig`(contig[, reference_genome]) | Returns `True` if contig is a valid contig name in reference_genome.  
`is_valid_locus`(contig, position[, ...]) | Returns `True` if contig and position is a valid site in reference_genome.  
`contig_length`(contig[, reference_genome]) | Returns the length of contig in reference_genome.  
`allele_type`(ref, alt) | Returns the type of the polymorphism as a string.  
`numeric_allele_type`(ref, alt) | Returns the type of the polymorphism as an integer.  
`pl_dosage`(pl) | Return expected genotype dosage from array of Phred-scaled genotype likelihoods with uniform prior.  
`gp_dosage`(gp) | Return expected genotype dosage from array of genotype probabilities.  
`get_sequence`(contig, position[, before, ...]) | Return the reference sequence at a given locus.  
`mendel_error_code`(locus, is_female, father, ...) | Compute a Mendelian violation code for genotypes.  
`liftover`(x, dest_reference_genome[, ...]) | Lift over coordinates to a different reference genome.  
`min_rep`(locus, alleles) | Computes the minimal representation of a (locus, alleles) polymorphism.  
`reverse_complement`(s[, rna]) | Reverses the string and translates base pairs into their complements .  
  
hail.expr.functions.locus(_contig_ , _pos_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#locus)

    

Construct a locus expression from a chromosome and position.

Examples

    
    
    >>> hl.eval(hl.locus("1", 10000, reference_genome='GRCh37'))
    Locus(contig=1, position=10000, reference_genome=GRCh37)
    

Parameters:

    

  * **contig** (str or ```StringExpression```) – Chromosome.

  * **pos** (int or ```Expression``` of type ```tint32```) – Base position along the chromosome.

  * **reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to use.

Returns:

    

[`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression")

hail.expr.functions.locus_from_global_position(_global_pos_ ,
_reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#locus_from_global_position)

    

Constructs a locus expression from a global position and a reference genome.
The inverse of
[`LocusExpression.global_position()`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression.global_position
"hail.expr.LocusExpression.global_position").

Examples

    
    
    >>> hl.eval(hl.locus_from_global_position(0))
    Locus(contig=1, position=1, reference_genome=GRCh37)
    
    
    
    >>> hl.eval(hl.locus_from_global_position(2824183054))
    Locus(contig=21, position=42584230, reference_genome=GRCh37)
    
    
    
    >>> hl.eval(hl.locus_from_global_position(2824183054, reference_genome='GRCh38'))
    Locus(contig=chr22, position=1, reference_genome=GRCh38)
    

Parameters:

    

  * **global_pos** (int or ```Expression``` of type ```tint64```) – Global base position along the reference genome.

  * **reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to use for converting the global position to a contig and local position.

Returns:

    

[`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression")

hail.expr.functions.locus_interval(_contig_ , _start_ , _end_ ,
_includes_start =True_, _includes_end =False_, _reference_genome ='default'_,
_invalid_missing
=False_)[[source]](../_modules/hail/expr/functions.html#locus_interval)

    

Construct a locus interval expression.

Examples

    
    
    >>> hl.eval(hl.locus_interval("1", 100, 1000, reference_genome='GRCh37'))
    Interval(start=Locus(contig=1, position=100, reference_genome=GRCh37),
             end=Locus(contig=1, position=1000, reference_genome=GRCh37),
             includes_start=True,
             includes_end=False)
    

Parameters:

    

  * **contig** (```StringExpression```) – Contig name.

  * **start** (```Int32Expression```) – Starting base position.

  * **end** (```Int32Expression```) – End base position.

  * **includes_start** (```BooleanExpression```) – If `True`, interval includes start point.

  * **includes_end** (```BooleanExpression```) – If `True`, interval includes end point.

  * **reference_genome** (```str``` or ```hail.genetics.ReferenceGenome```) – Reference genome to use.

  * **invalid_missing** (```BooleanExpression```) – If `True`, invalid intervals are set to NA rather than causing an exception.

Returns:

    

[`IntervalExpression`](../hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression")

hail.expr.functions.parse_locus(_s_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#parse_locus)

    

Construct a locus expression by parsing a string or string expression.

Examples

    
    
    >>> hl.eval(hl.parse_locus('1:10000', reference_genome='GRCh37'))
    Locus(contig=1, position=10000, reference_genome=GRCh37)
    

Notes

This method expects strings of the form `contig:position`, e.g. `16:29500000`
or `X:123456`.

Parameters:

    

  * **s** (str or ```StringExpression```) – String to parse.

  * **reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to use.

Returns:

    

[`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression")

hail.expr.functions.parse_variant(_s_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#parse_variant)

    

Construct a struct with a locus and alleles by parsing a string.

Examples

    
    
    >>> hl.eval(hl.parse_variant('1:100000:A:T,C', reference_genome='GRCh37'))
    Struct(locus=Locus(contig=1, position=100000, reference_genome=GRCh37), alleles=['A', 'T', 'C'])
    

Notes

This method returns an expression of type
```tstruct```
with the following fields:

>   * locus ([`tlocus`](../types.html#hail.expr.types.tlocus
> "hail.expr.types.tlocus"))
>
>   * alleles ([`tarray`](../types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of [`tstr`](../types.html#hail.expr.types.tstr
> "hail.expr.types.tstr"))
>
>

Parameters:

    

  * **s** (```StringExpression```) – String to parse.

  * **reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to use.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct with fields locus and alleles.

hail.expr.functions.parse_locus_interval(_s_ , _reference_genome ='default'_,
_invalid_missing
=False_)[[source]](../_modules/hail/expr/functions.html#parse_locus_interval)

    

Construct a locus interval expression by parsing a string or string
expression.

Examples

    
    
    >>> hl.eval(hl.parse_locus_interval('1:1000-2000', reference_genome='GRCh37'))
    Interval(start=Locus(contig=1, position=1000, reference_genome=GRCh37),
             end=Locus(contig=1, position=2000, reference_genome=GRCh37),
             includes_start=True,
             includes_end=False)
    
    
    
    >>> hl.eval(hl.parse_locus_interval('1:start-10M', reference_genome='GRCh37'))
    Interval(start=Locus(contig=1, position=1, reference_genome=GRCh37),
             end=Locus(contig=1, position=10000000, reference_genome=GRCh37),
             includes_start=True,
             includes_end=False)
    

Notes

The start locus must precede the end locus. The default bounds of the interval
are left-inclusive and right-exclusive. To change this, add one of `[` or `(`
at the beginning of the string for left-inclusive or left-exclusive
respectively. Likewise, add one of `]` or `)` at the end of the string for
right-inclusive or right-exclusive respectively.

There are several acceptable representations for s.

`CHR1:POS1-CHR2:POS2` is the fully specified representation, and we use this
to define the various shortcut representations.

In a `POS` field, `start` (`Start`, `START`) stands for 1.

In a `POS` field, `end` (`End`, `END`) stands for the contig length.

In a `POS` field, the qualifiers `m` (`M`) and `k` (`K`) multiply the given
number by `1,000,000` and `1,000`, respectively. `1.6K` is short for 1600, and
`29M` is short for 29000000.

`CHR:POS1-POS2` stands for `CHR:POS1-CHR:POS2`

`CHR1-CHR2` stands for `CHR1:START-CHR2:END`

`CHR` stands for `CHR:START-CHR:END`

Note

The bounds of the interval must be valid loci for the reference genome (contig
in reference genome and position is within the range [1-END]) except in the
case where the position is `0` **AND** the interval is **left-exclusive**
which is normalized to be `1` and left-inclusive. Likewise, in the case where
the position is `END + 1` **AND** the interval is **right-exclusive** which is
normalized to be `END` and right-inclusive.

Parameters:

    

  * **s** (str or ```StringExpression```) – String to parse.

  * **reference_genome** (```str``` or ```hail.genetics.ReferenceGenome```) – Reference genome to use.

  * **invalid_missing** (```BooleanExpression```) – If `True`, invalid intervals are set to NA rather than causing an exception.

Returns:

    

[`IntervalExpression`](../hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression")

hail.expr.functions.variant_str(_*
args_)[[source]](../_modules/hail/expr/functions.html#variant_str)

    

Create a variant colon-delimited string.

Parameters:

    

**args** – Arguments (see notes).

Returns:

    

[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

Notes

Expects either one argument of type `struct{locus: locus<RG>, alleles:
array<str>`, or two arguments of type `locus<RG>` and `array<str>`. The
function returns a string of the form

    
    
    CHR:POS:REF:ALT1,ALT2,...ALTN
    e.g.
    1:1:A:T
    16:250125:AAA:A,CAA
    

Examples

    
    
    >>> hl.eval(hl.variant_str(hl.locus('1', 10000), ['A', 'T', 'C']))
    '1:10000:A:T,C'
    

hail.expr.functions.call(_* alleles_, _phased
=False_)[[source]](../_modules/hail/expr/functions.html#call)

    

Construct a call expression.

Examples

    
    
    >>> hl.eval(hl.call(1, 0))
    Call(alleles=[0, 1], phased=False)
    

Parameters:

    

  * **alleles** (variable-length args of ```int``` or ```Expression``` of type ```tint32```) – List of allele indices.

  * **phased** (```bool```) – If `True`, preserve the order of alleles.

Returns:

    

[`CallExpression`](../hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression")

hail.expr.functions.unphased_diploid_gt_index_call(_gt_index_)[[source]](../_modules/hail/expr/functions.html#unphased_diploid_gt_index_call)

    

Construct an unphased, diploid call from a genotype index.

Examples

    
    
    >>> hl.eval(hl.unphased_diploid_gt_index_call(4))
    Call(alleles=[1, 2], phased=False)
    

Parameters:

    

**gt_index** ([`int`](constructors.html#hail.expr.functions.int
"hail.expr.functions.int") or
[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```) –
Unphased, diploid genotype index.

Returns:

    

[`CallExpression`](../hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression")

hail.expr.functions.parse_call(_s_)[[source]](../_modules/hail/expr/functions.html#parse_call)

    

Construct a call expression by parsing a string or string expression.

Examples

    
    
    >>> hl.eval(hl.parse_call('0|2'))
    Call(alleles=[0, 2], phased=True)
    

Notes

This method expects strings in the following format:

ploidy | Phased | Unphased  
---|---|---  
0 | `|-` | `-`  
1 | `|i` | `i`  
2 | `i|j` | `i/j`  
3 | `i|j|k` | `i/j/k`  
N | `i|j|k|...|N` | `i/j/k/.../N`  
  
Parameters:

    

**s** (str or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")) – String to parse.

Returns:

    

[`CallExpression`](../hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression")

hail.expr.functions.downcode(_c_ ,
_i_)[[source]](../_modules/hail/expr/functions.html#downcode)

    

Create a new call by setting all alleles other than i to ref

Examples

Preserve the third allele and downcode all other alleles to reference.

    
    
    >>> hl.eval(hl.downcode(hl.call(1, 2), 2))
    Call(alleles=[0, 1], phased=False)
    
    
    
    >>> hl.eval(hl.downcode(hl.call(2, 2), 2))
    Call(alleles=[1, 1], phased=False)
    
    
    
    >>> hl.eval(hl.downcode(hl.call(0, 1), 2))
    Call(alleles=[0, 0], phased=False)
    

Parameters:

    

  * **c** (```CallExpression```) – A call.

  * **i** (```Expression``` of type ```tint32```) – The index of the allele that will be sent to the alternate allele. All other alleles will be downcoded to reference.

Returns:

    

[`CallExpression`](../hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression")

hail.expr.functions.triangle(_n_)[[source]](../_modules/hail/expr/functions.html#triangle)

    

Returns the triangle number of n.

Examples

    
    
    >>> hl.eval(hl.triangle(3))
    6
    

Notes

The calculation is `n * (n + 1) / 2`.

Parameters:

    

**n** ([`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```

hail.expr.functions.is_snp(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_snp)

    

Returns `True` if the alleles constitute a single nucleotide polymorphism.

Examples

    
    
    >>> hl.eval(hl.is_snp('A', 'T'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_mnp(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_mnp)

    

Returns `True` if the alleles constitute a multiple nucleotide polymorphism.

Examples

    
    
    >>> hl.eval(hl.is_mnp('AA', 'GT'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_transition(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_transition)

    

Returns `True` if the alleles constitute a transition.

Examples

    
    
    >>> hl.eval(hl.is_transition('A', 'T'))
    False
    
    
    
    >>> hl.eval(hl.is_transition('AAA', 'AGA'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_transversion(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_transversion)

    

Returns `True` if the alleles constitute a transversion.

Examples

    
    
    >>> hl.eval(hl.is_transversion('A', 'T'))
    True
    
    
    
    >>> hl.eval(hl.is_transversion('AAA', 'AGA'))
    False
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_insertion(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_insertion)

    

Returns `True` if the alleles constitute an insertion.

Examples

    
    
    >>> hl.eval(hl.is_insertion('A', 'ATT'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_deletion(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_deletion)

    

Returns `True` if the alleles constitute a deletion.

Examples

    
    
    >>> hl.eval(hl.is_deletion('ATT', 'A'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_indel(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_indel)

    

Returns `True` if the alleles constitute an insertion or deletion.

Examples

    
    
    >>> hl.eval(hl.is_indel('ATT', 'A'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_star(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_star)

    

Returns `True` if the alleles constitute an upstream deletion.

Examples

    
    
    >>> hl.eval(hl.is_star('A', '*'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_complex(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_complex)

    

Returns `True` if the alleles constitute a complex polymorphism.

Examples

    
    
    >>> hl.eval(hl.is_complex('ATT', 'GCAC'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_strand_ambiguous(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_strand_ambiguous)

    

Returns `True` if the alleles are strand ambiguous.

Strand ambiguous allele pairs are `A/T`, `T/A`, `C/G`, and `G/C` where the
first allele is ref and the second allele is alt.

Examples

    
    
    >>> hl.eval(hl.is_strand_ambiguous('A', 'T'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_valid_contig(_contig_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#is_valid_contig)

    

Returns `True` if contig is a valid contig name in reference_genome.

Examples

    
    
    >>> hl.eval(hl.is_valid_contig('1', reference_genome='GRCh37'))
    True
    
    
    
    >>> hl.eval(hl.is_valid_contig('chr1', reference_genome='GRCh37'))
    False
    

Parameters:

    

  * **contig** (```Expression``` of type ```tstr```)

  * **reference_genome** (```str``` or ```ReferenceGenome```)

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_valid_locus(_contig_ , _position_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#is_valid_locus)

    

Returns `True` if contig and position is a valid site in reference_genome.

Examples

    
    
    >>> hl.eval(hl.is_valid_locus('1', 324254, 'GRCh37'))
    True
    
    
    
    >>> hl.eval(hl.is_valid_locus('chr1', 324254, 'GRCh37'))
    False
    

Parameters:

    

  * **contig** (```Expression``` of type ```tstr```)

  * **position** (```Expression``` of type ```tint```)

  * **reference_genome** (```str``` or ```ReferenceGenome```)

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.contig_length(_contig_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#contig_length)

    

Returns the length of contig in reference_genome.

Examples

    
    
    >>> hl.eval(hl.contig_length('5', reference_genome='GRCh37'))
    180915260
    

Parameters:

    

  * **contig** (```Expression``` of type ```tstr```)

  * **reference_genome** (```str``` or ```ReferenceGenome```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

hail.expr.functions.allele_type(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#allele_type)

    

Returns the type of the polymorphism as a string.

Examples

    
    
    >>> hl.eval(hl.allele_type('A', 'T'))
    'SNP'
    
    
    
    >>> hl.eval(hl.allele_type('ATT', 'A'))
    'Deletion'
    

Notes

The possible return values are:

    

  * `"SNP"`

  * `"MNP"`

  * `"Insertion"`

  * `"Deletion"`

  * `"Complex"`

  * `"Star"`

  * `"Symbolic"`

  * `"Unknown"`

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

hail.expr.functions.numeric_allele_type(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#numeric_allele_type)

    

Returns the type of the polymorphism as an integer. The value returned is the
integer value of
[`AlleleType`](../genetics/hail.genetics.AlleleType.html#hail.genetics.AlleleType
"hail.genetics.AlleleType") representing that kind of polymorphism.

Examples

    
    
    >>> hl.eval(hl.numeric_allele_type('A', 'T')) == AlleleType.SNP
    True
    

Notes

The values of
[`AlleleType`](../genetics/hail.genetics.AlleleType.html#hail.genetics.AlleleType
"hail.genetics.AlleleType") are not stable and thus should not be relied upon
across hail versions.

hail.expr.functions.pl_dosage(_pl_)[[source]](../_modules/hail/expr/functions.html#pl_dosage)

    

Return expected genotype dosage from array of Phred-scaled genotype
likelihoods with uniform prior. Only defined for bi-allelic variants. The pl
argument must be length 3.

For a PL array `[a, b, c]`, let:

\\[a^\prime = 10^{-a/10} \\\ b^\prime = 10^{-b/10} \\\ c^\prime = 10^{-c/10}
\\\\\\]

The genotype dosage is given by:

\\[\frac{b^\prime + 2 c^\prime} {a^\prime + b^\prime +c ^\prime}\\]

Examples

    
    
    >>> hl.eval(hl.pl_dosage([5, 10, 100]))
    0.24025307377482674
    

Parameters:

    

**pl**
([`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") of type
```tint32```) –
Length 3 array of bi-allelic Phred-scaled genotype likelihoods

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.gp_dosage(_gp_)[[source]](../_modules/hail/expr/functions.html#gp_dosage)

    

Return expected genotype dosage from array of genotype probabilities.

Examples

    
    
    >>> hl.eval(hl.gp_dosage([0.0, 0.5, 0.5]))
    1.5
    

Notes

This function is only defined for bi-allelic variants. The gp argument must be
length 3. The value is `gp[1] + 2 * gp[2]`.

Parameters:

    

**gp** ([`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tarray``` of
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")) – Length 3 array of bi-allelic genotype
probabilities

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.get_sequence(_contig_ , _position_ , _before =0_, _after
=0_, _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#get_sequence)

    

Return the reference sequence at a given locus.

Examples

Return the reference allele for `'GRCh37'` at the locus `'1:45323'`:

    
    
    >>> hl.eval(hl.get_sequence('1', 45323, reference_genome='GRCh37')) 
    "T"
    

Notes

This function requires reference genome has an attached reference sequence.
Use
[`ReferenceGenome.add_sequence()`](../genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome.add_sequence
"hail.genetics.ReferenceGenome.add_sequence") to load and attach a reference
sequence to a reference genome.

Returns `None` if contig and position are not valid coordinates in
reference_genome.

Parameters:

    

  * **contig** (```Expression``` of type ```tstr```) – Locus contig.

  * **position** (```Expression``` of type ```tint32```) – Locus position.

  * **before** (```Expression``` of type ```tint32```, optional) – Number of bases to include before the locus of interest. Truncates at contig boundary.

  * **after** (```Expression``` of type ```tint32```, optional) – Number of bases to include after the locus of interest. Truncates at contig boundary.

  * **reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to use. Must have a reference sequence available.

Returns:

    

[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

hail.expr.functions.mendel_error_code(_locus_ , _is_female_ , _father_ ,
_mother_ ,
_child_)[[source]](../_modules/hail/expr/functions.html#mendel_error_code)

    

Compute a Mendelian violation code for genotypes.

    
    
    >>> father = hl.call(0, 0)
    >>> mother = hl.call(1, 1)
    >>> child1 = hl.call(0, 1)  # consistent
    >>> child2 = hl.call(0, 0)  # Mendel error
    >>> locus = hl.locus('2', 2000000)
    
    
    
    >>> hl.eval(hl.mendel_error_code(locus, True, father, mother, child1))
    None
    
    
    
    >>> hl.eval(hl.mendel_error_code(locus, True, father, mother, child2))
    7
    

Note

Ignores call phasing, and assumes diploid and biallelic. Haploid calls for
hemiploid samples on sex chromosomes also are acceptable input.

Notes

In the table below, the copy state of a locus with respect to a trio is
defined as follows, where PAR is the [pseudoautosomal
region](https://en.wikipedia.org/wiki/Pseudoautosomal_region) (PAR) of X and Y
defined by the reference genome and the autosome is defined by
[`LocusExpression.in_autosome()`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression.in_autosome
"hail.expr.LocusExpression.in_autosome"):

  * Auto – in autosome or in PAR, or in non-PAR of X and female child

  * HemiX – in non-PAR of X and male child

  * HemiY – in non-PAR of Y and male child

Any refers to the set { HomRef, Het, HomVar, NoCall } and ~ denotes complement
in this set.

Code | Dad | Mom | Kid | Copy State | Implicated  
---|---|---|---|---|---  
1 | HomVar | HomVar | Het | Auto | Dad, Mom, Kid  
2 | HomRef | HomRef | Het | Auto | Dad, Mom, Kid  
3 | HomRef | ~HomRef | HomVar | Auto | Dad, Kid  
4 | ~HomRef | HomRef | HomVar | Auto | Mom, Kid  
5 | HomRef | HomRef | HomVar | Auto | Kid  
6 | HomVar | ~HomVar | HomRef | Auto | Dad, Kid  
7 | ~HomVar | HomVar | HomRef | Auto | Mom, Kid  
8 | HomVar | HomVar | HomRef | Auto | Kid  
9 | Any | HomVar | HomRef | HemiX | Mom, Kid  
10 | Any | HomRef | HomVar | HemiX | Mom, Kid  
11 | HomVar | Any | HomRef | HemiY | Dad, Kid  
12 | HomRef | Any | HomVar | HemiY | Dad, Kid  
  
Parameters:

    

  * **locus** (```LocusExpression```)

  * **is_female** (```BooleanExpression```)

  * **father** (```CallExpression```)

  * **mother** (```CallExpression```)

  * **child** (```CallExpression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

hail.expr.functions.liftover(_x_ , _dest_reference_genome_ , _min_match
=0.95_, _include_strand
=False_)[[source]](../_modules/hail/expr/functions.html#liftover)

    

Lift over coordinates to a different reference genome.

Examples

Lift over the locus coordinates from reference genome `'GRCh37'` to
`'GRCh38'`:

    
    
    >>> hl.eval(hl.liftover(hl.locus('1', 1034245, 'GRCh37'), 'GRCh38')) 
    Locus(contig='chr1', position=1098865, reference_genome='GRCh38')
    

Lift over the locus interval coordinates from reference genome `'GRCh37'` to
`'GRCh38'`:

    
    
    >>> hl.eval(hl.liftover(hl.locus_interval('20', 60001, 82456, True, True, 'GRCh37'), 'GRCh38')) 
    Interval(Locus(contig='chr20', position=79360, reference_genome='GRCh38'),
             Locus(contig='chr20', position=101815, reference_genome='GRCh38'),
             True,
             True)
    

See [Liftover variants from one coordinate system to
another](../guides/genetics.html#liftover-howto) for more instructions on
lifting over a Table or MatrixTable.

Notes

This function requires the reference genome of x has a chain file loaded for
dest_reference_genome. Use
[`ReferenceGenome.add_liftover()`](../genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome.add_liftover
"hail.genetics.ReferenceGenome.add_liftover") to load and attach a chain file
to a reference genome.

Returns `None` if x could not be converted.

Warning

Before using the result of `liftover()` as a new row key or column key, be
sure to filter out missing values.

Parameters:

    

  * **x** (```Expression``` of type ```tlocus``` or ```tinterval``` of ```tlocus```) – Locus or locus interval to lift over.

  * **dest_reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to convert to.

  * **min_match** (```float```) – Minimum ratio of bases that must remap.

  * **include_strand** (```bool```) – If True, output the result as a ```StructExpression``` with the first field result being the locus or locus interval and the second field is_negative_strand is a boolean indicating whether the locus or locus interval has been mapped to the negative strand of the destination reference genome. Otherwise, output the converted locus or locus interval.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – A locus or locus interval converted to
dest_reference_genome.

hail.expr.functions.min_rep(_locus_ ,
_alleles_)[[source]](../_modules/hail/expr/functions.html#min_rep)

    

Computes the minimal representation of a (locus, alleles) polymorphism.

Examples

    
    
    >>> hl.eval(hl.min_rep(hl.locus('1', 100000), ['TAA', 'TA']))
    Struct(locus=Locus(contig=1, position=100000, reference_genome=GRCh37), alleles=['TA', 'T'])
    
    
    
    >>> hl.eval(hl.min_rep(hl.locus('1', 100000), ['AATAA', 'AACAA']))
    Struct(locus=Locus(contig=1, position=100002, reference_genome=GRCh37), alleles=['T', 'C'])
    

Notes

Computing the minimal representation can cause the locus shift right (the
position can increase).

Parameters:

    

  * **locus** (```LocusExpression```)

  * **alleles** (```ArrayExpression``` of type ```tstr```)

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A
```tstruct```
expression with two fields, locus
([`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression")) and alleles
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") of type
```tstr```).

hail.expr.functions.reverse_complement(_s_ , _rna
=False_)[[source]](../_modules/hail/expr/functions.html#reverse_complement)

    

Reverses the string and translates base pairs into their complements ..
rubric:: Examples

    
    
    >>> bases = hl.literal('NNGATTACA')
    >>> hl.eval(hl.reverse_complement(bases))
    'TGTAATCNN'
    

Parameters:

    

  * **s** (```StringExpression```) – Base string.

  * **rna** (```bool```) – If `True`, pair adenine (A) with uracil (U) instead of thymine (T).

Returns:

    

[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

---

