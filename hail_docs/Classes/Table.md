## Table


_class _hail.Table[[source]](_modules/hail/table.html#Table)

    

Bases: [`BaseTable`](hail.table.BaseTable.html#hail.table.BaseTable
"hail.table.BaseTable")

Hail’s distributed implementation of a dataframe or SQL table.

Use [`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table") to read a table that was written with
`Table.write()`. Use `to_spark()` and `Table.from_spark()` to inter-operate
with PySpark’s [SQL](https://spark.apache.org/docs/latest/sql-programming-
guide.html) and [machine learning](https://spark.apache.org/docs/latest/ml-
guide.html) functionality.

Examples

The examples below use `table1` and `table2`, which are imported from text
files using [`import_table()`](methods/impex.html#hail.methods.import_table
"hail.methods.import_table").

    
    
    >>> table1 = hl.import_table('data/kt_example1.tsv', impute=True, key='ID')
    >>> table1.show()
    
    
    
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | M   |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | M   |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | F   |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | F   |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    
    
    
    >>> table2 = hl.import_table('data/kt_example2.tsv', impute=True, key='ID')
    >>> table2.show()
    
    
    
    +-------+-------+--------+
    |    ID |     A | B      |
    +-------+-------+--------+
    | int32 | int32 | str    |
    +-------+-------+--------+
    |     1 |    65 | cat    |
    |     2 |    72 | dog    |
    |     3 |    70 | mouse  |
    |     4 |    60 | rabbit |
    +-------+-------+--------+
    

Define new annotations:

    
    
    >>> height_mean_m = 68
    >>> height_sd_m = 3
    >>> height_mean_f = 65
    >>> height_sd_f = 2.5
    >>>
    >>> def get_z(height, sex):
    ...    return hl.if_else(sex == 'M',
    ...                     (height - height_mean_m) / height_sd_m,
    ...                     (height - height_mean_f) / height_sd_f)
    >>>
    >>> table1 = table1.annotate(height_z = get_z(table1.HT, table1.SEX))
    >>> table1 = table1.annotate_globals(global_field_1 = [1, 2, 3])
    

Filter rows of the table:

    
    
    >>> table2 = table2.filter(table2.B != 'rabbit')
    

Compute global aggregation statistics:

    
    
    >>> t1_stats = table1.aggregate(hl.struct(mean_c1 = hl.agg.mean(table1.C1),
    ...                                       mean_c2 = hl.agg.mean(table1.C2),
    ...                                       stats_c3 = hl.agg.stats(table1.C3)))
    >>> print(t1_stats)
    

Group by a field and aggregate to produce a new table:

    
    
    >>> table3 = (table1.group_by(table1.SEX)
    ...                 .aggregate(mean_height_data = hl.agg.mean(table1.HT)))
    >>> table3.show()
    

Join tables together inside an annotation expression:

    
    
    >>> table2 = table2.key_by('ID')
    >>> table1 = table1.annotate(B = table2[table1.ID].B)
    >>> table1.show()
    

Attributes

`globals` | Returns a struct expression including all global fields.  
---|---  
`key` | Row key struct.  
`row` | Returns a struct expression of all row-indexed fields, including keys.  
`row_value` | Returns a struct expression including all non-key row-indexed fields.  
  
Methods

`add_index` | Add the integer index of each row as a new row field.  
---|---  
`aggregate` | Aggregate over rows into a local value.  
`all` | Evaluate whether a boolean expression is true for all rows.  
`annotate` | Add new fields.  
`annotate_globals` | Add new global fields.  
`anti_join` | Filters the table to rows whose key does not appear in other.  
`any` | Evaluate whether a Boolean expression is true for at least one row.  
`cache` | Persist this table in memory.  
`checkpoint` | Checkpoint the table to disk by writing and reading.  
`collect` | Collect the rows of the table into a local list.  
`collect_by_key` | Collect values for each unique key into an array.  
`count` | Count the number of rows in the table.  
`describe` | Print information about the fields in the table.  
`distinct` | Deduplicate keys, keeping exactly one row for each unique key.  
`drop` | Drop fields from the table.  
`expand_types` | Expand complex types into structs and arrays.  
`explode` | Explode rows along a field of type array or set, copying the entire row for each element.  
`export` | Export to a text file.  
`filter` | Filter rows conditional on the value of each row's fields.  
`flatten` | Flatten nested structs.  
`from_pandas` | Create table from Pandas DataFrame  
`from_spark` | Convert PySpark SQL DataFrame to a table.  
`group_by` | Group by a new key for use with ```GroupedTable.aggregate()```.  
`head` | Subset table to first n rows.  
`index` | Expose the row values as if looked up in a dictionary, indexing with exprs.  
`index_globals` | Return this table's global variables for use in another expression context.  
`join` | Join two tables together.  
`key_by` | Key table by a new set of fields.  
`multi_way_zip_join` | Combine many tables in a zip join  
`n_partitions` | Returns the number of partitions in the table.  
`naive_coalesce` | Naively decrease the number of partitions.  
`order_by` | Sort by the specified fields, defaulting to ascending order.  
`parallelize` | Parallelize a local array of structs into a distributed table.  
`persist` | Persist this table in memory or on disk.  
`rename` | Rename fields of the table.  
`repartition` | Change the number of partitions.  
`sample` | Downsample the table by keeping each row with probability `p`.  
`select` | Select existing fields or create new fields by name, dropping the rest.  
`select_globals` | Select existing global fields or create new fields by name, dropping the rest.  
`semi_join` | Filters the table to rows whose key appears in other.  
`show` | Print the first few rows of the table to the console.  
`summarize` | Compute and print summary information about the fields in the table.  
`tail` | Subset table to last n rows.  
`take` | Collect the first n rows of the table into a local list.  
`to_matrix_table` | Construct a matrix table from a table in coordinate representation.  
`to_matrix_table_row_major` | Construct a matrix table from a table in row major representation.  
`to_pandas` | Converts this table to a Pandas DataFrame.  
`to_spark` | Converts this table to a Spark DataFrame.  
`transmute` | Add new fields and drop fields referenced.  
`transmute_globals` | Similar to `Table.annotate_globals()`, but drops referenced fields.  
`union` | Union the rows of multiple tables.  
`unpersist` | Unpersists this table from memory/disk.  
`write` | Write to disk.  
`write_many` | Write fields to distinct tables.  
  
add_index(_name ='idx'_)[[source]](_modules/hail/table.html#Table.add_index)

    

Add the integer index of each row as a new row field.

Examples

    
    
    >>> table_result = table1.add_index()
    >>> table_result.show()  
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |   idx |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | int64 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |     1 |    65 | M   |     5 |     4 |     2 |    50 |     5 |     0 |
    |     2 |    72 | M   |     6 |     3 |     2 |    61 |     1 |     1 |
    |     3 |    70 | F   |     7 |     3 |    10 |    81 |    -5 |     2 |
    |     4 |    60 | F   |     8 |     2 |    11 |    90 |   -10 |     3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    

Notes

This method returns a table with a new field whose name is given by the name
parameter, with type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64"). The value of this field is the integer index of
each row, starting from 0. Methods that respect ordering (like `Table.take()`
or `Table.export()`) will return rows in order.

This method is also helpful for creating a unique integer index for rows of a
table so that more complex types can be encoded as a simple number for
performance reasons.

Parameters:

    

**name** (_str_) – Name of index field.

Returns:

    

`Table` – Table with a new index field.

aggregate(_expr_ , __localize
=True_)[[source]](_modules/hail/table.html#Table.aggregate)

    

Aggregate over rows into a local value.

Examples

Aggregate over rows:

    
    
    >>> table1.aggregate(hl.struct(fraction_male=hl.agg.fraction(table1.SEX == 'M'),
    ...                            mean_x=hl.agg.mean(table1.X)))
    Struct(fraction_male=0.5, mean_x=6.5)
    

Note

This method supports (and expects!) aggregation over rows.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expression.

Returns:

    

_any_ – Aggregated value dependent on expr.

all(_expr_)[[source]](_modules/hail/table.html#Table.all)

    

Evaluate whether a boolean expression is true for all rows.

Examples

Test whether C1 is greater than 5 in all rows of the table:

    
    
    >>> if table1.all(table1.C1 == 5):
    ...     print("All rows have C1 equal 5.")
    

Parameters:

    

**expr**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Expression to test.

Returns:

    

[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

annotate(_** named_exprs_)[[source]](_modules/hail/table.html#Table.annotate)

    

Add new fields.

New Table fields may be defined in several ways:

  1. In terms of constant values. Every row will have the same value.

  2. In terms of other fields in the table.

  3. In terms of fields in other tables, this is called “joining”.

Examples

Consider this table:

    
    
    >>> ht = ht.drop('C1', 'C2', 'C3')
    >>> ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     2 |    72 | "M" |     6 |     3 |
    |     3 |    70 | "F" |     7 |     3 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    

Add field Y containing the square of field X

    
    
    >>> ht = ht.annotate(Y = ht.X ** 2)
    >>> ht.show()
    +-------+-------+-----+-------+-------+----------+
    |    ID |    HT | SEX |     X |     Z |        Y |
    +-------+-------+-----+-------+-------+----------+
    | int32 | int32 | str | int32 | int32 |  float64 |
    +-------+-------+-----+-------+-------+----------+
    |     1 |    65 | "M" |     5 |     4 | 2.50e+01 |
    |     2 |    72 | "M" |     6 |     3 | 3.60e+01 |
    |     3 |    70 | "F" |     7 |     3 | 4.90e+01 |
    |     4 |    60 | "F" |     8 |     2 | 6.40e+01 |
    +-------+-------+-----+-------+-------+----------+
    

Add multiple fields simultaneously:

    
    
    >>> ht = ht.annotate(
    ...     A = ht.X / 2,
    ...     B = ht.X + 21
    ... )
    >>> ht.show()
    +-------+-------+-----+-------+-------+----------+----------+-------+
    |    ID |    HT | SEX |     X |     Z |        Y |        A |     B |
    +-------+-------+-----+-------+-------+----------+----------+-------+
    | int32 | int32 | str | int32 | int32 |  float64 |  float64 | int32 |
    +-------+-------+-----+-------+-------+----------+----------+-------+
    |     1 |    65 | "M" |     5 |     4 | 2.50e+01 | 2.50e+00 |    26 |
    |     2 |    72 | "M" |     6 |     3 | 3.60e+01 | 3.00e+00 |    27 |
    |     3 |    70 | "F" |     7 |     3 | 4.90e+01 | 3.50e+00 |    28 |
    |     4 |    60 | "F" |     8 |     2 | 6.40e+01 | 4.00e+00 |    29 |
    +-------+-------+-----+-------+-------+----------+----------+-------+
    

Add a new field computed from extant fields and a small dictionary:

    
    
    >>> py_height_description = {65: 'sixty-five', 72: 'seventy-two', 70: 'seventy', 60: 'sixty'}
    >>> hail_height_description = hl.literal(py_height_description)
    >>> ht = ht.annotate(HT_DESCRIPTION=hail_height_description[ht.HT])
    >>> ht.select('HT', 'HT_DESCRIPTION').show()
    +-------+-------+----------------+
    |    ID |    HT | HT_DESCRIPTION |
    +-------+-------+----------------+
    | int32 | int32 | str            |
    +-------+-------+----------------+
    |     1 |    65 | "sixty-five"   |
    |     2 |    72 | "seventy-two"  |
    |     3 |    70 | "seventy"      |
    |     4 |    60 | "sixty"        |
    +-------+-------+----------------+
    

Add fields from another table onto this table:

    
    
    >>> ht2 = table2
    >>> ht2 = ht2.key_by('ID')
    >>> ht2.show()
    
    
    
    >>> ht = ht.key_by('ID')
    >>> ht = ht.annotate(
    ...    A=ht2[ht.key].A,
    ...    B=ht2[ht.key].B,
    ... )
    >>> ht.show()
    +-------+-------+-----+-------+-------+----------+-------+----------+
    |    ID |    HT | SEX |     X |     Z |        Y |     A | B        |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    | int32 | int32 | str | int32 | int32 |  float64 | int32 | str      |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    |     1 |    65 | "M" |     5 |     4 | 2.50e+01 |    65 | "cat"    |
    |     2 |    72 | "M" |     6 |     3 | 3.60e+01 |    72 | "dog"    |
    |     3 |    70 | "F" |     7 |     3 | 4.90e+01 |    70 | "mouse"  |
    |     4 |    60 | "F" |     8 |     2 | 6.40e+01 |    60 | "rabbit" |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    +----------------+
    | HT_DESCRIPTION |
    +----------------+
    | str            |
    +----------------+
    | "sixty-five"   |
    | "seventy-two"  |
    | "seventy"      |
    | "sixty"        |
    +----------------+
    

Instead of repeating all the fields from the other table, we may use Python’s
splat operator to indicate we want to copy all the non-key fields from the
other table:

    
    
    >>> ht = ht.annotate(**ht2[ht.key])
    >>> ht.show()
    +-------+-------+-----+-------+-------+----------+-------+----------+
    |    ID |    HT | SEX |     X |     Z |        Y |     A | B        |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    | int32 | int32 | str | int32 | int32 |  float64 | int32 | str      |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    |     1 |    65 | "M" |     5 |     4 | 2.50e+01 |    65 | "cat"    |
    |     2 |    72 | "M" |     6 |     3 | 3.60e+01 |    72 | "dog"    |
    |     3 |    70 | "F" |     7 |     3 | 4.90e+01 |    70 | "mouse"  |
    |     4 |    60 | "F" |     8 |     2 | 6.40e+01 |    60 | "rabbit" |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    +----------------+
    | HT_DESCRIPTION |
    +----------------+
    | str            |
    +----------------+
    | "sixty-five"   |
    | "seventy-two"  |
    | "seventy"      |
    | "sixty"        |
    +----------------+
    

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expressions for new fields.

Returns:

    

`Table` – Table with new fields.

annotate_globals(_**
named_exprs_)[[source]](_modules/hail/table.html#Table.annotate_globals)

    

Add new global fields.

Examples

Add a new global field:

    
    
    >>> ht = hl.utils.range_table(1)
    >>> ht = ht.annotate_globals(pops = ['EUR', 'AFR', 'EAS', 'SAS'])
    >>> ht.globals.show()
    +---------------------------+
    | <expr>.pops               |
    +---------------------------+
    | array<str>                |
    +---------------------------+
    | ["EUR","AFR","EAS","SAS"] |
    +---------------------------+
    

Global fields may be used to store metadata about an experiment:

    
    
    >>> ht = ht.annotate_globals(
    ...     study_name='HGDP+1kG',
    ...     release_date='2023-01-01'
    ... )
    

Note

This method does not support aggregation.

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expressions defining new global fields.

Returns:

    

`Table` – Table with new global field(s).

anti_join(_other_)[[source]](_modules/hail/table.html#Table.anti_join)

    

Filters the table to rows whose key does not appear in other.

Parameters:

    

**other** (`Table`) – Table with compatible key field(s).

Returns:

    

`Table`

Notes

The key type of the table must match the key type of other.

This method does not change the schema of the table; it is a method of
filtering the table to keys not present in another table.

To restrict to keys present in other, use `semi_join()`.

Examples

    
    
    >>> table_result = table1.anti_join(table2)
    

It may be expensive to key the left-side table by the right-side key. In this
case, it is possible to implement an anti-join using a non-key field as
follows:

    
    
    >>> table_result = table1.filter(hl.is_missing(table2.index(table1['ID'])))
    

See also

`semi_join()`, `filter()`

any(_expr_)[[source]](_modules/hail/table.html#Table.any)

    

Evaluate whether a Boolean expression is true for at least one row.

Examples

Test whether C1 is equal to 5 any row in any row of the table:

    
    
    >>> if table1.any(table1.C1 == 5):
    ...     print("At least one row has C1 equal 5.")
    

Parameters:

    

**expr**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Boolean expression.

Returns:

    

[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)") – `True` if the predicate evaluated for `True` for any row, otherwise
`False`.

cache()[[source]](_modules/hail/table.html#Table.cache)

    

Persist this table in memory.

Examples

Persist the table in memory:

    
    
    >>> table = table.cache() 
    

Notes

This method is an alias for `persist("MEMORY_ONLY")`.

Returns:

    

`Table` – Cached table.

checkpoint(_output_ , _overwrite =False_, _stage_locally =False_, __codec_spec
=None_, __read_if_exists =False_, __intervals =None_, __filter_intervals
=False_)[[source]](_modules/hail/table.html#Table.checkpoint)

    

Checkpoint the table to disk by writing and reading.

Parameters:

    

  * **output** (_str_) – Path at which to write.

  * **stage_locally** (_bool_) – If `True`, major output will be written to temporary local storage before being copied to `output`

  * **overwrite** (_bool_) – If `True`, overwrite an existing file at the destination.

Returns:

    

`Table`

Danger

Do not write or checkpoint to a path that is already an input source for the
query. This can cause data loss.

Notes

An alias for `write()` followed by
[`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table"). It is possible to read the file at this path later
with [`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table").

Examples

    
    
    >>> table1 = table1.checkpoint('output/table_checkpoint.ht', overwrite=True)
    

collect(__localize =True_, _*_ , __timed
=False_)[[source]](_modules/hail/table.html#Table.collect)

    

Collect the rows of the table into a local list.

Examples

Collect a list of all X records:

    
    
    >>> all_xs = [row['X'] for row in table1.select(table1.X).collect()]
    

Notes

This method returns a list whose elements are of type
```Struct```. Fields of
these structs can be accessed similarly to fields on a table, using dot
methods (`struct.foo`) or string indexing (`struct['foo']`).

Warning

Using this method can cause out of memory errors. Only collect small tables.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of ```Struct```
– List of rows.

collect_by_key(_name
='values'_)[[source]](_modules/hail/table.html#Table.collect_by_key)

    

Collect values for each unique key into an array.

Note

Requires a keyed table.

Examples

    
    
    >>> t1 = hl.Table.parallelize([
    ...     {'t': 'foo', 'x': 4, 'y': 'A'},
    ...     {'t': 'bar', 'x': 2, 'y': 'B'},
    ...     {'t': 'bar', 'x': -3, 'y': 'C'},
    ...     {'t': 'quam', 'x': 0, 'y': 'D'}],
    ...     hl.tstruct(t=hl.tstr, x=hl.tint32, y=hl.tstr),
    ...     key='t')
    
    
    
    >>> t1.show()
    +--------+-------+-----+
    | t      |     x | y   |
    +--------+-------+-----+
    | str    | int32 | str |
    +--------+-------+-----+
    | "bar"  |     2 | "B" |
    | "bar"  |    -3 | "C" |
    | "foo"  |     4 | "A" |
    | "quam" |     0 | "D" |
    +--------+-------+-----+
    
    
    
    >>> t1.collect_by_key().show()
    +--------+---------------------------------+
    | t      | values                          |
    +--------+---------------------------------+
    | str    | array<struct{x: int32, y: str}> |
    +--------+---------------------------------+
    | "bar"  | [(2,"B"),(-3,"C")]              |
    | "foo"  | [(4,"A")]                       |
    | "quam" | [(0,"D")]                       |
    +--------+---------------------------------+
    

Notes

The order of the values array is not guaranteed.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Field name for all values per key.

Returns:

    

`Table`

count()[[source]](_modules/hail/table.html#Table.count)

    

Count the number of rows in the table.

Examples

Count the number of rows in a table loaded from ‘data/kt_example1.tsv’. Each
line of the TSV becomes one row in the Hail Table.

    
    
    >>> ht = hl.import_table('data/kt_example1.tsv', impute=True)
    >>> ht.count()
    4
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") – The number of rows in the table.

describe(_handler= <built-in function print>_, _*_ ,
_widget=False_)[[source]](_modules/hail/table.html#Table.describe)

    

Print information about the fields in the table.

Note

The widget argument is **experimental**.

Parameters:

    

  * **handler** (_Callable[[str], None]_) – Handler function for returned string.

  * **widget** (_bool_) – Create an interactive IPython widget.

distinct()[[source]](_modules/hail/table.html#Table.distinct)

    

Deduplicate keys, keeping exactly one row for each unique key.

Note

Requires a keyed table.

Examples

    
    
    >>> t1 = hl.Table.parallelize([
    ...     {'a': 'foo', 'b': 1},
    ...     {'a': 'bar', 'b': 5},
    ...     {'a': 'bar', 'b': 2}],
    ...     hl.tstruct(a=hl.tstr, b=hl.tint32),
    ...     key='a')
    
    
    
    >>> t1.show()
    +-------+-------+
    | a     |     b |
    +-------+-------+
    | str   | int32 |
    +-------+-------+
    | "bar" |     5 |
    | "bar" |     2 |
    | "foo" |     1 |
    +-------+-------+
    
    
    
    >>> t1.distinct().show()
    +-------+-------+
    | a     |     b |
    +-------+-------+
    | str   | int32 |
    +-------+-------+
    | "bar" |     5 |
    | "foo" |     1 |
    +-------+-------+
    

Notes

The row chosen per distinct key is not guaranteed.

Returns:

    

`Table`

drop(_* exprs_)[[source]](_modules/hail/table.html#Table.drop)

    

Drop fields from the table.

Examples

Drop fields C1 and C2 using strings:

    
    
    >>> table_result = table1.drop('C1', 'C2')
    

Drop fields C1 and C2 using field references:

    
    
    >>> table_result = table1.drop(table1.C1, table1.C2)
    

Drop a list of fields:

    
    
    >>> fields_to_drop = ['C1', 'C2']
    >>> table_result = table1.drop(*fields_to_drop)
    

Notes

This method can be used to drop global or row-indexed fields. The arguments
can be either strings (`'field'`), or top-level field references
(`table.field` or `table['field']`).

Parameters:

    

**exprs** (varargs of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or [`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Names of fields to drop or field reference
expressions.

Returns:

    

`Table` – Table without specified fields.

expand_types()[[source]](_modules/hail/table.html#Table.expand_types)

    

Expand complex types into structs and arrays.

Examples

    
    
    >>> table_result = table1.expand_types()
    

Notes

Expands the following types: [`tlocus`](types.html#hail.expr.types.tlocus
"hail.expr.types.tlocus"), [`tinterval`](types.html#hail.expr.types.tinterval
"hail.expr.types.tinterval"), [`tset`](types.html#hail.expr.types.tset
"hail.expr.types.tset"), [`tdict`](types.html#hail.expr.types.tdict
"hail.expr.types.tdict"), [`ttuple`](types.html#hail.expr.types.ttuple
"hail.expr.types.ttuple").

The only types that will remain after this method are:
```tbool```,
```tint32```,
```tint64```,
```tfloat64```,
```tfloat32```,
```tarray```,
```tstruct```.

Note, expand_types always returns an unkeyed table.

Returns:

    

`Table` – Expanded table.

explode(_field_ , _name
=None_)[[source]](_modules/hail/table.html#Table.explode)

    

Explode rows along a field of type array or set, copying the entire row for
each element.

Examples

people_table is a `Table` with three fields: Name, Age and Children.

    
    
    >>> people_table.show()
    +------------+-------+--------------------------+
    | Name       |   Age | Children                 |
    +------------+-------+--------------------------+
    | str        | int32 | array<str>               |
    +------------+-------+--------------------------+
    | "Alice"    |    34 | ["Dave","Ernie","Frank"] |
    | "Bob"      |    51 | ["Gaby","Helen"]         |
    | "Caroline" |    10 | []                       |
    +------------+-------+--------------------------+
    

`Table.explode()` can be used to produce a distinct row for each element in
the Children field:

    
    
    >>> exploded = people_table.explode('Children')
    >>> exploded.show() 
    +---------+-------+----------+
    | Name    |   Age | Children |
    +---------+-------+----------+
    | str     | int32 | str      |
    +---------+-------+----------+
    | "Alice" |    34 | "Dave"   |
    | "Alice" |    34 | "Ernie"  |
    | "Alice" |    34 | "Frank"  |
    | "Bob"   |    51 | "Gaby"   |
    | "Bob"   |    51 | "Helen"  |
    +---------+-------+----------+
    

The name parameter can be used to produce more appropriate field names:

    
    
    >>> exploded = people_table.explode('Children', name='Child')
    >>> exploded.show() 
    +---------+-------+---------+
    | Name    |   Age | Child   |
    +---------+-------+---------+
    | str     | int32 | str     |
    +---------+-------+---------+
    | "Alice" |    34 | "Dave"  |
    | "Alice" |    34 | "Ernie" |
    | "Alice" |    34 | "Frank" |
    | "Bob"   |    51 | "Gaby"  |
    | "Bob"   |    51 | "Helen" |
    +---------+-------+---------+
    

Notes

Each row is copied for each element of field. The explode operation unpacks
the elements in a field of type `array` or `set` into its own row. If an empty
`array` or `set` is exploded, the entire row is removed from the table. In the
example above, notice that the name “Caroline” is not found in the exploded
table.

Missing arrays or sets are treated as empty.

Currently, the name argument may not be used if field is not a top-level field
of the table (e.g. name may be used with `ht.foo` but not `ht.foo.bar`).

Parameters:

    

  * **field** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Top-level field name or expression.

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or None) – If not None, rename the exploded field to name.

Returns:

    

`Table`

export(_output_ , _types_file =None_, _header =True_, _parallel =None_,
_delimiter ='\t'_)[[source]](_modules/hail/table.html#Table.export)

    

Export to a text file.

Examples

Export to a tab-separated file:

    
    
    >>> table1.export('output/table1.tsv.bgz')
    

Note

It is highly recommended to export large files with a `.bgz` extension, which
will use a block gzipped compression codec. These files can be read natively
with any Hail method, as well as with Python’s `gzip.open` and R’s
`read.table`.

Nested structures will be exported as JSON. In order to export nested struct
fields as separate fields in the resulting table, use `flatten()` first.

Warning

Do not export to a path that is being read from in the same pipeline.

See also

`flatten()`, `write()`

Parameters:

    

  * **output** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – URI at which to write exported file.

  * **types_file** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – URI at which to write file containing field type information.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Include a header in the file.

  * **parallel** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – If None, a single file is produced, otherwise a folder of file shards is produced. If ‘separate_header’, the header file is output separately from the file shards. If ‘header_per_shard’, each file shard has a header. If set to None the export will be slower.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Field delimiter.

filter(_expr_ , _keep
=True_)[[source]](_modules/hail/table.html#Table.filter)

    

Filter rows conditional on the value of each row’s fields.

Note

Hail will can read much less data if a Table filter condition references the
key field and the Table is stored in Hail native format (i.e. read using
[`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table"), _not_
[`import_table()`](methods/impex.html#hail.methods.import_table
"hail.methods.import_table")). In other words: filtering on the key will make
a pipeline faster by reading fewer rows. This optimization is prevented by
certain operations appearing between a
[`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table") and a `filter()`. For example, a key_by and
group_by, both force reading all the data.

Suppose we previously `write()` a Hail Table with one million rows keyed by a
field called idx. If we filter this table to one value of idx, the pipeline
will be fast because we read only the rows that have that value of idx:

    
    
    >>> ht = hl.read_table('large-table.ht')  
    >>> ht = ht.filter(ht.idx == 5)  
    

This also works with inequality conditions:

    
    
    >>> ht = hl.read_table('large-table.ht')  
    >>> ht = ht.filter(ht.idx <= 5)  
    

Examples

Consider this table:

    
    
    >>> ht = ht.drop('C1', 'C2', 'C3')
    >>> ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     2 |    72 | "M" |     6 |     3 |
    |     3 |    70 | "F" |     7 |     3 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    

Keep rows where `Z` is 3:

    
    
    >>> filtered_ht = ht.filter(ht.Z == 3)
    >>> filtered_ht.show()
    

ID | HT | SEX | X | Z  
---|---|---|---|---  
int32 | int32 | str | int32 | int32  
2 3 | 72 70 | “M” “F” | 6 7 | 3 3  
  
Remove rows where `Z` is 3:

    
    
    >>> filtered_ht = ht.filter(ht.Z == 3, keep=False)
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    

Keep rows where X is less than 7 and Z is greater than 2:

    
    
    >>> filtered_ht = ht.filter(hl.all(
    ...     ht.X < 7,
    ...     ht.Z > 2
    ... ))
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     2 |    72 | "M" |     6 |     3 |
    +-------+-------+-----+-------+-------+
    

Keep rows where X is less than 7 or Z is greater than 2:

    
    
    >>> filtered_ht = ht.filter(hl.any(
    ...     ht.X < 7,
    ...     ht.Z > 2
    ... ))
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     2 |    72 | "M" |     6 |     3 |
    |     3 |    70 | "F" |     7 |     3 |
    +-------+-------+-----+-------+-------+
    

Keep “M” rows where `HT` is less than 72 and “F” rows where `HT` is less than
65:

    
    
    >>> filtered_ht = ht.filter(
    ...     hl.if_else(
    ...         ht.SEX == "M",
    ...         ht.HT < 72,
    ...         ht.HT < 65
    ...     )
    ... )
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    

Notice that if the condition evaluates to missing, the row is _always_ removed
regardless of the setting of keep:

    
    
    >>> ht2 = ht
    >>> ht2 = ht.annotate(X = hl.or_missing(ht.X != 5, ht.X))
    >>> ht2.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |    NA |     4 |
    |     2 |    72 | "M" |     6 |     3 |
    |     3 |    70 | "F" |     7 |     3 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    >>> filtered_ht = ht2.filter(ht2.X < 7, keep=True)
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     2 |    72 | "M" |     6 |     3 |
    +-------+-------+-----+-------+-------+
    >>> filtered_ht = ht2.filter(ht2.X < 7, keep=False)
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     3 |    70 | "F" |     7 |     3 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    

Notes

The expression expr will be evaluated for every row of the table. If keep is
`True`, then rows where expr evaluates to `True` will be kept (the filter
removes the rows where the predicate evaluates to `False`). If keep is
`False`, then rows where expr evaluates to `True` will be removed (the filter
keeps the rows where the predicate evaluates to `False`).

Warning

When expr evaluates to missing, the row will be removed regardless of keep.

Note

This method does not support aggregation.

Parameters:

    

  * **expr** (bool or ```BooleanExpression```) – Filter expression.

  * **keep** (_bool_) – Keep rows where expr is true.

Returns:

    

`Table` – Filtered table.

flatten()[[source]](_modules/hail/table.html#Table.flatten)

    

Flatten nested structs.

Examples

Flatten table:

    
    
    >>> table_result = table1.flatten()
    

Notes

Consider a table with signature

    
    
    a: struct{
        p: int32,
        q: str
    },
    b: int32,
    c: struct{
        x: str,
        y: array<struct{
            y: str,
            z: str
        }>
    }
    

and key `a`. The result of flatten is

    
    
    a.p: int32
    a.q: str
    b: int32
    c.x: str
    c.y: array<struct{
        y: str,
        z: str
    }>
    

with key `a.p, a.q`.

Note, structures inside collections like arrays or sets will not be flattened.

Note, the result of flatten is always unkeyed.

Warning

Flattening a table will produces fields that cannot be referenced using the
`table.<field>` syntax, e.g. “a.b”. Reference these fields using square
bracket lookups: `table['a.b']`.

Returns:

    

`Table` – Table with a flat schema (no struct fields).

_static _from_pandas(_df_ , _key
=[]_)[[source]](_modules/hail/table.html#Table.from_pandas)

    

Create table from Pandas DataFrame

Examples

    
    
    >>> t = hl.Table.from_pandas(df) 
    

Parameters:

    

  * **df** ([`pandas.DataFrame`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html#pandas.DataFrame "\(in pandas v2.3.0\)")) – Pandas DataFrame.

  * **key** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Key fields.

Returns:

    

`Table`

_static _from_spark(_df_ , _key
=[]_)[[source]](_modules/hail/table.html#Table.from_spark)

    

Convert PySpark SQL DataFrame to a table.

Examples

    
    
    >>> t = Table.from_spark(df) 
    

Notes

Spark SQL data types are converted to Hail types as follows:

    
    
    BooleanType => :py``.tbool``
    IntegerType => :py``.tint32``
    LongType => :py``.tint64``
    FloatType => :py``.tfloat32``
    DoubleType => :py``.tfloat64``
    StringType => :py``.tstr``
    BinaryType => ``.TBinary``
    ArrayType => ``.tarray``
    StructType => ``.tstruct``
    

Unlisted Spark SQL data types are currently unsupported.

Parameters:

    

  * **df** ([`pyspark.sql.DataFrame`](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/api/pyspark.sql.DataFrame.html#pyspark.sql.DataFrame "\(in PySpark vmaster\)")) – PySpark DataFrame.

  * **key** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Key fields.

Returns:

    

`Table` – Table constructed from the Spark SQL DataFrame.

_property _globals

    

Returns a struct expression including all global fields.

Examples

The data type of the globals struct:

    
    
    >>> table1.globals.dtype
    dtype('struct{global_field_1: int32, global_field_2: int32}')
    

The number of global fields:

    
    
    >>> len(table1.globals)
    2
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all global fields.

group_by(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/table.html#Table.group_by)

    

Group by a new key for use with
[`GroupedTable.aggregate()`](hail.GroupedTable.html#hail.GroupedTable.aggregate
"hail.GroupedTable.aggregate").

Examples

Compute the mean value of X and the sum of Z per unique ID:

    
    
    >>> table_result = (table1.group_by(table1.ID)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X), sumZ = hl.agg.sum(table1.Z)))
    

Group by a height bin and compute sex ratio per bin:

    
    
    >>> table_result = (table1.group_by(height_bin = table1.HT // 20)
    ...                       .aggregate(fraction_female = hl.agg.fraction(table1.SEX == 'F')))
    

Notes

This function is always followed by
[`GroupedTable.aggregate()`](hail.GroupedTable.html#hail.GroupedTable.aggregate
"hail.GroupedTable.aggregate"). Follow the link for documentation on the
aggregation step.

Note

**Using group_by**

**group_by** and its sibling methods
([`MatrixTable.group_rows_by()`](hail.MatrixTable.html#hail.MatrixTable.group_rows_by
"hail.MatrixTable.group_rows_by") and
[`MatrixTable.group_cols_by()`](hail.MatrixTable.html#hail.MatrixTable.group_cols_by
"hail.MatrixTable.group_cols_by")) accept both variable-length (`f(x, y, z)`)
and keyword (`f(a=x, b=y, c=z)`) arguments.

Variable-length arguments can be either strings or expressions that reference
a (possibly nested) field of the table. Keyword arguments can be arbitrary
expressions.

**The following three usages are all equivalent** , producing a
```GroupedTable```
grouped by fields C1 and C2 of table1.

First, variable-length string arguments:

    
    
    >>> table_result = (table1.group_by('C1', 'C2')
    ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    

Second, field reference variable-length arguments:

    
    
    >>> table_result = (table1.group_by(table1.C1, table1.C2)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    

Last, expression keyword arguments:

    
    
    >>> table_result = (table1.group_by(C1 = table1.C1, C2 = table1.C2)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    

Additionally, the variable-length argument syntax also permits nested field
references. Given the following struct field s:

    
    
    >>> table3 = table1.annotate(s = hl.struct(x=table1.X, z=table1.Z))
    

The following two usages are equivalent, grouping by one field, x:

    
    
    >>> table_result = (table3.group_by(table3.s.x)
    ...                       .aggregate(meanX = hl.agg.mean(table3.X)))
    
    
    
    >>> table_result = (table3.group_by(x = table3.s.x)
    ...                       .aggregate(meanX = hl.agg.mean(table3.X)))
    

The keyword argument syntax permits arbitrary expressions:

    
    
    >>> table_result = (table1.group_by(foo=table1.X ** 2 + 1)
    ...                       .aggregate(meanZ = hl.agg.mean(table1.Z)))
    

These syntaxes can be mixed together, with the stipulation that all keyword
arguments must come at the end due to Python language restrictions.

    
    
    >>> table_result = (table1.group_by(table1.C1, 'C2', height_bin = table1.HT // 20)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    

Note

This method does not support aggregation in key expressions.

Parameters:

    

  * **exprs** (varargs of type str or ```Expression```) – Field names or field reference expressions.

  * **named_exprs** (keyword args of type ```Expression```) – Field names and expressions to compute them.

Returns:

    

```GroupedTable```
– Grouped table; use
[`GroupedTable.aggregate()`](hail.GroupedTable.html#hail.GroupedTable.aggregate
"hail.GroupedTable.aggregate") to complete the aggregation.

head(_n_)[[source]](_modules/hail/table.html#Table.head)

    

Subset table to first n rows.

Examples

Subset to the first three rows:

    
    
    >>> ht = hl.import_table('data/kt_example1.tsv', impute=True)
    >>> ht.head(3).show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Notes

The number of partitions in the new table is equal to the number of partitions
containing the first n rows.

Parameters:

    

**n** (_int_) – Number of rows to include.

Returns:

    

`Table` – Table limited to the first n rows.

index(_* exprs_, _all_matches
=False_)[[source]](_modules/hail/table.html#Table.index)

    

Expose the row values as if looked up in a dictionary, indexing with exprs.

Examples

In the example below, both table1 and table2 are keyed by one field ID of type
`int`.

    
    
    >>> table_result = table1.select(B = table2.index(table1.ID).B)
    >>> table_result.B.show()
    +-------+----------+
    |    ID | B        |
    +-------+----------+
    | int32 | str      |
    +-------+----------+
    |     1 | "cat"    |
    |     2 | "dog"    |
    |     3 | "mouse"  |
    |     4 | "rabbit" |
    +-------+----------+
    

Using key as the sole index expression is equivalent to passing all key fields
individually:

    
    
    >>> table_result = table1.select(B = table2.index(table1.key).B)
    

It is also possible to use non-key fields or expressions as the index
expressions:

    
    
    >>> table_result = table1.select(B = table2.index(table1.C1 % 4).B)
    >>> table_result.show()
    +-------+---------+
    |    ID | B       |
    +-------+---------+
    | int32 | str     |
    +-------+---------+
    |     1 | "dog"   |
    |     2 | "dog"   |
    |     3 | "dog"   |
    |     4 | "mouse" |
    +-------+---------+
    

Notes

`Table.index()` is used to expose one table’s fields for use in expressions
involving the another table or matrix table’s fields. The result of the method
call is a struct expression that is usable in the same scope as exprs, just as
if exprs were used to look up values of the table in a dictionary.

The type of the struct expression is the same as the indexed table’s
`row_value()` (the key fields are removed, as they are available in the form
of the index expressions).

Note

There is a shorthand syntax for `Table.index()` using square brackets (the
Python `__getitem__` syntax). This syntax is preferred.

    
    
    >>> table_result = table1.select(B = table2[table1.ID].B)
    

Parameters:

    

  * **exprs** (variable-length args of ```Expression```) – Index expressions.

  * **all_matches** (_bool_) – Experimental. If `True`, value of expression is array of all matches.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

index_globals()[[source]](_modules/hail/table.html#Table.index_globals)

    

Return this table’s global variables for use in another expression context.

Examples

    
    
    >>> table_result = table2.annotate(C = table2.A * table1.index_globals().global_field_1)
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

join(_right_ , _how='inner'_ , __mangle= <function Table.<lambda>>_,
__join_key=None_)[[source]](_modules/hail/table.html#Table.join)

    

Join two tables together.

Examples

Join table1 to table2 to produce table_joined:

    
    
    >>> table_joined = table1.key_by('ID').join(table2.key_by('ID'))
    

Notes

Tables are joined at rows whose key fields have equal values. Missing values
never match. The inclusion of a row with no match in the opposite table
depends on the join type:

  * **inner** – Only rows with a matching key in the opposite table are included in the resulting table.

  * **left** – All rows from the left table are included in the resulting table. If a row in the left table has no match in the right table, then the fields derived from the right table will be missing.

  * **right** – All rows from the right table are included in the resulting table. If a row in the right table has no match in the left table, then the fields derived from the left table will be missing.

  * **outer** – All rows are included in the resulting table. If a row in the right table has no match in the left table, then the fields derived from the left table will be missing. If a row in the right table has no match in the left table, then the fields derived from the left table will be missing.

Both tables must have the same number of keys and the corresponding types of
each key must be the same (order matters), but the key names can be different.
For example, if table1 is keyed by fields `['a', 'b']`, both of type `int32`,
and table2 is keyed by fields `['c', 'd']`, both of type `int32`, then the two
tables can be joined (their rows will be joined where `table1.a == table2.c`
and `table1.b == table2.d`).

The key fields and order from the left table are preserved, while the key
fields from the right table are not present in the result.

Note

These join methods implement a traditional [Cartesian
product](https://en.wikipedia.org/wiki/Cartesian_product) join, and the number
of records in the resulting table can be larger than the number of records on
the left or right if duplicate keys are present.

Parameters:

    

  * **right** (`Table`) – Table to join.

  * **how** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Join type. One of “inner”, “left”, “right”, “outer”

Returns:

    

`Table` – Joined table.

_property _key

    

Row key struct.

Examples

List of key field names:

    
    
    >>> list(table1.key)
    ['ID']
    

Number of key fields:

    
    
    >>> len(table1.key)
    1
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

key_by(_* keys_, _**
named_keys_)[[source]](_modules/hail/table.html#Table.key_by)

    

Key table by a new set of fields.

Table keys control both the order of the rows in the table and the ability to
join or annotate one table with the information in another table.

Examples

Consider a simple unkeyed table. Its rows appear are guaranteed to appear in
the same order as they were in the source text file.

    
    
    >>> ht = hl.import_table('data/kt_example1.tsv', impute=True)
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Changing the key forces the rows to appear in ascending order. For this
reason, `key_by()` is a relatively expensive operation. It must sort the
entire dataset.

    
    
    >>> ht = ht.key_by('HT')
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Suppose that ht represents some human subjects in an experiment. We might need
to combine sample metadata from ht with sample metadata from another source.
For example:

    
    
    >>> ht2 = hl.import_table('data/kt_example2.tsv', impute=True)
    >>> ht2 = ht2.key_by('ID')
    >>> ht2.show()
    +-------+-------+----------+
    |    ID |     A | B        |
    +-------+-------+----------+
    | int32 | int32 | str      |
    +-------+-------+----------+
    |     1 |    65 | "cat"    |
    |     2 |    72 | "dog"    |
    |     3 |    70 | "mouse"  |
    |     4 |    60 | "rabbit" |
    +-------+-------+----------+
    >>> combined_ht = ht
    >>> combined_ht = combined_ht.key_by('ID')
    >>> combined_ht = combined_ht.annotate(favorite_pet = ht2[combined_ht.key].B)
    >>> combined_ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 | favorite_pet |
    +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | str          |
    +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 | "cat"        |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 | "dog"        |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 | "mouse"      |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 | "rabbit"     |
    +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
    

Hail supports compound keys which enforce a dictionary ordering on the rows of
the Table.

    
    
    >>> ht = ht.key_by('SEX', 'HT')
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

A key may also be shortened by removing some fields. The ordering of two rows
with the same key is undefined. You should not rely on them appearing in any
particular order.

    
    
    >>> ht = ht.key_by('SEX')
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Key fields may also be a complex expression:

    
    
    >>> ht = ht.key_by(C4 = ht.X + ht.Z)
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |    C4 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |     9 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |     9 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |    10 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |    10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    

The key can be “removed” or set to the empty key. The ordering of the rows in
a table without a key is undefined.

    
    
    >>> ht = ht.key_by()
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |    C4 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |     9 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |     9 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |    10 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |    10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    

Notes

This method is used to specify all the fields of a new row key. The old key
fields may be overwritten by newly-assigned fields, as described in
`Table.annotate()`. If not overwritten, they are preserved as non-key fields.

See `Table.select()` for more information about how to define new key fields.

Parameters:

    

**keys** (varargs of type
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – Field(s) to key by.

Returns:

    

`Table` – Table with a new key.

_static _multi_way_zip_join(_tables_ , _data_field_name_ ,
_global_field_name_)[[source]](_modules/hail/table.html#Table.multi_way_zip_join)

    

Combine many tables in a zip join

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

Notes

The row type of the returned table is a struct with the key fields, and one
extra field, data_field_name, which is an array of structs with the non key
fields, one per input. The array elements are missing if their corresponding
input had no row with that key or possibly if there is another input with more
rows with that key than the corresponding input.

The global type of the returned table is an array of structs of the global
type of all of the inputs.

The types for every input must be identical, not merely compatible, including
the keys.

A zip join is similar to an outer join however rows are not duplicated to
create the full Cartesian product of duplicate keys. Instead, there is exactly
one entry in some data_field_name array for every row in the inputs.

The `multi_way_zip_join()` method assumes that inputs have distinct keys. If
any input has duplicate keys, the row value that is included in the result
array for that key is undefined.

Parameters:

    

  * **tables** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of `Table`) – A list of tables to combine

  * **data_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The name of the resulting data field

  * **global_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The name of the resulting global field

n_partitions()[[source]](_modules/hail/table.html#Table.n_partitions)

    

Returns the number of partitions in the table.

Examples

Range tables can be constructed with an explicit number of partitions:

    
    
    >>> ht = hl.utils.range_table(100, n_partitions=10)
    >>> ht.n_partitions()
    10
    

Small files are often imported with one partition:

    
    
    >>> ht2 = hl.import_table('data/coordinate_matrix.tsv', impute=True)
    >>> ht2.n_partitions()
    1
    

The min_partitions argument to
[`import_table()`](methods/impex.html#hail.methods.import_table
"hail.methods.import_table") forces more partitions, but it can produce empty
partitions. Empty partitions do not affect correctness but introduce
unnecessary extra bookkeeping that slows down the pipeline.

    
    
    >>> ht2 = hl.import_table('data/coordinate_matrix.tsv', impute=True, min_partitions=10)
    >>> ht2.n_partitions()
    10
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") – Number of partitions.

naive_coalesce(_max_partitions_)[[source]](_modules/hail/table.html#Table.naive_coalesce)

    

Naively decrease the number of partitions.

Example

Naively repartition to 10 partitions:

    
    
    >>> table_result = table1.naive_coalesce(10)
    

Warning

`naive_coalesce()` simply combines adjacent partitions to achieve the desired
number. It does not attempt to rebalance, unlike `repartition()`, so it can
produce a heavily unbalanced dataset. An unbalanced dataset can be inefficient
to operate on because the work is not evenly distributed across partitions.

Parameters:

    

**max_partitions** (_int_) – Desired number of partitions. If the current
number of partitions is less than or equal to max_partitions, do nothing.

Returns:

    

`Table` – Table with at most max_partitions partitions.

order_by(_* exprs_)[[source]](_modules/hail/table.html#Table.order_by)

    

Sort by the specified fields, defaulting to ascending order. Will unkey the
table if it is keyed.

Examples

Let’s assume we have a field called HT in our table.

By default, ascending order is used:

    
    
    >>> sorted_table = table1.order_by(table1.HT)
    
    
    
    >>> sorted_table = table1.order_by('HT')
    

You can sort in ascending order explicitly:

    
    
    >>> sorted_table = table1.order_by(hl.asc(table1.HT))
    
    
    
    >>> sorted_table = table1.order_by(hl.asc('HT'))
    

Tables can be sorted by field descending order as well:

    
    
    >>> sorted_table = table1.order_by(hl.desc(table1.HT))
    
    
    
    >>> sorted_table = table1.order_by(hl.desc('HT'))
    

Tables can also be sorted on multiple fields:

    
    
    >>> sorted_table = table1.order_by(hl.desc('HT'), hl.asc('SEX'))
    

Notes

Missing values are sorted after non-missing values. When multiple fields are
passed, the table will be sorted first by the first argument, then the second,
etc.

Note

This method unkeys the table.

Parameters:

    

**exprs** (varargs of ```asc()```,
```desc()```,
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"), or
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – Fields to sort by.

Returns:

    

`Table` – Table sorted by the given fields.

_classmethod _parallelize(_rows_ , _schema =None_, _key =None_, _n_partitions
=None_, _*_ , _partial_type =None_, _globals
=None_)[[source]](_modules/hail/table.html#Table.parallelize)

    

Parallelize a local array of structs into a distributed table.

Examples

Parallelize a list of dictionaries into a Hail Table. The fields of the
dictionary become the fields of the Table. The schema should always be a
```tstruct```
whose fields correspond to the dictionaries’ fields.

    
    
    >>> t = hl.Table.parallelize(
    ...     [{'a': 5, 'b': 10}, {'a': 0, 'b': 200}],
    ...     schema=hl.tstruct(a=hl.tint, b=hl.tint)
    ... )
    >>> t.show()
    +-------+-------+
    |     a |     b |
    +-------+-------+
    | int32 | int32 |
    +-------+-------+
    |     5 |    10 |
    |     0 |   200 |
    +-------+-------+
    

The key parameter sets the key of the Table. Notice that the order of the rows
changes, because the rows of a Table are always appear in ascending order of
the key.

    
    
    >>> t = hl.Table.parallelize(
    ...     [{'a': 5, 'b': 10}, {'a': 0, 'b': 200}],
    ...     schema=hl.tstruct(a=hl.tint, b=hl.tint),
    ...     key='a'
    ... )
    >>> t.show()
    +-------+-------+
    |     a |     b |
    +-------+-------+
    | int32 | int32 |
    +-------+-------+
    |     0 |   200 |
    |     5 |    10 |
    +-------+-------+
    

You may also elide schema entirely and let Hail guess the type. The list
elements must either be Hail [`Struct`](utils/index.html#hail.utils.Struct
"hail.utils.Struct") or
[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python
v3.9\)") s.

    
    
    >>> t = hl.Table.parallelize(
    ...     [{'a': 5, 'b': 10}, {'a': 0, 'b': 200}],
    ...     key='a'
    ... )
    >>> t.show()
    +-------+-------+
    |     a |     b |
    +-------+-------+
    | int32 | int32 |
    +-------+-------+
    |     0 |   200 |
    |     5 |    10 |
    +-------+-------+
    

You may also specify only a handful of types in partial_type. Hail will
automatically deduce the types of the other fields. Hail _cannot_ deduce the
type of a field which only contains empty arrays (the element type is
unspecified), so we specify the type of labels explicitly.

    
    
    >>> dictionaries = [
    ...     {"number":10038,"state":"open","user":{"login":"tpoterba","site_admin":False,"id":10562794}, "milestone":None,"labels":[]},
    ...     {"number":10037,"state":"open","user":{"login":"daniel-goldstein","site_admin":False,"id":24440116},"milestone":None,"labels":[]},
    ...     {"number":10036,"state":"open","user":{"login":"jigold","site_admin":False,"id":1693348},"milestone":None,"labels":[]},
    ...     {"number":10035,"state":"open","user":{"login":"tpoterba","site_admin":False,"id":10562794},"milestone":None,"labels":[]},
    ...     {"number":10033,"state":"open","user":{"login":"tpoterba","site_admin":False,"id":10562794},"milestone":None,"labels":[]},
    ... ]
    >>> t = hl.Table.parallelize(
    ...     dictionaries,
    ...     partial_type={"milestone": hl.tstr, "labels": hl.tarray(hl.tstr)}
    ... )
    >>> t.show()
    +--------+--------+--------------------+-----------------+----------+
    | number | state  | user.login         | user.site_admin |  user.id |
    +--------+--------+--------------------+-----------------+----------+
    |  int32 | str    | str                |            bool |    int32 |
    +--------+--------+--------------------+-----------------+----------+
    |  10038 | "open" | "tpoterba"         |           False | 10562794 |
    |  10037 | "open" | "daniel-goldstein" |           False | 24440116 |
    |  10036 | "open" | "jigold"           |           False |  1693348 |
    |  10035 | "open" | "tpoterba"         |           False | 10562794 |
    |  10033 | "open" | "tpoterba"         |           False | 10562794 |
    +--------+--------+--------------------+-----------------+----------+
    +-----------+------------+
    | milestone | labels     |
    +-----------+------------+
    | str       | array<str> |
    +-----------+------------+
    | NA        | []         |
    | NA        | []         |
    | NA        | []         |
    | NA        | []         |
    | NA        | []         |
    +-----------+------------+
    

Parallelizing with a specified number of partitions:

    
    
    >>> rows = [ {'a': i} for i in range(100) ]
    >>> ht = hl.Table.parallelize(rows, n_partitions=10)
    >>> ht.n_partitions()
    10
    >>> ht.count()
    100
    

Parallelizing with some global information:

    
    
    >>> rows = [ {'a': i} for i in range(5) ]
    >>> ht = hl.Table.parallelize(rows, globals=hl.Struct(global_value=3))
    >>> ht.aggregate(hl.agg.sum(ht.global_value * ht.a))
    30
    

Warning

Parallelizing very large local arrays will be slow.

Parameters:

    

  * **rows** – List of row values, or expression of type `array<struct{...}>`.

  * **schema** (str or a hail type (see ``Types``), optional) – Value type.

  * **key** (_Union[str, List[str]]], optional_) – Key field(s).

  * **n_partitions** (_int, optional_)

  * **partial_type** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)"), optional) – A value type which may elide fields or have `None` in arbitrary places. The partial type is used by hail where the type cannot be imputed.

  * **globals** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to `any` or ```StructExpression```, optional) – A dict or struct{..} containing supplementary global data.

Returns:

    

`Table` – A distributed Hail table created from the local collection of rows.

persist(_storage_level
='MEMORY_AND_DISK'_)[[source]](_modules/hail/table.html#Table.persist)

    

Persist this table in memory or on disk.

Examples

Persist the table to both memory and disk:

    
    
    >>> table = table.persist() 
    

Notes

The `Table.persist()` and `Table.cache()` methods store the current table on
disk or in memory temporarily to avoid redundant computation and improve the
performance of Hail pipelines. This method is not a substitution for
`Table.write()`, which stores a permanent file.

Most users should use the “MEMORY_AND_DISK” storage level. See the [Spark
documentation](http://spark.apache.org/docs/latest/programming-guide.html#rdd-
persistence) for a more in-depth discussion of persisting data.

Parameters:

    

**storage_level** (_str_) – Storage level. One of: NONE, DISK_ONLY,
DISK_ONLY_2, MEMORY_ONLY, MEMORY_ONLY_2, MEMORY_ONLY_SER, MEMORY_ONLY_SER_2,
MEMORY_AND_DISK, MEMORY_AND_DISK_2, MEMORY_AND_DISK_SER,
MEMORY_AND_DISK_SER_2, OFF_HEAP

Returns:

    

`Table` – Persisted table.

rename(_mapping_)[[source]](_modules/hail/table.html#Table.rename)

    

Rename fields of the table.

Examples

Rename C1 to col1 and C2 to col2:

    
    
    >>> table_result = table1.rename({'C1' : 'col1', 'C2' : 'col2'})
    

Parameters:

    

**mapping** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict
"\(in Python v3.9\)") of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)"), [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Mapping from old field names to new field names.

Notes

Any field that does not appear as a key in mapping will not be renamed.

Returns:

    

`Table` – Table with renamed fields.

repartition(_n_ , _shuffle
=True_)[[source]](_modules/hail/table.html#Table.repartition)

    

Change the number of partitions.

Examples

Repartition to 500 partitions:

    
    
    >>> table_result = table1.repartition(500)
    

Notes

Check the current number of partitions with `n_partitions()`.

The data in a dataset is divided into chunks called partitions, which may be
stored together or across a network, so that each partition may be read and
processed in parallel by available cores. When a table with \\(M\\) rows is
first imported, each of the \\(k\\) partitions will contain about \\(M/k\\) of
the rows. Since each partition has some computational overhead, decreasing the
number of partitions can improve performance after significant filtering.
Since it’s recommended to have at least 2 - 4 partitions per core, increasing
the number of partitions can allow one to take advantage of more cores.
Partitions are a core concept of distributed computation in Spark, see [their
documentation](http://spark.apache.org/docs/latest/programming-
guide.html#resilient-distributed-datasets-rdds) for details.

When `shuffle=True`, Hail does a full shuffle of the data and creates equal
sized partitions. When `shuffle=False`, Hail combines existing partitions to
avoid a full shuffle. These algorithms correspond to the repartition and
coalesce commands in Spark, respectively. In particular, when `shuffle=False`,
`n_partitions` cannot exceed current number of partitions.

Parameters:

    

  * **n** (_int_) – Desired number of partitions.

  * **shuffle** (_bool_) – If `True`, use full shuffle to repartition.

Returns:

    

`Table` – Repartitioned table.

_property _row

    

Returns a struct expression of all row-indexed fields, including keys.

Examples

The data type of the row struct:

    
    
    >>> table1.row.dtype
    dtype('struct{ID: int32, HT: int32, SEX: str, X: int32, Z: int32, C1: int32, C2: int32, C3: int32}')
    

The number of row fields:

    
    
    >>> len(table1.row)
    8
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all row fields, including key
fields.

_property _row_value

    

Returns a struct expression including all non-key row-indexed fields.

Examples

The data type of the row struct:

    
    
    >>> table1.row_value.dtype
    dtype('struct{HT: int32, SEX: str, X: int32, Z: int32, C1: int32, C2: int32, C3: int32}')
    

The number of row fields:

    
    
    >>> len(table1.row_value)
    7
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all non-key row fields.

sample(_p_ , _seed =None_)[[source]](_modules/hail/table.html#Table.sample)

    

Downsample the table by keeping each row with probability `p`.

Examples

Downsample the table to approximately 1% of its rows.

    
    
    >>> table1.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    >>> small_table1 = table1.sample(0.75, seed=0)
    >>> small_table1.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    >>> small_table1 = table1.sample(0.25, seed=4)
    >>> small_table1.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Parameters:

    

  * **p** ([`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Probability of keeping each row.

  * **seed** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Random seed.

Returns:

    

`Table` – Table with approximately `p * n_rows` rows.

select(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/table.html#Table.select)

    

Select existing fields or create new fields by name, dropping the rest.

Examples

Select a few old fields and compute a new one:

    
    
    >>> table_result = table1.select(table1.C1, Y=table1.Z - table1.X)
    

Notes

This method creates new row-indexed fields. If a created field shares its name
with a global field of the table, the method will fail.

Note

**Using select**

Select and its sibling methods (`Table.select_globals()`,
[`MatrixTable.select_globals()`](hail.MatrixTable.html#hail.MatrixTable.select_globals
"hail.MatrixTable.select_globals"),
[`MatrixTable.select_rows()`](hail.MatrixTable.html#hail.MatrixTable.select_rows
"hail.MatrixTable.select_rows"),
[`MatrixTable.select_cols()`](hail.MatrixTable.html#hail.MatrixTable.select_cols
"hail.MatrixTable.select_cols"), and
[`MatrixTable.select_entries()`](hail.MatrixTable.html#hail.MatrixTable.select_entries
"hail.MatrixTable.select_entries")) accept both variable-length (`f(x, y, z)`)
and keyword (`f(a=x, b=y, c=z)`) arguments.

Select methods will always preserve the key along that axis; e.g. for
`Table.select()`, the table key will aways be kept. To modify the key, use
`key_by()`.

Variable-length arguments can be either strings or expressions that reference
a (possibly nested) field of the table. Keyword arguments can be arbitrary
expressions.

**The following three usages are all equivalent** , producing a new table with
fields C1 and C2 of table1, and the table key ID.

First, variable-length string arguments:

    
    
    >>> table_result = table1.select('C1', 'C2')
    

Second, field reference variable-length arguments:

    
    
    >>> table_result = table1.select(table1.C1, table1.C2)
    

Last, expression keyword arguments:

    
    
    >>> table_result = table1.select(C1 = table1.C1, C2 = table1.C2)
    

Additionally, the variable-length argument syntax also permits nested field
references. Given the following struct field s:

    
    
    >>> table3 = table1.annotate(s = hl.struct(x=table1.X, z=table1.Z))
    

The following two usages are equivalent, producing a table with one field, x.:

    
    
    >>> table3_result = table3.select(table3.s.x)
    
    
    
    >>> table3_result = table3.select(x = table3.s.x)
    

The keyword argument syntax permits arbitrary expressions:

    
    
    >>> table_result = table1.select(foo=table1.X ** 2 + 1)
    

These syntaxes can be mixed together, with the stipulation that all keyword
arguments must come at the end due to Python language restrictions.

    
    
    >>> table_result = table1.select(table1.X, 'Z', bar = [table1.C1, table1.C2])
    

Note

This method does not support aggregation.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`Table` – Table with specified fields.

select_globals(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/table.html#Table.select_globals)

    

Select existing global fields or create new fields by name, dropping the rest.

Examples

Selecting two global fields, one by name and one new one, replacing any
previously annotated global fields.

    
    
    >>> ht = hl.utils.range_table(1)
    >>> ht = ht.annotate_globals(pops = ['EUR', 'AFR', 'EAS', 'SAS'])
    >>> ht = ht.annotate_globals(study_name = 'HGDP+1kg')
    >>> ht.describe()
    ----------------------------------------
    Global fields:
        'pops': array<str>
        'study_name': str
    ----------------------------------------
    Row fields:
        'idx': int32
    ----------------------------------------
    Key: ['idx']
    ----------------------------------------
    >>> ht = ht.select_globals(ht.pops, target_date='2025-01-01')
    >>> ht.describe()
    ----------------------------------------
    Global fields:
        'pops': array<str>
        'target_date': str
    ----------------------------------------
    Row fields:
        'idx': int32
    ----------------------------------------
    Key: ['idx']
    ----------------------------------------
    

Fields may also be selected by their name:

    
    
    >>> ht = ht.select_globals('target_date')
    >>> ht.globals.show()
    +--------------------+
    | <expr>.target_date |
    +--------------------+
    | str                |
    +--------------------+
    | "2025-01-01"       |
    +--------------------+
    

Notes

This method creates new global fields. If a created field shares its name with
a row-indexed field of the table, the method will fail.

Note

See `Table.select()` for more information about using `select` methods.

Note

This method does not support aggregation.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`Table` – Table with specified global fields.

semi_join(_other_)[[source]](_modules/hail/table.html#Table.semi_join)

    

Filters the table to rows whose key appears in other.

Parameters:

    

**other** (`Table`) – Table with compatible key field(s).

Returns:

    

`Table`

Notes

The key type of the table must match the key type of other.

This method does not change the schema of the table; it is a method of
filtering the table to keys present in another table.

To discard keys present in other, use `anti_join()`.

Examples

    
    
    >>> table1.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    >>> small_table2 = table2.head(2)
    >>> small_table2.show()
    +-------+-------+-------+
    |    ID |     A | B     |
    +-------+-------+-------+
    | int32 | int32 | str   |
    +-------+-------+-------+
    |     1 |    65 | "cat" |
    |     2 |    72 | "dog" |
    +-------+-------+-------+
    >>> table1.semi_join(small_table2).show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

It may be expensive to key the left-side table by the right-side key. In this
case, it is possible to implement a semi-join using a non-key field as
follows:

    
    
    >>> table1.filter(hl.is_defined(small_table2.index(table1['ID']))).show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

See also

`anti_join()`

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_)[[source]](_modules/hail/table.html#Table.show)

    

Print the first few rows of the table to the console.

Examples

Show the first lines of the table:

    
    
    >>> table1.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n or n_rows** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show, or negative to show all rows.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break fields.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

  * **handler** (_Callable[[str], Any]_) – Handler function for data string.

summarize(_handler
=None_)[[source]](_modules/hail/table.html#Table.summarize)

    

Compute and print summary information about the fields in the table.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

tail(_n_)[[source]](_modules/hail/table.html#Table.tail)

    

Subset table to last n rows.

Examples

Subset to the last three rows:

    
    
    >>> table_result = table1.tail(3)
    >>> table_result.count()
    3
    

Notes

The number of partitions in the new table is equal to the number of partitions
containing the last n rows.

Parameters:

    

**n** (_int_) – Number of rows to include.

Returns:

    

`Table` – Table including the last n rows.

take(_n_ , __localize =True_)[[source]](_modules/hail/table.html#Table.take)

    

Collect the first n rows of the table into a local list.

Examples

Take the first three rows:

    
    
    >>> first3 = table1.take(3)
    >>> first3
    [Struct(ID=1, HT=65, SEX='M', X=5, Z=4, C1=2, C2=50, C3=5),
     Struct(ID=2, HT=72, SEX='M', X=6, Z=3, C1=2, C2=61, C3=1),
     Struct(ID=3, HT=70, SEX='F', X=7, Z=3, C1=10, C2=81, C3=-5)]
    

Notes

This method does not need to look at all the data in the table, and allows for
fast queries of the start of the table.

This method is equivalent to `Table.head()` followed by `Table.collect()`.

Parameters:

    

**n** (_int_) – Number of rows to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of ```Struct```
– List of row structs.

to_matrix_table(_row_key_ , _col_key_ , _row_fields =[]_, _col_fields =[]_,
_n_partitions
=None_)[[source]](_modules/hail/table.html#Table.to_matrix_table)

    

Construct a matrix table from a table in coordinate representation.

Examples

Import a coordinate-representation table from disk:

    
    
    >>> coord_ht = hl.import_table('data/coordinate_matrix.tsv', impute=True)
    >>> coord_ht.show()
    +---------+---------+----------+
    | row_idx | col_idx |        x |
    +---------+---------+----------+
    |   int32 |   int32 |  float64 |
    +---------+---------+----------+
    |       1 |       1 | 2.50e-01 |
    |       1 |       2 | 3.30e-01 |
    |       2 |       1 | 1.10e-01 |
    |       3 |       1 | 1.00e+00 |
    |       3 |       2 | 0.00e+00 |
    +---------+---------+----------+
    

Convert to a matrix table and show:

    
    
    >>> dense_mt = coord_ht.to_matrix_table(row_key=['row_idx'], col_key=['col_idx'])
    >>> dense_mt.show()
    +---------+----------+----------+
    | row_idx |      1.x |      2.x |
    +---------+----------+----------+
    |   int32 |  float64 |  float64 |
    +---------+----------+----------+
    |       1 | 2.50e-01 | 3.30e-01 |
    |       2 | 1.10e-01 |       NA |
    |       3 | 1.00e+00 | 0.00e+00 |
    +---------+----------+----------+
    

Notes

Any row fields in the table that do not appear in one of the arguments to this
method are assumed to be entry fields of the resulting matrix table.

Parameters:

    

  * **row_key** (_Sequence[str]_) – Fields to be used as row key.

  * **col_key** (_Sequence[str]_) – Fields to be used as column key.

  * **row_fields** (_Sequence[str]_) – Fields to be stored once per row.

  * **col_fields** (_Sequence[str]_) – Fields to be stored once per column.

  * **n_partitions** (_int or None_) – Number of partitions.

Returns:

    

```MatrixTable```

to_matrix_table_row_major(_columns_ , _entry_field_name =None_,
_col_field_name
='col'_)[[source]](_modules/hail/table.html#Table.to_matrix_table_row_major)

    

Construct a matrix table from a table in row major representation. Each
element in columns is a field that will become an entry field in the matrix
table. Fields omitted from columns become row fields. If columns are structs,
then the matrix table will have the entry fields of those structs. Otherwise,
the matrix table will have one entry field named entry_field_name whose values
come from the values of the columns fields. The matrix table is column indexed
by col_field_name.

If you find yourself using this method after
[`import_table()`](methods/impex.html#hail.methods.import_table
"hail.methods.import_table"), consider instead using
[`import_matrix_table()`](methods/impex.html#hail.methods.import_matrix_table
"hail.methods.import_matrix_table").

Examples

Convert a table of RNA expression samples to a
```MatrixTable```:

    
    
    >>> t = hl.import_table('data/rna_expression.tsv', impute=True)
    >>> t = t.key_by('gene')
    >>> t.show()
    +---------+---------+---------+----------+-----------+-----------+-----------+
    | gene    | lung001 | lung002 | heart001 | muscle001 | muscle002 | muscle003 |
    +---------+---------+---------+----------+-----------+-----------+-----------+
    | str     |   int32 |   int32 |    int32 |     int32 |     int32 |     int32 |
    +---------+---------+---------+----------+-----------+-----------+-----------+
    | "LD4"   |       1 |       2 |        0 |         2 |         1 |         1 |
    | "SCN1A" |       2 |       1 |        1 |         0 |         0 |         0 |
    | "TITIN" |       3 |       0 |        0 |         1 |         2 |         1 |
    +---------+---------+---------+----------+-----------+-----------+-----------+
    >>> mt = t.to_matrix_table_row_major(
    ...          columns=['lung001', 'lung002', 'heart001',
    ...                   'muscle001', 'muscle002', 'muscle003'],
    ...          entry_field_name='expression',
    ...          col_field_name='sample')
    >>> mt.describe()
    ----------------------------------------
    Global fields:
        None
    ----------------------------------------
    Column fields:
        'sample': str
    ----------------------------------------
    Row fields:
        'gene': str
    ----------------------------------------
    Entry fields:
        'expression': int32
    ----------------------------------------
    Column key: ['sample']
    Row key: ['gene']
    ----------------------------------------
    >>> mt.show(n_cols=2)
    +---------+----------------------+----------------------+
    | gene    | 'lung001'.expression | 'lung002'.expression |
    +---------+----------------------+----------------------+
    | str     |                int32 |                int32 |
    +---------+----------------------+----------------------+
    | "LD4"   |                    1 |                    2 |
    | "SCN1A" |                    2 |                    1 |
    | "TITIN" |                    3 |                    0 |
    +---------+----------------------+----------------------+
    showing the first 2 of 6 columns
    

Notes

All fields in columns must have the same type.

Parameters:

    

  * **columns** (_Sequence[str]_) – Fields to be used as columns.

  * **entry_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or None) – Field name for the entries of the matrix table.

  * **col_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Field name for the columns of the matrix table.

Returns:

    

```MatrixTable```

to_pandas(_flatten =True_, _types
={}_)[[source]](_modules/hail/table.html#Table.to_pandas)

    

Converts this table to a Pandas DataFrame.

Parameters:

    

  * **flatten** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – If `True`, `flatten()` before converting to Pandas DataFrame.

  * **types** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") mapping [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```HailType``` to [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Dictionary defining Pandas DataFrame dtypes. If a key is [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), a field with the specified key name is mapped to a Pandas dtype. If a key is ```HailType```, all fields with the specified ```HailType``` are mapped to a Pandas dtype.

Returns:

    

[`pandas.DataFrame`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html#pandas.DataFrame
"\(in pandas v2.3.0\)")

to_spark(_flatten =True_)[[source]](_modules/hail/table.html#Table.to_spark)

    

Converts this table to a Spark DataFrame.

Because Spark cannot represent complex types, types are expanded before
flattening or conversion.

Parameters:

    

**flatten** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool
"\(in Python v3.9\)")) – If `True`, `flatten()` before converting to Spark
DataFrame.

Returns:

    

[`pyspark.sql.DataFrame`](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/api/pyspark.sql.DataFrame.html#pyspark.sql.DataFrame
"\(in PySpark vmaster\)")

transmute(_**
named_exprs_)[[source]](_modules/hail/table.html#Table.transmute)

    

Add new fields and drop fields referenced.

Examples

Consider this table:

    
    
    >>> ht = table1
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Transmuting a field without referencing other fields has the same effect as
annotating:

    
    
    >>> ht = ht.transmute(new_field=hl.struct(x=3, y=4))
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 | new_field.x |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |       int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |           3 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |           3 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |           3 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |           3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
    +-------------+
    | new_field.y |
    +-------------+
    |       int32 |
    +-------------+
    |           4 |
    |           4 |
    |           4 |
    |           4 |
    +-------------+
    

Transmuting a field while referencing other fields drops those other fields.
Notice how the compound field, new_field is dropped entirely even though we
only used one of its component fields.

    
    
    >>> ht = ht.transmute(F=ht.X + 2 * ht.new_field.x)
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     Z |    C1 |    C2 |    C3 |     F |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     4 |     2 |    50 |     5 |    11 |
    |     2 |    72 | "M" |     3 |     2 |    61 |     1 |    12 |
    |     3 |    70 | "F" |     3 |    10 |    81 |    -5 |    13 |
    |     4 |    60 | "F" |     2 |    11 |    90 |   -10 |    14 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Notes

This method functions to create new row-indexed fields and consume fields
found in the expressions in named_exprs.

All row-indexed top-level fields found in an expression are dropped after the
new fields are created.

Note

`transmute()` will not drop key fields.

Warning

References to fields inside a top-level struct will remove the entire struct,
as field new_field was removed in the example above since new_field.x was
referenced.

Note

This method does not support aggregation.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – New field expressions.

Returns:

    

`Table` – Table with transmuted fields.

transmute_globals(_**
named_exprs_)[[source]](_modules/hail/table.html#Table.transmute_globals)

    

Similar to `Table.annotate_globals()`, but drops referenced fields.

Notes

Consider a table with global fields population, area, and year:

    
    
    >>> ht = hl.utils.range_table(1)
    >>> ht = ht.annotate_globals(population=1000000, area=500, year=2020)
    

Compute a new field, density from population and area and also drop the latter
two fields:

    
    
    >>> ht = ht.transmute_globals(density=ht.population / ht.area)
    >>> ht.globals.show()
    +-------------+----------------+
    | <expr>.year | <expr>.density |
    +-------------+----------------+
    |       int32 |        float64 |
    +-------------+----------------+
    |        2020 |       2.00e+03 |
    +-------------+----------------+
    

Introduce a new global field next_year based on year:

    
    
    >>> ht = ht.transmute_globals(next_year=ht.year + 1)
    >>> ht.globals.show()
    +----------------+------------------+
    | <expr>.density | <expr>.next_year |
    +----------------+------------------+
    |        float64 |            int32 |
    +----------------+------------------+
    |       2.00e+03 |             2021 |
    +----------------+------------------+
    

See also

`Table.transmute()`, `Table.select_globals()`, `Table.annotate_globals()`

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Annotation expressions.

Returns:

    

`Table`

union(_* tables_, _unify
=False_)[[source]](_modules/hail/table.html#Table.union)

    

Union the rows of multiple tables.

Examples

Take the union of rows from two tables:

    
    
    >>> union_table = table1.union(other_table)
    

Notes

If a row appears in more than one table identically, it is duplicated in the
result. All tables must have the same key names and types. They must also have
the same row types, unless the unify parameter is `True`, in which case a
field appearing in any table will be included in the result, with missing
values for tables that do not contain the field. If a field appears in
multiple tables with incompatible types, like arrays and strings, then an
error will be raised.

Parameters:

    

  * **tables** (varargs of `Table`) – Tables to union.

  * **unify** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Attempt to unify table field.

Returns:

    

`Table` – Table with all rows from each component table.

unpersist()[[source]](_modules/hail/table.html#Table.unpersist)

    

Unpersists this table from memory/disk.

Notes

This function will have no effect on a table that was not previously
persisted.

Returns:

    

`Table` – Unpersisted table.

write(_output_ , _overwrite =False_, _stage_locally =False_, __codec_spec
=None_)[[source]](_modules/hail/table.html#Table.write)

    

Write to disk.

Examples

    
    
    >>> table1.write('output/table1.ht', overwrite=True)
    

Danger

Do not write or checkpoint to a path that is already an input source for the
query. This can cause data loss.

See also

[`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table")

Parameters:

    

  * **output** (_str_) – Path at which to write.

  * **stage_locally** (_bool_) – If `True`, major output will be written to temporary local storage before being copied to `output`.

  * **overwrite** (_bool_) – If `True`, overwrite an existing file at the destination.

write_many(_output_ , _fields_ , _*_ , _overwrite =False_, _stage_locally
=False_, __codec_spec
=None_)[[source]](_modules/hail/table.html#Table.write_many)

    

Write fields to distinct tables.

Examples

    
    
    >>> t = hl.utils.range_table(10)
    >>> t = t.annotate(a = t.idx, b = t.idx * t.idx, c = hl.str(t.idx))
    >>> t.write_many('output-many', fields=('a', 'b', 'c'), overwrite=True)
    >>> hl.read_table('output-many/a').describe()
    ----------------------------------------
    Global fields:
        None
    ----------------------------------------
    Row fields:
        'a': int32
        'idx': int32
    ----------------------------------------
    Key: ['idx']
    ----------------------------------------
    >>> hl.read_table('output-many/a').show()
    +-------+-------+
    |     a |   idx |
    +-------+-------+
    | int32 | int32 |
    +-------+-------+
    |     0 |     0 |
    |     1 |     1 |
    |     2 |     2 |
    |     3 |     3 |
    |     4 |     4 |
    |     5 |     5 |
    |     6 |     6 |
    |     7 |     7 |
    |     8 |     8 |
    |     9 |     9 |
    +-------+-------+
    >>> hl.read_table('output-many/b').describe()
    ----------------------------------------
    Global fields:
        None
    ----------------------------------------
    Row fields:
        'b': int32
        'idx': int32
    ----------------------------------------
    Key: ['idx']
    ----------------------------------------
    >>> hl.read_table('output-many/b').show()
    +-------+-------+
    |     b |   idx |
    +-------+-------+
    | int32 | int32 |
    +-------+-------+
    |     0 |     0 |
    |     1 |     1 |
    |     4 |     2 |
    |     9 |     3 |
    |    16 |     4 |
    |    25 |     5 |
    |    36 |     6 |
    |    49 |     7 |
    |    64 |     8 |
    |    81 |     9 |
    +-------+-------+
    >>> hl.read_table('output-many/c').describe()
    ----------------------------------------
    Global fields:
        None
    ----------------------------------------
    Row fields:
        'c': str
        'idx': int32
    ----------------------------------------
    Key: ['idx']
    ----------------------------------------
    >>> hl.read_table('output-many/c').show()
    +-----+-------+
    | c   |   idx |
    +-----+-------+
    | str | int32 |
    +-----+-------+
    | "0" |     0 |
    | "1" |     1 |
    | "2" |     2 |
    | "3" |     3 |
    | "4" |     4 |
    | "5" |     5 |
    | "6" |     6 |
    | "7" |     7 |
    | "8" |     8 |
    | "9" |     9 |
    +-----+-------+
    

Danger

Do not write or checkpoint to a path that is already an input source for the
query. This can cause data loss.

See also

[`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table")

Parameters:

    

  * **output** (_str_) – Path at which to write.

  * **fields** (_list of str_) – The fields to write.

  * **stage_locally** (_bool_) – If `True`, major output will be written to temporary local storage before being copied to `output`.

  * **overwrite** (_bool_) – If `True`, overwrite an existing file at the destination.

---

