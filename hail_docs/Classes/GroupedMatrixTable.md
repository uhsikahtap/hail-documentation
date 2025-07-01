## GroupedMatrixTable


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


