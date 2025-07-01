# How-To: Genetics

This page tailored how-to guides for small but commonly-used patterns appearing in genetics pipelines. For documentation on the suite of genetics functions built into Hail, see the genetics methods page.

## Formatting

### Convert variants in string format to separate locus and allele fields

**Code:**
```python
>>> ht = ht.key_by(**hl.parse_variant(ht.variant))
```
**Dependencies:** `parse_variant()`, `key_by()`

<details>
<summary>Understanding</summary>

If your variants are strings of the format ‘chr:pos:ref:alt’, you may want to convert them to separate locus and allele fields. This is useful if you have imported a table with variants in string format and you would like to join this table with other Hail tables that are keyed by locus and alleles.

`hl.parse_variant(ht.variant)` constructs a StructExpression containing two nested fields for the locus and alleles. The `**` syntax unpacks this struct so that the resulting table has two new fields, `locus` and `alleles`.

</details>

### Liftover variants from one coordinate system to another
**Tags:** `liftover`

**Description:**
Liftover a Table or MatrixTable from one reference genome to another.

**Code:**
First, we need to set up the two reference genomes (source and destination):
```python
>>> rg37 = hl.get_reference('GRCh37')  
>>> rg38 = hl.get_reference('GRCh38')  
>>> rg37.add_liftover('gs://hail-common/references/grch37_to_grch38.over.chain.gz', rg38)
```
Then we can liftover the locus coordinates in a Table or MatrixTable (here, `ht`) from reference genome 'GRCh37' to 'GRCh38':
```python
>>> ht = ht.annotate(new_locus=hl.liftover(ht.locus, 'GRCh38'))  
>>> ht = ht.filter(hl.is_defined(ht.new_locus))  
>>> ht = ht.key_by(locus=ht.new_locus)
```  
Note that this approach does not retain the old locus, nor does it verify that the allele has not changed strand. We can keep the old one for reference and filter out any liftover that changed strands using:
```python
>>> ht = ht.annotate(new_locus=hl.liftover(ht.locus, 'GRCh38', include_strand=True),
...                  old_locus=ht.locus)  
>>> ht = ht.filter(hl.is_defined(ht.new_locus) & ~ht.new_locus.is_negative_strand)  
>>> ht = ht.key_by(locus=ht.new_locus.result)
```  
**Dependencies:** `liftover()`, `add_liftover()`, `get_reference()`

## Filtering and Pruning

### Remove related individuals from a dataset
**Tags:** `kinship`

**Description:**
Compute a measure of kinship between individuals, and then prune related individuals from a matrix table.

**Code:**
```python
>>> pc_rel = hl.pc_relate(mt.GT, 0.001, k=2, statistics='kin')
>>> pairs = pc_rel.filter(pc_rel['kin'] > 0.125)
>>> related_samples_to_remove = hl.maximal_independent_set(pairs.i, pairs.j,
...                                                        keep=False)
>>> result = mt.filter_cols(
...     hl.is_defined(related_samples_to_remove[mt.col_key]), keep=False)
```
**Dependencies:** `pc_relate()`, `maximal_independent_set()`

<details>
<summary>Understanding</summary>

To remove related individuals from a dataset, we first compute a measure of relatedness between individuals using `pc_relate()`. We filter this result based on a kinship threshold, which gives us a table of related pairs.

From this table of pairs, we can compute the complement of the maximal independent set using `maximal_independent_set()`. The parameter `keep=False` in `maximal_independent_set` specifies that we want the complement of the set (the variants to remove), rather than the maximal independent set itself. It’s important to use the complement for filtering, rather than the set itself, because the maximal independent set will not contain the singleton individuals.

Once we have a list of samples to remove, we can filter the columns of the dataset to remove the related individuals.
</details>

### Filter loci by a list of locus intervals

#### From a table of intervals
**Tags:** `genomic region`, `genomic range`

**Description:**
Import a text file of locus intervals as a table, then use this table to filter the loci in a matrix table.

**Code:**
```python
>>> interval_table = hl.import_locus_intervals('data/gene.interval_list', reference_genome='GRCh37')
>>> filtered_mt = mt.filter_rows(hl.is_defined(interval_table[mt.locus]))
```
**Dependencies:** `import_locus_intervals()`, `MatrixTable.filter_rows()`

<details>
<summary>Understanding</summary>

We have a matrix table `mt` containing the loci we would like to filter, and a list of locus intervals stored in a file. We can import the intervals into a table with `import_locus_intervals()`.

Hail supports implicit joins between locus intervals and loci, so we can filter our dataset to the rows defined in the join between the interval table and our matrix table.

`interval_table[mt.locus]` joins the matrix table with the table of intervals based on locus and `interval<locus>` matches. This is a `StructExpression`, which will be defined if the locus was found in any interval, or missing if the locus is outside all intervals.

To do our filtering, we can filter to the rows of our matrix table where the struct expression `interval_table[mt.locus]` is defined.

This method will also work to filter a table of loci, as well as a matrix table.
</details>

#### From a UCSC BED file
**Description:**
Import a UCSC BED file as a table of intervals, then use this table to filter the loci in a matrix table.

**Code:**
```python
>>> interval_table = hl.import_bed('data/file1.bed', reference_genome='GRCh37')
>>> filtered_mt = mt.filter_rows(hl.is_defined(interval_table[mt.locus]))
```
**Dependencies:** `import_bed()`, `MatrixTable.filter_rows()`

#### Using hl.filter_intervals
**Description:**
Filter using an interval table, suitable for a small list of intervals.

**Code:**
```python
>>> filtered_mt = hl.filter_intervals(mt, interval_table['interval'].collect())
```
**Dependencies:** `methods.filter_intervals()`

#### Declaring intervals with hl.parse_locus_interval
**Description:**
Filter to declared intervals.

**Code:**
```python
>>> intervals = ['1:100M-200M', '16:29.1M-30.2M', 'X']
>>> filtered_mt = hl.filter_intervals(
...     mt,
...     [hl.parse_locus_interval(x, reference_genome='GRCh37') for x in intervals])
```
**Dependencies:** `methods.filter_intervals()`, `parse_locus_interval()`

### Pruning Variants in Linkage Disequilibrium
**Tags:** `LD Prune`

**Description:**
Remove correlated variants from a matrix table.

**Code:**
```python
>>> biallelic_mt = mt.filter_rows(hl.len(mt.alleles) == 2)
>>> pruned_variant_table = hl.ld_prune(mt.GT, r2=0.2, bp_window_size=500000)
>>> filtered_mt = mt.filter_rows(
...     hl.is_defined(pruned_variant_table[mt.row_key]))
```
**Dependencies:** `ld_prune()`

<details>
<summary>Understanding</summary>

Hail’s `ld_prune()` method takes a matrix table and returns a table with a subset of variants which are uncorrelated with each other. The method requires a biallelic dataset, so we first filter our dataset to biallelic variants. Next, we get a table of independent variants using `ld_prune()`, which we can use to filter the rows of our original dataset.

Note that it is more efficient to do the final filtering step on the original dataset, rather than on the biallelic dataset, so that the biallelic dataset does not need to be recomputed.
</details>

## Analysis

### Linear Regression

#### Single Phenotype
**Tags:** `Linear Regression`

**Description:**
Compute linear regression statistics for a single phenotype.

**Code:**
Approach #1: Use the `linear_regression_rows()` method
```python
>>> ht = hl.linear_regression_rows(y=mt.pheno.height,
...                                x=mt.GT.n_alt_alleles(),
...                                covariates=[1])
```
Approach #2: Use the `aggregators.linreg()` aggregator
```python
>>> mt_linreg = mt.annotate_rows(linreg=hl.agg.linreg(y=mt.pheno.height,
...                                                   x=[1, mt.GT.n_alt_alleles()]))
```
**Dependencies:** `linear_regression_rows()`, `aggregators.linreg()`

<details>
<summary>Understanding</summary>

The `linear_regression_rows()` method is more efficient than using the `aggregators.linreg()` aggregator. However, the `aggregators.linreg()` aggregator is more flexible (multiple covariates can vary by entry) and returns a richer set of statistics.
</details>

#### Multiple Phenotypes
**Tags:** `Linear Regression`

**Description:**
Compute linear regression statistics for multiple phenotypes.

**Code:**
Approach #1: Use the `linear_regression_rows()` method for all phenotypes simultaneously
```python
>>> ht_result = hl.linear_regression_rows(y=[mt.pheno.height, mt.pheno.blood_pressure],
...                                       x=mt.GT.n_alt_alleles(),
...                                       covariates=[1])
```
Approach #2: Use the `linear_regression_rows()` method for each phenotype sequentially
```python
>>> ht1 = hl.linear_regression_rows(y=mt.pheno.height,
...                                 x=mt.GT.n_alt_alleles(),
...                                 covariates=[1])
>>> ht2 = hl.linear_regression_rows(y=mt.pheno.blood_pressure,
...                                 x=mt.GT.n_alt_alleles(),
...                                 covariates=[1])
```
Approach #3: Use the `aggregators.linreg()` aggregator
```python
>>> mt_linreg = mt.annotate_rows(
...     linreg_height=hl.agg.linreg(y=mt.pheno.height,
...                                 x=[1, mt.GT.n_alt_alleles()]),
...     linreg_bp=hl.agg.linreg(y=mt.pheno.blood_pressure,
...                             x=[1, mt.GT.n_alt_alleles()]))
```
**Dependencies:** `linear_regression_rows()`, `aggregators.linreg()`

<details>
<summary>Understanding</summary>

The `linear_regression_rows()` method is more efficient than using the `aggregators.linreg()` aggregator, especially when analyzing many phenotypes. However, the `aggregators.linreg()` aggregator is more flexible (multiple covariates can vary by entry) and returns a richer set of statistics. The `linear_regression_rows()` method drops samples that have a missing value for any of the phenotypes. Therefore, Approach #1 may not be suitable for phenotypes with differential patterns of missingness. Approach #2 will do two passes over the data while Approaches #1 and #3 will do one pass over the data and compute the regression statistics for each phenotype simultaneously.
</details>

#### Using Variants (SNPs) as Covariates
**Tags:** `sample genotypes covariate`

**Description:**
Use sample genotype dosage at specific variant(s) as covariates in regression routines.

**Code:**
Create a sample annotation from the genotype dosage for each variant of interest by combining the filter and collect aggregators:
```python
>>> mt_annot = mt.annotate_cols(
...     snp1 = hl.agg.filter(hl.parse_variant('20:13714384:A:C') == mt.row_key,
...                          hl.agg.collect(mt.GT.n_alt_alleles()))[0],
...     snp2 = hl.agg.filter(hl.parse_variant('20:17479730:T:C') == mt.row_key,
...                          hl.agg.collect(mt.GT.n_alt_alleles()))[0])
```
Run the GWAS with `linear_regression_rows()` using variant dosages as covariates:
```python
>>> gwas = hl.linear_regression_rows(  
...     x=mt_annot.GT.n_alt_alleles(),
...     y=mt_annot.pheno.blood_pressure,
...     covariates=[1, mt_annot.pheno.age, mt_annot.snp1, mt_annot.snp2])
```
**Dependencies:** `linear_regression_rows()`, `aggregators.collect()`, `parse_variant()`, `variant_str()`

#### Stratified by Group
**Tags:** `Linear Regression`

**Description:**
Compute linear regression statistics for a single phenotype stratified by group.

**Code:**
Approach #1: Use the `linear_regression_rows()` method for each group
```python
>>> female_pheno = (hl.case()
...                   .when(mt.pheno.is_female, mt.pheno.height)
...                   .or_missing())
>>> linreg_female = hl.linear_regression_rows(y=female_pheno,
...                                           x=mt.GT.n_alt_alleles(),
...                                           covariates=[1])
>>> male_pheno = (hl.case()
...                 .when(~mt.pheno.is_female, mt.pheno.height)
...                 .or_missing())
>>> linreg_male = hl.linear_regression_rows(y=male_pheno,
...                                         x=mt.GT.n_alt_alleles(),
...                                         covariates=[1])
```
Approach #2: Use the `aggregators.group_by()` and `aggregators.linreg()` aggregators
```python
>>> mt_linreg = mt.annotate_rows(
...     linreg=hl.agg.group_by(mt.pheno.is_female,
...                            hl.agg.linreg(y=mt.pheno.height,
...                                          x=[1, mt.GT.n_alt_alleles()])))
```
**Dependencies:** `linear_regression_rows()`, `aggregators.group_by()`, `aggregators.linreg()`

<details>
<summary>Understanding</summary>

We have presented two ways to compute linear regression statistics for each value of a grouping variable. The first approach utilizes the `linear_regression_rows()` method and must be called separately for each group even though it can compute statistics for multiple phenotypes simultaneously. This is because the `linear_regression_rows()` method drops samples that have a missing value for any of the phenotypes. When the groups are mutually exclusive, such as ‘Male’ and ‘Female’, no samples remain! Note that we cannot define `male_pheno = ~female_pheno` because we subsequently need `male_pheno` to be an expression on the `mt_linreg` matrix table rather than `mt`. Lastly, the argument to `root` must be specified for both cases – otherwise the ‘Male’ output will overwrite the ‘Female’ output.

The second approach uses the `aggregators.group_by()` and `aggregators.linreg()` aggregators. The aggregation expression generates a dictionary where a key is a group (value of the grouping variable) and the corresponding value is the linear regression statistics for those samples in the group. The result of the aggregation expression is then used to annotate the matrix table.

The `linear_regression_rows()` method is more efficient than the `aggregators.linreg()` aggregator and can be extended to multiple phenotypes, but the `aggregators.linreg()` aggregator is more flexible (multiple covariates can be vary by entry) and returns a richer set of statistics.
</details>

### PLINK Conversions

#### Polygenic Score Calculation
> **plink:**
> ```
> plink --bfile data --score scores.txt sum
> ```

**Tags:** `PRS`

**Description:**
This command is analogous to plink’s `–score` command with the `sum` option. Biallelic variants are required.

**Code:**
```python
>>> mt = hl.import_plink(
...     bed="data/ldsc.bed", bim="data/ldsc.bim", fam="data/ldsc.fam",
...     quant_pheno=True, missing='-9')
>>> mt = hl.variant_qc(mt)
>>> scores = hl.import_table('data/scores.txt', delimiter=' ', key='rsid',
...                          types={'score': hl.tfloat32})
>>> mt = mt.annotate_rows(**scores[mt.rsid])
>>> flip = hl.case().when(mt.allele == mt.alleles[0], True).when(
...     mt.allele == mt.alleles[1], False).or_missing()
>>> mt = mt.annotate_rows(flip=flip)
>>> mt = mt.annotate_rows(
...     prior=2 * hl.if_else(mt.flip, mt.variant_qc.AF[0], mt.variant_qc.AF[1]))
>>> mt = mt.annotate_cols(
...     prs=hl.agg.sum(
...         mt.score * hl.coalesce(
...             hl.if_else(mt.flip, 2 - mt.GT.n_alt_alleles(),
...                        mt.GT.n_alt_alleles()), mt.prior)))
```
**Dependencies:** `import_plink()

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

# Hail Query Python API

This is the API documentation for `Hail Query`, and provides detailed
information on the Python programming interface.

Use `import hail as hl` to access this functionality.

## Classes

```hail.table.BaseTable``` | Base class for ```Table```, ```GroupedTable```, ```hail.matrixtable.MatrixTable```, and ```hail.matrixtable.GroupedMatrixTable```  
---|---  
```hail.Table``` | Hail's distributed implementation of a dataframe or SQL table.  
```hail.GroupedTable``` | Table grouped by row that can be aggregated into a new table.  
```hail.MatrixTable``` | Hail's distributed implementation of a structured matrix.  
```hail.GroupedMatrixTable``` | Matrix table grouped by row or column that can be aggregated into a new matrix table.  
  
## Modules

  * ``expressions``
  * ``types``
  * ``functions``
  * ``aggregators``
  * ``scans``
  * ``methods``
  * ``nd``
  * ``utils``
  * ``linalg``
  * ``stats``
  * ``genetics``
  * ``plot``
  * ``ggplot``
  * ``vds``
  * ``experimental``

## Top-Level Functions

hail.init(_sc =None_, _app_name =None_, _master =None_, _local ='local[*]'_,
_log =None_, _quiet =False_, _append =False_, _min_block_size =0_,
_branching_factor =50_, _tmp_dir =None_, _default_reference =None_,
_idempotent =False_, _global_seed =None_, _spark_conf =None_,
_skip_logging_configuration =False_, _local_tmpdir =None_,
__optimizer_iterations =None_, _*_ , _backend =None_, _driver_cores =None_,
_driver_memory =None_, _worker_cores =None_, _worker_memory =None_, _batch_id
=None_, _gcs_requester_pays_configuration =None_, _regions =None_,
_gcs_bucket_allow_list =None_, _copy_spark_log_on_error
=False_)[[source]](_modules/hail/context.html#init)

    

Initialize and configure Hail.

This function will be called with default arguments if any Hail functionality
is used. If you need custom configuration, you must explicitly call this
function before using Hail. For example, to set the global random seed to 0,
import Hail and immediately call `init()`:

    
    
    >>> import hail as hl
    >>> hl.init(global_seed=0)  
    

Hail has two backends, `spark` and `batch`. Hail selects a backend by
consulting, in order, these configuration locations:

  1. The `backend` parameter of this function.

  2. The `HAIL_QUERY_BACKEND` environment variable.

  3. The value of `hailctl config get query/backend`.

If no configuration is found, Hail will select the Spark backend.

Examples

Configure Hail to use the Batch backend:

    
    
    >>> import hail as hl
    >>> hl.init(backend='batch')  
    

If a
[`pyspark.SparkContext`](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.SparkContext.html#pyspark.SparkContext
"\(in PySpark vmaster\)") is already running, then Hail must be initialized
with it as an argument:

    
    
    >>> hl.init(sc=sc)  
    

Configure Hail to bill to my_project when accessing any Google Cloud Storage
bucket that has requester pays enabled:

    
    
    >>> hl.init(gcs_requester_pays_configuration='my-project')  
    

Configure Hail to bill to my_project when accessing the Google Cloud Storage
buckets named bucket_of_fish and bucket_of_eels:

    
    
    >>> hl.init(
    ...     gcs_requester_pays_configuration=('my-project', ['bucket_of_fish', 'bucket_of_eels'])
    ... )  
    

You may also use hailctl config set gcs_requester_pays/project and hailctl
config set gcs_requester_pays/buckets to achieve the same effect.

See also

`stop()`

Parameters:

    

  * **sc** (_pyspark.SparkContext, optional_) – Spark Backend only. Spark context. If not specified, the Spark backend will create a new Spark context.

  * **app_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – A name for this pipeline. In the Spark backend, this becomes the Spark application name. In the Batch backend, this is a prefix for the name of every Batch.

  * **master** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – Spark Backend only. URL identifying the Spark leader (master) node or local[N] for local clusters.

  * **local** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Spark Backend only. Local-mode core limit indicator. Must either be local[N] where N is a positive integer or local[*]. The latter indicates Spark should use all cores available. local[*] does not respect most containerization CPU limits. This option is only used if master is unset and spark.master is not set in the Spark configuration.

  * **log** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Local path for Hail log file. Does not currently support distributed file systems like Google Storage, S3, or HDFS.

  * **quiet** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print fewer log messages.

  * **append** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Append to the end of the log file.

  * **min_block_size** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Minimum file block size in MB.

  * **branching_factor** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Branching factor for tree aggregation.

  * **tmp_dir** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – Networked temporary directory. Must be a network-visible file path. Defaults to /tmp in the default scheme.

  * **default_reference** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – _Deprecated_. Please use `default_reference()` to set the default reference genome

Default reference genome. Either `'GRCh37'`, `'GRCh38'`, `'GRCm38'`, or
`'CanFam3'`.

  * **idempotent** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – If `True`, calling this function is a no-op if Hail has already been initialized.

  * **global_seed** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Global random seed.

  * **spark_conf** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to :class`str`, optional) – Spark backend only. Spark configuration parameters.

  * **skip_logging_configuration** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Spark Backend only. Skip logging configuration in java and python.

  * **local_tmpdir** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – Local temporary directory. Used on driver and executor nodes. Must use the file scheme. Defaults to TMPDIR, or /tmp.

  * **driver_cores** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Batch backend only. Number of cores to use for the driver process. May be 1, 2, 4, or 8. Default is 1.

  * **driver_memory** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – Batch backend only. Memory tier to use for the driver process. May be standard or highmem. Default is standard.

  * **worker_cores** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Batch backend only. Number of cores to use for the worker processes. May be 1, 2, 4, or 8. Default is 1.

  * **worker_memory** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – Batch backend only. Memory tier to use for the worker processes. May be standard or highmem. Default is standard.

  * **batch_id** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Batch backend only. An existing batch id to add jobs to.

  * **gcs_requester_pays_configuration** (either [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") and [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – If a string is provided, configure the Google Cloud Storage file system to bill usage to the project identified by that string. If a tuple is provided, configure the Google Cloud Storage file system to bill usage to the specified project for buckets specified in the list. See examples above.

  * **regions** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – List of regions to run jobs in when using the Batch backend. Use ```ANY_REGION``` to specify any region is allowed or use None to use the underlying default regions from the hailctl environment configuration. For example, use hailctl config set batch/regions region1,region2 to set the default regions to use.

  * **gcs_bucket_allow_list** – A list of buckets that Hail should be permitted to read from or write to, even if their default policy is to use “cold” storage. Should look like `["bucket1", "bucket2"]`.

  * **copy_spark_log_on_error** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)"), optional) – Spark backend only. If True, copy the log from the spark driver node to tmp_dir on error.

hail.asc(_col_)[[source]](_modules/hail/table.html#asc)

    

Sort by col ascending.

hail.desc(_col_)[[source]](_modules/hail/table.html#desc)

    

Sort by col descending.

hail.stop()[[source]](_modules/hail/context.html#stop)

    

Stop the currently running Hail session.

hail.spark_context()[[source]](_modules/hail/context.html#spark_context)

    

Returns the active Spark context.

Returns:

    

[`pyspark.SparkContext`](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.SparkContext.html#pyspark.SparkContext
"\(in PySpark vmaster\)")

hail.tmp_dir()[[source]](_modules/hail/context.html#tmp_dir)

    

Returns the Hail shared temporary directory.

Returns:

    

[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")

hail.default_reference(_new_default_reference
=None_)[[source]](_modules/hail/context.html#default_reference)

    

With no argument, returns the default reference genome (`'GRCh37'` by
default). With an argument, sets the default reference genome to the argument.

Returns:

    

[`ReferenceGenome`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome")

hail.get_reference(_name_)[[source]](_modules/hail/context.html#get_reference)

    

Returns the reference genome corresponding to name.

Notes

Hail’s built-in references are `'GRCh37'`, `GRCh38'`, `'GRCm38'`, and
`'CanFam3'`. The contig names and lengths come from the GATK resource bundle:
[human_g1k_v37.dict](ftp://gsapubftp-
anonymous@ftp.broadinstitute.org/bundle/b37/human_g1k_v37.dict) and
[Homo_sapiens_assembly38.dict](ftp://gsapubftp-
anonymous@ftp.broadinstitute.org/bundle/hg38/Homo_sapiens_assembly38.dict).

If `name='default'`, the value of `default_reference()` is returned.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Name of a previously loaded reference genome or one of
Hail’s built-in references: `'GRCh37'`, `'GRCh38'`, `'GRCm38'`, `'CanFam3'`,
and `'default'`.

Returns:

    

[`ReferenceGenome`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome")

hail.set_global_seed(_seed_)[[source]](_modules/hail/context.html#set_global_seed)

    

Deprecated.

Has no effect. To ensure reproducible randomness, use the global_seed argument
to `init()` and `reset_global_randomness()`.

See the ``random functions``
reference docs for more.

Parameters:

    

**seed** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in
Python v3.9\)")) – Integer used to seed Hail’s random number generator

hail.reset_global_randomness()[[source]](_modules/hail/context.html#reset_global_randomness)

    

Restore global randomness to initial state for test reproducibility.

hail.citation(_*_ , _bibtex
=False_)[[source]](_modules/hail/context.html#citation)

    

Generate a Hail citation.

Parameters:

    

**bibtex** (_bool_) – Generate a citation in BibTeX form.

Returns:

    

_str_

hail.version()[[source]](_modules/hail.html#version)

---

