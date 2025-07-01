# How-To: Annotation


Annotations are Hail’s way of adding data fields to Hail’s tables and matrix tables.

## Create a nested annotation

**Description:**
Add a new field `gq_mean` as a nested field inside `info`.

**Code:**
```python
>>> mt = mt.annotate_rows(info=mt.info.annotate(gq_mean=hl.agg.mean(mt.GQ)))
```

**Dependencies:** `StructExpression.annotate()`, `MatrixTable.annotate_rows()`

<details>
<summary>Understanding</summary>

To add a new field `gq_mean` as a nested field inside `info`, instead of a top-level field, we need to annotate the `info` field itself.

Construct an expression `mt.info.annotate(gq_mean=...)` which adds the field to `info`. Then, reassign this expression to `info` using `MatrixTable.annotate_rows()`.

</details>

## Remove a nested annotation

**Description:**
Drop a field `AF`, which is nested inside the `info` field.

**Code:**
```python
>>> mt = mt.annotate_rows(info=mt.info.drop('AF'))
```

**Dependencies:** `StructExpression.drop()`, `MatrixTable.annotate_rows()`

<details>
<summary>Understanding</summary>

To drop a nested field `AF`, construct an expression `mt.info.drop('AF')` which drops the field from its parent field, `info`. Then, reassign this expression to `info` using `MatrixTable.annotate_rows()`.

</details>

