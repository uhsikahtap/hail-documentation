## GroupedTable


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

