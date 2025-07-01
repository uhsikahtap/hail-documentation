# Common Pitfalls and Essential Framework



## Core Concepts

When working with Hail for genomics analysis, understand these fundamental principles:

1. **Distributed Data Model**: Hail is designed for large-scale computational genomics with a distributed architecture
2. **Lazy Evaluation**: Operations are deferred until explicitly triggered (`show()`, `write()`)
3. **Column-wise Operations**: Unlike pandas, Hail Tables operate column-wise, not row-wise

## Critical Mental Model Shifts

### 1. Expression System & Lazy Evaluation

- Use Hail expression system (not Python native operations)
- Operations are deferred until explicitly triggered
- Always use Hail API functions (`hl.if_else`, `hl.sum`, etc.) instead of Python equivalents
- Avoid Python loops, row-wise operations, or pandas-like methods

### 2. Data Types & Immutability

- Hail data types (`struct`, `array`, `set`) are **immutable**
- Transform data with `map` or `annotate`
- For nested structs, use `annotate` with `map` for collections
- Empty containers must use Hail constructors: `hl.empty_array()`, `hl.empty_dict()`, `hl.empty_set()`

### 3. Distributed Computation

- Always design for scalability with large datasets
- Use Hail's aggregation functions (`hl.agg.*`) for distributed computation
- Create lookup dictionaries properly with Hail tables (not driver-side loops)

## Common Mistakes to Avoid

### Type Errors & Array Operations

- **CRITICAL**: Be careful with nested type specifications for arrays and collections
- When filtering based on array comparison: `ht.filter(ht.order != hl.missing(hl.tarray(hl.tstr)))`
- NOT: `ht.filter(ht.order != hl.array(hl.missing(hl.tstr)))`
- Type errors are often cryptic; pay attention to error messages mentioning "cannot coerce type"

### Immutability & Annotations

- **CRITICAL**: Each `annotate` creates a new table - assignments must capture results
- No method chaining - each transformation step requires its own variable assignment
- Remember that filtering creates a new table that must be assigned to a variable

### API Function Substitutions

- Use `hl.if_else` not Python `if` statements
- Use `hl.missing` not `hl.null` (with proper type specification)
    - Correct: `hl.missing(hl.tarray(hl.tstr))` or `hl.missing('array<str>')`
    - **Important**: When creating empty arrays, you must specify correct nested types
    - Error example: `hl.array(hl.missing(hl.tstr))` will fail as it tries to coerce a string type to an array
- Use `|` for boolean OR operations
- For string comparisons, Hail's `<=`, `<`, `>=`, `>` operators support lexicographical comparison

### Dynamic & Generalizable Code

- Avoid hardcoding values (column names, group identifiers)
- Use `table.row`, `keys()` for dynamic field collection
- Use `hl.set` or similar for dynamic group extraction
- Use dictionary/struct expansion with `` and `*` for dynamic field creation

### Efficient Data Access & Manipulation

- Use bracket notation for dynamic field access: `x[f'{dynamic_variable}_data']`
- For dictionaries, get keys with `weights.keys().collect()[0]`
- Use `.distinct()` to subset rows so each key appears once
- Use `hl.set()` to find unique values in a list

### Complex Structures & Nested Data

- For deeply nested transformations, ensure operations remain lazy
- Remember the pattern for nested annotations:
    
    ```python
    all_positions = all_positions.annotate(
        species = all_positions.species.annotate(
            transcript_mappings = all_positions.species.transcript_mappings.filter(
                lambda x: x.region_type == 'CDS'
            )
        )
    )
    ```
    

### Adding Sequence to a Reference Genome

- Any import or read_table will overwrite the loaded sequences, so it’s best to perform these operations after any import/reading commands

```
human_rg = hl.ReferenceGenome.from_fasta_file(
    name="Homo_sapiens",
    fasta_file=args.human_fasta_path,
    index_file=args.human_index_path
)
```

## Debugging & Best Practices

1. Debug by isolating parts of computation with `show()` on intermediate steps
2. When importing files, handle unexpected BOM characters:
    
    ```python
    rename_dict = {f: f.replace('\\ufeff', '') for f in list(ht.row.dtype)}
    
    ```
    
3. For aggregations in grouped tables, use `hl.agg.collect_as_set()` or `hl.agg.take(ht.Field, 1)[0]`
4. With VCF imports, use `skip_invalid_loci=True` and `array_elements_required=False`
5. Remember reference genome contig naming conventions:
    - hg38: contigs have 'chr' prefix
    - hg37: contigs do not have 'chr' prefix

## Common Patterns & Examples

### Creating Tables

```python
**From list of dictionaries**
t = hl.Table.parallelize(
    [{'a': 5, 'b': 10}, {'a': 0, 'b': 200}],
    schema=hl.tstruct(a=hl.tint, b=hl.tint)
)

**With intervals**
ht = hl.Table.parallelize(
    [{'interval': interval} for interval in intervals],
    schema=hl.tstruct(interval=hl.tinterval(hl.tlocus("GRCh38")))
)

```

### Efficient Lookup Creation

```python
**Create table from list**
sample_info_table = hl.Table.parallelize(
    [{'Child_ID': x} for x in child_ids],
    schema=hl.tstruct(Child_ID=hl.tstr)
)

**Annotate with distributed computation**
sample_info_table = sample_info_table.annotate(
    sample_info = hl.struct(
        child = sample_info_table.Child_ID,
        some_metric = hl.len(sample_info_table.Child_ID)
    )
)

**Collect as lookup dictionary**
sample_info_lookup = hl.dict({
    row.Child_ID: row.sample_info
    for row in sample_info_table.collect()
})

** Use in annotations **
dummy_table = dummy_table.annotate(
    sample_info = sample_info_lookup[dummy_table.child_id]
)

**NOTE: using the .get parameter of hail dicts can be computationally expensive. It's best to use the bracket lookup which will throw an error if the key is not found. **
```

### Select and Annotate Combined

```python
syn_sites = syn_sites.select(
    locus = hl.locus(
        contig=hl.str("chr" + syn_sites.CHROM),
        position=hl.int(syn_sites.POS),
        reference_genome='GRCh38'
    ),
    alleles = [syn_sites.REF, syn_sites.ALT],
    roulette_syn_scaling_site = True
)

```

### Dynamic Field Creation

```python
** Using "**" expansion for dynamic fields **
hl.struct(
    annotation=group,
    **{
        f"{f}_def_species": hl.sum(
            all_positions.combined
                .filter(lambda x: x.annotation == group)
                .map(lambda x: x[f])
        ) for f in fields
    }
)

** NOTE: Be careful when annotating with many rows, sometimes including a checkpoint between annotations can speed up the computation as the hail backend may get too confused **

```

### Complex Nested Array Annotations

```python
trost_data = trost_data.annotate(
    trost_data = trost_data.trost_data.annotate(
        trost_sample_info = hl.map(
            lambda x: hl.struct(
                **{k: x[k] for k in fields},
                flags = hl.if_else(
                    x.gene_counts_over2.any(lambda y: hl.int(y[0]) > 2),
                    x.flags.extend(['gene_over2']),
                    x.flags
                )
            ),
            trost_data.trost_data.trost_sample_info
        )
    )
)

```

## Genomic-Specific Notes

- Allowed contigs: `hl.set([str(x) for x in range(1, 23)] + ["X", "Y", "M"])`
- Locus intervals: `hl.locus_interval("1", 100, 1000, reference_genome='GRCh37')`
- When working with genetic data, remember the difference between locus and variant:
    - A locus has one reference allele (e.g., A)
    - A locus can have multiple alternate alleles (T, C, G)
    - Data may have multiple variant rows at the same locus

Other Notes

-

`hl.flatmap(**lambda** x: x[1:], a)`

Error: invalid decimal literal tp_data.gnomAD4.1_joint_max_AF —> tp_data['gnomAD4.1_joint_max_AF']

Only one of these command per line: key_by, select, annotate

No ‘.’ in any column name 

Best practices:

```
    clean_clinvar_domNDD = hl.if_else(
        hl.is_missing(primate_data.clean_clinvar_domNDD),
        False,
        primate_data.clean_clinvar_domNDD
    )
    
    #hl.if_else(
	  #  primate_data.clean_clinvar_domNDD == True,
	  #  True,
	  #  False
	  #)
	  #This will result in None for any missing value not false
```

- Handle missing cases first
    - hl.if_else(primate_data.clean_clinvar_domNDD == True)


