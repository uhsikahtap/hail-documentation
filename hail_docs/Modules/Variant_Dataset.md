## Variant Dataset


The [`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset") is an extra layer of abstraction of the Hail Matrix
Table for working with large sequencing datasets. It was initially developed
in response to the gnomAD project’s need to combine, represent, and analyze
150,000 whole genomes. It has since been used on datasets as large as 955,000
whole exomes. The
[`VariantDatasetCombiner`](hail.vds.combiner.VariantDatasetCombiner.html#hail.vds.combiner.VariantDatasetCombiner
"hail.vds.combiner.VariantDatasetCombiner") produces a
[`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset") by combining any number of GVCF and/or
[`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset") files.

Warning

Hail 0.1 also had a Variant Dataset class. Although pieces of the interfaces
are similar, they should not be considered interchangeable and do not
represent the same data.

Variant Dataset

```VariantDataset``` | Class for representing cohort-level genomic data.  
---|---  
```read_vds```(path, *[, intervals, n_partitions, ...]) | Read in a ```VariantDataset``` written with ```VariantDataset.write()```.  
---|---  
```filter_samples```(vds, samples, *[, keep, ...]) | Filter samples in a ```VariantDataset```.  
```filter_variants```(vds, variants_table, *[, keep]) | Filter variants in a ```VariantDataset```, without removing reference data.  
```filter_intervals```(vds, intervals, *[, ...]) | Filter intervals in a ```VariantDataset```.  
```filter_chromosomes```(vds, *[, keep, remove, ...]) | Filter chromosomes of a ```VariantDataset``` in several possible modes.  
```sample_qc```(vds, *[, gq_bins, dp_bins, dp_field]) | Compute sample quality metrics about a ```VariantDataset```.  
```split_multi```(vds, *[, filter_changed_loci]) | Split the multiallelic variants in a ```VariantDataset```.  
```interval_coverage```(vds, intervals[, ...]) | Compute statistics about base coverage by interval.  
```impute_sex_chromosome_ploidy```(vds, ...[, ...]) | Impute sex chromosome ploidy from depth of reference or variant data within calling intervals.  
```impute_sex_chr_ploidy_from_interval_coverage```(mt, ...) | Impute sex chromosome ploidy from a precomputed interval coverage MatrixTable.  
```to_dense_mt```(vds) | Creates a single, dense ```MatrixTable``` from the split ```VariantDataset``` representation.  
```to_merged_sparse_mt```(vds, *[, ...]) | Creates a single, merged sparse ```MatrixTable``` from the split ```VariantDataset``` representation.  
```truncate_reference_blocks```(ds, *[, ...]) | Cap reference blocks at a maximum length in order to permit faster interval filtering.  
```merge_reference_blocks```(ds, equivalence_function) | Merge adjacent reference blocks according to user equivalence criteria.  
```lgt_to_gt```(lgt, la) | Transform LGT into GT using local alleles array.  
```local_to_global```(array, local_alleles, ...) | Reindex a locally-indexed array to globally-indexed.  
```store_ref_block_max_length```(vds_path) | Patches an existing VDS file to store the max reference block length for faster interval filters.  
  
Variant Dataset Combiner

```VDSMetadata``` | The path to a Variant Dataset and the number of samples within.  
---|---  
```VariantDatasetCombiner``` | A restartable and failure-tolerant method for combining one or more GVCFs and Variant Datasets.  
```new_combiner```(*, output_path, temp_path[, ...]) | Create a new ```VariantDatasetCombiner``` or load one from save_path.  
---|---  
```load_combiner```(path) | Load a ```VariantDatasetCombiner``` from path.  
  
## The data model of

[`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset")

A VariantDataset is the Hail implementation of a data structure called the
“scalable variant call representation”, or SVCR.

### The Scalable Variant Call Representation (SVCR)

Like the project VCF (multi-sample VCF) representation, the scalable variant
call representation is a variant-by-sample matrix of records. There are two
fundamental differences, however:

  1. The scalable variant call representation is **sparse**. It is not a dense matrix with every entry populated. Reference calls are defined as intervals (reference blocks) exactly as they appear in the original GVCFs. Compared to a VCF representation, this stores **less data but more information** , and makes it possible to keep reference information about every site in the genome, not just sites at which there is variation in the current cohort. A VariantDataset has a component table of reference information, `vds.reference_data`, which contains the sparse matrix of reference blocks. This matrix is keyed by locus (not locus and alleles), and contains an `END` field which denotes the last position included in the current reference block.

  2. The scalable variant call representation uses **local alleles**. In a VCF, the fields GT, AD, PL, etc contain information that refers to alleles in the VCF by index. At highly multiallelic sites, the number of elements in the AD/PL lists explodes to huge numbers, **even though the information content does not change**. To avoid this superlinear scaling, the SVCR renames these fields to their “local” versions: LGT, LAD, LPL, etc, and adds a new field, LA (local alleles). The information in the local fields refers to the alleles defined per row of the matrix indirectly through the LA list.

For instance, if a sample has the following information in its GVCF:

         
         Ref=G Alt=T GT=0/1 AD=5,6 PL=102,0,150
         

If the alternate alleles A,C,T are discovered in the cohort, this sample’s
entry would look like:

         
         LA=0,2 LGT=0/1 LAD=5,6 LPL=102,0,150
         

The “1” allele referred to in LGT, and the allele to which the reads in the
second position of LAD belong to, is not the allele with absolute index 1
(**C**), but rather the allele whose index is in position 1 of the LA list.
The _index_ at position 2 of the LA list is 2, and the allele with absolute
index 2 is **T**. Local alleles make it possible to keep the data small to
match its inherent information content.

### Component tables

The [`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset") is made up of two component matrix tables – the
`reference_data` and the `variant_data`.

The `reference_data` matrix table is a sparse matrix of reference blocks. The
`reference_data` matrix table has row key `locus`, but does not have an
`alleles` key or field. The column key is the sample ID. The entries indicate
regions of reference calls with similar sequencing metadata (depth, quality,
etc), starting from `vds.reference_data.locus.position` and ending at
`vds.reference_data.END` (inclusive!). There is no `GT` call field because all
calls in the reference data are implicitly homozygous reference (in the
future, a table of ploidy by interval may be included to allow for proper
representation of structural variation, but there is no standard
representation for this at current). A record from a component GVCF is
included in the `reference_data` if it defines the END INFO field (if the GT
is not reference, an error will be thrown by the Hail VDS combiner).

The `variant_data` matrix table is a sparse matrix of non-reference calls.
This table contains the complete schema from the component GVCFs, aside from
fields which are known to be defined only for reference blocks (e.g. END or
MIN_DP). A record from a component GVCF is included in the `variant_data` if
it does not define the END INFO field. This means that some records of the
`variant_data` can be no-call (`./.`) or reference, depending on the semantics
of the variant caller that produced the GVCFs.

### Building analyses on the
[`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset")

Analyses operating on sequencing data can be largely grouped into three
categories by functionality used.

  1. **Analyses that use prebuilt methods**. Some analyses can be supported by using only the utility functions defined in the `hl.vds` module, like ```vds.sample_qc()```.

  2. **Analyses that use variant data and/or reference data separately.** Some pipelines need to interrogate properties of the component tables individually. Examples might include singleton analysis or burden tests (which needs only to look at the variant data) or coverage analysis (which looks only at reference data). These pipelines should explicitly extract and manipulate the component tables with `vds.variant_data` and `vds.reference_data`.

  3. **Analyses that use the full variant-by-sample matrix with variant and reference data**. Many pipelines require variant and reference data together. There are helper functions provided for producing either the sparse (containing reference blocks) or dense (reference information is filled in at each variant site) representations. For more information, see the documentation for ```vds.to_dense_mt()``` and ```vds.to_merged_sparse_mt()```.

---

