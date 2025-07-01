# How-To: Aggregation

For a full list of aggregators, see the `aggregators` section of the API reference.

## Table Aggregations

### Aggregate Over Rows Into A Local Value

#### One aggregation
**Description:**
Compute the fraction of rows where `SEX` == `'M'` in a table.

**Code:**
```python
>>> ht.aggregate(hl.agg.fraction(ht.SEX == 'M'))
0.5
```

**Dependencies:** `Table.aggregate()`, `aggregators.fraction()`

#### Multiple aggregations
**Description:**
Compute two aggregation statistics, the fraction of rows where `SEX` == `'M'` and the mean value of `X`, from the rows of a table.

**Code:**
```python
>>> ht.aggregate(hl.struct(fraction_male = hl.agg.fraction(ht.SEX == 'M'),
...                        mean_x = hl.agg.mean(ht.X)))
Struct(fraction_male=0.5, mean_x=6.5)
```

**Dependencies:** `Table.aggregate()`, `aggregators.fraction()`, `aggregators.mean()`, `StructExpression`

### Aggregate Per Group
**Description:**
Group the table `ht` by `ID` and compute the mean value of `X` per group.

**Code:**
```python
>>> result_ht = ht.group_by(ht.ID).aggregate(mean_x=hl.agg.mean(ht.X))
```

**Dependencies:** `Table.group_by()`, `GroupedTable.aggregate()`, `aggregators.mean()`

## Matrix Table Aggregations

### Aggregate Entries Per Row (Over Columns)
**Description:**
Count the number of occurrences of each unique `GT` field per row, i.e. aggregate over the columns of the matrix table.
Methods `MatrixTable.filter_rows()`, `MatrixTable.select_rows()`, and `MatrixTable.transmute_rows()` also support aggregation over columns.

**Code:**
```python
>>> result_mt = mt.annotate_rows(gt_counter=hl.agg.counter(mt.GT))
```

**Dependencies:** `MatrixTable.annotate_rows()`, `aggregators.counter()`

### Aggregate Entries Per Column (Over Rows)
**Description:**
Compute the mean of the `GQ` field per column, i.e. aggregate over the rows of the MatrixTable.
Methods `MatrixTable.filter_cols()`, `MatrixTable.select_cols()`, and `MatrixTable.transmute_cols()` also support aggregation over rows.

**Code:**
```python
>>> result_mt = mt.annotate_cols(gq_mean=hl.agg.mean(mt.GQ))
```

**Dependencies:** `MatrixTable.annotate_cols()`, `aggregators.mean()`

### Aggregate Column Values Into a Local Value

#### One aggregation
**Description:**
Aggregate over the column-indexed field `pheno.is_female` to compute the fraction of female samples in the matrix table.

**Code:**
```python
>>> mt.aggregate_cols(hl.agg.fraction(mt.pheno.is_female))
0.44
```

**Dependencies:** `MatrixTable.aggregate_cols()`, `aggregators.fraction()`

#### Multiple aggregations
**Description:**
Perform multiple aggregations over column-indexed fields by using a struct expression. The result is a single struct containing two nested fields, `fraction_female` and `case_ratio`.

**Code:**
```python
>>> mt.aggregate_cols(hl.struct(
...         fraction_female=hl.agg.fraction(mt.pheno.is_female),
...         case_ratio=hl.agg.count_where(mt.is_case) / hl.agg.count()))
Struct(fraction_female=0.44, case_ratio=1.0)
```

**Dependencies:** `MatrixTable.aggregate_cols()`, `aggregators.fraction()`, `aggregators.count_where()`, `StructExpression`

### Aggregate Row Values Into a Local Value

#### One aggregation
**Description:**
Compute the mean value of the row-indexed field `qual`.

**Code:**
```python
>>> mt.aggregate_rows(hl.agg.mean(mt.qual))
140054.73333333334
```

**Dependencies:** `MatrixTable.aggregate_rows()`, `aggregators.mean()`

#### Multiple aggregations
**Description:**
Perform two row aggregations: count the number of row values of `qual` that are greater than 40, and compute the mean value of `qual`. The result is a single struct containing two nested fields, `n_high_quality` and `mean_qual`.

**Code:**
```python
>>> mt.aggregate_rows(
...             hl.struct(n_high_quality=hl.agg.count_where(mt.qual > 40),
...                       mean_qual=hl.agg.mean(mt.qual)))
Struct(n_high_quality=9, mean_qual=140054.73333333334)
```

**Dependencies:** `MatrixTable.aggregate_rows()`, `aggregators.count_where()`, `aggregators.mean()`, `StructExpression`

### Aggregate Entry Values Into A Local Value
**Description:**
Compute the mean of the entry-indexed field `GQ` and the call rate of the entry-indexed field `GT`. The result is returned as a single struct with two nested fields.

**Code:**
```python
>>> mt.aggregate_entries(
...     hl.struct(global_gq_mean=hl.agg.mean(mt.GQ),
...               call_rate=hl.agg.fraction(hl.is_defined(mt.GT))))
Struct(global_gq_mean=69.60514541387025, call_rate=0.9933333333333333)
```

**Dependencies:** `MatrixTable.aggregate_entries()`, `aggregators.mean()`, `aggregators.fraction()`, `StructExpression`

### Aggregate Per Column Group
**Description:**
Group the columns of the matrix table by the column-indexed field `cohort` and compute the call rate per cohort.

**Code:**
```python
>>> result_mt = (mt.group_cols_by(mt.cohort)
...              .aggregate(call_rate=hl.agg.fraction(hl.is_defined(mt.GT))))
```

**Dependencies:** `MatrixTable.group_cols_by()`, `GroupedMatrixTable`, `GroupedMatrixTable.aggregate()`

<details>
<summary>Understanding</summary>

Group the columns of the matrix table by the column-indexed field `cohort` using `MatrixTable.group_cols_by()`, which returns a `GroupedMatrixTable`. Then use `GroupedMatrixTable.aggregate()` to compute an aggregation per column group.
The result is a matrix table with an entry field `call_rate` that contains the result of the aggregation. The new matrix table has a row schema equal to the original row schema, a column schema equal to the fields passed to `group_cols_by`, and an entry schema determined by the expression passed to `aggregate`. Other column fields and entry fields are dropped.

</details>

### Aggregate Per Row Group
**Description:**
Compute the number of calls with one or more non-reference alleles per gene group.

**Code:**
```python
>>> result_mt = (mt.group_rows_by(mt.gene)
...              .aggregate(n_non_ref=hl.agg.count_where(mt.GT.is_non_ref())))
```

**Dependencies:** `MatrixTable.group_rows_by()`, `GroupedMatrixTable`, `GroupedMatrixTable.aggregate()`

<details>
<summary>Understanding</summary>

Group the rows of the matrix table by the row-indexed field `gene` using `MatrixTable.group_rows_by()`, which returns a `GroupedMatrixTable`. Then use `GroupedMatrixTable.aggregate()` to compute an aggregation per grouped row.
The result is a matrix table with an entry field `n_non_ref` that contains the result of the aggregation. This new matrix table has a row schema equal to the fields passed to `group_rows_by`, a column schema equal to the column schema of the original matrix table, and an entry schema determined by the expression passed to `aggregate`. Other row fields and entry fields are dropped.

</details>



