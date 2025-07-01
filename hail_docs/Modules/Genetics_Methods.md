## Genetics Methods


`load_dataset`(name, version, reference_genome) | Load a genetic dataset from Hail's repository.  
---|---  
`ld_score`(entry_expr, locus_expr, radius[, ...]) | Calculate LD scores.  
`ld_score_regression`(weight_expr, ...[, ...]) | Estimate SNP-heritability and level of confounding biases from genome-wide association study (GWAS) summary statistics.  
`write_expression`(expr, path[, overwrite]) | Write an Expression.  
`read_expression`(path[, _assert_type]) | Read an ```Expression``` written with `experimental.write_expression()`.  
`filtering_allele_frequency`(ac, an, ci) | Computes a filtering allele frequency (described below) for ac and an with confidence ci.  
`hail_metadata`(t_path) | Create a metadata plot for a Hail Table or MatrixTable.  
`plot_roc_curve`(ht, scores[, tp_label, ...]) | Create ROC curve from Hail Table.  
`phase_by_transmission`(locus, alleles, ...) | Phases genotype calls in a trio based allele transmission.  
`phase_trio_matrix_by_transmission`(tm[, ...]) | Adds a phased genoype entry to a trio MatrixTable based allele transmission in the trio.  
`explode_trio_matrix`(tm[, col_keys, ...]) | Splits a trio MatrixTable back into a sample MatrixTable.  
`import_gtf`(path[, reference_genome, ...]) | Import a GTF file.  
`get_gene_intervals`([gene_symbols, gene_ids, ...]) | Get intervals of genes or transcripts.  
`export_entries_by_col`(mt, path[, ...]) | Export entries of the mt by column as separate text files.  
`pc_project`(call_expr, loadings_expr, af_expr) | Projects genotypes onto pre-computed PCs.  
  
