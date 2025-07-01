# ReferenceGenome


_class
_hail.genetics.ReferenceGenome[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome)

    

Bases: [`object`](https://docs.python.org/3.9/library/functions.html#object
"\(in Python v3.9\)")

An object that represents a [reference
genome](https://en.wikipedia.org/wiki/Reference_genome).

Examples

    
    
    >>> contigs = ["1", "X", "Y", "MT"]
    >>> lengths = {"1": 249250621, "X": 155270560, "Y": 59373566, "MT": 16569}
    >>> par = [("X", 60001, 2699521)]
    >>> my_ref = hl.ReferenceGenome("my_ref", contigs, lengths, "X", "Y", "MT", par)
    

Notes

Hail comes with predefined reference genomes (case sensitive!):

>   * GRCh37, Genome Reference Consortium Human Build 37
>
>   * GRCh38, Genome Reference Consortium Human Build 38
>
>   * GRCm38, Genome Reference Consortium Mouse Build 38
>
>   * CanFam3, Canis lupus familiaris (dog)
>
>

You can access these reference genome objects using
```get_reference()```:

    
    
    >>> rg = hl.get_reference('GRCh37')
    >>> rg = hl.get_reference('GRCh38')
    >>> rg = hl.get_reference('GRCm38')
    >>> rg = hl.get_reference('CanFam3')
    

Note that constructing a new reference genome, either by using the class
constructor or by using read will add the reference genome to the list of
known references; it is possible to access the reference genome using
```get_reference()```
anytime afterwards.

Note

Reference genome names must be unique. It is not possible to overwrite the
built-in reference genomes.

Note

Hail allows setting a default reference so that the `reference_genome`
argument of [`import_vcf()`](../methods/impex.html#hail.methods.import_vcf
"hail.methods.import_vcf") does not need to be used constantly. It is a
current limitation of Hail that a custom reference genome cannot be used as
the `default_reference` argument of [`init()`](../api.html#hail.init
"hail.init"). In order to set a custom reference genome as default, pass the
reference as an argument to
[`default_reference()`](../api.html#hail.default_reference
"hail.default_reference") after initializing Hail.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Name of reference. Must be unique and NOT one of Hail’s predefined references: `'GRCh37'`, `'GRCh38'`, `'GRCm38'`, `'CanFam3'` and `'default'`.

  * **contigs** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contig names.

  * **lengths** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to [`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Dict of contig names to contig lengths.

  * **x_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as X chromosomes.

  * **y_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as Y chromosomes.

  * **mt_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as mitochondrial DNA.

  * **par** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python v3.9\)") of (str, int, int)) – List of tuples with (contig, start, end)

Attributes

`contigs` | Contig names.  
---|---  
`global_positions_dict` | Get a dictionary mapping contig names to their global genomic positions.  
`lengths` | Dict of contig name to contig length.  
`mt_contigs` | Mitochondrial contigs.  
`name` | Name of reference genome.  
`par` | Pseudoautosomal regions.  
`x_contigs` | X contigs.  
`y_contigs` | Y contigs.  
  
Methods

`add_liftover` | Register a chain file for liftover.  
---|---  
`add_sequence` | Load the reference sequence from a FASTA file.  
`contig_length` | Contig length.  
`from_fasta_file` | Create reference genome from a FASTA file.  
`has_liftover` | `True` if a liftover chain file is available from this reference genome to the destination reference.  
`has_sequence` | True if the reference sequence has been loaded.  
`locus_from_global_position` | "  
`read` | Load reference genome from a JSON file.  
`remove_liftover` | Remove liftover to dest_reference_genome.  
`remove_sequence` | Remove the reference sequence.  
`write` | "Write this reference genome to a file in JSON format.  
  
add_liftover(_chain_file_ ,
_dest_reference_genome_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.add_liftover)

    

Register a chain file for liftover.

Examples

Access GRCh37 and GRCh38 using
```get_reference()```:

    
    
    >>> rg37 = hl.get_reference('GRCh37') 
    >>> rg38 = hl.get_reference('GRCh38') 
    

Add a chain file from 37 to 38:

    
    
    >>> rg37.add_liftover('gs://hail-common/references/grch37_to_grch38.over.chain.gz', rg38) 
    

Notes

This method can only be run once per reference genome. Use `has_liftover()` to
test whether a chain file has been registered.

The chain file format is described
[here](https://genome.ucsc.edu/goldenpath/help/chain.html).

Chain files are hosted on google cloud for some of Hail’s built-in references:

**GRCh37 to GRCh38** gs://hail-
common/references/grch37_to_grch38.over.chain.gz

**GRCh38 to GRCh37** gs://hail-
common/references/grch38_to_grch37.over.chain.gz

Public download links are available
[here](https://console.cloud.google.com/storage/browser/hail-
common/references/).

Parameters:

    

  * **chain_file** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to chain file. Can be compressed (GZIP) or uncompressed.

  * **dest_reference_genome** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or `ReferenceGenome`) – Reference genome to convert to.

add_sequence(_fasta_file_ , _index_file
=None_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.add_sequence)

    

Load the reference sequence from a FASTA file.

Examples

Access the GRCh37 reference genome using
```get_reference()```:

    
    
    >>> rg = hl.get_reference('GRCh37') 
    

Add a sequence file:

    
    
    >>> rg.add_sequence('gs://hail-common/references/human_g1k_v37.fasta.gz',
    ...                 'gs://hail-common/references/human_g1k_v37.fasta.fai') 
    

Add a sequence file with the default index location:

    
    
    >>> rg.add_sequence('gs://hail-common/references/human_g1k_v37.fasta.gz') 
    

Notes

This method can only be run once per reference genome. Use `has_sequence()` to
test whether a sequence is loaded.

FASTA and index files are hosted on google cloud for some of Hail’s built-in
references:

**GRCh37**

  * FASTA file: `gs://hail-common/references/human_g1k_v37.fasta.gz`

  * Index file: `gs://hail-common/references/human_g1k_v37.fasta.fai`

**GRCh38**

  * FASTA file: `gs://hail-common/references/Homo_sapiens_assembly38.fasta.gz`

  * Index file: `gs://hail-common/references/Homo_sapiens_assembly38.fasta.fai`

Public download links are available
[here](https://console.cloud.google.com/storage/browser/hail-
common/references/).

Parameters:

    

  * **fasta_file** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to FASTA file. Can be compressed (GZIP) or uncompressed.

  * **index_file** ([`None`](https://docs.python.org/3.9/library/constants.html#None "\(in Python v3.9\)") or [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to FASTA index file. Must be uncompressed. If None, replace the fasta_file’s extension with fai.

contig_length(_contig_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.contig_length)

    

Contig length.

Parameters:

    

**contig** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")) – Contig name.

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") – Length of contig.

_property _contigs

    

Contig names.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")

_classmethod _from_fasta_file(_name_ , _fasta_file_ , _index_file_ ,
_x_contigs =[]_, _y_contigs =[]_, _mt_contigs =[]_, _par
=[]_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.from_fasta_file)

    

Create reference genome from a FASTA file.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Name for new reference genome.

  * **fasta_file** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to FASTA file. Can be compressed (GZIP) or uncompressed.

  * **index_file** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to FASTA index file. Must be uncompressed.

  * **x_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as X chromosomes.

  * **y_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as Y chromosomes.

  * **mt_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as mitochondrial DNA.

  * **par** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python v3.9\)") of (str, int, int)) – List of tuples with (contig, start, end)

Returns:

    

`ReferenceGenome`

_property _global_positions_dict

    

Get a dictionary mapping contig names to their global genomic positions.

Returns:

    

[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python
v3.9\)") – A dictionary of contig names to global genomic positions.

has_liftover(_dest_reference_genome_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.has_liftover)

    

`True` if a liftover chain file is available from this reference genome to the
destination reference.

Parameters:

    

**dest_reference_genome**
([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or `ReferenceGenome`)

Returns:

    

[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

has_sequence()[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.has_sequence)

    

True if the reference sequence has been loaded.

Returns:

    

[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

_property _lengths

    

Dict of contig name to contig length.

Returns:

    

[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python
v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)") to
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

locus_from_global_position(_global_pos_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.locus_from_global_position)

    

” Constructs a locus from a global position in reference genome. The inverse
of [`Locus.position()`](hail.genetics.Locus.html#hail.genetics.Locus.position
"hail.genetics.Locus.position").

Examples

    
    
    >>> rg = hl.get_reference('GRCh37')
    >>> rg.locus_from_global_position(0)
    Locus(contig=1, position=1, reference_genome=GRCh37)
    
    
    
    >>> rg.locus_from_global_position(2824183054)
    Locus(contig=21, position=42584230, reference_genome=GRCh37)
    
    
    
    >>> rg = hl.get_reference('GRCh38')
    >>> rg.locus_from_global_position(2824183054)
    Locus(contig=chr22, position=1, reference_genome=GRCh38)
    

Parameters:

    

**global_pos** (_int_) – Zero-based global base position along the reference
genome.

Returns:

    

```Locus```

_property _mt_contigs

    

Mitochondrial contigs.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")

_property _name

    

Name of reference genome.

Returns:

    

[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")

_property _par

    

Pseudoautosomal regions.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`Interval`](../utils/index.html#hail.utils.Interval
"hail.utils.Interval")

_classmethod
_read(_path_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.read)

    

Load reference genome from a JSON file.

Notes

The JSON file must have the following format:

    
    
    {"name": "my_reference_genome",
     "contigs": [{"name": "1", "length": 10000000},
                 {"name": "2", "length": 20000000},
                 {"name": "X", "length": 19856300},
                 {"name": "Y", "length": 78140000},
                 {"name": "MT", "length": 532}],
     "xContigs": ["X"],
     "yContigs": ["Y"],
     "mtContigs": ["MT"],
     "par": [{"start": {"contig": "X","position": 60001},"end": {"contig": "X","position": 2699521}},
             {"start": {"contig": "Y","position": 10001},"end": {"contig": "Y","position": 2649521}}]
    }
    

name must be unique and not overlap with Hail’s pre-instantiated references:
`'GRCh37'`, `'GRCh38'`, `'GRCm38'`, `'CanFam3'`, and `'default'`. The contig
names in xContigs, yContigs, and mtContigs must be present in contigs. The
intervals listed in par must have contigs in either xContigs or yContigs and
must have positions between 0 and the contig length given in contigs.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Path to JSON file.

Returns:

    

`ReferenceGenome`

remove_liftover(_dest_reference_genome_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.remove_liftover)

    

Remove liftover to dest_reference_genome.

Parameters:

    

**dest_reference_genome**
([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or `ReferenceGenome`)

remove_sequence()[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.remove_sequence)

    

Remove the reference sequence.

write(_output_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.write)

    

“Write this reference genome to a file in JSON format.

Examples

    
    
    >>> my_rg = hl.ReferenceGenome("new_reference", ["x", "y", "z"], {"x": 500, "y": 300, "z": 200})
    >>> my_rg.write(f"output/new_reference.json")
    

Notes

Use `read()` to reimport the exported reference genome in a new HailContext
session.

Parameters:

    

**output** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")) – Path of JSON file to write.

_property _x_contigs

    

X contigs.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")

_property _y_contigs

    

Y contigs.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")

---

