# Classes

## BaseTable

_class _hail.table.BaseTable[[source]](_modules/hail/table.html#BaseTable)

    

Bases: [`object`](https://docs.python.org/3.9/library/functions.html#object
"\(in Python v3.9\)")

Base class for ```Table```,
[`GroupedTable`](hail.GroupedTable.html#hail.GroupedTable
"hail.table.GroupedTable"),
[`hail.matrixtable.MatrixTable`](hail.MatrixTable.html#hail.MatrixTable
"hail.matrixtable.MatrixTable"), and
[`hail.matrixtable.GroupedMatrixTable`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable
"hail.matrixtable.GroupedMatrixTable")

Attributes

Methods

---

## Table

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

## GroupedTable

_class _hail.GroupedTable[[source]](_modules/hail/table.html#GroupedTable)

    

Bases: [`BaseTable`](hail.table.BaseTable.html#hail.table.BaseTable
"hail.table.BaseTable")

Table grouped by row that can be aggregated into a new table.

There are only two operations on a grouped table,
`GroupedTable.partition_hint()` and `GroupedTable.aggregate()`.

Attributes

Methods

`aggregate` | Aggregate by group, used after ```Table.group_by()```.  
---|---  
`partition_hint` | Set the target number of partitions for aggregation.  
  
aggregate(_**
named_exprs_)[[source]](_modules/hail/table.html#GroupedTable.aggregate)

    

Aggregate by group, used after
[`Table.group_by()`](hail.Table.html#hail.Table.group_by
"hail.Table.group_by").

Examples

Compute the mean value of X and the sum of Z per unique ID:

    
    
    >>> table_result = (table1.group_by(table1.ID)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X), sumZ = hl.agg.sum(table1.Z)))
    

Group by a height bin and compute sex ratio per bin:

    
    
    >>> table_result = (table1.group_by(height_bin = table1.HT // 20)
    ...                       .aggregate(fraction_female = hl.agg.fraction(table1.SEX == 'F')))
    

Notes

The resulting table has a key field for each group and a value field for each
aggregation. The names of the aggregation expressions must be distinct from
the names of the groups.

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

```Table``` – Aggregated table.

partition_hint(_n_)[[source]](_modules/hail/table.html#GroupedTable.partition_hint)

    

Set the target number of partitions for aggregation.

Examples

Use partition_hint in a
[`Table.group_by()`](hail.Table.html#hail.Table.group_by
"hail.Table.group_by") / `GroupedTable.aggregate()` pipeline:

    
    
    >>> table_result = (table1.group_by(table1.ID)
    ...                       .partition_hint(5)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X), sumZ = hl.agg.sum(table1.Z)))
    

Notes

Until Hail’s query optimizer is intelligent enough to sample records at all
stages of a pipeline, it can be necessary in some places to provide some
explicit hints.

The default number of partitions for `GroupedTable.aggregate()` is the number
of partitions in the upstream table. If the aggregation greatly reduces the
size of the table, providing a hint for the target number of partitions can
accelerate downstream operations.

Parameters:

    

**n** (_int_) – Number of partitions.

Returns:

    

`GroupedTable` – Same grouped table with a partition hint.

---

## MatrixTable

_class
_hail.MatrixTable[[source]](_modules/hail/matrixtable.html#MatrixTable)

    

Bases: [`BaseTable`](hail.table.BaseTable.html#hail.table.BaseTable
"hail.table.BaseTable")

Hail’s distributed implementation of a structured matrix.

Use [`read_matrix_table()`](methods/impex.html#hail.methods.read_matrix_table
"hail.methods.read_matrix_table") to read a matrix table that was written with
`MatrixTable.write()`.

Examples

Add annotations:

    
    
    >>> dataset = dataset.annotate_globals(pli = {'SCN1A': 0.999, 'SONIC': 0.014},
    ...                                    populations = ['AFR', 'EAS', 'EUR', 'SAS', 'AMR', 'HIS'])
    
    
    
    >>> dataset = dataset.annotate_cols(pop = dataset.populations[hl.int(hl.rand_unif(0, 6))],
    ...                                 sample_gq = hl.agg.mean(dataset.GQ),
    ...                                 sample_dp = hl.agg.mean(dataset.DP))
    
    
    
    >>> dataset = dataset.annotate_rows(variant_gq = hl.agg.mean(dataset.GQ),
    ...                                 variant_dp = hl.agg.mean(dataset.GQ),
    ...                                 sas_hets = hl.agg.count_where(dataset.GT.is_het()))
    
    
    
    >>> dataset = dataset.annotate_entries(gq_by_dp = dataset.GQ / dataset.DP)
    

Filter:

    
    
    >>> dataset = dataset.filter_cols(dataset.pop != 'EUR')
    
    
    
    >>> datasetm = dataset.filter_rows((dataset.variant_gq > 10) & (dataset.variant_dp > 5))
    
    
    
    >>> dataset = dataset.filter_entries(dataset.gq_by_dp > 1)
    

Query:

    
    
    >>> col_stats = dataset.aggregate_cols(hl.struct(pop_counts=hl.agg.counter(dataset.pop),
    ...                                              high_quality=hl.agg.fraction((dataset.sample_gq > 10) & (dataset.sample_dp > 5))))
    >>> print(col_stats.pop_counts)
    >>> print(col_stats.high_quality)
    
    
    
    >>> het_dist = dataset.aggregate_rows(hl.agg.stats(dataset.sas_hets))
    >>> print(het_dist)
    
    
    
    >>> entry_stats = dataset.aggregate_entries(hl.struct(call_rate=hl.agg.fraction(hl.is_defined(dataset.GT)),
    ...                                                   global_gq_mean=hl.agg.mean(dataset.GQ)))
    >>> print(entry_stats.call_rate)
    >>> print(entry_stats.global_gq_mean)
    

Attributes

`col` | Returns a struct expression of all column-indexed fields, including keys.  
---|---  
`col_key` | Column key struct.  
`col_value` | Returns a struct expression including all non-key column-indexed fields.  
`entry` | Returns a struct expression including all row-and-column-indexed fields.  
`globals` | Returns a struct expression including all global fields.  
`row` | Returns a struct expression of all row-indexed fields, including keys.  
`row_key` | Row key struct.  
`row_value` | Returns a struct expression including all non-key row-indexed fields.  
  
Methods

`add_col_index` | Add the integer index of each column as a new column field.  
---|---  
`add_row_index` | Add the integer index of each row as a new row field.  
`aggregate_cols` | Aggregate over columns to a local value.  
`aggregate_entries` | Aggregate over entries to a local value.  
`aggregate_rows` | Aggregate over rows to a local value.  
`annotate_cols` | Create new column-indexed fields by name.  
`annotate_entries` | Create new row-and-column-indexed fields by name.  
`annotate_globals` | Create new global fields by name.  
`annotate_rows` | Create new row-indexed fields by name.  
`anti_join_cols` | Filters the table to columns whose key does not appear in other.  
`anti_join_rows` | Filters the table to rows whose key does not appear in other.  
`cache` | Persist the dataset in memory.  
`checkpoint` | Checkpoint the matrix table to disk by writing and reading using a fast, but less space-efficient codec.  
`choose_cols` | Choose a new set of columns from a list of old column indices.  
`collect_cols_by_key` | Collect values for each unique column key into arrays.  
`cols` | Returns a table with all column fields in the matrix.  
`compute_entry_filter_stats` | Compute statistics about the number and fraction of filtered entries.  
`count` | Count the number of rows and columns in the matrix.  
`count_cols` | Count the number of columns in the matrix.  
`count_rows` | Count the number of rows in the matrix.  
`describe` | Print information about the fields in the matrix table.  
`distinct_by_col` | Remove columns with a duplicate row key, keeping exactly one column for each unique key.  
`distinct_by_row` | Remove rows with a duplicate row key, keeping exactly one row for each unique key.  
`drop` | Drop fields.  
`entries` | Returns a matrix in coordinate table form.  
`explode_cols` | Explodes a column field of type array or set, copying the entire column for each element.  
`explode_rows` | Explodes a row field of type array or set, copying the entire row for each element.  
`filter_cols` | Filter columns of the matrix.  
`filter_entries` | Filter entries of the matrix.  
`filter_rows` | Filter rows of the matrix.  
`from_parts` | Create a MatrixTable from its component parts.  
`from_rows_table` | Construct matrix table with no columns from a table.  
`globals_table` | Returns a table with a single row with the globals of the matrix table.  
`group_cols_by` | Group columns, used with ```GroupedMatrixTable.aggregate()```.  
`group_rows_by` | Group rows, used with ```GroupedMatrixTable.aggregate()```.  
`head` | Subset matrix to first n_rows rows and n_cols cols.  
`index_cols` | Expose the column values as if looked up in a dictionary, indexing with exprs.  
`index_entries` | Expose the entries as if looked up in a dictionary, indexing with exprs.  
`index_globals` | Return this matrix table's global variables for use in another expression context.  
`index_rows` | Expose the row values as if looked up in a dictionary, indexing with exprs.  
`key_cols_by` | Key columns by a new set of fields.  
`key_rows_by` | Key rows by a new set of fields.  
`localize_entries` | Convert the matrix table to a table with entries localized as an array of structs.  
`make_table` | Make a table from a matrix table with one field per sample.  
`n_partitions` | Number of partitions.  
`naive_coalesce` | Naively decrease the number of partitions.  
`persist` | Persist this table in memory or on disk.  
`rename` | Rename fields of a matrix table.  
`repartition` | Change the number of partitions.  
`rows` | Returns a table with all row fields in the matrix.  
`sample_cols` | Downsample the matrix table by keeping each column with probability `p`.  
`sample_rows` | Downsample the matrix table by keeping each row with probability `p`.  
`select_cols` | Select existing column fields or create new fields by name, dropping the rest.  
`select_entries` | Select existing entry fields or create new fields by name, dropping the rest.  
`select_globals` | Select existing global fields or create new fields by name, dropping the rest.  
`select_rows` | Select existing row fields or create new fields by name, dropping all other non-key fields.  
`semi_join_cols` | Filters the matrix table to columns whose key appears in other.  
`semi_join_rows` | Filters the matrix table to rows whose key appears in other.  
`show` | Print the first few rows of the matrix table to the console.  
`summarize` | Compute and print summary information about the fields in the matrix table.  
`tail` | Subset matrix to last n rows.  
`transmute_cols` | Similar to `MatrixTable.annotate_cols()`, but drops referenced fields.  
`transmute_entries` | Similar to `MatrixTable.annotate_entries()`, but drops referenced fields.  
`transmute_globals` | Similar to `MatrixTable.annotate_globals()`, but drops referenced fields.  
`transmute_rows` | Similar to `MatrixTable.annotate_rows()`, but drops referenced fields.  
`unfilter_entries` | Unfilters filtered entries, populating fields with missing values.  
`union_cols` | Take the union of dataset columns.  
`union_rows` | Take the union of dataset rows.  
`unpersist` | Unpersists this dataset from memory/disk.  
`write` | Write to disk.  
  
add_col_index(_name
='col_idx'_)[[source]](_modules/hail/matrixtable.html#MatrixTable.add_col_index)

    

Add the integer index of each column as a new column field.

Examples

    
    
    >>> dataset_result = dataset.add_col_index()
    

Notes

The field added is type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32").

The column index is 0-indexed; the values are found in the range `[0, N)`,
where `N` is the total number of columns.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Name for column index field.

Returns:

    

`MatrixTable` – Dataset with new field.

add_row_index(_name
='row_idx'_)[[source]](_modules/hail/matrixtable.html#MatrixTable.add_row_index)

    

Add the integer index of each row as a new row field.

Examples

    
    
    >>> dataset_result = dataset.add_row_index()
    

Notes

The field added is type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64").

The row index is 0-indexed; the values are found in the range `[0, N)`, where
`N` is the total number of rows.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Name for row index field.

Returns:

    

`MatrixTable` – Dataset with new field.

aggregate_cols(_expr_ , __localize
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.aggregate_cols)

    

Aggregate over columns to a local value.

Examples

Aggregate over columns:

    
    
    >>> dataset.aggregate_cols(
    ...    hl.struct(fraction_female=hl.agg.fraction(dataset.pheno.is_female),
    ...              case_ratio=hl.agg.count_where(dataset.is_case) / hl.agg.count()))
    Struct(fraction_female=0.44, case_ratio=1.0)
    

Notes

Unlike most `MatrixTable` methods, this method does not support meaningful
references to fields that are not global or indexed by column.

This method should be thought of as a more convenient alternative to the
following:

    
    
    >>> cols_table = dataset.cols()
    >>> cols_table.aggregate(
    ...     hl.struct(fraction_female=hl.agg.fraction(cols_table.pheno.is_female),
    ...               case_ratio=hl.agg.count_where(cols_table.is_case) / hl.agg.count()))
    

Note

This method supports (and expects!) aggregation over columns.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expression.

Returns:

    

_any_ – Aggregated value dependent on expr.

aggregate_entries(_expr_ , __localize
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.aggregate_entries)

    

Aggregate over entries to a local value.

Examples

Aggregate over entries:

    
    
    >>> dataset.aggregate_entries(hl.struct(global_gq_mean=hl.agg.mean(dataset.GQ),
    ...                                     call_rate=hl.agg.fraction(hl.is_defined(dataset.GT))))
    Struct(global_gq_mean=69.60514541387025, call_rate=0.9933333333333333)
    

Notes

This method should be thought of as a more convenient alternative to the
following:

    
    
    >>> entries_table = dataset.entries()
    >>> entries_table.aggregate(hl.struct(global_gq_mean=hl.agg.mean(entries_table.GQ),
    ...                                   call_rate=hl.agg.fraction(hl.is_defined(entries_table.GT))))
    

Note

This method supports (and expects!) aggregation over entries.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

_any_ – Aggregated value dependent on expr.

aggregate_rows(_expr_ , __localize
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.aggregate_rows)

    

Aggregate over rows to a local value.

Examples

Aggregate over rows:

    
    
    >>> dataset.aggregate_rows(hl.struct(n_high_quality=hl.agg.count_where(dataset.qual > 40),
    ...                                  mean_qual=hl.agg.mean(dataset.qual)))
    Struct(n_high_quality=9, mean_qual=140054.73333333334)
    

Notes

Unlike most `MatrixTable` methods, this method does not support meaningful
references to fields that are not global or indexed by row.

This method should be thought of as a more convenient alternative to the
following:

    
    
    >>> rows_table = dataset.rows()
    >>> rows_table.aggregate(hl.struct(n_high_quality=hl.agg.count_where(rows_table.qual > 40),
    ...                                mean_qual=hl.agg.mean(rows_table.qual)))
    

Note

This method supports (and expects!) aggregation over rows.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expression.

Returns:

    

_any_ – Aggregated value dependent on expr.

annotate_cols(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.annotate_cols)

    

Create new column-indexed fields by name.

Examples

Compute statistics about the GQ distribution per sample:

    
    
    >>> dataset_result = dataset.annotate_cols(sample_gq_stats = hl.agg.stats(dataset.GQ))
    

Add sample metadata from a [`hail.Table`](hail.Table.html#hail.Table
"hail.Table").

    
    
    >>> dataset_result = dataset.annotate_cols(population = s_metadata[dataset.s].pop)
    

Note

This method supports aggregation over rows. For instance, the usage:

    
    
    >>> dataset_result = dataset.annotate_cols(mean_GQ = hl.agg.mean(dataset.GQ))
    

will compute the mean per column.

Notes

This method creates new column fields, but can also overwrite existing fields.
Only same-scope fields can be overwritten: for example, it is not possible to
annotate a global field foo and later create an column field foo. However, it
would be possible to create an column field foo and later create another
column field foo, overwriting the first.

The arguments to the method should either be
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") objects, or should be implicitly interpretable as
expressions.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – Matrix table with new column-indexed field(s).

annotate_entries(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.annotate_entries)

    

Create new row-and-column-indexed fields by name.

Examples

Compute the allele dosage using the PL field:

    
    
    >>> def get_dosage(pl):
    ...    # convert to linear scale
    ...    linear_scaled = pl.map(lambda x: 10 ** - (x / 10))
    ...
    ...    # normalize to sum to 1
    ...    ls_sum = hl.sum(linear_scaled)
    ...    linear_scaled = linear_scaled.map(lambda x: x / ls_sum)
    ...
    ...    # multiply by [0, 1, 2] and sum
    ...    return hl.sum(linear_scaled * [0, 1, 2])
    >>>
    >>> dataset_result = dataset.annotate_entries(dosage = get_dosage(dataset.PL))
    

Note

This method does not support aggregation.

Notes

This method creates new entry fields, but can also overwrite existing fields.
Only same-scope fields can be overwritten: for example, it is not possible to
annotate a global field foo and later create an entry field foo. However, it
would be possible to create an entry field foo and later create another entry
field foo, overwriting the first.

The arguments to the method should either be
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") objects, or should be implicitly interpretable as
expressions.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – Matrix table with new row-and-column-indexed field(s).

annotate_globals(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.annotate_globals)

    

Create new global fields by name.

Examples

Add two global fields:

    
    
    >>> pops_1kg = {'EUR', 'AFR', 'EAS', 'SAS', 'AMR'}
    >>> dataset_result = dataset.annotate_globals(pops_in_1kg = pops_1kg,
    ...                                           gene_list = ['SHH', 'SCN1A', 'SPTA1', 'DISC1'])
    

Add global fields from another table and matrix table:

    
    
    >>> dataset_result = dataset.annotate_globals(thing1 = dataset2.index_globals().global_field,
    ...                                           thing2 = v_metadata.index_globals().global_field)
    

Note

This method does not support aggregation.

Notes

This method creates new global fields, but can also overwrite existing fields.
Only same-scope fields can be overwritten: for example, it is not possible to
annotate a row field foo and later create an global field foo. However, it
would be possible to create an global field foo and later create another
global field foo, overwriting the first.

The arguments to the method should either be
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") objects, or should be implicitly interpretable as
expressions.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – Matrix table with new global field(s).

annotate_rows(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.annotate_rows)

    

Create new row-indexed fields by name.

Examples

Compute call statistics for high quality samples per variant:

    
    
    >>> high_quality_calls = hl.agg.filter(dataset.sample_qc.gq_stats.mean > 20,
    ...                                    hl.agg.call_stats(dataset.GT, dataset.alleles))
    >>> dataset_result = dataset.annotate_rows(call_stats = high_quality_calls)
    

Add functional annotations from a [`Table`](hail.Table.html#hail.Table
"hail.Table"), v_metadata, and a `MatrixTable`, dataset2_AF, both keyed by
locus and alleles.

    
    
    >>> dataset_result = dataset.annotate_rows(consequence = v_metadata[dataset.locus, dataset.alleles].consequence,
    ...                                        dataset2_AF = dataset2.index_rows(dataset.row_key).info.AF)
    

Note

This method supports aggregation over columns. For instance, the usage:

    
    
    >>> dataset_result = dataset.annotate_rows(mean_GQ = hl.agg.mean(dataset.GQ))
    

will compute the mean per row.

Notes

This method creates new row fields, but can also overwrite existing fields.
Only non-key, same-scope fields can be overwritten: for example, it is not
possible to annotate a global field foo and later create an row field foo.
However, it would be possible to create an row field foo and later create
another row field foo, overwriting the first, as long as foo is not a row key.

The arguments to the method should either be
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") objects, or should be implicitly interpretable as
expressions.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – Matrix table with new row-indexed field(s).

anti_join_cols(_other_)[[source]](_modules/hail/matrixtable.html#MatrixTable.anti_join_cols)

    

Filters the table to columns whose key does not appear in other.

Parameters:

    

**other** (```Table```) – Table with
compatible key field(s).

Returns:

    

`MatrixTable`

Notes

The column key type of the matrix table must match the key type of other.

This method does not change the schema of the table; it is a method of
filtering the matrix table to column keys not present in another table.

To restrict to columns whose key is present in other, use `semi_join_cols()`.

Examples

    
    
    >>> ds_result = ds.anti_join_cols(cols_to_remove)
    

It may be inconvenient to key the matrix table by the right-side key. In this
case, it is possible to implement an anti-join using a non-key field as
follows:

    
    
    >>> ds_result = ds.filter_cols(hl.is_missing(cols_to_remove.index(ds['s'])))
    

See also

`semi_join_cols()`, `filter_cols()`, `anti_join_rows()`

anti_join_rows(_other_)[[source]](_modules/hail/matrixtable.html#MatrixTable.anti_join_rows)

    

Filters the table to rows whose key does not appear in other.

Parameters:

    

**other** (```Table```) – Table with
compatible key field(s).

Returns:

    

`MatrixTable`

Notes

The row key type of the matrix table must match the key type of other.

This method does not change the schema of the table; it is a method of
filtering the matrix table to row keys not present in another table.

To restrict to rows whose key is present in other, use `semi_join_rows()`.

Examples

    
    
    >>> ds_result = ds.anti_join_rows(rows_to_remove)
    

It may be expensive to key the matrix table by the right-side key. In this
case, it is possible to implement an anti-join using a non-key field as
follows:

    
    
    >>> ds_result = ds.filter_rows(hl.is_missing(rows_to_remove.index(ds['locus'], ds['alleles'])))
    

See also

`anti_join_rows()`, `filter_rows()`, `anti_join_cols()`

cache()[[source]](_modules/hail/matrixtable.html#MatrixTable.cache)

    

Persist the dataset in memory.

Examples

Persist the dataset in memory:

    
    
    >>> dataset = dataset.cache() 
    

Notes

This method is an alias for `persist("MEMORY_ONLY")`.

Returns:

    

`MatrixTable` – Cached dataset.

checkpoint(_output_ , _overwrite =False_, _stage_locally =False_, __codec_spec
=None_, __read_if_exists =False_, __intervals =None_, __filter_intervals
=False_, __drop_cols =False_, __drop_rows
=False_)[[source]](_modules/hail/matrixtable.html#MatrixTable.checkpoint)

    

Checkpoint the matrix table to disk by writing and reading using a fast, but
less space-efficient codec.

Parameters:

    

  * **output** (_str_) – Path at which to write.

  * **stage_locally** (_bool_) – If `True`, major output will be written to temporary local storage before being copied to `output`

  * **overwrite** (_bool_) – If `True`, overwrite an existing file at the destination.

Returns:

    

`MatrixTable`

Danger

Do not write or checkpoint to a path that is already an input source for the
query. This can cause data loss.

Notes

An alias for `write()` followed by
[`read_matrix_table()`](methods/impex.html#hail.methods.read_matrix_table
"hail.methods.read_matrix_table"). It is possible to read the file at this
path later with
[`read_matrix_table()`](methods/impex.html#hail.methods.read_matrix_table
"hail.methods.read_matrix_table"). A faster, but less efficient, codec is used
or writing the data so the file will be larger than if one used `write()`.

Examples

    
    
    >>> dataset = dataset.checkpoint('output/dataset_checkpoint.mt')
    

choose_cols(_indices_)[[source]](_modules/hail/matrixtable.html#MatrixTable.choose_cols)

    

Choose a new set of columns from a list of old column indices.

Examples

Randomly shuffle column order:

    
    
    >>> import random
    >>> indices = list(range(dataset.count_cols()))
    >>> random.shuffle(indices)
    >>> dataset_reordered = dataset.choose_cols(indices)
    

Take the first ten columns:

    
    
    >>> dataset_result = dataset.choose_cols(list(range(10)))
    

Parameters:

    

**indices** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list
"\(in Python v3.9\)") of
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")) – List of old column indices.

Returns:

    

`MatrixTable`

_property _col

    

Returns a struct expression of all column-indexed fields, including keys.

Examples

Get all column field names:

    
    
    >>> list(dataset.col)  
    ['s', 'sample_qc', 'is_case', 'pheno', 'cov', 'cov1', 'cov2', 'cohorts', 'pop']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all column fields.

_property _col_key

    

Column key struct.

Examples

Get the column key field names:

    
    
    >>> list(dataset.col_key)
    ['s']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

_property _col_value

    

Returns a struct expression including all non-key column-indexed fields.

Examples

Get all non-key column field names:

    
    
    >>> list(dataset.col_value)  
    ['sample_qc', 'is_case', 'pheno', 'cov', 'cov1', 'cov2', 'cohorts', 'pop']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all column fields, minus keys.

collect_cols_by_key()[[source]](_modules/hail/matrixtable.html#MatrixTable.collect_cols_by_key)

    

Collect values for each unique column key into arrays.

Examples

    
    
    >>> mt = hl.utils.range_matrix_table(3, 3)
    >>> col_dict = hl.literal({0: [1], 1: [2, 3], 2: [4, 5, 6]})
    >>> mt = (mt.annotate_cols(foo = col_dict.get(mt.col_idx))
    ...     .explode_cols('foo'))
    >>> mt = mt.annotate_entries(bar = mt.row_idx * mt.foo)
    
    
    
    >>> mt.cols().show() 
    +---------+-------+
    | col_idx |   foo |
    +---------+-------+
    |   int32 | int32 |
    +---------+-------+
    |       0 |     1 |
    |       1 |     2 |
    |       1 |     3 |
    |       2 |     4 |
    |       2 |     5 |
    |       2 |     6 |
    +---------+-------+
    
    
    
    >>> mt.entries().show() 
    +---------+---------+-------+-------+
    | row_idx | col_idx |   foo |   bar |
    +---------+---------+-------+-------+
    |   int32 |   int32 | int32 | int32 |
    +---------+---------+-------+-------+
    |       0 |       0 |     1 |     0 |
    |       0 |       1 |     2 |     0 |
    |       0 |       1 |     3 |     0 |
    |       0 |       2 |     4 |     0 |
    |       0 |       2 |     5 |     0 |
    |       0 |       2 |     6 |     0 |
    |       1 |       0 |     1 |     1 |
    |       1 |       1 |     2 |     2 |
    |       1 |       1 |     3 |     3 |
    |       1 |       2 |     4 |     4 |
    +---------+---------+-------+-------+
    showing top 10 rows
    
    
    
    >>> mt = mt.collect_cols_by_key()
    >>> mt.cols().show()
    +---------+--------------+
    | col_idx | foo          |
    +---------+--------------+
    |   int32 | array<int32> |
    +---------+--------------+
    |       0 | [1]          |
    |       1 | [2,3]        |
    |       2 | [4,5,6]      |
    +---------+--------------+
    
    
    
    >>> mt.entries().show() 
    +---------+---------+--------------+--------------+
    | row_idx | col_idx | foo          | bar          |
    +---------+---------+--------------+--------------+
    |   int32 |   int32 | array<int32> | array<int32> |
    +---------+---------+--------------+--------------+
    |       0 |       0 | [1]          | [0]          |
    |       0 |       1 | [2,3]        | [0,0]        |
    |       0 |       2 | [4,5,6]      | [0,0,0]      |
    |       1 |       0 | [1]          | [1]          |
    |       1 |       1 | [2,3]        | [2,3]        |
    |       1 |       2 | [4,5,6]      | [4,5,6]      |
    |       2 |       0 | [1]          | [2]          |
    |       2 |       1 | [2,3]        | [4,6]        |
    |       2 |       2 | [4,5,6]      | [8,10,12]    |
    +---------+---------+--------------+--------------+
    

Notes

Each entry field and each non-key column field of type t is replaced by a
field of type array<t>. The value of each such field is an array containing
all values of that field sharing the corresponding column key. In each column,
the newly collected arrays all have the same length, and the values of each
pre-collection column are guaranteed to be located at the same index in their
corresponding arrays.

Note

The order of the columns is not guaranteed.

Returns:

    

`MatrixTable`

cols()[[source]](_modules/hail/matrixtable.html#MatrixTable.cols)

    

Returns a table with all column fields in the matrix.

Examples

Extract the column table:

    
    
    >>> cols_table = dataset.cols()
    

Warning

Matrix table columns are typically sorted by the order at import, and not
necessarily by column key. Since tables are always sorted by key, the table
which results from this command will have its rows sorted by the column key
(which becomes the table key). To preserve the original column order as the
table row order, first unkey the columns using `key_cols_by()` with no
arguments.

Returns:

    

```Table``` – Table with all column
fields from the matrix, with one row per column of the matrix.

compute_entry_filter_stats(_row_field ='entry_stats_row'_, _col_field
='entry_stats_col'_)[[source]](_modules/hail/matrixtable.html#MatrixTable.compute_entry_filter_stats)

    

Compute statistics about the number and fraction of filtered entries.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

Parameters:

    

  * **row_field** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Name for computed row field (default: `entry_stats_row`.

  * **col_field** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Name for computed column field (default: `entry_stats_col`.

Returns:

    

`MatrixTable`

Notes

Adds a new row field, row_field, and a new column field, col_field, each of
which are structs with the following fields:

>   * _n_filtered_ ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")) - Number of filtered entries per row or column.
>
>   * _n_remaining_ ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")) - Number of entries not filtered per row or
> column.
>
>   * _fraction_filtered_ ([`tfloat32`](types.html#hail.expr.types.tfloat32
> "hail.expr.types.tfloat32")) - Number of filtered entries divided by the
> total number of filtered and remaining entries.
>
>

See also

`filter_entries()`, `unfilter_entries()`

count()[[source]](_modules/hail/matrixtable.html#MatrixTable.count)

    

Count the number of rows and columns in the matrix.

Examples

    
    
    >>> dataset.count()
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)"), [`int`](https://docs.python.org/3.9/library/functions.html#int "\(in
Python v3.9\)") – Number of rows, number of cols.

count_cols(__localize
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.count_cols)

    

Count the number of columns in the matrix.

Examples

Count the number of columns:

    
    
    >>> n_cols = dataset.count_cols()
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") – Number of columns in the matrix.

count_rows(__localize
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.count_rows)

    

Count the number of rows in the matrix.

Examples

Count the number of rows:

    
    
    >>> n_rows = dataset.count_rows()
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") – Number of rows in the matrix.

describe(_handler= <built-in function print>_, _*_ ,
_widget=False_)[[source]](_modules/hail/matrixtable.html#MatrixTable.describe)

    

Print information about the fields in the matrix table.

Note

The widget argument is **experimental**.

Parameters:

    

  * **handler** (_Callable[[str], None]_) – Handler function for returned string.

  * **widget** (_bool_) – Create an interactive IPython widget.

distinct_by_col()[[source]](_modules/hail/matrixtable.html#MatrixTable.distinct_by_col)

    

Remove columns with a duplicate row key, keeping exactly one column for each
unique key.

Returns:

    

`MatrixTable`

distinct_by_row()[[source]](_modules/hail/matrixtable.html#MatrixTable.distinct_by_row)

    

Remove rows with a duplicate row key, keeping exactly one row for each unique
key.

Returns:

    

`MatrixTable`

drop(_* exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.drop)

    

Drop fields.

Examples

Drop fields PL (an entry field), info (a row field), and pheno (a column
field): using strings:

    
    
    >>> dataset_result = dataset.drop('PL', 'info', 'pheno')
    

Drop fields PL (an entry field), info (a row field), and pheno (a column
field): using field references:

    
    
    >>> dataset_result = dataset.drop(dataset.PL, dataset.info, dataset.pheno)
    

Drop a list of fields:

    
    
    >>> fields_to_drop = ['PL', 'info', 'pheno']
    >>> dataset_result = dataset.drop(*fields_to_drop)
    

Notes

This method can be used to drop global, row-indexed, column-indexed, or row-
and-column-indexed (entry) fields. The arguments can be either strings
(`'field'`), or top-level field references (`table.field` or
`table['field']`).

Key fields (belonging to either the row key or the column key) cannot be
dropped using this method. In order to drop a key field, use `key_rows_by()`
or `key_cols_by()` to remove the field from the key before dropping.

While many operations exist independently for rows, columns, entries, and
globals, only one is needed for dropping due to the lack of any necessary
contextual information.

Parameters:

    

**exprs** (varargs of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or [`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Names of fields to drop or field reference
expressions.

Returns:

    

`MatrixTable` – Matrix table without specified fields.

entries()[[source]](_modules/hail/matrixtable.html#MatrixTable.entries)

    

Returns a matrix in coordinate table form.

Examples

Extract the entry table:

    
    
    >>> entries_table = dataset.entries()
    

Notes

The coordinate table representation of the source matrix table contains one
row for each **non-filtered** entry of the matrix – if a matrix table has no
filtered entries and contains N rows and M columns, the table will contain `M
* N` rows, which can be **a very large number**.

This representation can be useful for aggregating over both axes of a matrix
table at the same time – it is not possible to aggregate over a matrix table
using `group_rows_by()` and `group_cols_by()` at the same time (aggregating by
population and chromosome from a variant-by-sample genetics representation,
for instance). After moving to the coordinate representation with `entries()`,
it is possible to group and aggregate the resulting table much more flexibly,
albeit with potentially poorer computational performance.

Warning

The table returned by this method should be used for aggregation or queries,
but never exported or written to disk without extensive filtering and field
selection – the disk footprint of an entries_table could be 100x (or more!)
larger than its parent matrix. This means that if you try to export the
entries table of a 10 terabyte matrix, you could write a petabyte of data!

Warning

Matrix table columns are typically sorted by the order at import, and not
necessarily by column key. Since tables are always sorted by key, the table
which results from this command will have its rows sorted by the compound (row
key, column key) which becomes the table key. To preserve the original row-
major entry order as the table row order, first unkey the columns using
`key_cols_by()` with no arguments.

Warning

If the matrix table has no row key, but has a column key, this operation may
require a full shuffle to sort by the column key, depending on the pipeline.

Returns:

    

```Table``` – Table with all non-global
fields from the matrix, with **one row per entry of the matrix**.

_property _entry

    

Returns a struct expression including all row-and-column-indexed fields.

Examples

Get all entry field names:

    
    
    >>> list(dataset.entry)
    ['GT', 'AD', 'DP', 'GQ', 'PL']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all entry fields.

explode_cols(_field_expr_)[[source]](_modules/hail/matrixtable.html#MatrixTable.explode_cols)

    

Explodes a column field of type array or set, copying the entire column for
each element.

Examples

Explode columns by annotated cohorts:

    
    
    >>> dataset_result = dataset.explode_cols(dataset.cohorts)
    

Notes

The new matrix table will have N copies of each column, where N is the number
of elements that column contains for the field denoted by field_expr. The
field referenced in field_expr is replaced in the sequence of duplicated
columns by the sequence of elements in the array or set. All other fields
remain the same, including entry fields.

If the field referenced with field_expr is missing or empty, the column is
removed entirely.

Parameters:

    

**field_expr** (str or
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field name or (possibly nested) field reference
expression.

Returns:

    

`MatrixTable` – Matrix table exploded column-wise for each element of
field_expr.

explode_rows(_field_expr_)[[source]](_modules/hail/matrixtable.html#MatrixTable.explode_rows)

    

Explodes a row field of type array or set, copying the entire row for each
element.

Examples

Explode rows by annotated genes:

    
    
    >>> dataset_result = dataset.explode_rows(dataset.gene)
    

Notes

The new matrix table will have N copies of each row, where N is the number of
elements that row contains for the field denoted by field_expr. The field
referenced in field_expr is replaced in the sequence of duplicated rows by the
sequence of elements in the array or set. All other fields remain the same,
including entry fields.

If the field referenced with field_expr is missing or empty, the row is
removed entirely.

Parameters:

    

**field_expr** (str or
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field name or (possibly nested) field reference
expression.

Returns:

    

`MatrixTable` – Matrix table exploded row-wise for each element of field_expr.

filter_cols(_expr_ , _keep
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.filter_cols)

    

Filter columns of the matrix.

Examples

Keep columns where pheno.is_case is `True` and pheno.age is larger than 50:

    
    
    >>> dataset_result = dataset.filter_cols(dataset.pheno.is_case &
    ...                                      (dataset.pheno.age > 50),
    ...                                      keep=True)
    

Remove columns where sample_qc.gq_stats.mean is less than 20:

    
    
    >>> dataset_result = dataset.filter_cols(dataset.sample_qc.gq_stats.mean < 20,
    ...                                      keep=False)
    

Remove columns where s is found in a Python set:

    
    
    >>> samples_to_remove = {'NA12878', 'NA12891', 'NA12892'}
    >>> set_to_remove = hl.literal(samples_to_remove)
    >>> dataset_result = dataset.filter_cols(~set_to_remove.contains(dataset['s']))
    

Notes

The expression expr will be evaluated for every column of the table. If keep
is `True`, then columns where expr evaluates to `True` will be kept (the
filter removes the columns where the predicate evaluates to `False`). If keep
is `False`, then columns where expr evaluates to `True` will be removed (the
filter keeps the columns where the predicate evaluates to `False`).

Warning

When expr evaluates to missing, the column will be removed regardless of keep.

Note

This method supports aggregation over rows. For instance,

    
    
    >>> dataset_result = dataset.filter_cols(hl.agg.mean(dataset.GQ) > 20.0)
    

will remove columns where the mean GQ of all entries in the column is smaller
than 20.

Parameters:

    

  * **expr** (bool or ```BooleanExpression```) – Filter expression.

  * **keep** (_bool_) – Keep columns where expr is true.

Returns:

    

`MatrixTable` – Filtered matrix table.

filter_entries(_expr_ , _keep
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.filter_entries)

    

Filter entries of the matrix.

Parameters:

    

  * **expr** (bool or ```BooleanExpression```) – Filter expression.

  * **keep** (_bool_) – Keep entries where expr is true.

Returns:

    

`MatrixTable` – Filtered matrix table.

Examples

Keep entries where the sum of AD is greater than 10 and GQ is greater than 20:

    
    
    >>> dataset_result = dataset.filter_entries((hl.sum(dataset.AD) > 10) & (dataset.GQ > 20))
    

Warning

When expr evaluates to missing, the entry will be removed regardless of keep.

Note

This method does not support aggregation.

Notes

The expression expr will be evaluated for every entry of the table. If keep is
`True`, then entries where expr evaluates to `True` will be kept (the filter
removes the entries where the predicate evaluates to `False`). If keep is
`False`, then entries where expr evaluates to `True` will be removed (the
filter keeps the entries where the predicate evaluates to `False`).

Filtered entries are removed entirely from downstream operations. This means
that the resulting matrix table has sparsity – that is, that the number of
entries is **smaller** than the product of `count_rows()` and `count_cols()`.
To re-densify a filtered matrix table, use the `unfilter_entries()` method to
restore filtered entries, populated all fields with missing values. Below are
some properties of an entry-filtered matrix table.

  1. Filtered entries are not included in the `entries()` table.

    
    
    >>> mt_range = hl.utils.range_matrix_table(10, 10)
    >>> mt_range = mt_range.annotate_entries(x = mt_range.row_idx + mt_range.col_idx)
    >>> mt_range.count()
    (10, 10)
    
    
    
    >>> mt_range.entries().count()
    100
    
    
    
    >>> mt_filt = mt_range.filter_entries(mt_range.x % 2 == 0)
    >>> mt_filt.count()
    (10, 10)
    
    
    
    >>> mt_filt.count_rows() * mt_filt.count_cols()
    100
    
    
    
    >>> mt_filt.entries().count()
    50
    

  2. Filtered entries are not included in aggregation.

    
    
    >>> mt_filt.aggregate_entries(hl.agg.count())
    50
    
    
    
    >>> mt_filt = mt_filt.annotate_cols(col_n = hl.agg.count())
    >>> mt_filt.col_n.take(5)
    [5, 5, 5, 5, 5]
    
    
    
    >>> mt_filt = mt_filt.annotate_rows(row_n = hl.agg.count())
    >>> mt_filt.row_n.take(5)
    [5, 5, 5, 5, 5]
    

  3. Annotating a new entry field will not annotate filtered entries.

    
    
    >>> mt_filt = mt_filt.annotate_entries(y = 1)
    >>> mt_filt.aggregate_entries(hl.agg.sum(mt_filt.y))
    50
    

4\. If all the entries in a row or column of a matrix table are filtered, the
row or column remains.

    
    
    >>> mt_filt.filter_entries(False).count()
    (10, 10)
    

See also

`unfilter_entries()`, `compute_entry_filter_stats()`

filter_rows(_expr_ , _keep
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.filter_rows)

    

Filter rows of the matrix.

Examples

Keep rows where variant_qc.AF is below 1%:

    
    
    >>> dataset_result = dataset.filter_rows(dataset.variant_qc.AF[1] < 0.01, keep=True)
    

Remove rows where filters is non-empty:

    
    
    >>> dataset_result = dataset.filter_rows(dataset.filters.size() > 0, keep=False)
    

Notes

The expression expr will be evaluated for every row of the table. If keep is
`True`, then rows where expr evaluates to `True` will be kept (the filter
removes the rows where the predicate evaluates to `False`). If keep is
`False`, then rows where expr evaluates to `True` will be removed (the filter
keeps the rows where the predicate evaluates to `False`).

Warning

When expr evaluates to missing, the row will be removed regardless of keep.

Note

This method supports aggregation over columns. For instance,

    
    
    >>> dataset_result = dataset.filter_rows(hl.agg.mean(dataset.GQ) > 20.0)
    

will remove rows where the mean GQ of all entries in the row is smaller than
20.

Parameters:

    

  * **expr** (bool or ```BooleanExpression```) – Filter expression.

  * **keep** (_bool_) – Keep rows where expr is true.

Returns:

    

`MatrixTable` – Filtered matrix table.

_static _from_parts(_globals =None_, _rows =None_, _cols =None_, _entries
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.from_parts)

    

Create a MatrixTable from its component parts.

Example

    
    
    >>> mt = hl.MatrixTable.from_parts(
    ...     globals={'hello':'world'},
    ...     rows={'foo':[1, 2]},
    ...     cols={'bar':[3, 4]},
    ...     entries={'baz':[[1, 2],[3, 4]]}
    ... )
    >>> mt.describe()
    ----------------------------------------
    Global fields:
        'hello': str
    ----------------------------------------
    Column fields:
        'col_idx': int32
        'bar': int32
    ----------------------------------------
    Row fields:
        'row_idx': int32
        'foo': int32
    ----------------------------------------
    Entry fields:
        'baz': int32
    ----------------------------------------
    Column key: ['col_idx']
    Row key: ['row_idx']
    ----------------------------------------
    >>> mt.row.show()
    +---------+-------+
    | row_idx |   foo |
    +---------+-------+
    |   int32 | int32 |
    +---------+-------+
    |       0 |     1 |
    |       1 |     2 |
    +---------+-------+
    >>> mt.col.show()
    +---------+-------+
    | col_idx |   bar |
    +---------+-------+
    |   int32 | int32 |
    +---------+-------+
    |       0 |     3 |
    |       1 |     4 |
    +---------+-------+
    >>> mt.entry.show()
    +---------+-------+-------+
    | row_idx | 0.baz | 1.baz |
    +---------+-------+-------+
    |   int32 | int32 | int32 |
    +---------+-------+-------+
    |       0 |     1 |     2 |
    |       1 |     3 |     4 |
    +---------+-------+-------+
    

Notes

  * Matrix dimensions are inferred from input data.

  * You must provide row and column dimensions by specifying rows or entries (inclusive) and cols or entries (inclusive).

  * The respective dimensions of rows, cols and entries must match should you provide rows and entries or cols and entries (inclusive).

Parameters:

    

  * **globals** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") from [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to [`any`](https://docs.python.org/3.9/library/functions.html#any "\(in Python v3.9\)")) – Global fields by name.

  * **rows** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") from [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`any`](https://docs.python.org/3.9/library/functions.html#any "\(in Python v3.9\)")) – Row fields by name.

  * **cols** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") from [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`any`](https://docs.python.org/3.9/library/functions.html#any "\(in Python v3.9\)")) – Column fields by name.

  * **entries** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") from [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`any`](https://docs.python.org/3.9/library/functions.html#any "\(in Python v3.9\)")) – Matrix entries by name in the form entry[row_idx][col_idx].

Returns:

    

`MatrixTable` – A MatrixTable assembled from inputs whose rows are keyed by
row_idx and columns are keyed by col_idx.

_classmethod
_from_rows_table(_table_)[[source]](_modules/hail/matrixtable.html#MatrixTable.from_rows_table)

    

Construct matrix table with no columns from a table.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

Examples

Import a text table and construct a rows-only matrix table:

    
    
    >>> table = hl.import_table('data/variant-lof.tsv')
    >>> table = table.transmute(**hl.parse_variant(table['v'])).key_by('locus', 'alleles')
    >>> sites_mt = hl.MatrixTable.from_rows_table(table)
    

Notes

All fields in the table become row-indexed fields in the result.

Parameters:

    

**table** (```Table```) – The table to
be converted.

Returns:

    

`MatrixTable`

_property _globals

    

Returns a struct expression including all global fields.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

globals_table()[[source]](_modules/hail/matrixtable.html#MatrixTable.globals_table)

    

Returns a table with a single row with the globals of the matrix table.

Examples

Extract the globals table:

    
    
    >>> globals_table = dataset.globals_table()
    

Returns:

    

```Table``` – Table with the globals
from the matrix, with a single row.

group_cols_by(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.group_cols_by)

    

Group columns, used with
[`GroupedMatrixTable.aggregate()`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate
"hail.GroupedMatrixTable.aggregate").

Examples

Aggregate to a matrix with cohort as column keys, computing the call rate as
an entry field:

    
    
    >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
    ...                          .aggregate(call_rate = hl.agg.fraction(hl.is_defined(dataset.GT))))
    

Notes

All complex expressions must be passed as named expressions.

Parameters:

    

  * **exprs** (args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Column fields to group by.

  * **named_exprs** (keyword args of ```Expression```) – Column-indexed expressions to group by.

Returns:

    

[`GroupedMatrixTable`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable
"hail.GroupedMatrixTable") – Grouped matrix, can be used to call
[`GroupedMatrixTable.aggregate()`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate
"hail.GroupedMatrixTable.aggregate").

group_rows_by(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.group_rows_by)

    

Group rows, used with
[`GroupedMatrixTable.aggregate()`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate
"hail.GroupedMatrixTable.aggregate").

Examples

Aggregate to a matrix with genes as row keys, computing the number of non-
reference calls as an entry field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    

Notes

All complex expressions must be passed as named expressions.

Parameters:

    

  * **exprs** (args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Row fields to group by.

  * **named_exprs** (keyword args of ```Expression```) – Row-indexed expressions to group by.

Returns:

    

[`GroupedMatrixTable`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable
"hail.GroupedMatrixTable") – Grouped matrix. Can be used to call
[`GroupedMatrixTable.aggregate()`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate
"hail.GroupedMatrixTable.aggregate").

head(_n_rows_ , _n_cols =None_, _*_ , _n
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.head)

    

Subset matrix to first n_rows rows and n_cols cols.

Examples

    
    
    >>> mt_range = hl.utils.range_matrix_table(100, 100)
    

Passing only one argument will take the first n_rows rows:

    
    
    >>> mt_range.head(10).count()
    (10, 100)
    

Passing two arguments refers to rows and columns, respectively:

    
    
    >>> mt_range.head(10, 20).count()
    (10, 20)
    

Either argument may be `None` to indicate no filter.

First 10 rows, all columns:

    
    
    >>> mt_range.head(10, None).count()
    (10, 100)
    

All rows, first 10 columns:

    
    
    >>> mt_range.head(None, 10).count()
    (100, 10)
    

Notes

The number of partitions in the new matrix is equal to the number of
partitions containing the first n_rows rows.

Parameters:

    

  * **n_rows** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Number of rows to include (all rows included if `None`).

  * **n_cols** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Number of cols to include (all cols included if `None`).

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Deprecated in favor of n_rows.

Returns:

    

`MatrixTable` – Matrix including the first n_rows rows and first n_cols cols.

index_cols(_* exprs_, _all_matches
=False_)[[source]](_modules/hail/matrixtable.html#MatrixTable.index_cols)

    

Expose the column values as if looked up in a dictionary, indexing with exprs.

Examples

    
    
    >>> dataset_result = dataset.annotate_cols(pheno = dataset2.index_cols(dataset.s).pheno)
    

Or equivalently:

    
    
    >>> dataset_result = dataset.annotate_cols(pheno = dataset2.index_cols(dataset.col_key).pheno)
    

Parameters:

    

  * **exprs** (variable-length args of ```Expression```) – Index expressions.

  * **all_matches** (_bool_) – Experimental. If `True`, value of expression is array of all matches.

Notes

`index_cols(cols)` is equivalent to `cols().index(exprs)` or `cols()[exprs]`.

The type of the resulting struct is the same as the type of `col_value()`.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

index_entries(_row_exprs_ ,
_col_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.index_entries)

    

Expose the entries as if looked up in a dictionary, indexing with exprs.

Examples

    
    
    >>> dataset_result = dataset.annotate_entries(GQ2 = dataset2.index_entries(dataset.row_key, dataset.col_key).GQ)
    

Or equivalently:

    
    
    >>> dataset_result = dataset.annotate_entries(GQ2 = dataset2[dataset.row_key, dataset.col_key].GQ)
    

Parameters:

    

  * **row_exprs** (tuple of ```Expression```) – Row index expressions.

  * **col_exprs** (tuple of ```Expression```) – Column index expressions.

Notes

The type of the resulting struct is the same as the type of `entry()`.

Note

There is a shorthand syntax for `MatrixTable.index_entries()` using square
brackets (the Python `__getitem__` syntax). This syntax is preferred.

    
    
    >>> dataset_result = dataset.annotate_entries(GQ2 = dataset2[dataset.row_key, dataset.col_key].GQ)
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

index_globals()[[source]](_modules/hail/matrixtable.html#MatrixTable.index_globals)

    

Return this matrix table’s global variables for use in another expression
context.

Examples

    
    
    >>> dataset1 = dataset.annotate_globals(pli={'SCN1A': 0.999, 'SONIC': 0.014})
    >>> pli_dict = dataset1.index_globals().pli
    >>> dataset_result = dataset2.annotate_rows(gene_pli = dataset2.gene.map(lambda x: pli_dict.get(x)))
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

index_rows(_* exprs_, _all_matches
=False_)[[source]](_modules/hail/matrixtable.html#MatrixTable.index_rows)

    

Expose the row values as if looked up in a dictionary, indexing with exprs.

Examples

    
    
    >>> dataset_result = dataset.annotate_rows(qual = dataset2.index_rows(dataset.locus, dataset.alleles).qual)
    

Or equivalently:

    
    
    >>> dataset_result = dataset.annotate_rows(qual = dataset2.index_rows(dataset.row_key).qual)
    

Parameters:

    

  * **exprs** (variable-length args of ```Expression```) – Index expressions.

  * **all_matches** (_bool_) – Experimental. If `True`, value of expression is array of all matches.

Notes

`index_rows(exprs)` is equivalent to `rows().index(exprs)` or `rows()[exprs]`.

The type of the resulting struct is the same as the type of `row_value()`.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

key_cols_by(_* keys_, _**
named_keys_)[[source]](_modules/hail/matrixtable.html#MatrixTable.key_cols_by)

    

Key columns by a new set of fields.

See ```Table.key_by()```
for more information on defining a key.

Parameters:

    

  * **keys** (varargs of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```.) – Column fields to key by.

  * **named_keys** (keyword args of ```Expression```.) – Column fields to key by.

Returns:

    

`MatrixTable`

key_rows_by(_* keys_, _**
named_keys_)[[source]](_modules/hail/matrixtable.html#MatrixTable.key_rows_by)

    

Key rows by a new set of fields.

Examples

    
    
    >>> dataset_result = dataset.key_rows_by('locus')
    >>> dataset_result = dataset.key_rows_by(dataset['locus'])
    >>> dataset_result = dataset.key_rows_by(**dataset.row_key.drop('alleles'))
    

All of these expressions key the dataset by the ‘locus’ field, dropping the
‘alleles’ field from the row key.

    
    
    >>> dataset_result = dataset.key_rows_by(contig=dataset['locus'].contig,
    ...                                      position=dataset['locus'].position,
    ...                                      alleles=dataset['alleles'])
    

This keys the dataset by the newly defined fields, ‘contig’ and ‘position’,
and the ‘alleles’ field. The old row key field, ‘locus’, is preserved as a
non-key field.

Notes

See ```Table.key_by()```
for more information on defining a key.

Parameters:

    

  * **keys** (varargs of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```.) – Row fields to key by.

  * **named_keys** (keyword args of ```Expression```.) – Row fields to key by.

Returns:

    

`MatrixTable`

localize_entries(_entries_array_field_name =None_, _columns_array_field_name
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.localize_entries)

    

Convert the matrix table to a table with entries localized as an array of
structs.

Examples

Build a numpy ndarray from a small `MatrixTable`:

    
    
    >>> mt = hl.utils.range_matrix_table(3,3)
    >>> mt = mt.select_entries(x = mt.row_idx * mt.col_idx)
    >>> mt.show()
    +---------+-------+-------+-------+
    | row_idx |   0.x |   1.x |   2.x |
    +---------+-------+-------+-------+
    |   int32 | int32 | int32 | int32 |
    +---------+-------+-------+-------+
    |       0 |     0 |     0 |     0 |
    |       1 |     0 |     1 |     2 |
    |       2 |     0 |     2 |     4 |
    +---------+-------+-------+-------+
    
    
    
    >>> t = mt.localize_entries('entry_structs', 'columns')
    >>> t.describe()
    ----------------------------------------
    Global fields:
        'columns': array<struct {
            col_idx: int32
        }>
    ----------------------------------------
    Row fields:
        'row_idx': int32
        'entry_structs': array<struct {
            x: int32
        }>
    ----------------------------------------
    Key: ['row_idx']
    ----------------------------------------
    
    
    
    >>> t = t.select(entries = t.entry_structs.map(lambda entry: entry.x))
    >>> import numpy as np
    >>> np.array(t.entries.collect())
    array([[0, 0, 0],
           [0, 1, 2],
           [0, 2, 4]])
    

Notes

Both of the added fields are arrays of length equal to `mt.count_cols()`.
Missing entries are represented as missing structs in the entries array.

Parameters:

    

  * **entries_array_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The name of the table field containing the array of entry structs for the given row.

  * **columns_array_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The name of the global field containing the array of column structs.

Returns:

    

```Table``` – A table whose fields are
the row fields of this matrix table plus one field named
`entries_array_field_name`. The global fields of this table are the global
fields of this matrix table plus one field named `columns_array_field_name`.

make_table(_separator
='.'_)[[source]](_modules/hail/matrixtable.html#MatrixTable.make_table)

    

Make a table from a matrix table with one field per sample.

Deprecated since version 0.2.129: use `localize_entries()` instead because it
supports more columns

Parameters:

    

**separator** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")) – Separator between sample IDs and entry field names.

Returns:

    

```Table```

See also

`localize_entries()`

Notes

The table has one row for each row of the input matrix. The per sample and
entry fields are formed by concatenating the sample ID with the entry field
name using separator. If the entry field name is empty, the separator is
omitted.

The table inherits the globals from the matrix table.

Examples

Consider a matrix table with the following schema:

    
    
    Global fields:
        'batch': str
    Column fields:
        's': str
    Row fields:
        'locus': locus<GRCh37>
        'alleles': array<str>
    Entry fields:
        'GT': call
        'GQ': int32
    Column key:
        's': str
    Row key:
        'locus': locus<GRCh37>
        'alleles': array<str>
    

and three sample IDs: A, B and C. Then the result of `make_table()`:

    
    
    >>> ht = mt.make_table() 
    

has the original row fields along with 6 additional fields, one for each
sample and entry field:

    
    
    Global fields:
        'batch': str
    Row fields:
        'locus': locus<GRCh37>
        'alleles': array<str>
        'A.GT': call
        'A.GQ': int32
        'B.GT': call
        'B.GQ': int32
        'C.GT': call
        'C.GQ': int32
    Key:
        'locus': locus<GRCh37>
        'alleles': array<str>
    

n_partitions()[[source]](_modules/hail/matrixtable.html#MatrixTable.n_partitions)

    

Number of partitions.

Notes

The data in a dataset is divided into chunks called partitions, which may be
stored together or across a network, so that each partition may be read and
processed in parallel by available cores. Partitions are a core concept of
distributed computation in Spark, see
[here](http://spark.apache.org/docs/latest/programming-guide.html#resilient-
distributed-datasets-rdds) for details.

Returns:

    

_int_ – Number of partitions.

naive_coalesce(_max_partitions_)[[source]](_modules/hail/matrixtable.html#MatrixTable.naive_coalesce)

    

Naively decrease the number of partitions.

Example

Naively repartition to 10 partitions:

    
    
    >>> dataset_result = dataset.naive_coalesce(10)
    

Warning

`naive_coalesce()` simply combines adjacent partitions to achieve the desired
number. It does not attempt to rebalance, unlike `repartition()`, so it can
produce a heavily unbalanced dataset. An unbalanced dataset can be inefficient
to operate on because the work is not evenly distributed across partitions.

Parameters:

    

**max_partitions** (_int_) – Desired number of partitions. If the current
number of partitions is less than or equal to max_partitions, do nothing.

Returns:

    

`MatrixTable` – Matrix table with at most max_partitions partitions.

persist(_storage_level
='MEMORY_AND_DISK'_)[[source]](_modules/hail/matrixtable.html#MatrixTable.persist)

    

Persist this table in memory or on disk.

Examples

Persist the dataset to both memory and disk:

    
    
    >>> dataset = dataset.persist() 
    

Notes

The `MatrixTable.persist()` and `MatrixTable.cache()` methods store the
current dataset on disk or in memory temporarily to avoid redundant
computation and improve the performance of Hail pipelines. This method is not
a substitution for [`Table.write()`](hail.Table.html#hail.Table.write
"hail.Table.write"), which stores a permanent file.

Most users should use the “MEMORY_AND_DISK” storage level. See the [Spark
documentation](http://spark.apache.org/docs/latest/programming-guide.html#rdd-
persistence) for a more in-depth discussion of persisting data.

Parameters:

    

**storage_level** (_str_) – Storage level. One of: NONE, DISK_ONLY,
DISK_ONLY_2, MEMORY_ONLY, MEMORY_ONLY_2, MEMORY_ONLY_SER, MEMORY_ONLY_SER_2,
MEMORY_AND_DISK, MEMORY_AND_DISK_2, MEMORY_AND_DISK_SER,
MEMORY_AND_DISK_SER_2, OFF_HEAP

Returns:

    

`MatrixTable` – Persisted dataset.

rename(_fields_)[[source]](_modules/hail/matrixtable.html#MatrixTable.rename)

    

Rename fields of a matrix table.

Examples

Rename column key s to SampleID, still keying by SampleID.

    
    
    >>> dataset_result = dataset.rename({'s': 'SampleID'})
    

You can rename a field to a field name that already exists, as long as that
field also gets renamed (no name collisions). Here, we rename the column key s
to info, and the row field info to vcf_info:

    
    
    >>> dataset_result = dataset.rename({'s': 'info', 'info': 'vcf_info'})
    

Parameters:

    

**fields** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict
"\(in Python v3.9\)") from
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") to [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")) – Mapping from old field names to new field names.

Returns:

    

`MatrixTable` – Matrix table with renamed fields.

repartition(_n_partitions_ , _shuffle
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.repartition)

    

Change the number of partitions.

Examples

Repartition to 500 partitions:

    
    
    >>> dataset_result = dataset.repartition(500)
    

Notes

Check the current number of partitions with `n_partitions()`.

The data in a dataset is divided into chunks called partitions, which may be
stored together or across a network, so that each partition may be read and
processed in parallel by available cores. When a matrix with \\(M\\) rows is
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

    

  * **n_partitions** (_int_) – Desired number of partitions.

  * **shuffle** (_bool_) – If `True`, use full shuffle to repartition.

Returns:

    

`MatrixTable` – Repartitioned dataset.

_property _row

    

Returns a struct expression of all row-indexed fields, including keys.

Examples

Get the first five row field names:

    
    
    >>> list(dataset.row)[:5]
    ['locus', 'alleles', 'rsid', 'qual', 'filters']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all row fields.

_property _row_key

    

Row key struct.

Examples

Get the row key field names:

    
    
    >>> list(dataset.row_key)
    ['locus', 'alleles']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

_property _row_value

    

Returns a struct expression including all non-key row-indexed fields.

Examples

Get the first five non-key row field names:

    
    
    >>> list(dataset.row_value)[:5]
    ['rsid', 'qual', 'filters', 'info', 'use_as_marker']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all row fields, minus keys.

rows()[[source]](_modules/hail/matrixtable.html#MatrixTable.rows)

    

Returns a table with all row fields in the matrix.

Examples

Extract the row table:

    
    
    >>> rows_table = dataset.rows()
    

Returns:

    

```Table``` – Table with all row fields
from the matrix, with one row per row of the matrix.

sample_cols(_p_ , _seed
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.sample_cols)

    

Downsample the matrix table by keeping each column with probability `p`.

Examples

Downsample the dataset to approximately 1% of its columns.

    
    
    >>> small_dataset = dataset.sample_cols(0.01)
    

Parameters:

    

  * **p** ([`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Probability of keeping each column.

  * **seed** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Random seed.

Returns:

    

`MatrixTable` – Matrix table with approximately `p * n_cols` column.

sample_rows(_p_ , _seed
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.sample_rows)

    

Downsample the matrix table by keeping each row with probability `p`.

Examples

Downsample the dataset to approximately 1% of its rows.

    
    
    >>> small_dataset = dataset.sample_rows(0.01)
    

Notes

Although the `MatrixTable` returned by this method may be small, it requires a
full pass over the rows of the sampled object.

Parameters:

    

  * **p** ([`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Probability of keeping each row.

  * **seed** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Random seed.

Returns:

    

`MatrixTable` – Matrix table with approximately `p * n_rows` rows.

select_cols(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.select_cols)

    

Select existing column fields or create new fields by name, dropping the rest.

Examples

Select existing fields and compute a new one:

    
    
    >>> dataset_result = dataset.select_cols(
    ...     dataset.sample_qc,
    ...     dataset.pheno.age,
    ...     isCohort1 = dataset.pheno.cohort_name == 'Cohort1')
    

Notes

This method creates new column fields. If a created field shares its name with
a differently-indexed field of the table, the method will fail.

Note

See ```Table.select()```
for more information about using `select` methods.

Note

This method supports aggregation over rows. For instance, the usage:

    
    
    >>> dataset_result = dataset.select_cols(mean_GQ = hl.agg.mean(dataset.GQ))
    

will compute the mean per column.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – MatrixTable with specified column fields.

select_entries(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.select_entries)

    

Select existing entry fields or create new fields by name, dropping the rest.

Examples

Drop all entry fields aside from GT:

    
    
    >>> dataset_result = dataset.select_entries(dataset.GT)
    

Notes

This method creates new entry fields. If a created field shares its name with
a differently-indexed field of the table, the method will fail.

Note

See ```Table.select()```
for more information about using `select` methods.

Note

This method does not support aggregation.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – MatrixTable with specified entry fields.

select_globals(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.select_globals)

    

Select existing global fields or create new fields by name, dropping the rest.

Examples

Select one existing field and compute a new one:

    
    
    >>> dataset_result = dataset.select_globals(dataset.global_field_1,
    ...                                         another_global=['AFR', 'EUR', 'EAS', 'AMR', 'SAS'])
    

Notes

This method creates new global fields. If a created field shares its name with
a differently-indexed field of the table, the method will fail.

Note

See ```Table.select()```
for more information about using `select` methods.

Note

This method does not support aggregation.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – MatrixTable with specified global fields.

select_rows(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.select_rows)

    

Select existing row fields or create new fields by name, dropping all other
non-key fields.

Examples

Select existing fields and compute a new one:

    
    
    >>> dataset_result = dataset.select_rows(
    ...    dataset.variant_qc.gq_stats.mean,
    ...    high_quality_cases = hl.agg.count_where((dataset.GQ > 20) &
    ...                                         dataset.is_case))
    

Notes

This method creates new row fields. If a created field shares its name with a
differently-indexed field of the table, or with a row key, the method will
fail.

Row keys are preserved. To drop or change a row key field, use
`MatrixTable.key_rows_by()`.

Note

See ```Table.select()```
for more information about using `select` methods.

Note

This method supports aggregation over columns. For instance, the usage:

    
    
    >>> dataset_result = dataset.select_rows(mean_GQ = hl.agg.mean(dataset.GQ))
    

will compute the mean per row.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – MatrixTable with specified row fields.

semi_join_cols(_other_)[[source]](_modules/hail/matrixtable.html#MatrixTable.semi_join_cols)

    

Filters the matrix table to columns whose key appears in other.

Parameters:

    

**other** (```Table```) – Table with
compatible key field(s).

Returns:

    

`MatrixTable`

Notes

The column key type of the matrix table must match the key type of other.

This method does not change the schema of the matrix table; it is a filtering
the matrix table to column keys not present in another table.

To discard collumns whose key is present in other, use `anti_join_cols()`.

Examples

    
    
    >>> ds_result = ds.semi_join_cols(cols_to_keep)
    

It may be inconvenient to key the matrix table by the right-side key. In this
case, it is possible to implement a semi-join using a non-key field as
follows:

    
    
    >>> ds_result = ds.filter_cols(hl.is_defined(cols_to_keep.index(ds['s'])))
    

See also

`anti_join_cols()`, `filter_cols()`, `semi_join_rows()`

semi_join_rows(_other_)[[source]](_modules/hail/matrixtable.html#MatrixTable.semi_join_rows)

    

Filters the matrix table to rows whose key appears in other.

Parameters:

    

**other** (```Table```) – Table with
compatible key field(s).

Returns:

    

`MatrixTable`

Notes

The row key type of the matrix table must match the key type of other.

This method does not change the schema of the matrix table; it is filtering
the matrix table to row keys present in another table.

To discard rows whose key is present in other, use `anti_join_rows()`.

Examples

    
    
    >>> ds_result = ds.semi_join_rows(rows_to_keep)
    

It may be expensive to key the matrix table by the right-side key. In this
case, it is possible to implement a semi-join using a non-key field as
follows:

    
    
    >>> ds_result = ds.filter_rows(hl.is_defined(rows_to_keep.index(ds['locus'], ds['alleles'])))
    

See also

`anti_join_rows()`, `filter_rows()`, `semi_join_cols()`

show(_n_rows =None_, _n_cols =None_, _include_row_fields =False_, _width
=None_, _truncate =None_, _types =True_, _handler
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.show)

    

Print the first few rows of the matrix table to the console.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> mt.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n_rows** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **n_cols** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of columns to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break fields.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

  * **handler** (_Callable[[str], Any]_) – Handler function for data string.

summarize(_*_ , _rows =True_, _cols =True_, _entries =True_, _handler
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.summarize)

    

Compute and print summary information about the fields in the matrix table.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

Parameters:

    

  * **rows** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Compute summary for the row fields.

  * **cols** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Compute summary for the column fields.

  * **entries** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Compute summary for the entry fields.

tail(_n_rows_ , _n_cols =None_, _*_ , _n
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.tail)

    

Subset matrix to last n rows.

Examples

    
    
    >>> mt_range = hl.utils.range_matrix_table(100, 100)
    

Passing only one argument will take the last n rows:

    
    
    >>> mt_range.tail(10).count()
    (10, 100)
    

Passing two arguments refers to rows and columns, respectively:

    
    
    >>> mt_range.tail(10, 20).count()
    (10, 20)
    

Either argument may be `None` to indicate no filter.

Last 10 rows, all columns:

    
    
    >>> mt_range.tail(10, None).count()
    (10, 100)
    

All rows, last 10 columns:

    
    
    >>> mt_range.tail(None, 10).count()
    (100, 10)
    

Notes

For backwards compatibility, the n parameter is not named n_rows, but the
parameter refers to the number of rows to keep.

The number of partitions in the new matrix is equal to the number of
partitions containing the last n rows.

Parameters:

    

  * **n_rows** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Number of rows to include (all rows included if `None`).

  * **n_cols** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Number of cols to include (all cols included if `None`).

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Deprecated in favor of n_rows.

Returns:

    

`MatrixTable` – Matrix including the last n rows and last n_cols cols.

transmute_cols(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.transmute_cols)

    

Similar to `MatrixTable.annotate_cols()`, but drops referenced fields.

Notes

This method adds new column fields according to named_exprs, and drops all
column fields referenced in those expressions. See
[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute") for full documentation on how transmute methods work.

Note

`transmute_cols()` will not drop key fields.

Note

This method supports aggregation over rows.

See also

[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute"), `MatrixTable.select_cols()`,
`MatrixTable.annotate_cols()`

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Annotation expressions.

Returns:

    

`MatrixTable`

transmute_entries(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.transmute_entries)

    

Similar to `MatrixTable.annotate_entries()`, but drops referenced fields.

Notes

This method adds new entry fields according to named_exprs, and drops all
entry fields referenced in those expressions. See
[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute") for full documentation on how transmute methods work.

See also

[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute"), `MatrixTable.select_entries()`,
`MatrixTable.annotate_entries()`

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Annotation expressions.

Returns:

    

`MatrixTable`

transmute_globals(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.transmute_globals)

    

Similar to `MatrixTable.annotate_globals()`, but drops referenced fields.

Notes

This method adds new global fields according to named_exprs, and drops all
global fields referenced in those expressions. See
[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute") for full documentation on how transmute methods work.

See also

[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute"), `MatrixTable.select_globals()`,
`MatrixTable.annotate_globals()`

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Annotation expressions.

Returns:

    

`MatrixTable`

transmute_rows(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.transmute_rows)

    

Similar to `MatrixTable.annotate_rows()`, but drops referenced fields.

Notes

This method adds new row fields according to named_exprs, and drops all row
fields referenced in those expressions. See
[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute") for full documentation on how transmute methods work.

Note

`transmute_rows()` will not drop key fields.

Note

This method supports aggregation over columns.

See also

[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute"), `MatrixTable.select_rows()`,
`MatrixTable.annotate_rows()`

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Annotation expressions.

Returns:

    

`MatrixTable`

unfilter_entries()[[source]](_modules/hail/matrixtable.html#MatrixTable.unfilter_entries)

    

Unfilters filtered entries, populating fields with missing values.

Returns:

    

`MatrixTable`

Notes

This method is used in the case that a pipeline downstream of
`filter_entries()` requires a fully dense (no filtered entries) matrix table.

Generally, if this method is required in a pipeline, the upstream pipeline can
be rewritten to use annotation instead of entry filtering.

See also

`filter_entries()`, `compute_entry_filter_stats()`

union_cols(_other_ , _row_join_type ='inner'_, _drop_right_row_fields
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.union_cols)

    

Take the union of dataset columns.

Warning

This method does not preserve the global fields from the other matrix table.

Examples

Union the columns of two datasets:

    
    
    >>> dataset_result = dataset_to_union_1.union_cols(dataset_to_union_2)
    

Notes

In order to combine two datasets, three requirements must be met:

>   * The row keys must match.
>
>   * The column key schemas and column schemas must match.
>
>   * The entry schemas must match.
>
>

The row fields in the resulting dataset are the row fields from the first
dataset; the row schemas do not need to match.

This method creates a `MatrixTable` which contains all columns from both input
datasets. The set of rows included in the result is determined by the
row_join_type parameter.

  * With the default value of `'inner'`, an inner join is performed on rows, so that only rows whose row key exists in both input datasets are included. In this case, the entries for each row are the concatenation of all entries of the corresponding rows in the input datasets.

  * With row_join_type set to `'outer'`, an outer join is perfomed on rows, so that row keys which exist in only one input dataset are also included. For those rows, the entry fields for the columns coming from the other dataset will be missing.

Only distinct row keys from each dataset are included (equivalent to calling
`distinct_by_row()` on each dataset first).

This method does not deduplicate; if a column key exists identically in two
datasets, then it will be duplicated in the result.

Parameters:

    

  * **other** (`MatrixTable`) – Dataset to concatenate.

  * **row_join_type** (```str```) – If outer, perform an outer join on rows; if ‘inner’, perform an inner join. Default inner.

  * **drop_right_row_fields** (```bool```) – If true, non-key row fields of other are dropped. Otherwise, non-key row fields in the two datasets must have distinct names, and the result contains the union of the row fields.

Returns:

    

`MatrixTable` – Dataset with columns from both datasets.

union_rows(_*_ , __check_cols
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.union_rows)

    

Take the union of dataset rows.

Examples

Union the rows of two datasets:

    
    
    >>> dataset_result = dataset_to_union_1.union_rows(dataset_to_union_2)
    

Given a list of datasets, take the union of all rows:

    
    
    >>> all_datasets = [dataset_to_union_1, dataset_to_union_2]
    

The following three syntaxes are equivalent:

    
    
    >>> dataset_result = dataset_to_union_1.union_rows(dataset_to_union_2)
    >>> dataset_result = all_datasets[0].union_rows(*all_datasets[1:])
    >>> dataset_result = hl.MatrixTable.union_rows(*all_datasets)
    

Notes

In order to combine two datasets, three requirements must be met:

>   * The column keys must be identical, both in type, value, and ordering.
>
>   * The row key schemas and row schemas must match.
>
>   * The entry schemas must match.
>
>

The column fields in the resulting dataset are the column fields from the
first dataset; the column schemas do not need to match.

This method does not deduplicate; if a row exists identically in two datasets,
then it will be duplicated in the result.

Warning

This method can trigger a shuffle, if partitions from two datasets overlap.

Parameters:

    

**datasets** (varargs of `MatrixTable`) – Datasets to combine.

Returns:

    

`MatrixTable` – Dataset with rows from each member of datasets.

unpersist()[[source]](_modules/hail/matrixtable.html#MatrixTable.unpersist)

    

Unpersists this dataset from memory/disk.

Notes

This function will have no effect on a dataset that was not previously
persisted.

Returns:

    

`MatrixTable` – Unpersisted dataset.

write(_output_ , _overwrite =False_, _stage_locally =False_, __codec_spec
=None_, __partitions
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.write)

    

Write to disk.

Examples

    
    
    >>> dataset.write('output/dataset.mt')
    

Danger

Do not write or checkpoint to a path that is already an input source for the
query. This can cause data loss.

See also

[`read_matrix_table()`](methods/impex.html#hail.methods.read_matrix_table
"hail.methods.read_matrix_table")

Parameters:

    

  * **output** (_str_) – Path at which to write.

  * **stage_locally** (_bool_) – If `True`, major output will be written to temporary local storage before being copied to `output`

  * **overwrite** (_bool_) – If `True`, overwrite an existing file at the destination.

---

## GroupedMatrixTable

_class
_hail.GroupedMatrixTable[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable)

    

Bases: [`BaseTable`](hail.table.BaseTable.html#hail.table.BaseTable
"hail.table.BaseTable")

Matrix table grouped by row or column that can be aggregated into a new matrix
table.

Attributes

Methods

`aggregate` | Aggregate entries by group, used after ```MatrixTable.group_rows_by()``` or ```MatrixTable.group_cols_by()```.  
---|---  
`aggregate_cols` | Aggregate cols by group.  
`aggregate_entries` | Aggregate entries by group.  
`aggregate_rows` | Aggregate rows by group.  
`describe` | Print information about grouped matrix table.  
`group_cols_by` | Group columns.  
`group_rows_by` | Group rows.  
`partition_hint` | Set the target number of partitions for aggregation.  
`result` | Return the result of aggregating by group.  
  
aggregate(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.aggregate)

    

Aggregate entries by group, used after
[`MatrixTable.group_rows_by()`](hail.MatrixTable.html#hail.MatrixTable.group_rows_by
"hail.MatrixTable.group_rows_by") or
[`MatrixTable.group_cols_by()`](hail.MatrixTable.html#hail.MatrixTable.group_cols_by
"hail.MatrixTable.group_cols_by").

Examples

Aggregate to a matrix with genes as row keys, computing the number of non-
reference calls as an entry field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    

Notes

Alias for `aggregate_entries()`, `result()`.

See also

`aggregate_entries()`, `result()`

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

```MatrixTable``` –
Aggregated matrix table.

aggregate_cols(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.aggregate_cols)

    

Aggregate cols by group.

Examples

Aggregate to a matrix with cohort as column keys, computing the mean height
per cohort as a new column field:

    
    
    >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
    ...                          .aggregate_cols(mean_height = hl.agg.mean(dataset.pheno.height))
    ...                          .result())
    

Notes

The aggregation scope includes all column fields and global fields.

See also

`result()`

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

`GroupedMatrixTable`

aggregate_entries(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.aggregate_entries)

    

Aggregate entries by group.

Examples

Aggregate to a matrix with genes as row keys, computing the number of non-
reference calls as an entry field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate_entries(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref()))
    ...                          .result())
    

See also

`aggregate()`, `result()`

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

`GroupedMatrixTable`

aggregate_rows(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.aggregate_rows)

    

Aggregate rows by group.

Examples

Aggregate to a matrix with genes as row keys, collecting the functional
consequences per gene as a set as a new row field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate_rows(consequences = hl.agg.collect_as_set(dataset.consequence))
    ...                          .result())
    

Notes

The aggregation scope includes all row fields and global fields.

See also

`result()`

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

`GroupedMatrixTable`

describe(_handler= <built-in function
print>_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.describe)

    

Print information about grouped matrix table.

group_cols_by(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.group_cols_by)

    

Group columns.

Examples

Aggregate to a matrix with cohort as column keys, computing the call rate as
an entry field:

    
    
    >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
    ...                          .aggregate(call_rate = hl.agg.fraction(hl.is_defined(dataset.GT))))
    

Notes

All complex expressions must be passed as named expressions.

Parameters:

    

  * **exprs** (args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Column fields to group by.

  * **named_exprs** (keyword args of ```Expression```) – Column-indexed expressions to group by.

Returns:

    

`GroupedMatrixTable` – Grouped matrix, can be used to call
`GroupedMatrixTable.aggregate()`.

group_rows_by(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.group_rows_by)

    

Group rows.

Examples

Aggregate to a matrix with genes as row keys, computing the number of non-
reference calls as an entry field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    

Notes

All complex expressions must be passed as named expressions.

Parameters:

    

  * **exprs** (args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Row fields to group by.

  * **named_exprs** (keyword args of ```Expression```) – Row-indexed expressions to group by.

Returns:

    

`GroupedMatrixTable` – Grouped matrix. Can be used to call
`GroupedMatrixTable.aggregate()`.

partition_hint(_n_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.partition_hint)

    

Set the target number of partitions for aggregation.

Examples

Use partition_hint in a
[`MatrixTable.group_rows_by()`](hail.MatrixTable.html#hail.MatrixTable.group_rows_by
"hail.MatrixTable.group_rows_by") / `GroupedMatrixTable.aggregate()` pipeline:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .partition_hint(5)
    ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    

Notes

Until Hail’s query optimizer is intelligent enough to sample records at all
stages of a pipeline, it can be necessary in some places to provide some
explicit hints.

The default number of partitions for `GroupedMatrixTable.aggregate()` is the
number of partitions in the upstream dataset. If the aggregation greatly
reduces the size of the dataset, providing a hint for the target number of
partitions can accelerate downstream operations.

Parameters:

    

**n** (_int_) – Number of partitions.

Returns:

    

`GroupedMatrixTable` – Same grouped matrix table with a partition hint.

result()[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.result)

    

Return the result of aggregating by group.

Examples

Aggregate to a matrix with genes as row keys, collecting the functional
consequences per gene as a row field and computing the number of non-reference
calls as an entry field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate_rows(consequences = hl.agg.collect_as_set(dataset.consequence))
    ...                          .aggregate_entries(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref()))
    ...                          .result())
    

Aggregate to a matrix with cohort as column keys, computing the mean height
per cohort as a column field and computing the number of non-reference calls
as an entry field:

    
    
    >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
    ...                          .aggregate_cols(mean_height = hl.agg.stats(dataset.pheno.height).mean)
    ...                          .aggregate_entries(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref()))
    ...                          .result())
    

See also

`aggregate()`

Returns:

    

```MatrixTable``` –
Aggregated matrix table.

---


