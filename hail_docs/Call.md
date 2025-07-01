# Call


_class
_hail.genetics.Call[[source]](../_modules/hail/genetics/call.html#Call)

    

Bases: [`object`](https://docs.python.org/3.9/library/functions.html#object
"\(in Python v3.9\)")

An object that represents an individual’s call at a genomic locus.

Parameters:

    

  * **alleles** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – List of alleles that compose the call.

  * **phased** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – If `True`, the alleles are phased and the order is specified by alleles.

Note

This object refers to the Python value returned by taking or collecting Hail
expressions, e.g. `mt.GT.take(5`)`. This is rare; it is much more common to
manipulate the
[`CallExpression`](../hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression") object, which is constructed using the following
functions:

>   * [`call()`](../functions/genetics.html#hail.expr.functions.call
> "hail.expr.functions.call")
>
>   *
> [`unphased_diploid_gt_index_call()`](../functions/genetics.html#hail.expr.functions.unphased_diploid_gt_index_call
> "hail.expr.functions.unphased_diploid_gt_index_call")
>
>   *
> [`parse_call()`](../functions/genetics.html#hail.expr.functions.parse_call
> "hail.expr.functions.parse_call")
>
>

Attributes

`alleles` | Get the alleles of this call.  
---|---  
`phased` | True if the call is phased.  
`ploidy` | The number of alleles for this call.  
  
Methods

`is_diploid` | True if the ploidy == 2.  
---|---  
`is_haploid` | True if the ploidy == 1.  
`is_het` | True if the call contains two different alleles.  
`is_het_non_ref` | True if the call contains two different alternate alleles.  
`is_het_ref` | True if the call contains one reference and one alternate allele.  
`is_hom_ref` | True if the call has no alternate alleles.  
`is_hom_var` | True if the call contains identical alternate alleles.  
`is_non_ref` | True if the call contains any non-reference alleles.  
`n_alt_alleles` | Returns the count of non-reference alleles.  
`one_hot_alleles` | Returns a list containing the one-hot encoded representation of the called alleles.  
`unphased_diploid_gt_index` | Return the genotype index for unphased, diploid calls.  
  
_property _alleles

    

Get the alleles of this call.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`int`](https://docs.python.org/3.9/library/functions.html#int
"\(in Python v3.9\)")

is_diploid()[[source]](../_modules/hail/genetics/call.html#Call.is_diploid)

    

True if the ploidy == 2.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_haploid()[[source]](../_modules/hail/genetics/call.html#Call.is_haploid)

    

True if the ploidy == 1.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_het()[[source]](../_modules/hail/genetics/call.html#Call.is_het)

    

True if the call contains two different alleles.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_het_non_ref()[[source]](../_modules/hail/genetics/call.html#Call.is_het_non_ref)

    

True if the call contains two different alternate alleles.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_het_ref()[[source]](../_modules/hail/genetics/call.html#Call.is_het_ref)

    

True if the call contains one reference and one alternate allele.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_hom_ref()[[source]](../_modules/hail/genetics/call.html#Call.is_hom_ref)

    

True if the call has no alternate alleles.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_hom_var()[[source]](../_modules/hail/genetics/call.html#Call.is_hom_var)

    

True if the call contains identical alternate alleles.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_non_ref()[[source]](../_modules/hail/genetics/call.html#Call.is_non_ref)

    

True if the call contains any non-reference alleles.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

n_alt_alleles()[[source]](../_modules/hail/genetics/call.html#Call.n_alt_alleles)

    

Returns the count of non-reference alleles.

Return type:

    

[int](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

one_hot_alleles(_n_alleles_)[[source]](../_modules/hail/genetics/call.html#Call.one_hot_alleles)

    

Returns a list containing the one-hot encoded representation of the called
alleles.

Examples

    
    
    >>> n_alleles = 2
    >>> hom_ref = hl.Call([0, 0])
    >>> het = hl.Call([0, 1])
    >>> hom_var = hl.Call([1, 1])
    
    
    
    >>> het.one_hot_alleles(n_alleles)
    [1, 1]
    
    
    
    >>> hom_var.one_hot_alleles(n_alleles)
    [0, 2]
    

Notes

This one-hot representation is the positional sum of the one-hot encoding for
each called allele. For a biallelic variant, the one-hot encoding for a
reference allele is [1, 0] and the one-hot encoding for an alternate allele is
[0, 1].

Parameters:

    

**n_alleles** ([`int`](https://docs.python.org/3.9/library/functions.html#int
"\(in Python v3.9\)")) – Number of total alleles, including the reference.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`int`](https://docs.python.org/3.9/library/functions.html#int
"\(in Python v3.9\)")

_property _phased

    

True if the call is phased.

Returns:

    

[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

_property _ploidy

    

The number of alleles for this call.

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

unphased_diploid_gt_index()[[source]](../_modules/hail/genetics/call.html#Call.unphased_diploid_gt_index)

    

Return the genotype index for unphased, diploid calls.

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

---

