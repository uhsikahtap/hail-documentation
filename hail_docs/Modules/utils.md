## utils


`ANY_REGION` | Built-in mutable sequence.  
---|---  
`Interval`(start, end[, includes_start, ...]) | An object representing a range of values between start and end.  
`Struct`(**kwargs) | Nested annotation structure.  
`frozendict`(d) | An object representing an immutable dictionary.  
`hadoop_open`(path[, mode, buffer_size]) | Open a file through the Hadoop filesystem API.  
`hadoop_copy`(src, dest) | Copy a file through the Hadoop filesystem API.  
`hadoop_exists`(path) | Returns `True` if path exists.  
`hadoop_is_file`(path) | Returns `True` if path both exists and is a file.  
`hadoop_is_dir`(path) | Returns `True` if path both exists and is a directory.  
`hadoop_stat`(path) | Returns information about the file or directory at a given path.  
`hadoop_ls`(path) | Returns information about files at path.  
`hadoop_scheme_supported`(scheme) | Returns `True` if the Hadoop filesystem supports URLs with the given scheme.  
`copy_log`(path) | Attempt to copy the session log to a hadoop-API-compatible location.  
`range_table`(n[, n_partitions]) | Construct a table with the row index and no other fields.  
`range_matrix_table`(n_rows, n_cols[, ...]) | Construct a matrix table with row and column indices and no entry fields.  
`get_1kg`(output_dir[, overwrite]) | Download subset of the [1000 Genomes](http://www.internationalgenome.org/) dataset and sample annotations.  
`get_hgdp`(output_dir[, overwrite]) | Download subset of the [Human Genome Diversity Panel](https://www.internationalgenome.org/data-portal/data-collection/hgdp/) dataset and sample annotations.  
`get_movie_lens`(output_dir[, overwrite]) | Download public Movie Lens dataset.  
  
_class _hail.utils.Interval(_start_ , _end_ , _includes_start =True_,
_includes_end =False_, _point_type
=None_)[[source]](../_modules/hail/utils/interval.html#Interval)

    

An object representing a range of values between start and end.

    
    
    >>> interval2 = hl.Interval(3, 6)
    

Parameters:

    

  * **start** (_any type_) – Object with type point_type.

  * **end** (_any type_) – Object with type point_type.

  * **includes_start** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Interval includes start.

  * **includes_end** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Interval includes end.

Note

This object refers to the Python value returned by taking or collecting Hail
expressions, e.g. `mt.interval.take(5)`. This is rare; it is much more common
to manipulate the
[`IntervalExpression`](../hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression") object, which is constructed using the
following functions:

>   *
> [`interval()`](../functions/constructors.html#hail.expr.functions.interval
> "hail.expr.functions.interval")
>
>   *
> [`locus_interval()`](../functions/genetics.html#hail.expr.functions.locus_interval
> "hail.expr.functions.locus_interval")
>
>   *
> [`parse_locus_interval()`](../functions/genetics.html#hail.expr.functions.parse_locus_interval
> "hail.expr.functions.parse_locus_interval")
>
>

_class _hail.utils.Struct(_**
kwargs_)[[source]](../_modules/hail/utils/struct.html#Struct)

    

Nested annotation structure.

    
    
    >>> bar = hl.Struct(**{'foo': 5, '1kg': 10})
    

Struct elements are treated as both ‘items’ and ‘attributes’, which allows
either syntax for accessing the element “foo” of struct “bar”:

    
    
    >>> bar.foo
    >>> bar['foo']
    

Field names that are not valid Python identifiers, such as fields that start
with numbers or contain spaces, must be accessed with the latter syntax:

    
    
    >>> bar['1kg']
    

The `pprint` module can be used to print nested Structs in a more human-
readable fashion:

    
    
    >>> from pprint import pprint
    >>> pprint(bar)
    

Parameters:

    

**attributes** – Field names and values.

Note

This object refers to the Python value returned by taking or collecting Hail
expressions, e.g. `mt.info.take(5)`. This is rare; it is much more common to
manipulate the
[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") object, which is constructed using the
[`struct()`](../functions/constructors.html#hail.expr.functions.struct
"hail.expr.functions.struct") function.

_class
_hail.utils.frozendict(_d_)[[source]](../_modules/hailtop/frozendict.html#frozendict)

    

An object representing an immutable dictionary.

    
    
    >>> my_frozen_dict = hl.utils.frozendict({1:2, 7:5})
    

To get a normal python dictionary with the same elements from a frozendict:

    
    
    >>> dict(frozendict({'a': 1, 'b': 2}))
    

Note

This object refers to the Python value returned by taking or collecting Hail
expressions, e.g. `mt.my_dict.take(5)`. This is rare; it is much more common
to manipulate the
[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression") object, which is constructed using
[`dict()`](../functions/constructors.html#hail.expr.functions.dict
"hail.expr.functions.dict"). This class is necessary because hail supports
using dicts as keys to other dicts or as elements in sets, while python does
not.

hail.utils.hadoop_open(_path_ , _mode ='r'_, _buffer_size
=8192_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_open)

    

Open a file through the Hadoop filesystem API. Supports distributed file
systems like hdfs, gs, and s3.

Warning

Due to an implementation limitation, `hadoop_open()` may be quite slow for
large data sets (anything larger than 50 MB).

Examples

Write a Pandas DataFrame as a CSV directly into Google Cloud Storage:

    
    
    >>> with hadoop_open('gs://my-bucket/df.csv', 'w') as f: 
    ...     pandas_df.to_csv(f)
    

Read and print the lines of a text file stored in Google Cloud Storage:

    
    
    >>> with hadoop_open('gs://my-bucket/notes.txt') as f: 
    ...     for line in f:
    ...         print(line.strip())
    

Write two lines directly to a file in Google Cloud Storage:

    
    
    >>> with hadoop_open('gs://my-bucket/notes.txt', 'w') as f: 
    ...     f.write('result1: %s\n' % result1)
    ...     f.write('result2: %s\n' % result2)
    

Unpack a packed Python struct directly from a file in Google Cloud Storage:

    
    
    >>> from struct import unpack
    >>> with hadoop_open('gs://my-bucket/notes.txt', 'rb') as f: 
    ...     print(unpack('<f', bytearray(f.read())))
    

Notes

The supported modes are:

>   * `'r'` – Readable text file
> ([`io.TextIOWrapper`](https://docs.python.org/3.9/library/io.html#io.TextIOWrapper
> "\(in Python v3.9\)")). Default behavior.
>
>   * `'w'` – Writable text file
> ([`io.TextIOWrapper`](https://docs.python.org/3.9/library/io.html#io.TextIOWrapper
> "\(in Python v3.9\)")).
>
>   * `'x'` – Exclusive writable text file
> ([`io.TextIOWrapper`](https://docs.python.org/3.9/library/io.html#io.TextIOWrapper
> "\(in Python v3.9\)")). Throws an error if a file already exists at the
> path.
>
>   * `'rb'` – Readable binary file
> ([`io.BufferedReader`](https://docs.python.org/3.9/library/io.html#io.BufferedReader
> "\(in Python v3.9\)")).
>
>   * `'wb'` – Writable binary file
> ([`io.BufferedWriter`](https://docs.python.org/3.9/library/io.html#io.BufferedWriter
> "\(in Python v3.9\)")).
>
>   * `'xb'` – Exclusive writable binary file
> ([`io.BufferedWriter`](https://docs.python.org/3.9/library/io.html#io.BufferedWriter
> "\(in Python v3.9\)")). Throws an error if a file already exists at the
> path.
>
>

The provided destination file path must be a URI (uniform resource
identifier).

Caution

These file handles are slower than standard Python file handles. If you are
writing a large file (larger than ~50M), it will be faster to write to a local
file using standard Python I/O and use `hadoop_copy()` to move your file to a
distributed file system.

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to file.

  * **mode** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – File access mode.

  * **buffer_size** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Buffer size, in bytes.

Returns:

    

_Readable or writable file handle._

hail.utils.hadoop_copy(_src_ ,
_dest_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_copy)

    

Copy a file through the Hadoop filesystem API. Supports distributed file
systems like hdfs, gs, and s3.

Examples

Copy a file from Google Cloud Storage to a local file:

    
    
    >>> hadoop_copy('gs://hail-common/LCR.interval_list',
    ...             'file:///mnt/data/LCR.interval_list') 
    

Notes

Try using `hadoop_open()` first, it’s simpler, but not great for large data!
For example:

    
    
    >>> with hadoop_open('gs://my_bucket/results.csv', 'r') as f: 
    ...     pandas_df.to_csv(f)
    

The provided source and destination file paths must be URIs (uniform resource
identifiers).

Parameters:

    

  * **src** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Source file URI.

  * **dest** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Destination file URI.

hail.utils.hadoop_exists(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_exists)

    

Returns `True` if path exists.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

Returns:

    

[`bool`](../functions/constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")

hail.utils.hadoop_is_file(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_is_file)

    

Returns `True` if path both exists and is a file.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

Returns:

    

[`bool`](../functions/constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")

hail.utils.hadoop_is_dir(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_is_dir)

    

Returns `True` if path both exists and is a directory.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

Returns:

    

[`bool`](../functions/constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")

hail.utils.hadoop_stat(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_stat)

    

Returns information about the file or directory at a given path.

Notes

Raises an error if path does not exist.

The resulting dictionary contains the following data:

  * is_dir ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Path is a directory.

  * size_bytes ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Size in bytes.

  * size ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Size as a readable string.

  * modification_time ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Time of last file modification.

  * owner ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Owner.

  * path ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

Returns:

    

[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python
v3.9\)")

hail.utils.hadoop_ls(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_ls)

    

Returns information about files at path.

Notes

Raises an error if path does not exist.

If path is a file, returns a list with one element. If path is a directory,
returns an element for each file contained in path (does not search
recursively).

Each dict element of the result list contains the following data:

  * is_dir ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Path is a directory.

  * size_bytes ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Size in bytes.

  * size ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Size as a readable string.

  * modification_time ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Time of last file modification.

  * owner ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Owner.

  * path ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") [[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict
"\(in Python v3.9\)")]

hail.utils.hadoop_scheme_supported(_scheme_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_scheme_supported)

    

Returns `True` if the Hadoop filesystem supports URLs with the given scheme.

Examples

    
    
    >>> hadoop_scheme_supported('gs') 
    

Notes

URLs with the https scheme are only supported if they are specifically Azure
Blob Storage URLs of the form
https://<ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<PATH>

Parameters:

    

**scheme** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)"))

Returns:

    

[`bool`](../functions/constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")

hail.utils.copy_log(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#copy_log)

    

Attempt to copy the session log to a hadoop-API-compatible location.

Examples

Specify a manual path:

    
    
    >>> hl.copy_log('gs://my-bucket/analysis-10-jan19.log')  
    INFO: copying log to 'gs://my-bucket/analysis-10-jan19.log'...
    

Copy to a directory:

    
    
    >>> hl.copy_log('gs://my-bucket/')  
    INFO: copying log to 'gs://my-bucket/hail-20180924-2018-devel-46e5fad57524.log'...
    

Notes

Since Hail cannot currently log directly to distributed file systems, this
function is provided as a utility for offloading logs from ephemeral nodes.

If path is a directory, then the log file will be copied using its base name
to the directory (e.g. `/home/hail.log` would be copied as `gs://my-
bucket/hail.log` if path is `gs://my-bucket`.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

hail.utils.range_table(_n_ , _n_partitions
=None_)[[source]](../_modules/hail/utils/misc.html#range_table)

    

Construct a table with the row index and no other fields.

Examples

    
    
    >>> df = hl.utils.range_table(100)
    
    
    
    >>> df.count()
    100
    

Notes

The resulting table contains one field:

>   * idx ([`tint32`](../types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Row index (key).
>
>

This method is meant for testing and learning, and is not optimized for
production performance.

Parameters:

    

  * **n** (_int_) – Number of rows.

  * **n_partitions** (_int, optional_) – Number of partitions (uses Spark default parallelism if None).

Returns:

    

```Table```

hail.utils.range_matrix_table(_n_rows_ , _n_cols_ , _n_partitions
=None_)[[source]](../_modules/hail/utils/misc.html#range_matrix_table)

    

Construct a matrix table with row and column indices and no entry fields.

Examples

    
    
    >>> range_ds = hl.utils.range_matrix_table(n_rows=100, n_cols=10)
    
    
    
    >>> range_ds.count_rows()
    100
    
    
    
    >>> range_ds.count_cols()
    10
    

Notes

The resulting matrix table contains the following fields:

>   * row_idx ([`tint32`](../types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Row index (row key).
>
>   * col_idx ([`tint32`](../types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Column index (column key).
>
>

It contains no entry fields.

This method is meant for testing and learning, and is not optimized for
production performance.

Parameters:

    

  * **n_rows** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Number of rows.

  * **n_cols** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Number of columns.

  * **n_partitions** (_int, optional_) – Number of partitions (uses Spark default parallelism if None).

Returns:

    

```MatrixTable```

hail.utils.get_1kg(_output_dir_ , _overwrite
=False_)[[source]](../_modules/hail/utils/tutorial.html#get_1kg)

    

Download subset of the [1000 Genomes](http://www.internationalgenome.org/)
dataset and sample annotations.

Notes

The download is about 15M.

Parameters:

    

  * **output_dir** – Directory in which to write data.

  * **overwrite** – If `True`, overwrite any existing files/directories at output_dir.

hail.utils.get_hgdp(_output_dir_ , _overwrite
=False_)[[source]](../_modules/hail/utils/tutorial.html#get_hgdp)

    

Download subset of the [Human Genome Diversity
Panel](https://www.internationalgenome.org/data-portal/data-collection/hgdp/)
dataset and sample annotations.

Notes

The download is about 30MB.

Parameters:

    

  * **output_dir** – Directory in which to write data.

  * **overwrite** – If `True`, overwrite any existing files/directories at output_dir.

hail.utils.get_movie_lens(_output_dir_ , _overwrite
=False_)[[source]](../_modules/hail/utils/tutorial.html#get_movie_lens)

    

Download public Movie Lens dataset.

Notes

The download is about 6M.

See the [MovieLens website](https://grouplens.org/datasets/movielens/100k/)
for more information about this dataset.

Parameters:

    

  * **output_dir** – Directory in which to write data.

  * **overwrite** – If `True`, overwrite existing files/directories at those locations.

hail.utils.ANY_REGION

    

Built-in mutable sequence.

If no argument is given, the constructor creates a new empty list. The
argument must be an iterable if specified.

---

