## Notes


This is a recently added, experimental module. We would love to hear what use
cases you have for this as we expand this functionality. As much as possible,
we try to mimic the numpy array interface.

`array`(input_array[, dtype]) | Construct an ```NDArrayExpression```  
---|---  
`arange`(start[, stop, step]) | Returns a 1-dimensions ndarray of integers from start to stop by step.  
`full`(shape, value[, dtype]) | Creates a hail ```NDArrayNumericExpression``` full of the specified value.  
`zeros`(shape[, dtype]) | Creates a hail ```NDArrayNumericExpression``` full of zeros.  
`ones`(shape[, dtype]) | Creates a hail ```NDArrayNumericExpression``` full of ones.  
`diagonal`(nd) | Gets the diagonal of a 2 dimensional NDArray.  
`solve`(a, b[, no_crash]) | Solve a linear system.  
`solve_triangular`(A, b[, lower, no_crash]) | Solve a triangular linear system Ax = b for x.  
`qr`(nd[, mode]) | Performs a QR decomposition.  
`svd`(nd[, full_matrices, compute_uv]) | Performs a singular value decomposition.  
`inv`(nd) | Performs a matrix inversion.  
`concatenate`(nds[, axis]) | Join a sequence of arrays along an existing axis.  
`hstack`(arrs) | Stack arrays in sequence horizontally (column wise).  
`vstack`(arrs) | Stack arrays in sequence vertically (row wise).  
`eye`(N[, M, dtype]) | Construct a 2-D ```NDArrayExpression``` with ones on the _main_ diagonal and zeros elsewhere.  
`identity`(N[, dtype]) | Constructs a 2-D ```NDArrayExpression``` representing the identity array.  
`maximum`(nd1, nd2) | Compares elements at corresponding indexes in arrays and returns an array of the maximum element found at each compared index.  
`minimum`(nd1, nd2) | Compares elements at corresponding indexes in arrays and returns an array of the minimum element found at each compared index.  
  
hail.nd.array(_input_array_ , _dtype
=None_)[[source]](../_modules/hail/nd/nd.html#array)

    

Construct an
[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression")

Examples

    
    
    >>> hl.eval(hl.nd.array([1, 2, 3, 4]))
    array([1, 2, 3, 4], dtype=int32)
    
    
    
    >>> hl.eval(hl.nd.array([[1, 2, 3], [4, 5, 6]]))
    array([[1, 2, 3],
       [4, 5, 6]], dtype=int32)
    
    
    
    >>> hl.eval(hl.nd.array(np.identity(3)))
    array([[1., 0., 0.],
       [0., 1., 0.],
       [0., 0., 1.]])
    
    
    
    >>> hl.eval(hl.nd.array(hl.range(10, 20)))
    array([10, 11, 12, 13, 14, 15, 16, 17, 18, 19], dtype=int32)
    

Parameters:

    

  * **input_array** (```ArrayExpression```, numpy ndarray, or nested python lists/tuples) – The array to convert to a Hail ndarray.

  * **dtype** (```HailType```) – Desired hail type. Default: float64.

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – An ndarray based on the input array.

hail.nd.arange(_start_ , _stop =None_, _step
=1_)[[source]](../_modules/hail/nd/nd.html#arange)

    

Returns a 1-dimensions ndarray of integers from start to stop by step.

Examples

    
    
    >>> hl.eval(hl.nd.arange(10))
    array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9], dtype=int32)
    
    
    
    >>> hl.eval(hl.nd.arange(3, 10))
    array([3, 4, 5, 6, 7, 8, 9], dtype=int32)
    
    
    
    >>> hl.eval(hl.nd.arange(0, 10, step=3))
    array([0, 3, 6, 9], dtype=int32)
    

Notes

The range includes start, but excludes stop.

If provided exactly one argument, the argument is interpreted as stop and
start is set to zero. This matches the behavior of Python’s `range`.

Parameters:

    

  * **start** (int or ```Expression``` of type ```tint32```) – Start of range.

  * **stop** (int or ```Expression``` of type ```tint32```) – End of range.

  * **step** (int or ```Expression``` of type ```tint32```) – Step of range.

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") – A 1-dimensional ndarray from start to
stop by step.

hail.nd.full(_shape_ , _value_ , _dtype
=None_)[[source]](../_modules/hail/nd/nd.html#full)

    

Creates a hail
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") full of the specified value.

Examples

Create a 5 by 7 NDArray of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") 9s.

    
    
    >>> hl.nd.full((5, 7), 9)
    

It is possible to specify a type other than
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") with the dtype argument.

    
    
    >>> hl.nd.full((5, 7), 9, dtype=hl.tint32)
    

Parameters:

    

  * **shape** (tuple or ```TupleExpression```) – Desired shape.

  * **value** (```Expression``` or python value) – Value to fill ndarray with.

  * **dtype** (```HailType```) – Desired hail type.

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") – An ndarray of the specified shape
filled with the specified value.

hail.nd.zeros(_shape_ , _dtype
=dtype('float64')_)[[source]](../_modules/hail/nd/nd.html#zeros)

    

Creates a hail
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") full of zeros.

Examples

Create a 5 by 7 NDArray of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") zeros.

    
    
    >>> hl.nd.zeros((5, 7))
    

It is possible to specify a type other than
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") with the dtype argument.

    
    
    >>> hl.nd.zeros((5, 7), dtype=hl.tfloat32)
    

Parameters:

    

  * **shape** (tuple or ```TupleExpression```) – Desired shape.

  * **dtype** (```HailType```) – Desired hail type. Default: float64.

See also

`full()`

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") – ndarray of the specified size full of
zeros.

hail.nd.ones(_shape_ , _dtype
=dtype('float64')_)[[source]](../_modules/hail/nd/nd.html#ones)

    

Creates a hail
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") full of ones.

Examples

Create a 5 by 7 NDArray of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") ones.

    
    
    >>> hl.nd.ones((5, 7))
    

It is possible to specify a type other than
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") with the dtype argument.

    
    
    >>> hl.nd.ones((5, 7), dtype=hl.tfloat32)
    

Parameters:

    

  * **shape** (tuple or ```TupleExpression```) – Desired shape.

  * **dtype** (```HailType```) – Desired hail type. Default: float64.

See also

`full()`

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") – ndarray of the specified size full of
ones.

hail.nd.diagonal(_nd_)[[source]](../_modules/hail/nd/nd.html#diagonal)

    

Gets the diagonal of a 2 dimensional NDArray.

Examples

    
    
    >>> hl.eval(hl.nd.diagonal(hl.nd.array([[1, 2], [3, 4]])))
    array([1, 4], dtype=int32)
    

Parameters:

    

**nd**
([`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression")) – A 2 dimensional NDArray, shape(M, N).

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – A 1 dimension NDArray of length min(M, N),
containing the diagonal of nd.

hail.nd.solve(_a_ , _b_ , _no_crash
=False_)[[source]](../_modules/hail/nd/nd.html#solve)

    

Solve a linear system.

Parameters:

    

  * **a** (```NDArrayNumericExpression```, (N, N)) – Coefficient matrix.

  * **b** (```NDArrayNumericExpression```, (N,) or (N, K)) – Dependent variables.

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression"), (N,) or (N, K) – Solution to the system
Ax = B. Shape is same as shape of B.

hail.nd.solve_triangular(_A_ , _b_ , _lower =False_, _no_crash
=False_)[[source]](../_modules/hail/nd/nd.html#solve_triangular)

    

Solve a triangular linear system Ax = b for x.

Parameters:

    

  * **A** (```NDArrayNumericExpression```, (N, N)) – Triangular coefficient matrix.

  * **b** (```NDArrayNumericExpression```, (N,) or (N, K)) – Dependent variables.

  * **lower** (bool:) – If true, A is interpreted as a lower triangular matrix If false, A is interpreted as a upper triangular matrix

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression"), (N,) or (N, K) – Solution to the
triangular system Ax = B. Shape is same as shape of B.

hail.nd.qr(_nd_ , _mode
='reduced'_)[[source]](../_modules/hail/nd/nd.html#qr)

    

Performs a QR decomposition.

If K = min(M, N), then:

  * reduced: returns q and r with dimensions (M, K), (K, N)

  * complete: returns q and r with dimensions (M, M), (M, N)

  * r: returns only r with dimensions (K, N)

  * raw: returns h, tau with dimensions (N, M), (K,)

Notes

The reduced QR, the default output of this function, has the following
properties:

\\[m \ge n \\\ nd : \mathbb{R}^{m \times n} \\\ Q : \mathbb{R}^{m \times n}
\\\ R : \mathbb{R}^{n \times n} \\\ \\\ Q^T Q = \mathbb{1}\\]

The complete QR, has the following properties:

\\[m \ge n \\\ nd : \mathbb{R}^{m \times n} \\\ Q : \mathbb{R}^{m \times m}
\\\ R : \mathbb{R}^{m \times n} \\\ \\\ Q^T Q = \mathbb{1} Q Q^T =
\mathbb{1}\\]

Parameters:

    

  * **nd** (```NDArrayExpression```) – A 2 dimensional ndarray, shape(M, N)

  * **mode** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – One of “reduced”, “complete”, “r”, or “raw”. Defaults to “reduced”.

Returns:

    

  * **\- q** (_ndarray of float64_) – A matrix with orthonormal columns.

  * **\- r** (_ndarray of float64_) – The upper-triangular matrix R.

  * **\- (h, tau)** (_ndarrays of float64_) – The array h contains the Householder reflectors that generate q along with r. The tau array contains scaling factors for the reflectors

hail.nd.svd(_nd_ , _full_matrices =True_, _compute_uv
=True_)[[source]](../_modules/hail/nd/nd.html#svd)

    

Performs a singular value decomposition.

Parameters:

    

  * **nd** (```NDArrayNumericExpression```) – A 2 dimensional ndarray, shape(M, N).

  * **full_matrices** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – If True (default), u and vt have dimensions (M, M) and (N, N) respectively. Otherwise, they have dimensions (M, K) and (K, N), where K = min(M, N)

  * **compute_uv** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – If True (default), compute the singular vectors u and v. Otherwise, only return a single ndarray, s.

Returns:

    

  * **\- u** (```NDArrayNumericExpression```) – The left singular vectors.

  * **\- s** (```NDArrayNumericExpression```) – The singular values.

  * **\- vt** (```NDArrayNumericExpression```) – The right singular vectors.

hail.nd.inv(_nd_)[[source]](../_modules/hail/nd/nd.html#inv)

    

Performs a matrix inversion.

Parameters:

    

**nd**
([`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression")) – A 2 dimensional ndarray.

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") – The inverted matrix.

hail.nd.concatenate(_nds_ , _axis
=0_)[[source]](../_modules/hail/nd/nd.html#concatenate)

    

Join a sequence of arrays along an existing axis.

Examples

    
    
    >>> x = hl.nd.array([[1., 2.], [3., 4.]])
    >>> y = hl.nd.array([[5.], [6.]])
    >>> hl.eval(hl.nd.concatenate([x, y], axis=1))
    array([[1., 2., 5.],
           [3., 4., 6.]])
    >>> x = hl.nd.array([1., 2.])
    >>> y = hl.nd.array([3., 4.])
    >>> hl.eval(hl.nd.concatenate((x, y), axis=0))
    array([1., 2., 3., 4.])
    

Parameters:

    

  * **nds** (a sequence of ```NDArrayNumericExpression```) – The arrays must have the same shape, except in the dimension corresponding to axis (the first, by default). Note: unlike Numpy, the numerical element type of each array_like must match.

  * **axis** (_int, optional_) – The axis along which the arrays will be joined. Default is 0. Note: unlike Numpy, if provided, axis cannot be None.

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – The concatenated array

hail.nd.hstack(_arrs_)[[source]](../_modules/hail/nd/nd.html#hstack)

    

Stack arrays in sequence horizontally (column wise). Equivalent to
concatenation along the second axis, except for 1-D arrays where it
concatenates along the first axis.

This function makes most sense for arrays with up to 3 dimensions.
`concatenate()` provides more general stacking and concatenation operations.

Parameters:

    

**tup** (sequence of
[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression")) – The arrays must have the same shape along
all but the second axis, except 1-D arrays which can be any length.

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – The array formed by stacking the given
arrays.

See also

`concatenate()`, `vstack()`

Examples

    
    
    >>> a = hl.nd.array([1,2,3])
    >>> b = hl.nd.array([2, 3, 4])
    >>> hl.eval(hl.nd.hstack((a,b)))
    array([1, 2, 3, 2, 3, 4], dtype=int32)
    >>> a = hl.nd.array([[1],[2],[3]])
    >>> b = hl.nd.array([[2],[3],[4]])
    >>> hl.eval(hl.nd.hstack((a,b)))
    array([[1, 2],
           [2, 3],
           [3, 4]], dtype=int32)
    

hail.nd.vstack(_arrs_)[[source]](../_modules/hail/nd/nd.html#vstack)

    

Stack arrays in sequence vertically (row wise). 1-D arrays of shape (N,), will
reshaped to (1,N) before concatenation. For all other arrays, equivalent to
`concatenate()` with axis=0.

Examples

    
    
    >>> a = hl.nd.array([1, 2, 3])
    >>> b = hl.nd.array([2, 3, 4])
    >>> hl.eval(hl.nd.vstack((a,b)))
    array([[1, 2, 3],
           [2, 3, 4]], dtype=int32)
    >>> a = hl.nd.array([[1], [2], [3]])
    >>> b = hl.nd.array([[2], [3], [4]])
    >>> hl.eval(hl.nd.vstack((a,b)))
    array([[1],
           [2],
           [3],
           [2],
           [3],
           [4]], dtype=int32)
    

Parameters:

    

**arrs** (sequence of
[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression")) – The arrays must have the same shape along
all but the first axis. 1-D arrays must have the same length.

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – The array formed by stacking the given
arrays, will be at least 2-D.

See also

`concatenate()`

    

Join a sequence of arrays along an existing axis.

hail.nd.eye(_N_ , _M =None_, _dtype
=dtype('float64')_)[[source]](../_modules/hail/nd/nd.html#eye)

    

Construct a 2-D
[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") with ones on the _main_ diagonal and zeros
elsewhere.

Examples

    
    
    >>> hl.eval(hl.nd.eye(3))
    array([[1., 0., 0.],
           [0., 1., 0.],
           [0., 0., 1.]])
    >>> hl.eval(hl.nd.eye(2, 5, dtype=hl.tint32))
    array([[1, 0, 0, 0, 0],
           [0, 1, 0, 0, 0]], dtype=int32)
    

Parameters:

    

  * **N** (```NumericExpression``` or Python number) – Number of rows in the output.

  * **M** (```NumericExpression``` or Python number, optional) – Number of columns in the output. If None, defaults to N.

  * **dtype** (numeric ```HailType```, optional) – Element type of the returned array. Defaults to ```tfloat64```

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – An (N, M) matrix with ones on the main
diagonal, zeros elsewhere.

See also

`identity()`, `diagonal()`

hail.nd.identity(_N_ , _dtype
=dtype('float64')_)[[source]](../_modules/hail/nd/nd.html#identity)

    

Constructs a 2-D
[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") representing the identity array. The identity
array is a square array with ones on the main diagonal.

Examples

    
    
    >>> hl.eval(hl.nd.identity(3))
    array([[1., 0., 0.],
           [0., 1., 0.],
           [0., 0., 1.]])
    

Parameters:

    

  * **n** (```NumericExpression``` or Python number) – Number of rows and columns in the output.

  * **dtype** (numeric ```HailType```, optional) – Element type of the returned array. Defaults to ```tfloat64```

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – An (N, N) matrix with its main diagonal set
to one, and all other elements 0.

See also

`eye()`

hail.nd.maximum(_nd1_ , _nd2_)[[source]](../_modules/hail/nd/nd.html#maximum)

    

Compares elements at corresponding indexes in arrays and returns an array of
the maximum element found at each compared index.

If an array element being compared has the value NaN, the maximum for that
index will be NaN.

Examples

    
    
    >>> a = hl.nd.array([1, 5, 3])
    >>> b = hl.nd.array([2, 3, 4])
    >>> hl.eval(hl.nd.maximum(a, b))
    array([2, 5, 4], dtype=int32)
    >>> a = hl.nd.array([hl.float64(float("NaN")), 5.0, 3.0])
    >>> b = hl.nd.array([2.0, 3.0, hl.float64(float("NaN"))])
    >>> hl.eval(hl.nd.maximum(a, b))
    array([nan, 5., nan])
    

Parameters:

    

  * **nd1** (```NDArrayExpression```)

  * **nd2** (class:.NDArrayExpression, .ArrayExpression, numpy ndarray, or nested python lists/tuples.) – Nd1 and nd2 must be the same shape or broadcastable into common shape. Nd1 and nd2 must have elements of comparable types

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – Element-wise maximums of nd1 and nd2. If nd1
has the same shape as nd2, the resulting array will be of that shape. If nd1
and nd2 were broadcasted into a common shape, the resulting array will be of
that shape

hail.nd.minimum(_nd1_ , _nd2_)[[source]](../_modules/hail/nd/nd.html#minimum)

    

Compares elements at corresponding indexes in arrays and returns an array of
the minimum element found at each compared index.

If an array element being compared has the value NaN, the minimum for that
index will be NaN.

Examples

    
    
    >>> a = hl.nd.array([1, 5, 3])
    >>> b = hl.nd.array([2, 3, 4])
    >>> hl.eval(hl.nd.minimum(a, b))
    array([1, 3, 3], dtype=int32)
    >>> a = hl.nd.array([hl.float64(float("NaN")), 5.0, 3.0])
    >>> b = hl.nd.array([2.0, 3.0, hl.float64(float("NaN"))])
    >>> hl.eval(hl.nd.minimum(a, b))
    array([nan, 3., nan])
    

Parameters:

    

  * **nd1** (```NDArrayExpression```)

  * **nd2** (class:.NDArrayExpression, .ArrayExpression, numpy ndarray, or nested python lists/tuples.) – nd1 and nd2 must be the same shape or broadcastable into common shape. Nd1 and nd2 must have elements of comparable types

Returns:

    

**min_array**
([`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression")) – Element-wise minimums of nd1 and nd2. If nd1
has the same shape as nd2, the resulting array will be of that shape. If nd1
and nd2 were broadcasted into a common shape, resulting array will be of that
shape

---

