## Types


Fields and expressions in Hail have types. Throughout the documentation, you
will find type descriptions like `array<str>` or `tlocus`. It is generally
more important to know how to use expressions of various types than to know
how to manipulate the types themselves, but some operations like
[`missing()`](functions/core.html#hail.expr.functions.missing
"hail.expr.functions.missing") require type arguments.

In Python, `5` is of type
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") while `"hello"` is of type
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)"). Python is a dynamically-typed language, meaning that a function
like:

    
    
    >>> def add_x_and_y(x, y):
    ...     return x + y
    

…can be called on any two objects which can be added, like numbers, strings,
or [`numpy`](https://numpy.org/doc/stable/reference/index.html#module-numpy
"\(in NumPy v2.3\)") arrays.

Types are very important in Hail, because the fields of
```Table``` and
```MatrixTable```
objects have data types.

### Primitive types

Hail’s primitive data types for boolean, numeric and string objects are:

`tint` | Alias for `tint32`.  
---|---  
`tint32` | Hail type for signed 32-bit integers.  
`tint64` | Hail type for signed 64-bit integers.  
`tfloat` | Alias for `tfloat64`.  
`tfloat32` | Hail type for 32-bit floating point numbers.  
`tfloat64` | Hail type for 64-bit floating point numbers.  
`tstr` | Hail type for text strings.  
`tbool` | Hail type for Boolean (`True` or `False`) values.  
  
### Container types

Hail’s container types are:

>   * `tarray` \- Ordered collection of homogenous objects.
>
>   * `tndarray` \- Ordered n-dimensional arrays of homogenous objects.
>
>   * `tset` \- Unordered collection of distinct homogenous objects.
>
>   * `tdict` \- Key-value map. Keys and values are both homogenous.
>
>   * `ttuple` \- Tuple of heterogeneous values.
>
>   * `tstruct` \- Structure containing named fields, each with its own type.
>
>

`tarray` | Hail type for variable-length arrays of elements.  
---|---  
`tndarray` | Hail type for n-dimensional arrays.  
`tset` | Hail type for collections of distinct elements.  
`tdict` | Hail type for key-value maps.  
`ttuple` | Hail type for tuples.  
`tinterval` | Hail type for intervals of ordered values.  
`tstruct` | Hail type for structured groups of heterogeneous fields.  
  
### Genetics types

Hail has two genetics-specific types:

`tlocus` | Hail type for a genomic coordinate with a contig and a position.  
---|---  
`tcall` | Hail type for a diploid genotype.  
  
### When to work with types

In general, you won’t need to mention types explicitly.

There are a few situations where you may want to specify types explicitly:

  * To specify column types in ```import_table()``` if the impute flag does not infer the type you want.

  * When converting a Python value to a Hail expression with ```literal()```, if you don’t wish to rely on the inferred type.

  * With functions like ```missing()``` and ```empty_array()```.

### Viewing an object’s type

Hail objects have a `dtype` field that will print their type.

    
    
    >>> hl.rand_norm().dtype
    dtype('float64')
    

Printing the representation of a Hail expression will also show the type:

    
    
    >>> hl.rand_norm()
    <Float64Expression of type float64>
    

We can see that `hl.rand_norm()` is of type `tfloat64`, but what does
Expression mean? Each data type in Hail is represented by its own Expression
class. Data of type `tfloat64` is represented by an
[`Float64Expression`](hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression"). Data of type `tstruct` is represented by a
[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression").

### Collection Types

Hail’s collection types (arrays, ndarrays, sets, and dicts) have homogenous
elements, meaning that all values in the collection must be of the same type.
Python allows mixed collections: `['1', 2, 3.0]` is a valid Python list.
However, Hail arrays cannot contain both `tstr` and `tint32` values. Likewise,
the [`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in
Python v3.9\)") `{'a': 1, 2: 'b'}` is a valid Python dictionary, but a Hail
dictionary cannot contain keys of different types. An example of a valid
dictionary in Hail is `{'a': 1, 'b': 2}`, where the keys are all strings and
the values are all integers. The type of this dictionary would be `dict<str,
int32>`.

### Constructing types

Constructing types can be done either by using the type objects and classes
(prefixed by “t”) or by parsing from strings with `dtype()`. As an example, we
will construct a `tstruct` with each option:

    
    
    >>> t = hl.tstruct(a = hl.tint32, b = hl.tstr, c = hl.tarray(hl.tfloat64))
    >>> t
    dtype('struct{a: int32, b: str, c: array<float64>}')
    
    >>> t = hl.dtype('struct{a: int32, b: str, c: array<float64>}')
    >>> t
    dtype('struct{a: int32, b: str, c: array<float64>}')
    

### Reference documentation

_class
_hail.expr.types.HailType[[source]](_modules/hail/expr/types.html#HailType)

    

Hail type superclass.

hail.expr.types.dtype(_type_str_)[[source]](_modules/hail/expr/types.html#dtype)

    

Parse a type from its string representation.

Examples

    
    
    >>> hl.dtype('int')
    dtype('int32')
    
    
    
    >>> hl.dtype('float')
    dtype('float64')
    
    
    
    >>> hl.dtype('array<int32>')
    dtype('array<int32>')
    
    
    
    >>> hl.dtype('dict<str, bool>')
    dtype('dict<str, bool>')
    
    
    
    >>> hl.dtype('struct{a: int32, `field with spaces`: int64}')
    dtype('struct{a: int32, `field with spaces`: int64}')
    

Notes

This function is able to reverse `str(t)` on a `HailType`.

The grammar is defined as follows:

    
    
    type = _ ( array / bool / call / dict / interval / int64 / int32 / float32 / float64 / locus / ndarray / rng_state / set / stream / struct / str / tuple / union / void / variable ) _
    variable = "?" simple_identifier (":" simple_identifier)?
    void = "void" / "tvoid"
    int64 = "int64" / "tint64"
    int32 = "int32" / "tint32" / "int" / "tint"
    float32 = "float32" / "tfloat32"
    float64 = "float64" / "tfloat64" / "tfloat" / "float"
    bool = "tbool" / "bool"
    call = "tcall" / "call"
    str = "tstr" / "str"
    locus = ("tlocus" / "locus") _ "<" identifier ">"
    array = ("tarray" / "array") _ "<" type ">"
    ndarray = ("tndarray" / "ndarray") _ "<" type "," nat ">"
    set = ("tset" / "set") _ "<" type ">"
    stream = ("tstream" / "stream") _ "<" type ">"
    dict = ("tdict" / "dict") _ "<" type "," type ">"
    struct = ("tstruct" / "struct") _ "{" (fields / _) "}"
    union = ("tunion" / "union") _ "{" (fields / _) "}"
    tuple = ("ttuple" / "tuple") _ "(" ((type ("," type)*) / _) ")"
    fields = field ("," field)*
    field = identifier ":" type
    interval = ("tinterval" / "interval") _ "<" type ">"
    identifier = _ (simple_identifier / escaped_identifier) _
    simple_identifier = ~r"\w+"
    escaped_identifier = ~"`([^`\\\\]|\\\\.)*`"
    nat = _ (nat_literal / nat_variable) _
    nat_literal = ~"[0-9]+"
    nat_variable = "?nat"
    rng_state = "rng_state"
    _ = ~r"\s*"
    

Parameters:

    

**type_str** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")) – String representation of type.

Returns:

    

`HailType`

hail.expr.types.tint _ = dtype('int32')_

    

Alias for `tint32`.

hail.expr.types.tint32 _ = dtype('int32')_

    

Hail type for signed 32-bit integers.

Their values can range from \\(-2^{31}\\) to \\(2^{31} - 1\\) (approximately
2.15 billion).

In Python, these are represented as
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)").

See also

[`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression"),
[`int()`](functions/constructors.html#hail.expr.functions.int
"hail.expr.functions.int"),
[`int32()`](functions/constructors.html#hail.expr.functions.int32
"hail.expr.functions.int32")

hail.expr.types.tint64 _ = dtype('int64')_

    

Hail type for signed 64-bit integers.

Their values can range from \\(-2^{63}\\) to \\(2^{63} - 1\\).

In Python, these are represented as
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)").

See also

[`Int64Expression`](hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression"),
[`int64()`](functions/constructors.html#hail.expr.functions.int64
"hail.expr.functions.int64")

hail.expr.types.tfloat _ = dtype('float64')_

    

Alias for `tfloat64`.

hail.expr.types.tfloat32 _ = dtype('float32')_

    

Hail type for 32-bit floating point numbers.

In Python, these are represented as
[`float`](https://docs.python.org/3.9/library/functions.html#float "\(in
Python v3.9\)").

See also

[`Float32Expression`](hail.expr.Float32Expression.html#hail.expr.Float32Expression
"hail.expr.Float32Expression"),
[`float64()`](functions/constructors.html#hail.expr.functions.float64
"hail.expr.functions.float64")

hail.expr.types.tfloat64 _ = dtype('float64')_

    

Hail type for 64-bit floating point numbers.

In Python, these are represented as
[`float`](https://docs.python.org/3.9/library/functions.html#float "\(in
Python v3.9\)").

See also

[`Float64Expression`](hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression"),
[`float()`](functions/constructors.html#hail.expr.functions.float
"hail.expr.functions.float"),
[`float64()`](functions/constructors.html#hail.expr.functions.float64
"hail.expr.functions.float64")

hail.expr.types.tstr _ = dtype('str')_

    

Hail type for text strings.

In Python, these are represented as strings.

See also

[`StringExpression`](hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"),
[`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str")

hail.expr.types.tbool _ = dtype('bool')_

    

Hail type for Boolean (`True` or `False`) values.

In Python, these are represented as
[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)").

See also

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"),
[`bool()`](functions/constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")

_class
_hail.expr.types.tarray(_element_type_)[[source]](_modules/hail/expr/types.html#tarray)

    

Hail type for variable-length arrays of elements.

In Python, these are represented as
[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)").

Notes

Arrays contain elements of only one type, which is parameterized by
element_type.

Parameters:

    

**element_type** (`HailType`) – Element type of array.

See also

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression"),
[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"),
[`array()`](functions/constructors.html#hail.expr.functions.array
"hail.expr.functions.array"), [Collection
functions](functions/collections.html#sec-collection-functions)

_class _hail.expr.types.tndarray(_element_type_ ,
_ndim_)[[source]](_modules/hail/expr/types.html#tndarray)

    

Hail type for n-dimensional arrays.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

In Python, these are represented as NumPy
[`numpy.ndarray`](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray
"\(in NumPy v2.3\)").

Notes

NDArrays contain elements of only one type, which is parameterized by
element_type.

Parameters:

    

  * **element_type** (`HailType`) – Element type of array.

  * **ndim** (_int32_) – Number of dimensions.

See also

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression"), [`nd.array`](nd/index.html#hail.nd.array
"hail.nd.array")

_class
_hail.expr.types.tset(_element_type_)[[source]](_modules/hail/expr/types.html#tset)

    

Hail type for collections of distinct elements.

In Python, these are represented as
[`set`](https://docs.python.org/3.9/library/stdtypes.html#set "\(in Python
v3.9\)").

Notes

Sets contain elements of only one type, which is parameterized by
element_type.

Parameters:

    

**element_type** (`HailType`) – Element type of set.

See also

[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"),
[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"),
[`set()`](functions/constructors.html#hail.expr.functions.set
"hail.expr.functions.set"), [Collection
functions](functions/collections.html#sec-collection-functions)

_class _hail.expr.types.tdict(_key_type_ ,
_value_type_)[[source]](_modules/hail/expr/types.html#tdict)

    

Hail type for key-value maps.

In Python, these are represented as
[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python
v3.9\)").

Notes

Dicts parameterize the type of both their keys and values with key_type and
value_type.

Parameters:

    

  * **key_type** (`HailType`) – Key type.

  * **value_type** (`HailType`) – Value type.

See also

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"),
[`dict()`](functions/constructors.html#hail.expr.functions.dict
"hail.expr.functions.dict"), [Collection
functions](functions/collections.html#sec-collection-functions)

_class _hail.expr.types.tstruct(_**
field_types_)[[source]](_modules/hail/expr/types.html#tstruct)

    

Hail type for structured groups of heterogeneous fields.

In Python, these are represented as
```Struct```.

Hail’s `tstruct` type is commonly used to compose types together to form
nested structures. Structs can contain any combination of types, and are
ordered mappings from field name to field type. Each field name must be
unique.

Structs are very common in Hail. Each component of a
```Table``` and
```MatrixTable``` is
a struct:

  * ```Table.row()```

  * ```Table.globals()```

  * ```MatrixTable.row()```

  * ```MatrixTable.col()```

  * ```MatrixTable.entry()```

  * ```MatrixTable.globals()```

Structs appear below the top-level component types as well. Consider the
following join:

    
    
    >>> new_table = table1.annotate(table2_fields = table2.index(table1.key))
    

This snippet adds a field to `table1` called `table2_fields`. In the new
table, `table2_fields` will be a struct containing all the non-key fields from
`table2`.

Parameters:

    

**field_types** (keyword args of `HailType`) – Fields.

See also

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression"), [`Struct`](utils/index.html#hail.utils.Struct
"hail.utils.Struct")

_class _hail.expr.types.ttuple(_*
types_)[[source]](_modules/hail/expr/types.html#ttuple)

    

Hail type for tuples.

In Python, these are represented as
[`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python
v3.9\)").

Parameters:

    

**types** (varargs of `HailType`) – Element types.

See also

[`TupleExpression`](hail.expr.TupleExpression.html#hail.expr.TupleExpression
"hail.expr.TupleExpression")

hail.expr.types.tcall _ = dtype('call')_

    

Hail type for a diploid genotype.

In Python, these are represented by
[`Call`](genetics/hail.genetics.Call.html#hail.genetics.Call
"hail.genetics.Call").

See also

[`CallExpression`](hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression"),
[`Call`](genetics/hail.genetics.Call.html#hail.genetics.Call
"hail.genetics.Call"),
[`call()`](functions/genetics.html#hail.expr.functions.call
"hail.expr.functions.call"),
[`parse_call()`](functions/genetics.html#hail.expr.functions.parse_call
"hail.expr.functions.parse_call"),
[`unphased_diploid_gt_index_call()`](functions/genetics.html#hail.expr.functions.unphased_diploid_gt_index_call
"hail.expr.functions.unphased_diploid_gt_index_call")

_class _hail.expr.types.tlocus(_reference_genome
='default'_)[[source]](_modules/hail/expr/types.html#tlocus)

    

Hail type for a genomic coordinate with a contig and a position.

In Python, these are represented by
[`Locus`](genetics/hail.genetics.Locus.html#hail.genetics.Locus
"hail.genetics.Locus").

Parameters:

    

**reference_genome**
([`ReferenceGenome`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome") or
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – Reference genome to use.

See also

[`LocusExpression`](hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression"),
[`locus()`](functions/genetics.html#hail.expr.functions.locus
"hail.expr.functions.locus"),
[`parse_locus()`](functions/genetics.html#hail.expr.functions.parse_locus
"hail.expr.functions.parse_locus"),
[`Locus`](genetics/hail.genetics.Locus.html#hail.genetics.Locus
"hail.genetics.Locus")

reference_genome

    

Reference genome.

Returns:

    

[`ReferenceGenome`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome") – Reference genome.

_class
_hail.expr.types.tinterval(_point_type_)[[source]](_modules/hail/expr/types.html#tinterval)

    

Hail type for intervals of ordered values.

In Python, these are represented by
```Interval```.

Parameters:

    

**point_type** (`HailType`) – Interval point type.

See also

[`IntervalExpression`](hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression"),
```Interval```,
[`interval()`](functions/constructors.html#hail.expr.functions.interval
"hail.expr.functions.interval"),
[`parse_locus_interval()`](functions/genetics.html#hail.expr.functions.parse_locus_interval
"hail.expr.functions.parse_locus_interval")

---

