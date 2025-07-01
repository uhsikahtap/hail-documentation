# Locus


_class
_hail.genetics.Locus[[source]](../_modules/hail/genetics/locus.html#Locus)

    

Bases: [`object`](https://docs.python.org/3.9/library/functions.html#object
"\(in Python v3.9\)")

An object that represents a location in the genome.

Parameters:

    

  * **contig** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Chromosome identifier.

  * **position** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Chromosomal position (1-indexed).

  * **reference_genome** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```ReferenceGenome```) – Reference genome to use.

Note

This object refers to the Python value returned by taking or collecting Hail
expressions, e.g. `mt.locus.take(5)`. This is rare; it is much more common to
manipulate the
[`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression") object, which is constructed using the following
functions:

>   * [`locus()`](../functions/genetics.html#hail.expr.functions.locus
> "hail.expr.functions.locus")
>
>   *
> [`parse_locus()`](../functions/genetics.html#hail.expr.functions.parse_locus
> "hail.expr.functions.parse_locus")
>
>   *
> [`locus_from_global_position()`](../functions/genetics.html#hail.expr.functions.locus_from_global_position
> "hail.expr.functions.locus_from_global_position")
>
>

Attributes

`contig` | Chromosome identifier.  
---|---  
`position` | Chromosomal position (1-based).  
`reference_genome` | Reference genome.  
  
Methods

`parse` | Parses a locus object from a CHR:POS string.  
---|---  
  
_property _contig

    

Chromosome identifier. :rtype: str

_classmethod _parse(_string_ , _reference_genome
='default'_)[[source]](../_modules/hail/genetics/locus.html#Locus.parse)

    

Parses a locus object from a CHR:POS string.

**Examples**

    
    
    >>> l1 = hl.Locus.parse('1:101230')
    >>> l2 = hl.Locus.parse('X:4201230')
    

Parameters:

    

  * **string** ([_str_](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – String to parse.

  * **reference_genome** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```ReferenceGenome```) – Reference genome to use. Default is ```default_reference()```.

Return type:

    

`Locus`

_property _position

    

Chromosomal position (1-based). :rtype: int

_property _reference_genome

    

Reference genome.

Returns:

    

[`ReferenceGenome`](hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome")

---

