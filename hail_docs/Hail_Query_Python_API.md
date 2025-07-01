# Hail Query Python API


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

