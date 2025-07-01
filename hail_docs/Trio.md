# Trio


_class
_hail.genetics.Trio[[source]](../_modules/hail/genetics/pedigree.html#Trio)

    

Bases: [`object`](https://docs.python.org/3.9/library/functions.html#object
"\(in Python v3.9\)")

Class containing information about nuclear family relatedness and sex.

Parameters:

    

  * **s** ([_str_](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Sample ID of proband.

  * **fam_id** ([_str_](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") _or_ _None_) – Family ID.

  * **pat_id** ([_str_](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") _or_ _None_) – Sample ID of father.

  * **mat_id** ([_str_](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") _or_ _None_) – Sample ID of mother.

  * **is_female** ([_bool_](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)") _or_ _None_) – Sex of proband.

Attributes

`fam_id` | Family ID.  
---|---  
`is_female` | Returns `True` if the proband is a reported female, `False` if reported male, and `None` if no sex is defined.  
`is_male` | Returns `True` if the proband is a reported male, `False` if reported female, and `None` if no sex is defined.  
`mat_id` | ID of mother in trio, may be missing.  
`pat_id` | ID of father in trio, may be missing.  
`s` | ID of proband in trio, never missing.  
  
Methods

`is_complete` | Returns True if the trio has a defined mother and father.  
---|---  
  
_property _fam_id

    

Family ID.

Return type:

    

[str](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or None

is_complete()[[source]](../_modules/hail/genetics/pedigree.html#Trio.is_complete)

    

Returns True if the trio has a defined mother and father.

The considered fields are `mat_id()` and `pat_id()`. Recall that `s` may never
be missing. The `fam_id()` and `is_female()` fields may be missing in a
complete trio.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

_property _is_female

    

Returns `True` if the proband is a reported female, `False` if reported male,
and `None` if no sex is defined.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)") or None

_property _is_male

    

Returns `True` if the proband is a reported male, `False` if reported female,
and `None` if no sex is defined.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)") or None

_property _mat_id

    

ID of mother in trio, may be missing.

Return type:

    

[str](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or None

_property _pat_id

    

ID of father in trio, may be missing.

Return type:

    

[str](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or None

_property _s

    

ID of proband in trio, never missing.

Return type:

    

[str](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")