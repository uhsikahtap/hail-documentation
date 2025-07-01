## Methods


  * ``Import / Export``
    * ``Native file formats``
    * ``Import``
    * ``Export``
    * ``Reference documentation``
  * ``Statistics``
    * ```linear_mixed_model()```
    * ```linear_mixed_regression_rows()```
    * ```linear_regression_rows()```
    * ```logistic_regression_rows()```
    * ```poisson_regression_rows()```
    * ```pca()```
    * ```row_correlation()```
  * ``Genetics``
    * ```VEPConfig```
    * ```VEPConfigGRCh37Version85```
    * ```VEPConfigGRCh38Version95```
    * ```balding_nichols_model()```
    * ```concordance()```
    * ```filter_intervals()```
    * ```filter_alleles()```
    * ```filter_alleles_hts()```
    * ```hwe_normalized_pca()```
    * ```genetic_relatedness_matrix()```
    * ```realized_relationship_matrix()```
    * ```impute_sex()```
    * ```ld_matrix()```
    * ```ld_prune()```
    * ```compute_charr()```
    * ```mendel_errors()```
    * ```de_novo()```
    * ```nirvana()```
    * ```sample_qc()```
    * ```_logistic_skat()```
    * ```skat()```
    * ```lambda_gc()```
    * ```split_multi()```
    * ```split_multi_hts()```
    * ```summarize_variants()```
    * ```transmission_disequilibrium_test()```
    * ```trio_matrix()```
    * ```variant_qc()```
    * ```vep()```
  * ``Relatedness``
    * ```identity_by_descent()```
    * ```king()```
    * ```pc_relate()```
    * ```simulate_random_mating()```
  * ``Miscellaneous``
    * ```grep()```
    * ```maximal_independent_set()```
    * ```rename_duplicates()```
    * ```segment_intervals()```

Import / Export

```export_elasticsearch```(t, host, port, index, ...) | Export a ```Table``` to Elasticsearch.  
---|---  
```export_gen```(dataset, output[, precision, gp, ...]) | Export a ```MatrixTable``` as GEN and SAMPLE files.  
```export_bgen```(mt, output[, gp, varid, rsid, ...]) | Export MatrixTable as ```MatrixTable``` as BGEN 1.2 file with 8 bits of per probability.  
```export_plink```(dataset, output[, call, ...]) | Export a ```MatrixTable``` as [PLINK2](https://www.cog-genomics.org/plink2/formats) BED, BIM and FAM files.  
```export_vcf```(dataset, output[, ...]) | Export a ```MatrixTable``` or ```Table``` as a VCF file.  
```get_vcf_metadata```(path) | Extract metadata from VCF header.  
```import_bed```(path[, reference_genome, ...]) | Import a UCSC BED file as a ```Table```.  
```import_bgen```(path, entry_fields[, ...]) | Import BGEN file(s) as a ```MatrixTable```.  
```import_fam```(path[, quant_pheno, delimiter, ...]) | Import a PLINK FAM file into a ```Table```.  
```import_gen```(path[, sample_file, tolerance, ...]) | Import GEN file(s) as a ```MatrixTable```.  
```import_locus_intervals```(path[, ...]) | Import a locus interval list as a ```Table```.  
```import_matrix_table```(paths[, row_fields, ...]) | Import tab-delimited file(s) as a ```MatrixTable```.  
```import_plink```(bed, bim, fam[, ...]) | Import a PLINK dataset (BED, BIM, FAM) as a ```MatrixTable```.  
```import_table```(paths[, key, min_partitions, ...]) | Import delimited text file (text table) as ```Table```.  
```import_vcf```(path[, force, force_bgz, ...]) | Import VCF file(s) as a ```MatrixTable```.  
```index_bgen```(path[, index_file_map, ...]) | Index BGEN files as required by ```import_bgen()```.  
```read_matrix_table```(path, *[, _intervals, ...]) | Read in a ```MatrixTable``` written with ```MatrixTable.write()```.  
```read_table```(path, *[, _intervals, ...]) | Read in a ```Table``` written with ```Table.write()```.  
  
Statistics

```linear_mixed_model```(y, x[, z_t, k, p_path, ...]) | Initialize a linear mixed model from a matrix table.  
---|---  
```linear_mixed_regression_rows```(entry_expr, model) | For each row, test an input variable for association using a linear mixed model.  
```linear_regression_rows```(y, x, covariates[, ...]) | For each row, test an input variable for association with response variables using linear regression.  
```logistic_regression_rows```(test, y, x, covariates) | For each row, test an input variable for association with a binary response variable using logistic regression.  
```poisson_regression_rows```(test, y, x, covariates) | For each row, test an input variable for association with a count response variable using [Poisson regression](https://en.wikipedia.org/wiki/Poisson_regression).  
```pca```(entry_expr[, k, compute_loadings]) | Run principal component analysis (PCA) on numeric columns derived from a matrix table.  
```row_correlation```(entry_expr[, block_size]) | Computes the correlation matrix between row vectors.  
  
Genetics

```balding_nichols_model```(n_populations, ...[, ...]) | Generate a matrix table of variants, samples, and genotypes using the Balding-Nichols or Pritchard-Stephens-Donnelly model.  
---|---  
```concordance```(left, right, *[, ...]) | Calculate call concordance with another dataset.  
```filter_intervals```(ds, intervals[, keep]) | Filter rows with a list of intervals.  
```filter_alleles```(mt, f) | Filter alternate alleles.  
```filter_alleles_hts```(mt, f[, subset]) | Filter alternate alleles and update standard GATK entry fields.  
```genetic_relatedness_matrix```(call_expr) | Compute the genetic relatedness matrix (GRM).  
```hwe_normalized_pca```(call_expr[, k, ...]) | Run principal component analysis (PCA) on the Hardy-Weinberg-normalized genotype call matrix.  
```impute_sex```(call[, aaf_threshold, ...]) | Impute sex of samples by calculating inbreeding coefficient on the X chromosome.  
```ld_matrix```(entry_expr, locus_expr, radius[, ...]) | Computes the windowed correlation (linkage disequilibrium) matrix between variants.  
```ld_prune```(call_expr[, r2, bp_window_size, ...]) | Returns a maximal subset of variants that are nearly uncorrelated within each window.  
```compute_charr```(ds[, min_af, max_af, min_dp, ...]) | Compute CHARR, the DNA sample contamination estimator.  
```mendel_errors```(call, pedigree) | Find Mendel errors; count per variant, individual and nuclear family.  
```de_novo```(mt, pedigree, pop_frequency_prior, *) | Call putative _de novo_ events from trio data.  
```nirvana```(dataset, config[, block_size, name]) | Annotate variants using [Nirvana](https://github.com/Illumina/Nirvana).  
```realized_relationship_matrix```(call_expr) | Computes the realized relationship matrix (RRM).  
```sample_qc```(mt[, name]) | Compute per-sample metrics useful for quality control.  
```skat```(key_expr, weight_expr, y, x, covariates) | Test each keyed group of rows for association by linear or logistic SKAT test.  
```lambda_gc```(p_value[, approximate]) | Compute genomic inflation factor (lambda GC) from an Expression of p-values.  
```split_multi```(ds[, keep_star, left_aligned, ...]) | Split multiallelic variants.  
```split_multi_hts```(ds[, keep_star, ...]) | Split multiallelic variants for datasets that contain one or more fields from a standard high-throughput sequencing entry schema.  
```transmission_disequilibrium_test```(dataset, ...) | Performs the transmission disequilibrium test on trios.  
```trio_matrix```(dataset, pedigree[, complete_trios]) | Builds and returns a matrix where columns correspond to trios and entries contain genotypes for the trio.  
```variant_qc```(mt[, name]) | Compute common variant statistics (quality control metrics).  
```vep```(dataset[, config, block_size, name, ...]) | Annotate variants with VEP.  
  
Relatedness

Hail provides three methods for the inference of relatedness: PLINK-style
identity by descent [1], KING [2], and PC-Relate [3].

  * ```identity_by_descent()``` is appropriate for datasets containing one homogeneous population.

  * ```king()``` is appropriate for datasets containing multiple homogeneous populations and no admixture. It is also used to prune close relatives before using ```pc_relate()```.

  * ```pc_relate()``` is appropriate for datasets containing multiple homogeneous populations and admixture.

```identity_by_descent```(dataset[, maf, bounded, ...]) | Compute matrix of identity-by-descent estimates.  
---|---  
```king```(call_expr, *[, block_size]) | Compute relatedness estimates between individuals using a KING variant.  
```pc_relate```(call_expr, min_individual_maf, *) | Compute relatedness estimates between individuals using a variant of the PC-Relate method.  
  
Miscellaneous

```grep```(regex, path[, max_count, show, force, ...]) | Searches given paths for all lines containing regex matches.  
---|---  
```maximal_independent_set```(i, j[, keep, ...]) | Return a table containing the vertices in a near [maximal independent set](https://en.wikipedia.org/wiki/Maximal_independent_set) of an undirected graph whose edges are given by a two-column table.  
```rename_duplicates```(dataset[, name]) | Rename duplicate column keys.  
```segment_intervals```(ht, points) | Segment the interval keys of ht at a given set of points.  
[1]

Purcell, Shaun et al. “PLINK: a tool set for whole-genome association and
population-based linkage analyses.” American journal of human genetics vol.
81,3 (2007): 559-75. doi:10.1086/519795.
https://www.ncbi.nlm.nih.gov/pmc/articles/PMC1950838/

[2]

Manichaikul, Ani et al. “Robust relationship inference in genome-wide
association studies.” Bioinformatics (Oxford, England) vol. 26,22 (2010):
2867-73. doi:10.1093/bioinformatics/btq559.
https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3025716/

[3]

Conomos, Matthew P et al. “Model-free Estimation of Recent Genetic
Relatedness.” American journal of human genetics vol. 98,1 (2016): 127-48.
doi:10.1016/j.ajhg.2015.11.022.
https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4716688/

---

