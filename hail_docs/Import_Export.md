# Import / Export


This page describes functionality for moving data in and out of Hail.

Hail has a suite of functionality for importing and exporting data to and from
general-purpose, genetics-specific, and high-performance native file formats.

## Native file formats

When saving data to disk with the intent to later use Hail, we highly
recommend that you use the native file formats to store
```Table``` and
```MatrixTable```
objects. These binary formats not only smaller than other formats (especially
textual ones) in most cases, but also are significantly faster to read into
Hail later.

These files can be created with methods on the
```Table``` and
```MatrixTable```
objects:

  * ```Table.write()```

  * ```MatrixTable.write()```

These files can be read into a Hail session later using the following methods:

`read_matrix_table`(path, *[, _intervals, ...]) | Read in a ```MatrixTable``` written with ```MatrixTable.write()```.  
---|---  
`read_table`(path, *[, _intervals, ...]) | Read in a ```Table``` written with ```Table.write()```.  
  
## Import

### General purpose

The `import_table()` function is widely-used to import textual data into a
Hail ```Table```.
`import_matrix_table()` is used to import two-dimensional matrix data in
textual representations into a Hail
```MatrixTable```.
Finally, it is possible to create a Hail Table from a
[`pandas`](https://pandas.pydata.org/docs/index.html#module-pandas "\(in
pandas v2.3.0\)") DataFrame with
[`Table.from_pandas()`](../hail.Table.html#hail.Table.from_pandas
"hail.Table.from_pandas").

`import_table`(paths[, key, min_partitions, ...]) | Import delimited text file (text table) as ```Table```.  
---|---  
`import_matrix_table`(paths[, row_fields, ...]) | Import tab-delimited file(s) as a ```MatrixTable```.  
`import_lines`(paths[, min_partitions, ...]) | Import lines of file(s) as a ```Table``` of strings.  
  
### Genetics

Hail has several functions to import genetics-specific file formats into Hail
```MatrixTable```
or ```Table``` objects:

`import_vcf`(path[, force, force_bgz, ...]) | Import VCF file(s) as a ```MatrixTable```.  
---|---  
`import_plink`(bed, bim, fam[, ...]) | Import a PLINK dataset (BED, BIM, FAM) as a ```MatrixTable```.  
`import_bed`(path[, reference_genome, ...]) | Import a UCSC BED file as a ```Table```.  
`import_bgen`(path, entry_fields[, ...]) | Import BGEN file(s) as a ```MatrixTable```.  
`index_bgen`(path[, index_file_map, ...]) | Index BGEN files as required by `import_bgen()`.  
`import_gen`(path[, sample_file, tolerance, ...]) | Import GEN file(s) as a ```MatrixTable```.  
`import_fam`(path[, quant_pheno, delimiter, ...]) | Import a PLINK FAM file into a ```Table```.  
`import_locus_intervals`(path[, ...]) | Import a locus interval list as a ```Table```.  
  
## Export


