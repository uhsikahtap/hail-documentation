## MatrixTable


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

