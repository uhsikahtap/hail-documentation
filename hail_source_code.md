# Table of Contents

- [Source code for hail.table](#source-code-for-hailtable)
- [Source code for hail.matrixtable](#source-code-for-hailmatrixtable)
- [Source code for hail.expr.expressions.expression_utils](#source-code-for-hailexprexpresionsexpression_utils)
- [Source code for hail.expr.types](#source-code-for-hailexprtypes)
- [Source code for hail.plot.plots](#source-code-for-hailplotplots)
- [Source code for hail.expr.aggregators.aggregators](#source-code-for-hailexpraggregatorsaggregators) 




# Source code for hail.table

    
    
    import collections
    import itertools
    import pprint
    import shutil
    from typing import Callable, ClassVar, Dict, List, Optional, Sequence, Union, overload
    
    import numpy as np
    import pandas
    import pyspark
    
    import hail as hl
    from hail import ir
    from hail.expr.expressions import (
        ArrayExpression,
        BooleanExpression,
        CallExpression,
        CollectionExpression,
        DictExpression,
        Expression,
        ExpressionException,
        Indices,
        IntervalExpression,
        LocusExpression,
        NDArrayExpression,
        NumericExpression,
        StringExpression,
        StructExpression,
        TupleExpression,
        analyze,
        construct_expr,
        construct_reference,
        expr_any,
        expr_array,
        expr_bool,
        expr_stream,
        expr_struct,
        extract_refs_by_indices,
        to_expr,
        unify_all,
    )
    from hail.expr.table_type import ttable
    from hail.expr.types import dtypes_from_pandas, hail_type, tarray, tset, tstruct, types_match
    from hail.typecheck import (
        anyfunc,
        anytype,
        dictof,
        enumeration,
        func_spec,
        lazy,
        nullable,
        numeric,
        oneof,
        sequenceof,
        table_key_type,
        typecheck,
        typecheck_method,
    )
    from hail.utils import deduplicate
    from hail.utils.interval import Interval
    from hail.utils.java import Env, info, warning
    from hail.utils.misc import (
        check_annotate_exprs,
        check_collisions,
        check_keys,
        get_key_by_exprs,
        get_nice_attr_error,
        get_nice_field_error,
        get_select_exprs,
        plural,
        process_joins,
        storage_level,
        wrap_to_tuple,
    )
    from hail.utils.placement_tree import PlacementTree
    
    table_type = lazy()
    
    
    class TableIndexKeyError(Exception):
        def __init__(self, key_type, index_expressions):
            super().__init__()
            self.key_type = key_type
            self.index_expressions = index_expressions
    
    
    class Ascending:
        def __init__(self, col):
            self.col = col
    
        def __eq__(self, other):
            return isinstance(other, Ascending) and self.col == other.col
    
        def __ne__(self, other):
            return not self == other
    
    
    class Descending:
        def __init__(self, col):
            self.col = col
    
        def __eq__(self, other):
            return isinstance(other, Descending) and self.col == other.col
    
        def __ne__(self, other):
            return not self == other
    
    
    
    
    [[docs]](../../api.html#hail.asc)@typecheck(col=oneof(Expression, str))
    def asc(col):
        """Sort by `col` ascending."""
    
        return Ascending(col)
    
    
    
    
    
    
    [[docs]](../../api.html#hail.desc)@typecheck(col=oneof(Expression, str))
    def desc(col):
        """Sort by `col` descending."""
    
        return Descending(col)
    
    
    
    
    
    
    [[docs]](../../hail.table.BaseTable.html#hail.BaseTable)class BaseTable:
        """Base class for ``.Table``, ``.GroupedTable``, ``hail.matrixtable.MatrixTable``,
        and ``hail.matrixtable.GroupedMatrixTable``"""
    
        # this can only grow as big as the object dir, so no need to worry about memory leak
        _warned_about: ClassVar = set()
    
        def __init__(self):
            self._fields: Dict[str, Expression] = {}
            self._fields_inverse: Dict[Expression, str] = {}
            self._dir = set(dir(self))
            super(BaseTable, self).__init__()
    
        def _set_field(self, key, value):
            assert key not in self._fields_inverse, key
            self._fields[key] = value
            self._fields_inverse[value] = key
    
            # key is in __dir for methods
            # key is in __dict__ for private class fields
            if key in self._dir or key in self.__dict__:
                if key not in BaseTable._warned_about:
                    BaseTable._warned_about.add(key)
                    warning(
                        f"Name collision: field {key!r} already in object dict. "
                        f"\n  This field must be referenced with __getitem__ syntax: obj[{key!r}]"
                    )
            else:
                self.__dict__[key] = value
    
        def _get_field(self, item) -> Expression:
            if item in self._fields:
                return self._fields[item]
    
            raise LookupError(get_nice_field_error(self, item))
    
        def __iter__(self):
            raise TypeError(f"'{self.__class__.__name__}' object is not iterable")
    
        def __delattr__(self, item):
            if not item[0] == '_':
                raise NotImplementedError(f"'{self.__class__.__name__}' object is not mutable")
    
        def __setattr__(self, key, value):
            if not key[0] == '_':
                raise NotImplementedError(f"'{self.__class__.__name__}' object is not mutable")
            self.__dict__[key] = value
    
        def __getattr__(self, item):
            if item in self.__dict__:
                return self.__dict__[item]
    
            raise AttributeError(get_nice_attr_error(self, item))
    
        def _copy_fields_from(self, other: 'BaseTable'):
            self._fields = other._fields
            self._fields_inverse = other._fields_inverse
    
    
    
    
    
    
    [[docs]](../../hail.GroupedTable.html#hail.GroupedTable)class GroupedTable(BaseTable):
        """Table grouped by row that can be aggregated into a new table.
    
        There are only two operations on a grouped table, ``.GroupedTable.partition_hint``
        and ``.GroupedTable.aggregate``.
        """
    
        def __init__(self, parent: 'Table', key_expr):
            super(GroupedTable, self).__init__()
            self._key_expr = key_expr
            self._parent = parent
            self._npartitions = None
            self._buffer_size = 50
    
            self._copy_fields_from(parent)
    
    
    
    [[docs]](../../hail.GroupedTable.html#hail.GroupedTable.partition_hint)    def partition_hint(self, n: int) -> 'GroupedTable':
            """Set the target number of partitions for aggregation.
    
            Examples
            --------
    
            Use `partition_hint` in a ``.Table.group_by`` / ``.GroupedTable.aggregate``
            pipeline:
    
            >>> table_result = (table1.group_by(table1.ID)
            ...                       .partition_hint(5)
            ...                       .aggregate(meanX = hl.agg.mean(table1.X), sumZ = hl.agg.sum(table1.Z)))
    
            Notes
            -----
            Until Hail's query optimizer is intelligent enough to sample records at all
            stages of a pipeline, it can be necessary in some places to provide some
            explicit hints.
    
            The default number of partitions for ``.GroupedTable.aggregate`` is the
            number of partitions in the upstream table. If the aggregation greatly
            reduces the size of the table, providing a hint for the target number of
            partitions can accelerate downstream operations.
    
            Parameters
            ----------
            n : int
                Number of partitions.
    
            Returns
            -------
            ``.GroupedTable``
                Same grouped table with a partition hint.
            """
            self._npartitions = n
            return self
    
    
    
        def _set_buffer_size(self, n: int) -> 'GroupedTable':
            """Set the map-side combiner buffer size (in rows).
    
            Parameters
            ----------
            n : int
                Buffer size.
    
            Returns
            -------
            ``.GroupedTable``
                Same grouped table with a buffer size.
            """
            if n <= 0:
                raise ValueError(n)
            self._buffer_size = n
            return self
    
    
    
    [[docs]](../../hail.GroupedTable.html#hail.GroupedTable.aggregate)    @typecheck_method(named_exprs=expr_any)
        def aggregate(self, **named_exprs) -> 'Table':
            """Aggregate by group, used after ``.Table.group_by``.
    
            Examples
            --------
            Compute the mean value of `X` and the sum of `Z` per unique `ID`:
    
            >>> table_result = (table1.group_by(table1.ID)
            ...                       .aggregate(meanX = hl.agg.mean(table1.X), sumZ = hl.agg.sum(table1.Z)))
    
            Group by a height bin and compute sex ratio per bin:
    
            >>> table_result = (table1.group_by(height_bin = table1.HT // 20)
            ...                       .aggregate(fraction_female = hl.agg.fraction(table1.SEX == 'F')))
    
            Notes
            -----
            The resulting table has a key field for each group and a value field for
            each aggregation. The names of the aggregation expressions must be
            distinct from the names of the groups.
    
            Parameters
            ----------
            named_exprs : varargs of ``.Expression``
                Aggregation expressions.
    
            Returns
            -------
            ``.Table``
                Aggregated table.
            """
            caller = 'GroupedTable.aggregate'
            for name, expr in named_exprs.items():
                analyze(f'{caller}: ({name!r})', expr, self._parent._global_indices, {self._parent._row_axis})
            check_collisions(caller, list(named_exprs), self._parent._row_indices)
            if not named_exprs.keys().isdisjoint(set(self._key_expr)):
                intersection = set(named_exprs.keys()) & set(self._key_expr)
                raise ValueError(
                    f'GroupedTable.aggregate: Group names and aggregration expression names overlap: {intersection}'
                )
    
            base, _ = self._parent._process_joins(self._key_expr, *named_exprs.values())
    
            key_struct = self._key_expr
            return Table(
                ir.TableKeyByAndAggregate(
                    base._tir, hl.struct(**named_exprs)._ir, key_struct._ir, self._npartitions, self._buffer_size
                )
            )
    
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table)class Table(BaseTable):
        """Hail's distributed implementation of a dataframe or SQL table.
    
        Use ``.read_table`` to read a table that was written with
        ``.Table.write``. Use ``.to_spark`` and ``.Table.from_spark``
        to inter-operate with PySpark's
        `SQL <https://spark.apache.org/docs/latest/sql-programming-guide.html>`__ and
        `machine learning <https://spark.apache.org/docs/latest/ml-guide.html>`__
        functionality.
    
        Examples
        --------
    
        The examples below use ``table1`` and ``table2``, which are imported
        from text files using ``.import_table``.
    
        >>> table1 = hl.import_table('data/kt_example1.tsv', impute=True, key='ID')
        >>> table1.show()
    
        .. code-block:: text
    
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | M   |     5 |     4 |     2 |    50 |     5 |
            |     2 |    72 | M   |     6 |     3 |     2 |    61 |     1 |
            |     3 |    70 | F   |     7 |     3 |    10 |    81 |    -5 |
            |     4 |    60 | F   |     8 |     2 |    11 |    90 |   -10 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
        >>> table2 = hl.import_table('data/kt_example2.tsv', impute=True, key='ID')
        >>> table2.show()
    
        .. code-block:: text
    
            +-------+-------+--------+
            |    ID |     A | B      |
            +-------+-------+--------+
            | int32 | int32 | str    |
            +-------+-------+--------+
            |     1 |    65 | cat    |
            |     2 |    72 | dog    |
            |     3 |    70 | mouse  |
            |     4 |    60 | rabbit |
            +-------+-------+--------+
    
        Define new annotations:
    
        >>> height_mean_m = 68
        >>> height_sd_m = 3
        >>> height_mean_f = 65
        >>> height_sd_f = 2.5
        >>>
        >>> def get_z(height, sex):
        ...    return hl.if_else(sex == 'M',
        ...                     (height - height_mean_m) / height_sd_m,
        ...                     (height - height_mean_f) / height_sd_f)
        >>>
        >>> table1 = table1.annotate(height_z = get_z(table1.HT, table1.SEX))
        >>> table1 = table1.annotate_globals(global_field_1 = [1, 2, 3])
    
        Filter rows of the table:
    
        >>> table2 = table2.filter(table2.B != 'rabbit')
    
        Compute global aggregation statistics:
    
        >>> t1_stats = table1.aggregate(hl.struct(mean_c1 = hl.agg.mean(table1.C1),
        ...                                       mean_c2 = hl.agg.mean(table1.C2),
        ...                                       stats_c3 = hl.agg.stats(table1.C3)))
        >>> print(t1_stats)
    
        Group by a field and aggregate to produce a new table:
    
        >>> table3 = (table1.group_by(table1.SEX)
        ...                 .aggregate(mean_height_data = hl.agg.mean(table1.HT)))
        >>> table3.show()
    
        Join tables together inside an annotation expression:
    
        >>> table2 = table2.key_by('ID')
        >>> table1 = table1.annotate(B = table2[table1.ID].B)
        >>> table1.show()
        """
    
        @staticmethod
        def _from_java(table_type, ir_id):
            return Table(ir.JavaTable(table_type, ir_id))
    
        def __init__(self, tir):
            super(Table, self).__init__()
    
            self._tir = tir
            self._type = self._tir.typ
    
            self._row_axis = 'row'
    
            self._global_indices = Indices(axes=set(), source=self)
            self._row_indices = Indices(axes={self._row_axis}, source=self)
    
            self._global_type = self._type.global_type
            self._row_type = self._type.row_type
    
            self._globals = construct_reference('global', self._global_type, indices=self._global_indices)
            self._row = construct_reference('row', self._row_type, indices=self._row_indices)
    
            self._indices_from_ref = {'global': self._global_indices, 'row': self._row_indices}
    
            self._key = hl.struct(**{k: self._row[k] for k in self._type.row_key})
    
            for k, v in itertools.chain(self._globals.items(), self._row.items()):
                self._set_field(k, v)
    
        @property
        def _schema(self) -> ttable:
            return ttable(self._global_type, self._row_type, list(self._key))
    
        def __getitem__(self, item):
            if isinstance(item, str):
                return self._get_field(item)
    
            try:
                return self.index(*wrap_to_tuple(item))
            except TypeError as e:
                raise TypeError(
                    "Table.__getitem__: invalid index argument(s)\n"
                    "  Usage 1: field selection: ht['field']\n"
                    "  Usage 2: Left distinct join: ht[ht2.key] or ht[ht2.field1, ht2.field2]"
                ) from e
    
        @property
        def key(self) -> StructExpression:
            """Row key struct.
    
            Examples
            --------
    
            List of key field names:
    
            >>> list(table1.key)
            ['ID']
    
            Number of key fields:
    
            >>> len(table1.key)
            1
    
    
            Returns
            -------
            ``.StructExpression``
            """
            return self._key
    
        @property
        def _value(self) -> 'StructExpression':
            return self.row.drop(*self.key)
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.n_partitions)    def n_partitions(self):
            """Returns the number of partitions in the table.
    
            Examples
            --------
    
            Range tables can be constructed with an explicit number of partitions:
    
            >>> ht = hl.utils.range_table(100, n_partitions=10)
            >>> ht.n_partitions()
            10
    
            Small files are often imported with one partition:
    
            >>> ht2 = hl.import_table('data/coordinate_matrix.tsv', impute=True)
            >>> ht2.n_partitions()
            1
    
            The `min_partitions` argument to ``.import_table`` forces more partitions, but it can
            produce empty partitions. Empty partitions do not affect correctness but introduce
            unnecessary extra bookkeeping that slows down the pipeline.
    
            >>> ht2 = hl.import_table('data/coordinate_matrix.tsv', impute=True, min_partitions=10)
            >>> ht2.n_partitions()
            10
    
            Returns
            -------
            ``int``
                Number of partitions.
    
            """
            return Env.backend().execute(ir.TableToValueApply(self._tir, {'name': 'NPartitionsTable'}))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.count)    def count(self):
            """Count the number of rows in the table.
    
            Examples
            --------
    
            Count the number of rows in a table loaded from 'data/kt_example1.tsv'. Each line of the TSV
            becomes one row in the Hail Table.
    
            >>> ht = hl.import_table('data/kt_example1.tsv', impute=True)
            >>> ht.count()
            4
    
            Returns
            -------
            ``int``
                The number of rows in the table.
    
            """
            return Env.backend().execute(ir.TableCount(self._tir))
    
    
    
        async def _async_count(self):
            return await Env.backend()._async_execute(ir.TableCount(self._tir))
    
        def _force_count(self):
            return Env.backend().execute(ir.TableToValueApply(self._tir, {'name': 'ForceCountTable'}))
    
        async def _async_force_count(self):
            return await Env.backend()._async_execute(ir.TableToValueApply(self._tir, {'name': 'ForceCountTable'}))
    
        @typecheck_method(caller=str, row=expr_struct())
        def _select(self, caller, row) -> 'Table':
            analyze(caller, row, self._row_indices)
            base, cleanup = self._process_joins(row)
            return cleanup(Table(ir.TableMapRows(base._tir, row._ir)))
    
        @typecheck_method(caller=str, s=expr_struct())
        def _select_globals(self, caller, s) -> 'Table':
            base, cleanup = self._process_joins(s)
            analyze(caller, s, self._global_indices)
            return cleanup(Table(ir.TableMapGlobals(base._tir, s._ir)))
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.parallelize)    @classmethod
        @typecheck_method(
            rows=anytype,
            schema=nullable(hail_type),
            key=table_key_type,
            n_partitions=nullable(int),
            partial_type=nullable(dict),
            globals=nullable(expr_struct()),
        )
        def parallelize(cls, rows, schema=None, key=None, n_partitions=None, *, partial_type=None, globals=None) -> 'Table':
            """Parallelize a local array of structs into a distributed table.
    
            Examples
            --------
    
            Parallelize a list of dictionaries into a Hail Table. The fields of the dictionary become
            the fields of the Table. The schema should always be a ``.tstruct`` whose fields
            correspond to the dictionaries' fields.
    
            >>> t = hl.Table.parallelize(
            ...     [{'a': 5, 'b': 10}, {'a': 0, 'b': 200}],
            ...     schema=hl.tstruct(a=hl.tint, b=hl.tint)
            ... )
            >>> t.show()
            +-------+-------+
            |     a |     b |
            +-------+-------+
            | int32 | int32 |
            +-------+-------+
            |     5 |    10 |
            |     0 |   200 |
            +-------+-------+
    
            The `key` parameter sets the key of the Table. Notice that the order of the rows changes,
            because the rows of a Table are always appear in ascending order of the key.
    
            >>> t = hl.Table.parallelize(
            ...     [{'a': 5, 'b': 10}, {'a': 0, 'b': 200}],
            ...     schema=hl.tstruct(a=hl.tint, b=hl.tint),
            ...     key='a'
            ... )
            >>> t.show()
            +-------+-------+
            |     a |     b |
            +-------+-------+
            | int32 | int32 |
            +-------+-------+
            |     0 |   200 |
            |     5 |    10 |
            +-------+-------+
    
            You may also elide schema entirely and let Hail guess the type. The list elements must
            either be Hail ``.Struct`` or ``.dict`` s.
    
            >>> t = hl.Table.parallelize(
            ...     [{'a': 5, 'b': 10}, {'a': 0, 'b': 200}],
            ...     key='a'
            ... )
            >>> t.show()
            +-------+-------+
            |     a |     b |
            +-------+-------+
            | int32 | int32 |
            +-------+-------+
            |     0 |   200 |
            |     5 |    10 |
            +-------+-------+
    
            You may also specify only a handful of types in `partial_type`. Hail will automatically
            deduce the types of the other fields. Hail _cannot_ deduce the type of a field which only
            contains empty arrays (the element type is unspecified), so we specify the type of labels
            explicitly.
    
            >>> dictionaries = [
            ...     {"number":10038,"state":"open","user":{"login":"tpoterba","site_admin":False,"id":10562794}, "milestone":None,"labels":[]},
            ...     {"number":10037,"state":"open","user":{"login":"daniel-goldstein","site_admin":False,"id":24440116},"milestone":None,"labels":[]},
            ...     {"number":10036,"state":"open","user":{"login":"jigold","site_admin":False,"id":1693348},"milestone":None,"labels":[]},
            ...     {"number":10035,"state":"open","user":{"login":"tpoterba","site_admin":False,"id":10562794},"milestone":None,"labels":[]},
            ...     {"number":10033,"state":"open","user":{"login":"tpoterba","site_admin":False,"id":10562794},"milestone":None,"labels":[]},
            ... ]
            >>> t = hl.Table.parallelize(
            ...     dictionaries,
            ...     partial_type={"milestone": hl.tstr, "labels": hl.tarray(hl.tstr)}
            ... )
            >>> t.show()
            +--------+--------+--------------------+-----------------+----------+
            | number | state  | user.login         | user.site_admin |  user.id |
            +--------+--------+--------------------+-----------------+----------+
            |  int32 | str    | str                |            bool |    int32 |
            +--------+--------+--------------------+-----------------+----------+
            |  10038 | "open" | "tpoterba"         |           False | 10562794 |
            |  10037 | "open" | "daniel-goldstein" |           False | 24440116 |
            |  10036 | "open" | "jigold"           |           False |  1693348 |
            |  10035 | "open" | "tpoterba"         |           False | 10562794 |
            |  10033 | "open" | "tpoterba"         |           False | 10562794 |
            +--------+--------+--------------------+-----------------+----------+
            +-----------+------------+
            | milestone | labels     |
            +-----------+------------+
            | str       | array<str> |
            +-----------+------------+
            | NA        | []         |
            | NA        | []         |
            | NA        | []         |
            | NA        | []         |
            | NA        | []         |
            +-----------+------------+
    
            Parallelizing with a specified number of partitions:
    
            >>> rows = [ {'a': i} for i in range(100) ]
            >>> ht = hl.Table.parallelize(rows, n_partitions=10)
            >>> ht.n_partitions()
            10
            >>> ht.count()
            100
    
            Parallelizing with some global information:
    
            >>> rows = [ {'a': i} for i in range(5) ]
            >>> ht = hl.Table.parallelize(rows, globals=hl.Struct(global_value=3))
            >>> ht.aggregate(hl.agg.sum(ht.global_value * ht.a))
            30
    
            Warning
            -------
    
            Parallelizing very large local arrays will be slow.
    
            Parameters
            ----------
            rows
                List of row values, or expression of type ``array<struct{...}>``.
            schema : str or a hail type (see ``hail_types``), optional
                Value type.
            key : Union[str, List[str]]], optional
                Key field(s).
            n_partitions : int, optional
            partial_type : ``dict``, optional
                A value type which may elide fields or have ``None`` in arbitrary places. The partial
                type is used by hail where the type cannot be imputed.
            globals: ``dict`` of ``str`` to ``any`` or ``.StructExpression``, optional
                A `dict` or `struct{..}` containing supplementary global data.
    
            Returns
            -------
            ``.Table``
                A distributed Hail table created from the local collection of rows.
    
            """
            if schema and partial_type:
                raise ValueError("define either schema or partial type, not both.")
    
            dtype = schema
            if schema is not None:
                if not isinstance(schema, hl.tstruct):
                    raise ValueError(
                        "parallelize expectes the 'schema' argument to be an hl.tstruct, see docs for details."
                    )
                dtype = hl.tarray(schema)
            elif partial_type is not None:
                partial_type = hl.tarray(hl.tstruct(**partial_type))
            else:
                partial_type = hl.tarray(hl.tstruct())
            rows = to_expr(rows, dtype=dtype, partial_type=partial_type)
            if not isinstance(rows.dtype.element_type, tstruct):
                raise TypeError("'parallelize' expects an array with element type 'struct', found '{}'".format(rows.dtype))
            table = Table(
                ir.TableParallelize(
                    ir.MakeStruct([('rows', rows._ir), ('global', (globals or hl.struct())._ir)]), n_partitions
                )
            )
            if key is not None:
                table = table.key_by(*key)
            return table
    
    
    
        @staticmethod
        @typecheck(
            contexts=expr_array(expr_any),
            partitions=oneof(sequenceof(Interval), int),
            rowfn=func_spec(2, expr_array(expr_struct())),
            globals=nullable(expr_struct()),
        )
        def _generate(
            contexts: 'ArrayExpression',
            partitions: 'Union[Sequence[Interval], int]',
            rowfn: 'Callable[[hl.Expression, hl.StructExpression], ArrayExpression]',
            globals: 'Optional[hl.StructExpression]' = None,
        ) -> 'Table':
            """
            Never you mind.
            """
            context_name = f"context_{Env.get_uid()}"
            ctype = contexts.dtype.element_type
            cexpr = construct_expr(ir.Ref(context_name, ctype), ctype)
    
            globals_name = f"globals_{Env.get_uid()}"
            globals = globals or hl.struct()
            gexpr = construct_expr(ir.Ref(globals_name, globals.dtype), globals.dtype)
    
            body = ir.toStream(rowfn(cexpr, gexpr)._ir)
    
            if isinstance(partitions, int):
                partitions = [Interval(hl.Struct(), hl.Struct(), True, True) for _ in range(partitions)]
    
            partitioner = ir.Partitioner(partitions[0].point_type, partitions)
    
            return Table(ir.TableGen(ir.toStream(contexts._ir), globals._ir, context_name, globals_name, body, partitioner))
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.key_by)    @typecheck_method(keys=oneof(str, expr_any), named_keys=expr_any)
        def key_by(self, *keys, **named_keys) -> 'Table':
            """Key table by a new set of fields.
    
            Table keys control both the order of the rows in the table and the ability to join or
            annotate one table with the information in another table.
    
            Examples
            --------
    
            Consider a simple unkeyed table. Its rows appear are guaranteed to appear in the same order
            as they were in the source text file.
    
            >>> ht = hl.import_table('data/kt_example1.tsv', impute=True)
            >>> ht.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
            Changing the key forces the rows to appear in ascending order. For this reason,
            ``.key_by`` is a relatively expensive operation. It must sort the entire dataset.
    
            >>> ht = ht.key_by('HT')
            >>> ht.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
            Suppose that `ht` represents some human subjects in an experiment. We might need to combine
            sample metadata from `ht` with sample metadata from another source. For example:
    
            >>> ht2 = hl.import_table('data/kt_example2.tsv', impute=True)
            >>> ht2 = ht2.key_by('ID')
            >>> ht2.show()
            +-------+-------+----------+
            |    ID |     A | B        |
            +-------+-------+----------+
            | int32 | int32 | str      |
            +-------+-------+----------+
            |     1 |    65 | "cat"    |
            |     2 |    72 | "dog"    |
            |     3 |    70 | "mouse"  |
            |     4 |    60 | "rabbit" |
            +-------+-------+----------+
            >>> combined_ht = ht
            >>> combined_ht = combined_ht.key_by('ID')
            >>> combined_ht = combined_ht.annotate(favorite_pet = ht2[combined_ht.key].B)
            >>> combined_ht.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 | favorite_pet |
            +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | str          |
            +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 | "cat"        |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 | "dog"        |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 | "mouse"      |
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 | "rabbit"     |
            +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
    
            Hail supports compound keys which enforce a dictionary ordering on the rows of the Table.
    
            >>> ht = ht.key_by('SEX', 'HT')
            >>> ht.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
            A key may also be shortened by removing some fields. The ordering of two rows with the same
            key is undefined. You should not rely on them appearing in any particular order.
    
            >>> ht = ht.key_by('SEX')
            >>> ht.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
            Key fields may also be a complex expression:
    
            >>> ht = ht.key_by(C4 = ht.X + ht.Z)
            >>> ht.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |    C4 |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |     9 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |     9 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |    10 |
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |    10 |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    
            The key can be "removed" or set to the empty key. The ordering of the rows in a table
            without a key is undefined.
    
            >>> ht = ht.key_by()
            >>> ht.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |    C4 |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |     9 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |     9 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |    10 |
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |    10 |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    
            Notes
            -----
            This method is used to specify all the fields of a new row key. The old
            key fields may be overwritten by newly-assigned fields, as described in
            ``.Table.annotate``. If not overwritten, they are preserved as non-key
            fields.
    
            See ``.Table.select`` for more information about how to define new
            key fields.
    
            Parameters
            ----------
            keys : varargs of type ``str``
                Field(s) to key by.
    
            Returns
            -------
            ``.Table``
                Table with a new key.
    
            """
            key_fields, computed_keys = get_key_by_exprs("Table.key_by", keys, named_keys, self._row_indices)
    
            if not computed_keys:
                return Table(ir.TableKeyBy(self._tir, key_fields))
    
            new_row = self.row.annotate(**computed_keys)
            base, cleanup = self._process_joins(new_row)
    
            return cleanup(
                Table(ir.TableKeyBy(ir.TableMapRows(ir.TableKeyBy(base._tir, []), new_row._ir), list(key_fields)))
            )
    
    
    
        @typecheck_method(keys=oneof(str, expr_any), named_keys=expr_any)
        def _key_by_assert_sorted(self, *keys, **named_keys) -> 'Table':
            key_fields, computed_keys = get_key_by_exprs("Table.key_by", keys, named_keys, self._row_indices)
    
            if not computed_keys:
                return Table(ir.TableKeyBy(self._tir, key_fields, is_sorted=True))
            else:
                new_row = self.row.annotate(**computed_keys)
                base, cleanup = self._process_joins(new_row)
    
                return cleanup(
                    Table(
                        ir.TableKeyBy(
                            ir.TableMapRows(ir.TableKeyBy(base._tir, []), new_row._ir), list(key_fields), is_sorted=True
                        )
                    )
                )
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.annotate_globals)    @typecheck_method(named_exprs=expr_any)
        def annotate_globals(self, **named_exprs) -> 'Table':
            """Add new global fields.
    
            Examples
            --------
    
            Add a new global field:
    
            >>> ht = hl.utils.range_table(1)
            >>> ht = ht.annotate_globals(pops = ['EUR', 'AFR', 'EAS', 'SAS'])
            >>> ht.globals.show()
            +---------------------------+
            | <expr>.pops               |
            +---------------------------+
            | array<str>                |
            +---------------------------+
            | ["EUR","AFR","EAS","SAS"] |
            +---------------------------+
    
            Global fields may be used to store metadata about an experiment:
    
            >>> ht = ht.annotate_globals(
            ...     study_name='HGDP+1kG',
            ...     release_date='2023-01-01'
            ... )
    
            Note
            ----
            This method does not support aggregation.
    
            Parameters
            ----------
            named_exprs : varargs of ``.Expression``
                Expressions defining new global fields.
    
            Returns
            -------
            ``.Table``
                Table with new global field(s).
            """
            caller = 'Table.annotate_globals'
            check_annotate_exprs(caller, named_exprs, self._global_indices, set())
            return self._select_globals('Table.annotate_globals', self.globals.annotate(**named_exprs))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.select_globals)    def select_globals(self, *exprs, **named_exprs) -> 'Table':
            """Select existing global fields or create new fields by name, dropping the rest.
    
            Examples
            --------
    
            Selecting two global fields, one by name and one new one, replacing any previously annotated
            global fields.
    
            >>> ht = hl.utils.range_table(1)
            >>> ht = ht.annotate_globals(pops = ['EUR', 'AFR', 'EAS', 'SAS'])
            >>> ht = ht.annotate_globals(study_name = 'HGDP+1kg')
            >>> ht.describe()
            ----------------------------------------
            Global fields:
                'pops': array<str>
                'study_name': str
            ----------------------------------------
            Row fields:
                'idx': int32
            ----------------------------------------
            Key: ['idx']
            ----------------------------------------
            >>> ht = ht.select_globals(ht.pops, target_date='2025-01-01')
            >>> ht.describe()
            ----------------------------------------
            Global fields:
                'pops': array<str>
                'target_date': str
            ----------------------------------------
            Row fields:
                'idx': int32
            ----------------------------------------
            Key: ['idx']
            ----------------------------------------
    
            Fields may also be selected by their name:
    
            >>> ht = ht.select_globals('target_date')
            >>> ht.globals.show()
            +--------------------+
            | <expr>.target_date |
            +--------------------+
            | str                |
            +--------------------+
            | "2025-01-01"       |
            +--------------------+
    
            Notes
            -----
            This method creates new global fields. If a created field shares its name
            with a row-indexed field of the table, the method will fail.
    
            Note
            ----
    
            See ``.Table.select`` for more information about using ``select`` methods.
    
            Note
            ----
            This method does not support aggregation.
    
            Parameters
            ----------
            exprs : variable-length args of ``str`` or ``.Expression``
                Arguments that specify field names or nested field reference expressions.
            named_exprs : keyword args of ``.Expression``
                Field names and the expressions to compute them.
    
            Returns
            -------
            ``.Table``
                Table with specified global fields.
    
            """
            caller = 'Table.select_globals'
            new_globals = get_select_exprs(caller, exprs, named_exprs, self._global_indices, self._globals)
    
            return self._select_globals(caller, new_globals)
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.transmute_globals)    @typecheck_method(named_exprs=expr_any)
        def transmute_globals(self, **named_exprs) -> 'Table':
            """Similar to ``.Table.annotate_globals``, but drops referenced fields.
    
            Notes
            -----
            Consider a table with global fields `population`, `area`, and `year`:
    
            >>> ht = hl.utils.range_table(1)
            >>> ht = ht.annotate_globals(population=1000000, area=500, year=2020)
    
            Compute a new field, `density` from `population` and `area` and also drop the latter two
            fields:
    
            >>> ht = ht.transmute_globals(density=ht.population / ht.area)
            >>> ht.globals.show()
            +-------------+----------------+
            | <expr>.year | <expr>.density |
            +-------------+----------------+
            |       int32 |        float64 |
            +-------------+----------------+
            |        2020 |       2.00e+03 |
            +-------------+----------------+
    
            Introduce a new global field `next_year` based on `year`:
    
            >>> ht = ht.transmute_globals(next_year=ht.year + 1)
            >>> ht.globals.show()
            +----------------+------------------+
            | <expr>.density | <expr>.next_year |
            +----------------+------------------+
            |        float64 |            int32 |
            +----------------+------------------+
            |       2.00e+03 |             2021 |
            +----------------+------------------+
    
            See Also
            --------
            ``.Table.transmute``, ``.Table.select_globals``,
            ``.Table.annotate_globals``
    
            Parameters
            ----------
            named_exprs : keyword args of ``.Expression``
                Annotation expressions.
    
            Returns
            -------
            ``.Table``
    
            """
            caller = 'Table.transmute_globals'
            check_annotate_exprs(caller, named_exprs, self._global_indices, set())
            fields_referenced = extract_refs_by_indices(named_exprs.values(), self._global_indices) - set(
                named_exprs.keys()
            )
    
            return self._select_globals(caller, self.globals.annotate(**named_exprs).drop(*fields_referenced))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.transmute)    @typecheck_method(named_exprs=expr_any)
        def transmute(self, **named_exprs) -> 'Table':
            """Add new fields and drop fields referenced.
    
            Examples
            --------
    
            Consider this table:
    
            >>> ht = table1
            >>> ht.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
            Transmuting a field without referencing other fields has the same effect as annotating:
    
            >>> ht = ht.transmute(new_field=hl.struct(x=3, y=4))
            >>> ht.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 | new_field.x |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |       int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |           3 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |           3 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |           3 |
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |           3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
            +-------------+
            | new_field.y |
            +-------------+
            |       int32 |
            +-------------+
            |           4 |
            |           4 |
            |           4 |
            |           4 |
            +-------------+
    
            Transmuting a field while referencing other fields drops those other fields. Notice how the
            compound field, `new_field` is dropped entirely even though we only used one of its
            component fields.
    
            >>> ht = ht.transmute(F=ht.X + 2 * ht.new_field.x)
            >>> ht.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     Z |    C1 |    C2 |    C3 |     F |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     4 |     2 |    50 |     5 |    11 |
            |     2 |    72 | "M" |     3 |     2 |    61 |     1 |    12 |
            |     3 |    70 | "F" |     3 |    10 |    81 |    -5 |    13 |
            |     4 |    60 | "F" |     2 |    11 |    90 |   -10 |    14 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
            Notes
            -----
            This method functions to create new row-indexed fields and consume
            fields found in the expressions in `named_exprs`.
    
            All row-indexed top-level fields found in an expression are dropped
            after the new fields are created.
    
            Note
            ----
            ``transmute`` will not drop key fields.
    
            Warning
            -------
    
            References to fields inside a top-level struct will remove the entire struct, as field
            `new_field` was removed in the example above since `new_field.x` was referenced.
    
            Note
            ----
            This method does not support aggregation.
    
            Parameters
            ----------
            named_exprs : keyword args of ``.Expression``
                New field expressions.
    
            Returns
            -------
            ``.Table``
                Table with transmuted fields.
    
            """
            caller = "Table.transmute"
            check_annotate_exprs(caller, named_exprs, self._row_indices, set())
            fields_referenced = extract_refs_by_indices(named_exprs.values(), self._row_indices) - set(named_exprs.keys())
            fields_referenced -= set(self.key)
    
            return self._select(caller, self.row.annotate(**named_exprs).drop(*fields_referenced))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.annotate)    @typecheck_method(named_exprs=expr_any)
        def annotate(self, **named_exprs) -> 'Table':
            """Add new fields.
    
            New Table fields may be defined in several ways:
    
            1. In terms of constant values. Every row will have the same value.
            2. In terms of other fields in the table.
            3. In terms of fields in other tables, this is called "joining".
    
            Examples
            --------
    
            Consider this table:
    
            >>> ht = ht.drop('C1', 'C2', 'C3')
            >>> ht.show()
            +-------+-------+-----+-------+-------+
            |    ID |    HT | SEX |     X |     Z |
            +-------+-------+-----+-------+-------+
            | int32 | int32 | str | int32 | int32 |
            +-------+-------+-----+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |
            |     2 |    72 | "M" |     6 |     3 |
            |     3 |    70 | "F" |     7 |     3 |
            |     4 |    60 | "F" |     8 |     2 |
            +-------+-------+-----+-------+-------+
    
            Add field Y containing the square of field X
    
            >>> ht = ht.annotate(Y = ht.X ** 2)
            >>> ht.show()
            +-------+-------+-----+-------+-------+----------+
            |    ID |    HT | SEX |     X |     Z |        Y |
            +-------+-------+-----+-------+-------+----------+
            | int32 | int32 | str | int32 | int32 |  float64 |
            +-------+-------+-----+-------+-------+----------+
            |     1 |    65 | "M" |     5 |     4 | 2.50e+01 |
            |     2 |    72 | "M" |     6 |     3 | 3.60e+01 |
            |     3 |    70 | "F" |     7 |     3 | 4.90e+01 |
            |     4 |    60 | "F" |     8 |     2 | 6.40e+01 |
            +-------+-------+-----+-------+-------+----------+
    
            Add multiple fields simultaneously:
    
            >>> ht = ht.annotate(
            ...     A = ht.X / 2,
            ...     B = ht.X + 21
            ... )
            >>> ht.show()
            +-------+-------+-----+-------+-------+----------+----------+-------+
            |    ID |    HT | SEX |     X |     Z |        Y |        A |     B |
            +-------+-------+-----+-------+-------+----------+----------+-------+
            | int32 | int32 | str | int32 | int32 |  float64 |  float64 | int32 |
            +-------+-------+-----+-------+-------+----------+----------+-------+
            |     1 |    65 | "M" |     5 |     4 | 2.50e+01 | 2.50e+00 |    26 |
            |     2 |    72 | "M" |     6 |     3 | 3.60e+01 | 3.00e+00 |    27 |
            |     3 |    70 | "F" |     7 |     3 | 4.90e+01 | 3.50e+00 |    28 |
            |     4 |    60 | "F" |     8 |     2 | 6.40e+01 | 4.00e+00 |    29 |
            +-------+-------+-----+-------+-------+----------+----------+-------+
    
            Add a new field computed from extant fields and a small dictionary:
    
            >>> py_height_description = {65: 'sixty-five', 72: 'seventy-two', 70: 'seventy', 60: 'sixty'}
            >>> hail_height_description = hl.literal(py_height_description)
            >>> ht = ht.annotate(HT_DESCRIPTION=hail_height_description[ht.HT])
            >>> ht.select('HT', 'HT_DESCRIPTION').show()
            +-------+-------+----------------+
            |    ID |    HT | HT_DESCRIPTION |
            +-------+-------+----------------+
            | int32 | int32 | str            |
            +-------+-------+----------------+
            |     1 |    65 | "sixty-five"   |
            |     2 |    72 | "seventy-two"  |
            |     3 |    70 | "seventy"      |
            |     4 |    60 | "sixty"        |
            +-------+-------+----------------+
    
            Add fields from another table onto this table:
    
            >>> ht2 = table2
            >>> ht2 = ht2.key_by('ID')
            >>> ht2.show()
    
            >>> ht = ht.key_by('ID')
            >>> ht = ht.annotate(
            ...    A=ht2[ht.key].A,
            ...    B=ht2[ht.key].B,
            ... )
            >>> ht.show()
            +-------+-------+-----+-------+-------+----------+-------+----------+
            |    ID |    HT | SEX |     X |     Z |        Y |     A | B        |
            +-------+-------+-----+-------+-------+----------+-------+----------+
            | int32 | int32 | str | int32 | int32 |  float64 | int32 | str      |
            +-------+-------+-----+-------+-------+----------+-------+----------+
            |     1 |    65 | "M" |     5 |     4 | 2.50e+01 |    65 | "cat"    |
            |     2 |    72 | "M" |     6 |     3 | 3.60e+01 |    72 | "dog"    |
            |     3 |    70 | "F" |     7 |     3 | 4.90e+01 |    70 | "mouse"  |
            |     4 |    60 | "F" |     8 |     2 | 6.40e+01 |    60 | "rabbit" |
            +-------+-------+-----+-------+-------+----------+-------+----------+
            +----------------+
            | HT_DESCRIPTION |
            +----------------+
            | str            |
            +----------------+
            | "sixty-five"   |
            | "seventy-two"  |
            | "seventy"      |
            | "sixty"        |
            +----------------+
    
            Instead of repeating all the fields from the other table, we may use Python's splat operator
            to indicate we want to copy all the non-key fields from the other table:
    
            >>> ht = ht.annotate(**ht2[ht.key])
            >>> ht.show()
            +-------+-------+-----+-------+-------+----------+-------+----------+
            |    ID |    HT | SEX |     X |     Z |        Y |     A | B        |
            +-------+-------+-----+-------+-------+----------+-------+----------+
            | int32 | int32 | str | int32 | int32 |  float64 | int32 | str      |
            +-------+-------+-----+-------+-------+----------+-------+----------+
            |     1 |    65 | "M" |     5 |     4 | 2.50e+01 |    65 | "cat"    |
            |     2 |    72 | "M" |     6 |     3 | 3.60e+01 |    72 | "dog"    |
            |     3 |    70 | "F" |     7 |     3 | 4.90e+01 |    70 | "mouse"  |
            |     4 |    60 | "F" |     8 |     2 | 6.40e+01 |    60 | "rabbit" |
            +-------+-------+-----+-------+-------+----------+-------+----------+
            +----------------+
            | HT_DESCRIPTION |
            +----------------+
            | str            |
            +----------------+
            | "sixty-five"   |
            | "seventy-two"  |
            | "seventy"      |
            | "sixty"        |
            +----------------+
    
            Parameters
            ----------
            named_exprs : keyword args of ``.Expression``
                Expressions for new fields.
    
            Returns
            -------
            ``.Table``
                Table with new fields.
    
            """
            caller = "Table.annotate"
            check_annotate_exprs(caller, named_exprs, self._row_indices, set())
            return self._select(caller, self.row.annotate(**named_exprs))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.filter)    @typecheck_method(expr=expr_bool, keep=bool)
        def filter(self, expr, keep: bool = True) -> 'Table':
            """Filter rows conditional on the value of each row's fields.
    
            Note
            ----
    
            Hail will can read much less data if a Table filter condition references the key field and
            the Table is stored in Hail native format (i.e. read using ``.read_table``, _not_
            ``.import_table``). In other words: filtering on the key will make a pipeline faster by
            reading fewer rows. This optimization is prevented by certain operations appearing between a
            ``.read_table`` and a ``.filter``. For example, a `key_by` and `group_by`, both
            force reading all the data.
    
            Suppose we previously ``.write`` a Hail Table with one million rows keyed by a field
            called `idx`. If we filter this table to one value of `idx`, the pipeline will be fast
            because we read only the rows that have that value of `idx`:
    
            >>> ht = hl.read_table('large-table.ht')  # doctest: +SKIP
            >>> ht = ht.filter(ht.idx == 5)  # doctest: +SKIP
    
            This also works with inequality conditions:
    
            >>> ht = hl.read_table('large-table.ht')  # doctest: +SKIP
            >>> ht = ht.filter(ht.idx <= 5)  # doctest: +SKIP
    
            Examples
            --------
    
            Consider this table:
    
            >>> ht = ht.drop('C1', 'C2', 'C3')
            >>> ht.show()
            +-------+-------+-----+-------+-------+
            |    ID |    HT | SEX |     X |     Z |
            +-------+-------+-----+-------+-------+
            | int32 | int32 | str | int32 | int32 |
            +-------+-------+-----+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |
            |     2 |    72 | "M" |     6 |     3 |
            |     3 |    70 | "F" |     7 |     3 |
            |     4 |    60 | "F" |     8 |     2 |
            +-------+-------+-----+-------+-------+
    
            Keep rows where ``Z`` is 3:
    
            >>> filtered_ht = ht.filter(ht.Z == 3)
            >>> filtered_ht.show()
    
            +-------+-------+-----+-------+-------+
            |    ID |    HT | SEX |     X |     Z |
            +-------+-------+-----+-------+-------+
            | int32 | int32 | str | int32 | int32 |
            +-------+-------+-----+-------+-------+
            |     2 |    72 | "M" |     6 |     3 |
            |     3 |    70 | "F" |     7 |     3 |
            +-------+-------+-----+-------+-------+
    
            Remove rows where ``Z`` is 3:
    
            >>> filtered_ht = ht.filter(ht.Z == 3, keep=False)
            >>> filtered_ht.show()
            +-------+-------+-----+-------+-------+
            |    ID |    HT | SEX |     X |     Z |
            +-------+-------+-----+-------+-------+
            | int32 | int32 | str | int32 | int32 |
            +-------+-------+-----+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |
            |     4 |    60 | "F" |     8 |     2 |
            +-------+-------+-----+-------+-------+
    
            Keep rows where X is less than 7 and Z is greater than 2:
    
            >>> filtered_ht = ht.filter(hl.all(
            ...     ht.X < 7,
            ...     ht.Z > 2
            ... ))
            >>> filtered_ht.show()
            +-------+-------+-----+-------+-------+
            |    ID |    HT | SEX |     X |     Z |
            +-------+-------+-----+-------+-------+
            | int32 | int32 | str | int32 | int32 |
            +-------+-------+-----+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |
            |     2 |    72 | "M" |     6 |     3 |
            +-------+-------+-----+-------+-------+
    
            Keep rows where X is less than 7 or Z is greater than 2:
    
            >>> filtered_ht = ht.filter(hl.any(
            ...     ht.X < 7,
            ...     ht.Z > 2
            ... ))
            >>> filtered_ht.show()
            +-------+-------+-----+-------+-------+
            |    ID |    HT | SEX |     X |     Z |
            +-------+-------+-----+-------+-------+
            | int32 | int32 | str | int32 | int32 |
            +-------+-------+-----+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |
            |     2 |    72 | "M" |     6 |     3 |
            |     3 |    70 | "F" |     7 |     3 |
            +-------+-------+-----+-------+-------+
    
            Keep "M" rows where ``HT`` is less than 72 and "F" rows where ``HT`` is less than 65:
    
            >>> filtered_ht = ht.filter(
            ...     hl.if_else(
            ...         ht.SEX == "M",
            ...         ht.HT < 72,
            ...         ht.HT < 65
            ...     )
            ... )
            >>> filtered_ht.show()
            +-------+-------+-----+-------+-------+
            |    ID |    HT | SEX |     X |     Z |
            +-------+-------+-----+-------+-------+
            | int32 | int32 | str | int32 | int32 |
            +-------+-------+-----+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |
            |     4 |    60 | "F" |     8 |     2 |
            +-------+-------+-----+-------+-------+
    
            Notice that if the condition evaluates to missing, the row is _always_ removed regardless of
            the setting of `keep`:
    
            >>> ht2 = ht
            >>> ht2 = ht.annotate(X = hl.or_missing(ht.X != 5, ht.X))
            >>> ht2.show()
            +-------+-------+-----+-------+-------+
            |    ID |    HT | SEX |     X |     Z |
            +-------+-------+-----+-------+-------+
            | int32 | int32 | str | int32 | int32 |
            +-------+-------+-----+-------+-------+
            |     1 |    65 | "M" |    NA |     4 |
            |     2 |    72 | "M" |     6 |     3 |
            |     3 |    70 | "F" |     7 |     3 |
            |     4 |    60 | "F" |     8 |     2 |
            +-------+-------+-----+-------+-------+
            >>> filtered_ht = ht2.filter(ht2.X < 7, keep=True)
            >>> filtered_ht.show()
            +-------+-------+-----+-------+-------+
            |    ID |    HT | SEX |     X |     Z |
            +-------+-------+-----+-------+-------+
            | int32 | int32 | str | int32 | int32 |
            +-------+-------+-----+-------+-------+
            |     2 |    72 | "M" |     6 |     3 |
            +-------+-------+-----+-------+-------+
            >>> filtered_ht = ht2.filter(ht2.X < 7, keep=False)
            >>> filtered_ht.show()
            +-------+-------+-----+-------+-------+
            |    ID |    HT | SEX |     X |     Z |
            +-------+-------+-----+-------+-------+
            | int32 | int32 | str | int32 | int32 |
            +-------+-------+-----+-------+-------+
            |     3 |    70 | "F" |     7 |     3 |
            |     4 |    60 | "F" |     8 |     2 |
            +-------+-------+-----+-------+-------+
    
            Notes
            -----
            The expression `expr` will be evaluated for every row of the table. If
            `keep` is ``True``, then rows where `expr` evaluates to ``True`` will be
            kept (the filter removes the rows where the predicate evaluates to
            ``False``). If `keep` is ``False``, then rows where `expr` evaluates to
            ``True`` will be removed (the filter keeps the rows where the predicate
            evaluates to ``False``).
    
            Warning
            -------
            When `expr` evaluates to missing, the row will be removed regardless of `keep`.
    
            Note
            ----
            This method does not support aggregation.
    
            Parameters
            ----------
            expr : bool or ``.BooleanExpression``
                Filter expression.
            keep : bool
                Keep rows where `expr` is true.
    
            Returns
            -------
            ``.Table``
                Filtered table.
    
            """
            analyze('Table.filter', expr, self._row_indices)
            base, cleanup = self._process_joins(expr)
    
            return cleanup(Table(ir.TableFilter(base._tir, ir.filter_predicate_with_keep(expr._ir, keep))))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.select)    @typecheck_method(exprs=oneof(Expression, str), named_exprs=anytype)
        def select(self, *exprs, **named_exprs) -> 'Table':
            """Select existing fields or create new fields by name, dropping the rest.
    
            Examples
            --------
            Select a few old fields and compute a new one:
    
            >>> table_result = table1.select(table1.C1, Y=table1.Z - table1.X)
    
            Notes
            -----
            This method creates new row-indexed fields. If a created field shares its name
            with a global field of the table, the method will fail.
    
            Note
            ----
    
            **Using select**
    
            Select and its sibling methods (``.Table.select_globals``,
            ``.MatrixTable.select_globals``, ``.MatrixTable.select_rows``,
            ``.MatrixTable.select_cols``, and ``.MatrixTable.select_entries``) accept
            both variable-length (``f(x, y, z)``) and keyword (``f(a=x, b=y, c=z)``)
            arguments.
    
            Select methods will always preserve the key along that axis; e.g. for
            ``.Table.select``, the table key will aways be kept. To modify the
            key, use ``.key_by``.
    
            Variable-length arguments can be either strings or expressions that reference a
            (possibly nested) field of the table. Keyword arguments can be arbitrary
            expressions.
    
            **The following three usages are all equivalent**, producing a new table with
            fields `C1` and `C2` of `table1`, and the table key `ID`.
    
            First, variable-length string arguments:
    
            >>> table_result = table1.select('C1', 'C2')
    
            Second, field reference variable-length arguments:
    
            >>> table_result = table1.select(table1.C1, table1.C2)
    
            Last, expression keyword arguments:
    
            >>> table_result = table1.select(C1 = table1.C1, C2 = table1.C2)
    
            Additionally, the variable-length argument syntax also permits nested field
            references. Given the following struct field `s`:
    
            >>> table3 = table1.annotate(s = hl.struct(x=table1.X, z=table1.Z))
    
            The following two usages are equivalent, producing a table with one field, `x`.:
    
            >>> table3_result = table3.select(table3.s.x)
    
            >>> table3_result = table3.select(x = table3.s.x)
    
            The keyword argument syntax permits arbitrary expressions:
    
            >>> table_result = table1.select(foo=table1.X ** 2 + 1)
    
            These syntaxes can be mixed together, with the stipulation that all keyword arguments
            must come at the end due to Python language restrictions.
    
            >>> table_result = table1.select(table1.X, 'Z', bar = [table1.C1, table1.C2])
    
            Note
            ----
            This method does not support aggregation.
    
            Parameters
            ----------
            exprs : variable-length args of ``str`` or ``.Expression``
                Arguments that specify field names or nested field reference expressions.
            named_exprs : keyword args of ``.Expression``
                Field names and the expressions to compute them.
    
            Returns
            -------
            ``.Table``
                Table with specified fields.
            """
            row = get_select_exprs('Table.select', exprs, named_exprs, self._row_indices, self._row)
    
            return self._select('Table.select', row)
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.drop)    @typecheck_method(exprs=oneof(str, Expression))
        def drop(self, *exprs) -> 'Table':
            """Drop fields from the table.
    
            Examples
            --------
    
            Drop fields `C1` and `C2` using strings:
    
            >>> table_result = table1.drop('C1', 'C2')
    
            Drop fields `C1` and `C2` using field references:
    
            >>> table_result = table1.drop(table1.C1, table1.C2)
    
            Drop a list of fields:
    
            >>> fields_to_drop = ['C1', 'C2']
            >>> table_result = table1.drop(*fields_to_drop)
    
            Notes
            -----
    
            This method can be used to drop global or row-indexed fields. The arguments
            can be either strings (``'field'``), or top-level field references
            (``table.field`` or ``table['field']``).
    
            Parameters
            ----------
            exprs : varargs of ``str`` or ``.Expression``
                Names of fields to drop or field reference expressions.
    
            Returns
            -------
            ``.Table``
                Table without specified fields.
            """
            all_field_exprs = {e: k for k, e in self._fields.items()}
            fields_to_drop = set()
            for e in exprs:
                if isinstance(e, Expression):
                    if e in all_field_exprs:
                        fields_to_drop.add(all_field_exprs[e])
                    else:
                        raise ExpressionException(
                            "method 'drop' expects string field names or top-level field expressions (e.g. table['foo'])"
                        )
                else:
                    assert isinstance(e, str)
                    if e not in self._fields:
                        raise IndexError("table has no field '{}'".format(e))
                    fields_to_drop.add(e)
    
            table = self
            if any(self._fields[field]._indices == self._global_indices for field in fields_to_drop):
                # need to drop globals
                table = table._select_globals(
                    'drop', self._globals.drop(*[f for f in table.globals if f in fields_to_drop])
                )
    
            if any(self._fields[field]._indices == self._row_indices for field in fields_to_drop):
                # need to drop row fields
                protected_key = set(self._row_indices.protected_key)
                for f in fields_to_drop:
                    check_keys('drop', f, protected_key)
                row_fields = set(table.row)
                to_drop = [f for f in fields_to_drop if f in row_fields]
                table = table._select('drop', table.row.drop(*to_drop))
    
            return table
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.export)    @typecheck_method(
            output=str, types_file=nullable(str), header=bool, parallel=nullable(ir.ExportType.checker), delimiter=str
        )
        def export(self, output, types_file=None, header=True, parallel=None, delimiter='\t'):
            """Export to a text file.
    
            Examples
            --------
            Export to a tab-separated file:
    
            >>> table1.export('output/table1.tsv.bgz')
    
            Note
            ----
            It is highly recommended to export large files with a ``.bgz`` extension,
            which will use a block gzipped compression codec. These files can be
            read natively with any Hail method, as well as with Python's ``gzip.open``
            and R's ``read.table``.
    
            Nested structures will be exported as JSON. In order to export nested struct
            fields as separate fields in the resulting table, use ``flatten`` first.
    
            Warning
            -------
            Do not export to a path that is being read from in the same pipeline.
    
            See Also
            --------
            ``flatten``, ``write``
    
            Parameters
            ----------
            output : ``str``
                URI at which to write exported file.
            types_file : ``str``, optional
                URI at which to write file containing field type information.
            header : ``bool``
                Include a header in the file.
            parallel : ``str``, optional
                If None, a single file is produced, otherwise a
                folder of file shards is produced. If 'separate_header',
                the header file is output separately from the file shards. If
                'header_per_shard', each file shard has a header. If set to None
                the export will be slower.
            delimiter : ``str``
                Field delimiter.
            """
            hl.current_backend().validate_file(output)
    
            parallel = ir.ExportType.default(parallel)
            Env.backend().execute(
                ir.TableWrite(self._tir, ir.TableTextWriter(output, types_file, header, parallel, delimiter))
            )
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.group_by)    def group_by(self, *exprs, **named_exprs) -> 'GroupedTable':
            """Group by a new key for use with ``.GroupedTable.aggregate``.
    
            Examples
            --------
            Compute the mean value of `X` and the sum of `Z` per unique `ID`:
    
            >>> table_result = (table1.group_by(table1.ID)
            ...                       .aggregate(meanX = hl.agg.mean(table1.X), sumZ = hl.agg.sum(table1.Z)))
    
            Group by a height bin and compute sex ratio per bin:
    
            >>> table_result = (table1.group_by(height_bin = table1.HT // 20)
            ...                       .aggregate(fraction_female = hl.agg.fraction(table1.SEX == 'F')))
    
            Notes
            -----
            This function is always followed by ``.GroupedTable.aggregate``. Follow the
            link for documentation on the aggregation step.
    
            Note
            ----
            **Using group_by**
    
            **group_by** and its sibling methods (``.MatrixTable.group_rows_by`` and
            ``.MatrixTable.group_cols_by``) accept both variable-length (``f(x, y, z)``)
            and keyword (``f(a=x, b=y, c=z)``) arguments.
    
            Variable-length arguments can be either strings or expressions that reference a
            (possibly nested) field of the table. Keyword arguments can be arbitrary
            expressions.
    
            **The following three usages are all equivalent**, producing a
            ``.GroupedTable`` grouped by fields `C1` and `C2` of `table1`.
    
            First, variable-length string arguments:
    
            >>> table_result = (table1.group_by('C1', 'C2')
            ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    
            Second, field reference variable-length arguments:
    
            >>> table_result = (table1.group_by(table1.C1, table1.C2)
            ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    
            Last, expression keyword arguments:
    
            >>> table_result = (table1.group_by(C1 = table1.C1, C2 = table1.C2)
            ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    
            Additionally, the variable-length argument syntax also permits nested field
            references. Given the following struct field `s`:
    
            >>> table3 = table1.annotate(s = hl.struct(x=table1.X, z=table1.Z))
    
            The following two usages are equivalent, grouping by one field, `x`:
    
            >>> table_result = (table3.group_by(table3.s.x)
            ...                       .aggregate(meanX = hl.agg.mean(table3.X)))
    
            >>> table_result = (table3.group_by(x = table3.s.x)
            ...                       .aggregate(meanX = hl.agg.mean(table3.X)))
    
            The keyword argument syntax permits arbitrary expressions:
    
            >>> table_result = (table1.group_by(foo=table1.X ** 2 + 1)
            ...                       .aggregate(meanZ = hl.agg.mean(table1.Z)))
    
            These syntaxes can be mixed together, with the stipulation that all keyword arguments
            must come at the end due to Python language restrictions.
    
            >>> table_result = (table1.group_by(table1.C1, 'C2', height_bin = table1.HT // 20)
            ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    
            Note
            ----
            This method does not support aggregation in key expressions.
    
            Arguments
            ---------
            exprs : varargs of type str or ``.Expression``
                Field names or field reference expressions.
            named_exprs : keyword args of type ``.Expression``
                Field names and expressions to compute them.
    
            Returns
            -------
            ``.GroupedTable``
                Grouped table; use ``.GroupedTable.aggregate`` to complete the aggregation.
            """
            key, computed_key = get_key_by_exprs(
                'Table.group_by', exprs, named_exprs, self._row_indices, override_protected_indices={self._global_indices}
            )
            return GroupedTable(self, self.row.annotate(**computed_key).select(*key))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.aggregate)    @typecheck_method(expr=expr_any, _localize=bool)
        def aggregate(self, expr, _localize=True):
            """Aggregate over rows into a local value.
    
            Examples
            --------
            Aggregate over rows:
    
            >>> table1.aggregate(hl.struct(fraction_male=hl.agg.fraction(table1.SEX == 'M'),
            ...                            mean_x=hl.agg.mean(table1.X)))
            Struct(fraction_male=0.5, mean_x=6.5)
    
            Note
            ----
            This method supports (and expects!) aggregation over rows.
    
            Parameters
            ----------
            expr : ``.Expression``
                Aggregation expression.
    
            Returns
            -------
            any
                Aggregated value dependent on `expr`.
            """
            expr = to_expr(expr)
            base, _ = self._process_joins(expr)
            analyze('Table.aggregate', expr, self._global_indices, {self._row_axis})
    
            agg_ir = ir.TableAggregate(base._tir, expr._ir)
    
            if _localize:
                return Env.backend().execute(hl.ir.MakeTuple([agg_ir]))[0]
    
            return construct_expr(ir.LiftMeOut(agg_ir), expr.dtype)
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.checkpoint)    @typecheck_method(
            output=str,
            overwrite=bool,
            stage_locally=bool,
            _codec_spec=nullable(str),
            _read_if_exists=bool,
            _intervals=nullable(sequenceof(anytype)),
            _filter_intervals=bool,
        )
        def checkpoint(
            self,
            output: str,
            overwrite: bool = False,
            stage_locally: bool = False,
            _codec_spec: Optional[str] = None,
            _read_if_exists: bool = False,
            _intervals=None,
            _filter_intervals=False,
        ) -> 'Table':
            """Checkpoint the table to disk by writing and reading.
    
            Parameters
            ----------
            output : str
                Path at which to write.
            stage_locally: bool
                If ``True``, major output will be written to temporary local storage
                before being copied to ``output``
            overwrite : bool
                If ``True``, overwrite an existing file at the destination.
    
            Returns
            -------
            ``Table``
    
    
            .. include:: _templates/write_warning.rst
    
            Notes
            -----
            An alias for ``write`` followed by ``.read_table``. It is
            possible to read the file at this path later with ``.read_table``.
    
            Examples
            --------
            >>> table1 = table1.checkpoint('output/table_checkpoint.ht', overwrite=True)
    
            """
            hl.current_backend().validate_file(output)
    
            if not _read_if_exists or not hl.hadoop_exists(f'{output}/_SUCCESS'):
                self.write(output=output, overwrite=overwrite, stage_locally=stage_locally, _codec_spec=_codec_spec)
                _assert_type = self._type
                _load_refs = False
            else:
                _assert_type = None
                _load_refs = True
            return hl.read_table(
                output,
                _intervals=_intervals,
                _filter_intervals=_filter_intervals,
                _assert_type=_assert_type,
                _load_refs=_load_refs,
            )
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.write)    @typecheck_method(output=str, overwrite=bool, stage_locally=bool, _codec_spec=nullable(str))
        def write(self, output: str, overwrite=False, stage_locally: bool = False, _codec_spec: Optional[str] = None):
            """Write to disk.
    
            Examples
            --------
    
            >>> table1.write('output/table1.ht', overwrite=True)
    
            .. include:: _templates/write_warning.rst
    
            See Also
            --------
            ``.read_table``
    
            Parameters
            ----------
            output : str
                Path at which to write.
            stage_locally: bool
                If ``True``, major output will be written to temporary local storage
                before being copied to ``output``.
            overwrite : bool
                If ``True``, overwrite an existing file at the destination.
            """
    
            hl.current_backend().validate_file(output)
    
            Env.backend().execute(
                ir.TableWrite(self._tir, ir.TableNativeWriter(output, overwrite, stage_locally, _codec_spec))
            )
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.write_many)    @typecheck_method(output=str, fields=sequenceof(str), overwrite=bool, stage_locally=bool, _codec_spec=nullable(str))
        def write_many(
            self,
            output: str,
            fields: Sequence[str],
            *,
            overwrite: bool = False,
            stage_locally: bool = False,
            _codec_spec: Optional[str] = None,
        ):
            """Write fields to distinct tables.
    
            Examples
            --------
    
            >>> t = hl.utils.range_table(10)
            >>> t = t.annotate(a = t.idx, b = t.idx * t.idx, c = hl.str(t.idx))
            >>> t.write_many('output-many', fields=('a', 'b', 'c'), overwrite=True)
            >>> hl.read_table('output-many/a').describe()
            ----------------------------------------
            Global fields:
                None
            ----------------------------------------
            Row fields:
                'a': int32
                'idx': int32
            ----------------------------------------
            Key: ['idx']
            ----------------------------------------
            >>> hl.read_table('output-many/a').show()
            +-------+-------+
            |     a |   idx |
            +-------+-------+
            | int32 | int32 |
            +-------+-------+
            |     0 |     0 |
            |     1 |     1 |
            |     2 |     2 |
            |     3 |     3 |
            |     4 |     4 |
            |     5 |     5 |
            |     6 |     6 |
            |     7 |     7 |
            |     8 |     8 |
            |     9 |     9 |
            +-------+-------+
            >>> hl.read_table('output-many/b').describe()
            ----------------------------------------
            Global fields:
                None
            ----------------------------------------
            Row fields:
                'b': int32
                'idx': int32
            ----------------------------------------
            Key: ['idx']
            ----------------------------------------
            >>> hl.read_table('output-many/b').show()
            +-------+-------+
            |     b |   idx |
            +-------+-------+
            | int32 | int32 |
            +-------+-------+
            |     0 |     0 |
            |     1 |     1 |
            |     4 |     2 |
            |     9 |     3 |
            |    16 |     4 |
            |    25 |     5 |
            |    36 |     6 |
            |    49 |     7 |
            |    64 |     8 |
            |    81 |     9 |
            +-------+-------+
            >>> hl.read_table('output-many/c').describe()
            ----------------------------------------
            Global fields:
                None
            ----------------------------------------
            Row fields:
                'c': str
                'idx': int32
            ----------------------------------------
            Key: ['idx']
            ----------------------------------------
            >>> hl.read_table('output-many/c').show()
            +-----+-------+
            | c   |   idx |
            +-----+-------+
            | str | int32 |
            +-----+-------+
            | "0" |     0 |
            | "1" |     1 |
            | "2" |     2 |
            | "3" |     3 |
            | "4" |     4 |
            | "5" |     5 |
            | "6" |     6 |
            | "7" |     7 |
            | "8" |     8 |
            | "9" |     9 |
            +-----+-------+
    
            .. include:: _templates/write_warning.rst
    
            See Also
            --------
            ``.read_table``
    
            Parameters
            ----------
            output : str
                Path at which to write.
            fields : list of str
                The fields to write.
            stage_locally: bool
                If ``True``, major output will be written to temporary local storage
                before being copied to ``output``.
            overwrite : bool
                If ``True``, overwrite an existing file at the destination.
            """
    
            hl.current_backend().validate_file(output)
    
            Env.backend().execute(
                ir.TableWrite(self._tir, ir.TableNativeFanoutWriter(output, fields, overwrite, stage_locally, _codec_spec))
            )
    
    
    
        def _show(self, n, width, truncate, types):
            return Table._Show(self, n, width, truncate, types)
    
        class _Show:
            def __init__(self, table, n, width, truncate, types):
                if n is None or width is None:
                    (columns, lines) = shutil.get_terminal_size((80, 10))
                    width = width or columns
                    n = n or min(max(10, (lines - 20)), 100)
                self.table = table
                self.n = n
                self.width = max(width, 8)
                if truncate:
                    self.truncate = min(max(truncate, 4), width - 4)
                else:
                    self.truncate = width - 4
                self.types = types
                self._data = None
    
            def __str__(self):
                return self._ascii_str()
    
            def __repr__(self):
                return self.__str__()
    
            def data(self):
                if self._data is None:
                    t = self.table.flatten()
                    row_dtype = t.row.dtype
                    t = t.select(**{k: hl._showstr(v) for (k, v) in t.row.items()})
                    rows, has_more = t._take_n(self.n)
                    self._data = (rows, has_more, row_dtype)
                return self._data
    
            def _repr_html_(self):
                return self._html_str()
    
            def _ascii_str(self):
                truncate = self.truncate
                types = self.types
    
                def trunc(s):
                    if len(s) > truncate:
                        return s[: truncate - 3] + "..."
                    return s
    
                rows, has_more, dtype = self.data()
                fields = list(dtype)
                trunc_fields = [trunc(f) for f in fields]
                n_fields = len(fields)
    
                type_strs = [trunc(str(dtype[f])) for f in fields] if types else [''] * len(fields)
                right_align = [hl.expr.types.is_numeric(dtype[f]) for f in fields]
    
                rows = [[trunc(row[f]) for f in fields] for row in rows]
    
                def max_value_width(i):
                    return max(itertools.chain([0], (len(row[i]) for row in rows)))
    
                column_width = [max(len(trunc_fields[i]), len(type_strs[i]), max_value_width(i)) for i in range(n_fields)]
    
                column_blocks = []
                start = 0
                i = 1
                w = column_width[0] + 4 if column_width else 0
                while i < n_fields:
                    w = w + column_width[i] + 3
                    if w > self.width:
                        column_blocks.append((start, i))
                        start = i
                        w = column_width[i] + 4
                    i = i + 1
                column_blocks.append((start, i))
    
                def format_hline(widths):
                    if not widths:
                        return "++\n"
                    return '+-' + '-+-'.join(['-' * w for w in widths]) + '-+\n'
    
                def pad(v, w, ra):
                    e = w - len(v)
                    if ra:
                        return ' ' * e + v
                    else:
                        return v + ' ' * e
    
                def format_line(values, widths, right_align):
                    if not values:
                        return "||\n"
                    values = map(pad, values, widths, right_align)
                    return '| ' + ' | '.join(values) + ' |\n'
    
                s = ''
                first = True
                for start, end in column_blocks:
                    if first:
                        first = False
                    else:
                        s += '\n'
    
                    block_column_width = column_width[start:end]
                    block_right_align = right_align[start:end]
                    hline = format_hline(block_column_width)
    
                    s += hline
                    s += format_line(trunc_fields[start:end], block_column_width, block_right_align)
                    s += hline
                    if types:
                        s += format_line(type_strs[start:end], block_column_width, block_right_align)
                        s += hline
                    for row in rows:
                        _row = row[start:end]
                        s += format_line(_row, block_column_width, block_right_align)
                    s += hline
    
                if has_more:
                    n_rows = len(rows)
                    s += f"showing top {n_rows} {'row' if n_rows == 1 else 'rows'}\n"
    
                return s
    
            def _html_str(self):
                import html
    
                types = self.types
    
                rows, has_more, dtype = self.data()
                fields = list(dtype)
    
                default_td_style = 'white-space: nowrap; max-width: 500px; overflow: hidden; text-overflow: ellipsis; '
    
                def format_line(values, extra_style=''):
                    style = default_td_style + extra_style
                    return f'<tr><td style="{style}">' + f'</td><td style="{style}">'.join(values) + '</td></tr>\n'
    
                arranged_field_names = PlacementTree.from_named_type('row', self.table.row.dtype)
    
                s = '<table>'
                s += '<thead>'
                for header_row in arranged_field_names.to_grid():
                    s += '<tr>'
                    div_style = 'text-align: left;'
                    non_empty_div_style = 'border-bottom: solid 2px #000; padding-bottom: 5px'
                    for header_cell in header_row:
                        text, width = header_cell
                        s += f'<td style="{default_td_style}" colspan="{width}">'
                        if text is not None:
                            s += f'<div style="{div_style}{non_empty_div_style}">'
                            s += html.escape(text)
                            s += '</div>'
                        else:
                            s += f'<div style="{div_style}"></div>'
                        s += '</td>'
                    s += '</tr>'
                if types:
                    s += format_line([html.escape(str(dtype[f])) for f in fields], extra_style="text-align: left;")
                s += '</thead><tbody>'
                for row in rows:
                    s += format_line([html.escape(row[f]) for f in row])
                s += '</tbody></table>'
    
                if has_more:
                    n_rows = len(rows)
                    s += '<p style="background: #fdd; padding: 0.4em;">'
                    s += f"showing top {n_rows} {plural('row', n_rows)}"
                    s += '</p>\n'
    
                return s
    
        def _take_n(self, n):
            if n < 0:
                rows = self.collect()
                has_more = False
            else:
                rows = self.take(n + 1)
                has_more = len(rows) > n
                rows = rows[:n]
            return rows, has_more
    
        @staticmethod
        def _hl_format(v, truncate):
            return hl._showstr(v, truncate)
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.show)    @typecheck_method(
            n=nullable(int),
            width=nullable(int),
            truncate=nullable(int),
            types=bool,
            handler=nullable(anyfunc),
            n_rows=nullable(int),
        )
        def show(self, n=None, width=None, truncate=None, types=True, handler=None, n_rows=None):
            """Print the first few rows of the table to the console.
    
            Examples
            --------
            Show the first lines of the table:
    
            >>> table1.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
            Notes
            -----
            The output can be passed piped to another output source using the `handler` argument:
    
            >>> ht.show(handler=lambda x: logging.info(x))  # doctest: +SKIP
    
            Parameters
            ----------
            n or n_rows : ``int``
                Maximum number of rows to show, or negative to show all rows.
            width : ``int``
                Horizontal width at which to break fields.
            truncate : ``int``, optional
                Truncate each field to the given number of characters. If
                ``None``, truncate fields to the given `width`.
            types : ``bool``
                Print an extra header line with the type of each field.
            handler : Callable[[str], Any]
                Handler function for data string.
            """
            if n_rows is not None and n is not None:
                raise ValueError(f'specify one of n_rows or n, received {n_rows} and {n}')
            if n_rows is not None:
                n = n_rows
            del n_rows
            if handler is None:
                handler = hl.utils.default_handler()
            return handler(self._show(n, width, truncate, types))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.index)    def index(self, *exprs, all_matches=False) -> 'Expression':
            """Expose the row values as if looked up in a dictionary, indexing
            with `exprs`.
    
            Examples
            --------
            In the example below, both `table1` and `table2` are keyed by one
            field `ID` of type ``int``.
    
            >>> table_result = table1.select(B = table2.index(table1.ID).B)
            >>> table_result.B.show()
            +-------+----------+
            |    ID | B        |
            +-------+----------+
            | int32 | str      |
            +-------+----------+
            |     1 | "cat"    |
            |     2 | "dog"    |
            |     3 | "mouse"  |
            |     4 | "rabbit" |
            +-------+----------+
    
            Using `key` as the sole index expression is equivalent to passing all
            key fields individually:
    
            >>> table_result = table1.select(B = table2.index(table1.key).B)
    
            It is also possible to use non-key fields or expressions as the index
            expressions:
    
            >>> table_result = table1.select(B = table2.index(table1.C1 % 4).B)
            >>> table_result.show()
            +-------+---------+
            |    ID | B       |
            +-------+---------+
            | int32 | str     |
            +-------+---------+
            |     1 | "dog"   |
            |     2 | "dog"   |
            |     3 | "dog"   |
            |     4 | "mouse" |
            +-------+---------+
    
            Notes
            -----
            ``.Table.index`` is used to expose one table's fields for use in
            expressions involving the another table or matrix table's fields. The
            result of the method call is a struct expression that is usable in the
            same scope as `exprs`, just as if `exprs` were used to look up values of
            the table in a dictionary.
    
            The type of the struct expression is the same as the indexed table's
            ``.row_value`` (the key fields are removed, as they are available
            in the form of the index expressions).
    
            Note
            ----
            There is a shorthand syntax for ``.Table.index`` using square
            brackets (the Python ``__getitem__`` syntax). This syntax is preferred.
    
            >>> table_result = table1.select(B = table2[table1.ID].B)
    
            Parameters
            ----------
            exprs : variable-length args of ``.Expression``
                Index expressions.
            all_matches : bool
                Experimental. If ``True``, value of expression is array of all matches.
    
            Returns
            -------
            ``.Expression``
            """
            try:
                return self._index(*exprs, all_matches=all_matches)
            except TableIndexKeyError as err:
                raise ExpressionException(
                    f"Key type mismatch: cannot index table with given expressions:\n"
                    f"  Table key:         {', '.join(str(t) for t in err.key_type.values()) or '<<<empty key>>>'}\n"
                    f"  Index Expressions: {', '.join(str(e.dtype) for e in err.index_expressions)}"
                )
    
    
    
        @staticmethod
        def _maybe_truncate_for_flexindex(indexer, indexee_dtype):
            if not len(indexee_dtype) > 0:
                raise ValueError('Must have non-empty key to index')
    
            if not isinstance(indexer.dtype, (hl.tstruct, hl.ttuple)):
                indexer = hl.tuple([indexer])
    
            matching_prefix = 0
            for x, y in zip(indexer.dtype.types, indexee_dtype.types):
                if x != y:
                    break
                matching_prefix += 1
            prefix_match = matching_prefix == len(indexee_dtype)
            direct_match = prefix_match and len(indexer) == len(indexee_dtype)
            prefix_interval_match = (
                len(indexee_dtype) == 1
                and isinstance(indexee_dtype[0], hl.tinterval)
                and indexer.dtype[0] == indexee_dtype[0].point_type
            )
            direct_interval_match = prefix_interval_match and len(indexer) == 1
            if direct_match or direct_interval_match:
                return indexer
            if prefix_match:
                return indexer[0:matching_prefix]
            if prefix_interval_match:
                return indexer[0]
            return None
    
        @typecheck_method(indexer=expr_any, all_matches=bool)
        def _maybe_flexindex_table_by_expr(self, indexer, all_matches=False):
            truncated_indexer = Table._maybe_truncate_for_flexindex(indexer, self.key.dtype)
            if truncated_indexer is not None:
                return self.index(truncated_indexer, all_matches=all_matches)
            return None
    
        def _index(self, *exprs, all_matches=False) -> 'Expression':
            exprs = tuple(exprs)
            if not len(exprs) > 0:
                raise ValueError('Require at least one expression to index')
            non_exprs = list(filter(lambda e: not isinstance(e, Expression), exprs))
            if non_exprs:
                raise TypeError(f"Index arguments must be expressions, found {non_exprs}")
    
            from hail.matrixtable import MatrixTable
    
            indices, aggregations = unify_all(*exprs)
            src = indices.source
    
            if src is None or len(indices.axes) == 0:
                # FIXME: this should be OK: table[m.global_index_into_table]
                raise ExpressionException('Cannot index with a scalar expression')
    
            is_interval = (
                len(exprs) == 1
                and len(self.key) > 0
                and isinstance(self.key[0].dtype, hl.tinterval)
                and exprs[0].dtype == self.key[0].dtype.point_type
            )
    
            if not types_match(list(self.key.values()), list(exprs)):
                if len(exprs) == 1 and isinstance(exprs[0], TupleExpression):
                    return self._index(*exprs[0], all_matches=all_matches)
    
                if len(exprs) == 1 and isinstance(exprs[0], StructExpression):
                    return self._index(*exprs[0].values(), all_matches=all_matches)
    
                if not is_interval:
                    raise TableIndexKeyError(self.key.dtype, exprs)
    
            uid = Env.get_uid()
    
            if all_matches and not is_interval:
                return self.collect_by_key(uid).index(*exprs)[uid]
    
            new_schema = self.row_value.dtype
            if all_matches:
                new_schema = hl.tarray(new_schema)
    
            if isinstance(src, Table):
                for e in exprs:
                    analyze('Table.index', e, src._row_indices)
    
                is_key = len(src.key) >= len(exprs) and all(
                    expr is key_field for expr, key_field in zip(exprs, src.key.values())
                )
    
                if not is_key:
                    uids = [Env.get_uid() for i in range(len(exprs))]
                    all_uids = uids[:]
                else:
                    all_uids = []
    
                def joiner(left):
                    if not is_key:
                        original_key = list(left.key)
                        left = Table(
                            ir.TableMapRows(
                                left.key_by()._tir,
                                ir.InsertFields(left._row._ir, list(zip(uids, [e._ir for e in exprs])), None),
                            )
                        ).key_by(*uids)
    
                        def rekey_f(t):
                            return t.key_by(*original_key)
    
                    else:
    
                        def rekey_f(t):
                            return t
    
                    if is_interval:
                        left = Table(ir.TableIntervalJoin(left._tir, self._tir, uid, all_matches))
                    else:
                        left = Table(ir.TableLeftJoinRightDistinct(left._tir, self._tir, uid))
                    return rekey_f(left)
    
                all_uids.append(uid)
                join_ir = ir.Join(ir.ProjectedTopLevelReference('row', uid, new_schema), all_uids, exprs, joiner)
                return construct_expr(join_ir, new_schema, indices, aggregations)
            elif isinstance(src, MatrixTable):
                for e in exprs:
                    analyze('Table.index', e, src._entry_indices)
    
                right = self
                # match on indices to determine join type
                if indices == src._entry_indices:
                    raise NotImplementedError('entry-based matrix joins')
                elif indices == src._row_indices:
                    is_subset_row_key = len(exprs) <= len(src.row_key) and all(
                        expr is key_field for expr, key_field in zip(exprs, src.row_key.values())
                    )
    
                    if not (is_subset_row_key or is_interval):
                        # foreign-key join
                        foreign_key_annotates = {Env.get_uid(): e for e in exprs}
    
                        # contains original key and join key
                        join_table = src.select_rows(**foreign_key_annotates).rows()
    
                        join_table = join_table.key_by(*foreign_key_annotates)
    
                        value_uid = Env.get_uid()
                        join_table = join_table.annotate(**{value_uid: right.index(join_table.key)})
    
                        #  FIXME: Maybe zip join here?
                        join_table = join_table.group_by(*src.row_key).aggregate(**{
                            uid: hl.dict(
                                hl.agg.collect(
                                    hl.tuple([
                                        hl.tuple([join_table[f] for f in foreign_key_annotates]),
                                        join_table[value_uid],
                                    ])
                                )
                            )
                        })
    
                        def joiner(left: MatrixTable):
                            mart = ir.MatrixAnnotateRowsTable(left._mir, join_table._tir, uid)
                            return MatrixTable(
                                ir.MatrixMapRows(
                                    mart,
                                    ir.InsertFields(
                                        ir.Ref('va', mart.typ.row_type),
                                        [
                                            (
                                                uid,
                                                ir.Apply(
                                                    'get',
                                                    join_table._row_type[uid].value_type,
                                                    ir.GetField(ir.GetField(ir.Ref('va', mart.typ.row_type), uid), uid),
                                                    ir.MakeTuple([e._ir for e in exprs]),
                                                ),
                                            )
                                        ],
                                        None,
                                    ),
                                )
                            )
    
                    else:
    
                        def joiner(left: MatrixTable):
                            return MatrixTable(ir.MatrixAnnotateRowsTable(left._mir, right._tir, uid, all_matches))
    
                    ast = ir.Join(ir.ProjectedTopLevelReference('va', uid, new_schema), [uid], exprs, joiner)
                    return construct_expr(ast, new_schema, indices, aggregations)
                elif indices == src._col_indices and not (is_interval and all_matches):
                    all_uids = [uid]
                    if len(exprs) == len(src.col_key) and all([exprs[i] is src.col_key[i] for i in range(len(exprs))]):
                        # key is already correct
                        def joiner(left):
                            return MatrixTable(ir.MatrixAnnotateColsTable(left._mir, right._tir, uid))
    
                    else:
                        index_uid = Env.get_uid()
                        uids = [Env.get_uid() for _ in exprs]
    
                        all_uids.append(index_uid)
                        all_uids.extend(uids)
    
                        def joiner(left: MatrixTable):
                            prev_key = list(src.col_key)
                            joined = (
                                src.annotate_cols(**dict(zip(uids, exprs)))
                                .add_col_index(index_uid)
                                .key_cols_by(*uids)
                                .cols()
                                .select(index_uid)
                                .join(self, 'inner')
                                .key_by(index_uid)
                                .drop(*uids)
                            )
                            result = MatrixTable(
                                ir.MatrixAnnotateColsTable(
                                    (left.add_col_index(index_uid).key_cols_by(index_uid)._mir), joined._tir, uid
                                )
                            ).key_cols_by(*prev_key)
                            return result
    
                    join_ir = ir.Join(ir.ProjectedTopLevelReference('sa', uid, new_schema), all_uids, exprs, joiner)
                    return construct_expr(join_ir, new_schema, indices, aggregations)
                else:
                    raise NotImplementedError()
            else:
                raise TypeError("Cannot join with expressions derived from '{}'".format(src.__class__))
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.index_globals)    def index_globals(self) -> 'StructExpression':
            """Return this table's global variables for use in another
            expression context.
    
            Examples
            --------
            >>> table_result = table2.annotate(C = table2.A * table1.index_globals().global_field_1)
    
            Returns
            -------
            ``.StructExpression``
            """
            return construct_expr(ir.TableGetGlobals(self._tir), self.globals.dtype)
    
    
    
        def _process_joins(self, *exprs) -> 'Table':
            return process_joins(self, exprs)
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.cache)    def cache(self) -> 'Table':
            """Persist this table in memory.
    
            Examples
            --------
            Persist the table in memory:
    
            >>> table = table.cache() # doctest: +SKIP
    
            Notes
            -----
    
            This method is an alias for ``persist("MEMORY_ONLY") <hail.Table.persist>``.
    
            Returns
            -------
            ``.Table``
                Cached table.
            """
            return self.persist('MEMORY_ONLY')
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.persist)    @typecheck_method(storage_level=storage_level)
        def persist(self, storage_level='MEMORY_AND_DISK') -> 'Table':
            """Persist this table in memory or on disk.
    
            Examples
            --------
            Persist the table to both memory and disk:
    
            >>> table = table.persist() # doctest: +SKIP
    
            Notes
            -----
    
            The ``.Table.persist`` and ``.Table.cache`` methods store the
            current table on disk or in memory temporarily to avoid redundant computation
            and improve the performance of Hail pipelines. This method is not a substitution
            for ``.Table.write``, which stores a permanent file.
    
            Most users should use the "MEMORY_AND_DISK" storage level. See the `Spark
            documentation
            <http://spark.apache.org/docs/latest/programming-guide.html#rdd-persistence>`__
            for a more in-depth discussion of persisting data.
    
            Parameters
            ----------
            storage_level : str
                Storage level.  One of: NONE, DISK_ONLY,
                DISK_ONLY_2, MEMORY_ONLY, MEMORY_ONLY_2, MEMORY_ONLY_SER,
                MEMORY_ONLY_SER_2, MEMORY_AND_DISK, MEMORY_AND_DISK_2,
                MEMORY_AND_DISK_SER, MEMORY_AND_DISK_SER_2, OFF_HEAP
    
            Returns
            -------
            ``.Table``
                Persisted table.
            """
            return Env.backend().persist(self)
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.unpersist)    def unpersist(self) -> 'Table':
            """
            Unpersists this table from memory/disk.
    
            Notes
            -----
            This function will have no effect on a table that was not previously
            persisted.
    
            Returns
            -------
            ``.Table``
                Unpersisted table.
            """
            return Env.backend().unpersist(self)
    
    
    
        @overload
        def collect(self) -> List[hl.Struct]: ...
    
        @overload
        def collect(self, _localize=False) -> ArrayExpression: ...
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.collect)    @typecheck_method(_localize=bool, _timed=bool)
        def collect(self, _localize=True, *, _timed=False):
            """Collect the rows of the table into a local list.
    
            Examples
            --------
            Collect a list of all `X` records:
    
            >>> all_xs = [row['X'] for row in table1.select(table1.X).collect()]
    
            Notes
            -----
            This method returns a list whose elements are of type ``.Struct``. Fields
            of these structs can be accessed similarly to fields on a table, using dot
            methods (``struct.foo``) or string indexing (``struct['foo']``).
    
            Warning
            -------
            Using this method can cause out of memory errors. Only collect small tables.
    
            Returns
            -------
            ``list`` of ``.Struct``
                List of rows.
            """
            if len(self.key) > 0:
                t = self.order_by(*self.key)
            else:
                t = self
            rows_ir = ir.GetField(ir.TableCollect(t._tir), 'rows')
            e = construct_expr(rows_ir, hl.tarray(t.row.dtype))
            if _localize:
                return Env.backend().execute(e._ir, timed=_timed)
            else:
                return e
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.describe)    def describe(self, handler=print, *, widget=False):
            """Print information about the fields in the table.
    
            Note
            ----
            The `widget` argument is **experimental**.
    
            Parameters
            ----------
            handler : Callable[[str], None]
                Handler function for returned string.
            widget : bool
                Create an interactive IPython widget.
            """
            if widget:
                from hail.experimental.interact import interact
    
                return interact(self)
    
            def format_type(typ):
                return typ.pretty(indent=4).lstrip()
    
            if len(self.globals) == 0:
                global_fields = '\n    None'
            else:
                global_fields = ''.join(
                    "\n    '{name}': {type} ".format(name=f, type=format_type(t)) for f, t in self.globals.dtype.items()
                )
    
            if len(self.row) == 0:
                row_fields = '\n    None'
            else:
                row_fields = ''.join(
                    "\n    '{name}': {type} ".format(name=f, type=format_type(t)) for f, t in self.row.dtype.items()
                )
    
            row_key = '[' + ', '.join("'{name}'".format(name=f) for f in self.key) + ']'
    
            s = (
                '----------------------------------------\n'
                'Global fields:{g}\n'
                '----------------------------------------\n'
                'Row fields:{r}\n'
                '----------------------------------------\n'
                'Key: {rk}\n'
                '----------------------------------------'.format(g=global_fields, rk=row_key, r=row_fields)
            )
            handler(s)
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.add_index)    @typecheck_method(name=str)
        def add_index(self, name='idx') -> 'Table':
            """Add the integer index of each row as a new row field.
    
            Examples
            --------
    
            >>> table_result = table1.add_index()
            >>> table_result.show()  # doctest: +SKIP_OUTPUT_CHECK
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |   idx |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | int64 |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
            |     1 |    65 | M   |     5 |     4 |     2 |    50 |     5 |     0 |
            |     2 |    72 | M   |     6 |     3 |     2 |    61 |     1 |     1 |
            |     3 |    70 | F   |     7 |     3 |    10 |    81 |    -5 |     2 |
            |     4 |    60 | F   |     8 |     2 |    11 |    90 |   -10 |     3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    
            Notes
            -----
    
            This method returns a table with a new field whose name is given by
            the `name` parameter, with type :py``.tint64``. The value of this field
            is the integer index of each row, starting from 0. Methods that respect
            ordering (like ``.Table.take`` or ``.Table.export``) will
            return rows in order.
    
            This method is also helpful for creating a unique integer index for
            rows of a table so that more complex types can be encoded as a simple
            number for performance reasons.
    
            Parameters
            ----------
            name : str
                Name of index field.
    
            Returns
            -------
            ``.Table``
                Table with a new index field.
            """
    
            return self.annotate(**{name: hl.scan.count()})
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.union)    @typecheck_method(tables=table_type, unify=bool)
        def union(self, *tables, unify: bool = False) -> 'Table':
            """Union the rows of multiple tables.
    
            Examples
            --------
    
            Take the union of rows from two tables:
    
            >>> union_table = table1.union(other_table)
    
            Notes
            -----
            If a row appears in more than one table identically, it is duplicated
            in the result. All tables must have the same key names and types. They
            must also have the same row types, unless the `unify` parameter is
            ``True``, in which case a field appearing in any table will be included
            in the result, with missing values for tables that do not contain the
            field. If a field appears in multiple tables with incompatible types,
            like arrays and strings, then an error will be raised.
    
            Parameters
            ----------
            tables : varargs of ``.Table``
                Tables to union.
            unify : ``bool``
                Attempt to unify table field.
    
            Returns
            -------
            ``.Table``
                Table with all rows from each component table.
            """
            left_key = self.key.dtype
            for (
                i,
                ht,
            ) in enumerate(tables):
                if left_key != ht.key.dtype:
                    raise ValueError(
                        f"'union': table {i} has a different key.  Expected:  {left_key}\n  Table {i}: {ht.key.dtype}"
                    )
    
                if not (unify or ht.row.dtype == self.row.dtype):
                    raise ValueError(
                        f"'union': table {i} has a different row type.\n"
                        f"  Expected:  {self.row.dtype}\n"
                        f"  Table {i}: {ht.row.dtype}\n"
                        f"  If the tables have the same fields in different orders, or some\n"
                        f"    common and some unique fields, then the 'unify' parameter may be\n"
                        f"    able to coerce the tables to a common type."
                    )
            all_tables = [self]
            all_tables.extend(tables)
    
            if unify and not len(set(ht.row_value.dtype for ht in all_tables)) == 1:
                discovered = collections.defaultdict(dict)
                for i, ht in enumerate(all_tables):
                    for field_name in ht.row_value:
                        discovered[field_name][i] = ht[field_name]
                all_fields = [{} for _ in all_tables]
                for field_name, expr_dict in discovered.items():
                    *unified, can_unify = hl.expr.expressions.unify_exprs(*expr_dict.values())
                    if not can_unify:
                        raise ValueError(
                            f"cannot unify field {field_name!r}: found fields of types "
                            f"{[str(t) for t in {e.dtype for e in expr_dict.values()}]}"
                        )
                    unified_map = dict(zip(expr_dict.keys(), unified))
                    default = hl.missing(unified[0].dtype)
                    for i in range(len(all_tables)):
                        all_fields[i][field_name] = unified_map.get(i, default)
    
                for i, t in enumerate(all_tables):
                    all_tables[i] = t.select(**all_fields[i])
    
            return Table(ir.TableUnion([table._tir for table in all_tables]))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.take)    @typecheck_method(n=int, _localize=bool)
        def take(self, n, _localize=True):
            """Collect the first `n` rows of the table into a local list.
    
            Examples
            --------
            Take the first three rows:
    
            >>> first3 = table1.take(3)
            >>> first3
            [Struct(ID=1, HT=65, SEX='M', X=5, Z=4, C1=2, C2=50, C3=5),
             Struct(ID=2, HT=72, SEX='M', X=6, Z=3, C1=2, C2=61, C3=1),
             Struct(ID=3, HT=70, SEX='F', X=7, Z=3, C1=10, C2=81, C3=-5)]
    
            Notes
            -----
    
            This method does not need to look at all the data in the table, and
            allows for fast queries of the start of the table.
    
            This method is equivalent to ``.Table.head`` followed by
            ``.Table.collect``.
    
            Parameters
            ----------
            n : int
                Number of rows to take.
    
            Returns
            -------
            ``list`` of ``.Struct``
                List of row structs.
            """
    
            return self.head(n).collect(_localize)
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.head)    @typecheck_method(n=int)
        def head(self, n) -> 'Table':
            """Subset table to first `n` rows.
    
            Examples
            --------
            Subset to the first three rows:
    
            >>> ht = hl.import_table('data/kt_example1.tsv', impute=True)
            >>> ht.head(3).show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
            Notes
            -----
    
            The number of partitions in the new table is equal to the number of
            partitions containing the first `n` rows.
    
            Parameters
            ----------
            n : int
                Number of rows to include.
    
            Returns
            -------
            ``.Table``
                Table limited to the first `n` rows.
            """
    
            return Table(ir.TableHead(self._tir, n))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.tail)    @typecheck_method(n=int)
        def tail(self, n) -> 'Table':
            """Subset table to last `n` rows.
    
            Examples
            --------
            Subset to the last three rows:
    
            >>> table_result = table1.tail(3)
            >>> table_result.count()
            3
    
            Notes
            -----
    
            The number of partitions in the new table is equal to the number of
            partitions containing the last `n` rows.
    
            Parameters
            ----------
            n : int
                Number of rows to include.
    
            Returns
            -------
            ``.Table``
                Table including the last `n` rows.
            """
    
            return Table(ir.TableTail(self._tir, n))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.sample)    @typecheck_method(p=numeric, seed=nullable(int))
        def sample(self, p, seed=None) -> 'Table':
            """Downsample the table by keeping each row with probability ``p``.
    
            Examples
            --------
    
            Downsample the table to approximately 1% of its rows.
    
            >>> table1.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            >>> small_table1 = table1.sample(0.75, seed=0)
            >>> small_table1.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            >>> small_table1 = table1.sample(0.25, seed=4)
            >>> small_table1.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
            Parameters
            ----------
            p : ``float``
                Probability of keeping each row.
            seed : ``int``
                Random seed.
    
            Returns
            -------
            ``.Table``
                Table with approximately ``p * n_rows`` rows.
            """
    
            if not 0 <= p <= 1:
                raise ValueError("Requires 'p' in [0,1]. Found p={}".format(p))
    
            return self.filter(hl.rand_bool(p, seed))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.repartition)    @typecheck_method(n=int, shuffle=bool)
        def repartition(self, n, shuffle=True) -> 'Table':
            """Change the number of partitions.
    
            Examples
            --------
    
            Repartition to 500 partitions:
    
            >>> table_result = table1.repartition(500)
    
            Notes
            -----
    
            Check the current number of partitions with ``.n_partitions``.
    
            The data in a dataset is divided into chunks called partitions, which
            may be stored together or across a network, so that each partition may
            be read and processed in parallel by available cores. When a table with
            ``M`` rows is first imported, each of the ``k`` partitions will
            contain about ``M/k`` of the rows. Since each partition has some
            computational overhead, decreasing the number of partitions can improve
            performance after significant filtering. Since it's recommended to have
            at least 2 - 4 partitions per core, increasing the number of partitions
            can allow one to take advantage of more cores. Partitions are a core
            concept of distributed computation in Spark, see `their documentation
            <http://spark.apache.org/docs/latest/programming-guide.html#resilient-distributed-datasets-rdds>`__
            for details.
    
            When ``shuffle=True``, Hail does a full shuffle of the data
            and creates equal sized partitions.  When ``shuffle=False``,
            Hail combines existing partitions to avoid a full shuffle.
            These algorithms correspond to the `repartition` and
            `coalesce` commands in Spark, respectively. In particular,
            when ``shuffle=False``, ``n_partitions`` cannot exceed current
            number of partitions.
    
            Parameters
            ----------
            n : int
                Desired number of partitions.
            shuffle : bool
                If ``True``, use full shuffle to repartition.
    
            Returns
            -------
            ``.Table``
                Repartitioned table.
            """
            if hl.current_backend().requires_lowering:
                tmp = hl.utils.new_temp_file()
    
                if len(self.key) == 0:
                    uid = Env.get_uid()
                    tmp2 = hl.utils.new_temp_file()
                    self.checkpoint(tmp2)
                    ht = hl.read_table(tmp2).add_index(uid).key_by(uid)
                    ht.checkpoint(tmp)
                    return hl.read_table(tmp, _n_partitions=n).key_by().drop(uid)
                else:
                    # checkpoint rather than write to use fast codec
                    self.checkpoint(tmp)
                    return hl.read_table(tmp, _n_partitions=n)
    
            return Table(
                ir.TableRepartition(
                    self._tir, n, ir.RepartitionStrategy.SHUFFLE if shuffle else ir.RepartitionStrategy.COALESCE
                )
            )
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.naive_coalesce)    @typecheck_method(max_partitions=int)
        def naive_coalesce(self, max_partitions: int) -> 'Table':
            """Naively decrease the number of partitions.
    
            Example
            -------
            Naively repartition to 10 partitions:
    
            >>> table_result = table1.naive_coalesce(10)
    
            Warning
            -------
            ``.naive_coalesce`` simply combines adjacent partitions to achieve
            the desired number. It does not attempt to rebalance, unlike
            ``.repartition``, so it can produce a heavily unbalanced dataset. An
            unbalanced dataset can be inefficient to operate on because the work is
            not evenly distributed across partitions.
    
            Parameters
            ----------
            max_partitions : int
                Desired number of partitions. If the current number of partitions is
                less than or equal to `max_partitions`, do nothing.
    
            Returns
            -------
            ``.Table``
                Table with at most `max_partitions` partitions.
            """
            return Table(ir.TableRepartition(self._tir, max_partitions, ir.RepartitionStrategy.NAIVE_COALESCE))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.semi_join)    @typecheck_method(other=table_type)
        def semi_join(self, other: 'Table') -> 'Table':
            """Filters the table to rows whose key appears in `other`.
    
            Parameters
            ----------
            other : ``.Table``
                Table with compatible key field(s).
    
            Returns
            -------
            ``.Table``
    
            Notes
            -----
            The key type of the table must match the key type of `other`.
    
            This method does not change the schema of the table; it is a method of
            filtering the table to keys present in another table.
    
            To discard keys present in `other`, use ``.anti_join``.
    
            Examples
            --------
            >>> table1.show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
            |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
            |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            >>> small_table2 = table2.head(2)
            >>> small_table2.show()
            +-------+-------+-------+
            |    ID |     A | B     |
            +-------+-------+-------+
            | int32 | int32 | str   |
            +-------+-------+-------+
            |     1 |    65 | "cat" |
            |     2 |    72 | "dog" |
            +-------+-------+-------+
            >>> table1.semi_join(small_table2).show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
            It may be expensive to key the left-side table by the right-side key.
            In this case, it is possible to implement a semi-join using a non-key
            field as follows:
    
            >>> table1.filter(hl.is_defined(small_table2.index(table1['ID']))).show()
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
            |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
            |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
            +-------+-------+-----+-------+-------+-------+-------+-------+
    
            See Also
            --------
            ``.anti_join``
            """
            if len(other.key) == 0:
                raise ValueError('semi_join: cannot join with a table with no key')
            if len(other.key) > len(self.key) or any(
                t[0].dtype != t[1].dtype for t in zip(self.key.values(), other.key.values())
            ):
                raise ValueError(
                    'semi_join: cannot join: table must have a key of the same type(s) and be the same length or shorter:'
                    f'\n   Left key: {", ".join(str(x.dtype) for x in self.key.values())}'
                    f'\n  Right key: {", ".join(str(x.dtype) for x in other.key.values())}'
                )
    
            return self.filter(hl.is_defined(other.index(*(self.key[i] for i in range(len(other.key))))))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.anti_join)    @typecheck_method(other=table_type)
        def anti_join(self, other: 'Table') -> 'Table':
            """Filters the table to rows whose key does not appear in `other`.
    
            Parameters
            ----------
            other : ``.Table``
                Table with compatible key field(s).
    
            Returns
            -------
            ``.Table``
    
            Notes
            -----
            The key type of the table must match the key type of `other`.
    
            This method does not change the schema of the table; it is a method of
            filtering the table to keys not present in another table.
    
            To restrict to keys present in `other`, use ``.semi_join``.
    
            Examples
            --------
            >>> table_result = table1.anti_join(table2)
    
            It may be expensive to key the left-side table by the right-side key.
            In this case, it is possible to implement an anti-join using a non-key
            field as follows:
    
            >>> table_result = table1.filter(hl.is_missing(table2.index(table1['ID'])))
    
            See Also
            --------
            ``.semi_join``, ``.filter``
            """
            if len(other.key) == 0:
                raise ValueError('anti_join: cannot join with a table with no key')
            if len(other.key) > len(self.key) or any(
                t[0].dtype != t[1].dtype for t in zip(self.key.values(), other.key.values())
            ):
                raise ValueError(
                    'anti_join: cannot join: table must have a key of the same type(s) and be the same length or shorter:'
                    f'\n   Left key: {", ".join(str(x.dtype) for x in self.key.values())}'
                    f'\n  Right key: {", ".join(str(x.dtype) for x in other.key.values())}'
                )
    
            return self.filter(hl.is_missing(other.index(*(self.key[i] for i in range(len(other.key))))))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.join)    @typecheck_method(
            right=table_type, how=enumeration('inner', 'outer', 'left', 'right'), _mangle=anyfunc, _join_key=nullable(int)
        )
        def join(
            self,
            right: 'Table',
            how='inner',
            _mangle: Callable[[str, int], str] = lambda s, i: f'{s}_{i}',
            _join_key: Optional[int] = None,
        ) -> 'Table':
            """Join two tables together.
    
            Examples
            --------
            Join `table1` to `table2` to produce `table_joined`:
    
            >>> table_joined = table1.key_by('ID').join(table2.key_by('ID'))
    
            Notes
            -----
            Tables are joined at rows whose key fields have equal values. Missing values never match.
            The inclusion of a row with no match in the opposite table depends on the
            join type:
    
            - **inner** -- Only rows with a matching key in the opposite table are included
              in the resulting table.
            - **left** -- All rows from the left table are included in the resulting table.
              If a row in the left table has no match in the right table, then the fields
              derived from the right table will be missing.
            - **right** -- All rows from the right table are included in the resulting table.
              If a row in the right table has no match in the left table, then the fields
              derived from the left table will be missing.
            - **outer** -- All rows are included in the resulting table. If a row in the right
              table has no match in the left table, then the fields derived from the left
              table will be missing. If a row in the right table has no match in the left table,
              then the fields derived from the left table will be missing.
    
            Both tables must have the same number of keys and the corresponding
            types of each key must be the same (order matters), but the key names
            can be different. For example, if `table1` is keyed by fields ``['a',
            'b']``, both of type ``int32``, and `table2` is keyed by fields ``['c',
            'd']``, both of type ``int32``, then the two tables can be joined (their
            rows will be joined where ``table1.a == table2.c`` and ``table1.b ==
            table2.d``).
    
            The key fields and order from the left table are preserved,
            while the key fields from the right table are not present in
            the result.
    
            Note
            ----
            These join methods implement a traditional `Cartesian product
            <https://en.wikipedia.org/wiki/Cartesian_product>`__ join, and
            the number of records in the resulting table can be larger than
            the number of records on the left or right if duplicate keys are
            present.
    
            Parameters
            ----------
            right : ``.Table``
                Table to join.
            how : ``str``
                Join type. One of "inner", "left", "right", "outer"
    
            Returns
            -------
            ``.Table``
                Joined table.
    
            """
            if _join_key is None:
                _join_key = max(len(self.key), len(right.key))
    
            left_key_types = list(self.key.dtype.values())[:_join_key]
            right_key_types = list(right.key.dtype.values())[:_join_key]
            if not left_key_types == right_key_types:
                raise ValueError(
                    f"'join': key mismatch:\n  "
                    f"  left:  [{', '.join(str(t) for t in left_key_types)}]\n  "
                    f"  right: [{', '.join(str(t) for t in right_key_types)}]"
                )
            left_fields = set(self._fields)
            right_fields = set(right._fields) - set(right.key)
    
            renames, _ = deduplicate(right_fields, max_attempts=100, already_used=left_fields)
    
            if renames:
                renames = dict(renames)
                right = right.rename(renames)
                info(
                    'Table.join: renamed the following fields on the right to avoid name conflicts:'
                    + ''.join(f'\n    {k!r} -> {v!r}' for k, v in renames.items())
                )
    
            return Table(ir.TableJoin(self._tir, right._tir, how, _join_key))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.all)    @typecheck_method(expr=BooleanExpression)
        def all(self, expr):
            """Evaluate whether a boolean expression is true for all rows.
    
            Examples
            --------
            Test whether `C1` is greater than 5 in all rows of the table:
    
            >>> if table1.all(table1.C1 == 5):
            ...     print("All rows have C1 equal 5.")
    
            Parameters
            ----------
            expr : ``.BooleanExpression``
                Expression to test.
    
            Returns
            -------
            ``bool``
            """
            return self.aggregate(hl.agg.all(expr))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.any)    @typecheck_method(expr=BooleanExpression)
        def any(self, expr):
            """Evaluate whether a Boolean expression is true for at least one row.
    
            Examples
            --------
    
            Test whether `C1` is equal to 5 any row in any row of the table:
    
            >>> if table1.any(table1.C1 == 5):
            ...     print("At least one row has C1 equal 5.")
    
            Parameters
            ----------
            expr : ``.BooleanExpression``
                Boolean expression.
    
            Returns
            -------
            ``bool``
                ``True`` if the predicate evaluated for ``True`` for any row, otherwise ``False``.
            """
            return self.aggregate(hl.agg.any(expr))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.rename)    @typecheck_method(mapping=dictof(str, str))
        def rename(self, mapping) -> 'Table':
            """Rename fields of the table.
    
            Examples
            --------
            Rename `C1` to `col1` and `C2` to `col2`:
    
            >>> table_result = table1.rename({'C1' : 'col1', 'C2' : 'col2'})
    
            Parameters
            ----------
            mapping : ``dict`` of ``str``, ``str``
                Mapping from old field names to new field names.
    
            Notes
            -----
            Any field that does not appear as a key in `mapping` will not be
            renamed.
    
            Returns
            -------
            ``.Table``
                Table with renamed fields.
            """
            seen = {}
    
            row_map = {}
            global_map = {}
    
            for k, v in mapping.items():
                if v in seen:
                    raise ValueError(
                        "Cannot rename two fields to the same name: attempted to rename {} and {} both to {}".format(
                            repr(seen[v]), repr(k), repr(v)
                        )
                    )
                if v in self._fields and v not in mapping:
                    raise ValueError("Cannot rename {} to {}: field already exists.".format(repr(k), repr(v)))
                seen[v] = k
                if self[k]._indices == self._row_indices:
                    row_map[k] = v
                else:
                    assert self[k]._indices == self._global_indices
                    global_map[k] = v
    
            stray = set(mapping.keys()) - set(seen.values())
            if stray:
                raise ValueError(f"found rename rules for fields not present in table: {list(stray)}")
    
            return Table(ir.TableRename(self._tir, row_map, global_map))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.expand_types)    def expand_types(self) -> 'Table':
            """Expand complex types into structs and arrays.
    
            Examples
            --------
    
            >>> table_result = table1.expand_types()
    
            Notes
            -----
            Expands the following types: ``.tlocus``, ``.tinterval``,
            ``.tset``, ``.tdict``, ``.ttuple``.
    
            The only types that will remain after this method are:
            :py``.tbool``, :py``.tint32``, :py``.tint64``,
            :py``.tfloat64``, :py``.tfloat32``, ``.tarray``,
            ``.tstruct``.
    
            Note, expand_types always returns an unkeyed table.
    
            Returns
            -------
            ``.Table``
                Expanded table.
            """
    
            t = self
            if len(t.key) > 0:
                t = t.order_by(*t.key)
    
            def _expand(e):
                if isinstance(e, (CollectionExpression, DictExpression)):
                    return hl.map(lambda x: _expand(x), hl.array(e))
                elif isinstance(e, StructExpression):
                    return hl.struct(**{k: _expand(v) for (k, v) in e.items()})
                elif isinstance(e, TupleExpression):
                    return hl.struct(**{f'_{i}': x for (i, x) in enumerate(e)})
                elif isinstance(e, IntervalExpression):
                    return hl.struct(start=e.start, end=e.end, includesStart=e.includes_start, includesEnd=e.includes_end)
                elif isinstance(e, LocusExpression):
                    return hl.struct(contig=e.contig, position=e.position)
                elif isinstance(e, CallExpression):
                    return hl.struct(alleles=hl.map(lambda i: e[i], hl.range(0, e.ploidy)), phased=e.phased)
                elif isinstance(e, NDArrayExpression):
                    return hl.struct(shape=e.shape, data=_expand(e._data_array()))
                else:
                    assert isinstance(e, (NumericExpression, BooleanExpression, StringExpression))
                    return e
    
            t = t.select(**_expand(t.row))
            t = t.select_globals(**_expand(t.globals))
            return t
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.flatten)    def flatten(self) -> 'Table':
            """Flatten nested structs.
    
            Examples
            --------
            Flatten table:
    
            >>> table_result = table1.flatten()
    
            Notes
            -----
            Consider a table with signature
    
            .. code-block:: text
    
                a: struct{
                    p: int32,
                    q: str
                },
                b: int32,
                c: struct{
                    x: str,
                    y: array<struct{
                        y: str,
                        z: str
                    }>
                }
    
            and key ``a``.  The result of flatten is
    
            .. code-block:: text
    
                a.p: int32
                a.q: str
                b: int32
                c.x: str
                c.y: array<struct{
                    y: str,
                    z: str
                }>
    
            with key ``a.p, a.q``.
    
            Note, structures inside collections like arrays or sets will not be
            flattened.
    
            Note, the result of flatten is always unkeyed.
    
            Warning
            -------
            Flattening a table will produces fields that cannot be referenced using
            the ``table.<field>`` syntax, e.g. "a.b". Reference these fields using
            square bracket lookups: ``table['a.b']``.
    
            Returns
            -------
            ``.Table``
                Table with a flat schema (no struct fields).
            """
            # unkey but preserve order
            t = self.order_by(*self.key)
            t = Table(ir.TableMapRows(t._tir, t.row.flatten()._ir))
            return t
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.order_by)    @typecheck_method(exprs=oneof(str, Expression, Ascending, Descending))
        def order_by(self, *exprs) -> 'Table':
            """Sort by the specified fields, defaulting to ascending order. Will unkey the table if it is keyed.
    
            Examples
            --------
            Let's assume we have a field called `HT` in our table.
    
            By default, ascending order is used:
    
            >>> sorted_table = table1.order_by(table1.HT)
    
            >>> sorted_table = table1.order_by('HT')
    
            You can sort in ascending order explicitly:
    
            >>> sorted_table = table1.order_by(hl.asc(table1.HT))
    
            >>> sorted_table = table1.order_by(hl.asc('HT'))
    
            Tables can be sorted by field descending order as well:
    
            >>> sorted_table = table1.order_by(hl.desc(table1.HT))
    
            >>> sorted_table = table1.order_by(hl.desc('HT'))
    
            Tables can also be sorted on multiple fields:
    
            >>> sorted_table = table1.order_by(hl.desc('HT'), hl.asc('SEX'))
    
            Notes
            -----
            Missing values are sorted after non-missing values. When multiple
            fields are passed, the table will be sorted first by the first
            argument, then the second, etc.
    
            Note
            ----
            This method unkeys the table.
    
            Parameters
            ----------
            exprs : varargs of ``~.asc``, ``.desc``, ``.Expression``, or ``str``
                Fields to sort by.
    
            Returns
            -------
            ``.Table``
                Table sorted by the given fields.
            """
            lifted_exprs = []
            for e in exprs:
                _e = e
                sort_type = 'A'
                if isinstance(e, Ascending):
                    _e = e.col
                elif isinstance(e, Descending):
                    _e = e.col
                    sort_type = 'D'
    
                if isinstance(_e, str):
                    expr = self[_e]
                else:
                    expr = _e
                lifted_exprs.append((expr, sort_type))
    
            sort_fields = []
            complex_exprs = {}
    
            for e, sort_type in lifted_exprs:
                if e._indices.source is not self:
                    if e._indices.source is None:
                        raise ValueError("Sort fields must be fields of the callee Table, found scalar expression")
                    else:
                        raise ValueError(
                            f"Sort fields must be fields of the callee Table, found field of {e._indices.source}"
                        )
                elif e._indices != self._row_indices:
                    raise ValueError("Sort fields must be row-indexed, found global sort expression")
                else:
                    field_name = self._fields_inverse.get(e)
                    if field_name is None:
                        field_name = Env.get_uid()
                        complex_exprs[field_name] = e
                    sort_fields.append((field_name, sort_type))
    
            t = self
            if complex_exprs:
                t = t.annotate(**complex_exprs)
            t = Table(ir.TableOrderBy(t._tir, sort_fields))
            if complex_exprs:
                t = t.drop(*complex_exprs.keys())
            return t
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.explode)    @typecheck_method(field=oneof(str, Expression), name=nullable(str))
        def explode(self, field, name=None) -> 'Table':
            """Explode rows along a field of type array or set, copying the entire row for each element.
    
            Examples
            --------
            `people_table` is a ``.Table`` with three fields: `Name`, `Age`
            and `Children`.
    
            >>> people_table.show()
            +------------+-------+--------------------------+
            | Name       |   Age | Children                 |
            +------------+-------+--------------------------+
            | str        | int32 | array<str>               |
            +------------+-------+--------------------------+
            | "Alice"    |    34 | ["Dave","Ernie","Frank"] |
            | "Bob"      |    51 | ["Gaby","Helen"]         |
            | "Caroline" |    10 | []                       |
            +------------+-------+--------------------------+
    
            ``.Table.explode`` can be used to produce a distinct row for each
            element in the `Children` field:
    
            >>> exploded = people_table.explode('Children')
            >>> exploded.show() # doctest: +SKIP_OUTPUT_CHECK
            +---------+-------+----------+
            | Name    |   Age | Children |
            +---------+-------+----------+
            | str     | int32 | str      |
            +---------+-------+----------+
            | "Alice" |    34 | "Dave"   |
            | "Alice" |    34 | "Ernie"  |
            | "Alice" |    34 | "Frank"  |
            | "Bob"   |    51 | "Gaby"   |
            | "Bob"   |    51 | "Helen"  |
            +---------+-------+----------+
    
            The `name` parameter can be used to produce more appropriate field
            names:
    
            >>> exploded = people_table.explode('Children', name='Child')
            >>> exploded.show() # doctest: +SKIP_OUTPUT_CHECK
            +---------+-------+---------+
            | Name    |   Age | Child   |
            +---------+-------+---------+
            | str     | int32 | str     |
            +---------+-------+---------+
            | "Alice" |    34 | "Dave"  |
            | "Alice" |    34 | "Ernie" |
            | "Alice" |    34 | "Frank" |
            | "Bob"   |    51 | "Gaby"  |
            | "Bob"   |    51 | "Helen" |
            +---------+-------+---------+
    
            Notes
            -----
            Each row is copied for each element of `field`. The explode operation
            unpacks the elements in a field of type ``array`` or ``set`` into its
            own row. If an empty ``array`` or ``set`` is exploded, the entire row is
            removed from the table. In the example above, notice that the name
            "Caroline" is not found in the exploded table.
    
            Missing arrays or sets are treated as empty.
    
            Currently, the `name` argument may not be used if `field` is not a
            top-level field of the table (e.g. `name` may be used with ``ht.foo``
            but not ``ht.foo.bar``).
    
            Parameters
            ----------
            field : ``str`` or ``.Expression``
                Top-level field name or expression.
            name : ``str`` or None
                If not `None`, rename the exploded field to `name`.
    
            Returns
            -------
            ``.Table``
            """
            if isinstance(field, str):
                if field not in self._fields:
                    raise KeyError("Table has no field '{}'".format(field))
                elif self._fields[field]._indices != self._row_indices:
                    raise ExpressionException(
                        "Method 'explode' expects a field indexed by row, found axes '{}'".format(
                            self._fields[field]._indices.axes
                        )
                    )
                root = [field]
                field = self._fields[field]
            else:
                analyze('Table.explode', field, self._row_indices, set(self._fields.keys()))
                if not field._ir.is_nested_field:
                    raise ExpressionException("method 'explode' requires a field or subfield, not a complex expression")
                nested = field._ir
                root = []
                while isinstance(nested, ir.GetField):
                    root.append(nested.name)
                    nested = nested.o
                root = root[::-1]
    
            if not isinstance(field.dtype, (tarray, tset)):
                raise ValueError(f"method 'explode' expects array or set, found: {field.dtype}")
    
            for k in self.key.values():
                if k is field:
                    raise ValueError("method 'explode' cannot explode a key field")
    
            t = Table(ir.TableExplode(self._tir, root))
            if name is not None:
                if len(root) > 1:
                    raise ValueError("'Table.explode' does not support the 'name' argument when exploding nested fields")
                t = t.rename({root[0]: name})
            return t
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.to_matrix_table)    @typecheck_method(
            row_key=sequenceof(str),
            col_key=sequenceof(str),
            row_fields=sequenceof(str),
            col_fields=sequenceof(str),
            n_partitions=nullable(int),
        )
        def to_matrix_table(self, row_key, col_key, row_fields=[], col_fields=[], n_partitions=None) -> 'hl.MatrixTable':
            """Construct a matrix table from a table in coordinate representation.
    
            Examples
            --------
            Import a coordinate-representation table from disk:
    
            >>> coord_ht = hl.import_table('data/coordinate_matrix.tsv', impute=True)
            >>> coord_ht.show()
            +---------+---------+----------+
            | row_idx | col_idx |        x |
            +---------+---------+----------+
            |   int32 |   int32 |  float64 |
            +---------+---------+----------+
            |       1 |       1 | 2.50e-01 |
            |       1 |       2 | 3.30e-01 |
            |       2 |       1 | 1.10e-01 |
            |       3 |       1 | 1.00e+00 |
            |       3 |       2 | 0.00e+00 |
            +---------+---------+----------+
    
            Convert to a matrix table and show:
    
            >>> dense_mt = coord_ht.to_matrix_table(row_key=['row_idx'], col_key=['col_idx'])
            >>> dense_mt.show()
            +---------+----------+----------+
            | row_idx |      1.x |      2.x |
            +---------+----------+----------+
            |   int32 |  float64 |  float64 |
            +---------+----------+----------+
            |       1 | 2.50e-01 | 3.30e-01 |
            |       2 | 1.10e-01 |       NA |
            |       3 | 1.00e+00 | 0.00e+00 |
            +---------+----------+----------+
    
            Notes
            -----
            Any row fields in the table that do not appear in one of the arguments
            to this method are assumed to be entry fields of the resulting matrix
            table.
    
            Parameters
            ----------
            row_key : Sequence[str]
                Fields to be used as row key.
            col_key : Sequence[str]
                Fields to be used as column key.
            row_fields : Sequence[str]
                Fields to be stored once per row.
            col_fields : Sequence[str]
                Fields to be stored once per column.
            n_partitions : int or None
                Number of partitions.
    
            Returns
            -------
            ``.MatrixTable``
            """
            all_fields = list(itertools.chain(row_key, col_key, row_fields, col_fields))
            c = collections.Counter(all_fields)
            row_field_set = set(self.row)
            for k, v in c.items():
                if k not in row_field_set:
                    raise ValueError(f"'to_matrix_table': field {k!r} is not a row field")
                if v > 1:
                    raise ValueError(f"'to_matrix_table': field {k!r} appeared in {v} field groups")
    
            if len(row_key) == 0:
                raise ValueError("'to_matrix_table': require at least one row key field")
            if len(col_key) == 0:
                raise ValueError("'to_matrix_table': require at least one col key field")
    
            ht = self.key_by()
    
            non_entry_fields = set(itertools.chain(row_key, col_key, row_fields, col_fields))
            entry_fields = [x for x in ht.row if x not in non_entry_fields]
    
            if not entry_fields:
                raise ValueError(
                    "'Table.to_matrix_table': no fields remain as entry fields:\n"
                    "  all table fields found in one of 'row_key', 'col_key', 'row_fields', 'col_fields'"
                )
    
            col_data = hl.rbind(
                hl.array(
                    ht.aggregate(
                        hl.agg.group_by(ht.row.select(*col_key), hl.agg.take(ht.row.select(*col_fields), 1)[0]),
                        _localize=False,
                    )
                ),
                lambda data: hl.struct(
                    data=data, key_to_index=hl.dict(hl.range(0, hl.len(data)).map(lambda i: (data[i][0], i)))
                ),
            )
    
            col_data_uid = Env.get_uid()
            ht = ht.drop(*col_fields)
            ht = ht.annotate_globals(**{col_data_uid: col_data})
    
            entries_uid = Env.get_uid()
            ht = (
                ht.group_by(*row_key)
                .partition_hint(n_partitions)
                # FIXME: should be agg._prev_nonnull https://github.com/hail-is/hail/issues/5345
                .aggregate(
                    **{x: hl.agg.take(ht[x], 1)[0] for x in row_fields},
                    **{
                        entries_uid: hl.rbind(
                            hl.dict(
                                hl.agg.collect((
                                    ht[col_data_uid]['key_to_index'][ht.row.select(*col_key)],
                                    ht.row.select(*entry_fields),
                                ))
                            ),
                            lambda entry_dict: hl.range(0, hl.len(ht[col_data_uid]['key_to_index'])).map(
                                lambda i: entry_dict.get(i)
                            ),
                        )
                    },
                )
            )
            ht = ht.annotate_globals(**{
                col_data_uid: hl.array(ht[col_data_uid]['data'].map(lambda elt: hl.struct(**elt[0], **elt[1])))
            })
            return ht._unlocalize_entries(entries_uid, col_data_uid, col_key)
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.to_matrix_table_row_major)    @typecheck_method(columns=sequenceof(str), entry_field_name=nullable(str), col_field_name=str)
        def to_matrix_table_row_major(self, columns, entry_field_name=None, col_field_name='col'):
            """Construct a matrix table from a table in row major representation. Each element in `columns`
            is a field that will become an entry field in the matrix table. Fields omitted from `columns` become row
            fields. If `columns` are structs, then the matrix table will have the entry fields of those structs. Otherwise,
            the matrix table will have one entry field named `entry_field_name` whose values come from the values
            of the `columns` fields. The matrix table is column indexed by `col_field_name`.
    
            If you find yourself using this method after ``.import_table``,
            consider instead using ``.import_matrix_table``.
    
            Examples
            --------
    
            Convert a table of RNA expression samples to a ``.MatrixTable``:
    
            >>> t = hl.import_table('data/rna_expression.tsv', impute=True)
            >>> t = t.key_by('gene')
            >>> t.show()
            +---------+---------+---------+----------+-----------+-----------+-----------+
            | gene    | lung001 | lung002 | heart001 | muscle001 | muscle002 | muscle003 |
            +---------+---------+---------+----------+-----------+-----------+-----------+
            | str     |   int32 |   int32 |    int32 |     int32 |     int32 |     int32 |
            +---------+---------+---------+----------+-----------+-----------+-----------+
            | "LD4"   |       1 |       2 |        0 |         2 |         1 |         1 |
            | "SCN1A" |       2 |       1 |        1 |         0 |         0 |         0 |
            | "TITIN" |       3 |       0 |        0 |         1 |         2 |         1 |
            +---------+---------+---------+----------+-----------+-----------+-----------+
            >>> mt = t.to_matrix_table_row_major(
            ...          columns=['lung001', 'lung002', 'heart001',
            ...                   'muscle001', 'muscle002', 'muscle003'],
            ...          entry_field_name='expression',
            ...          col_field_name='sample')
            >>> mt.describe()
            ----------------------------------------
            Global fields:
                None
            ----------------------------------------
            Column fields:
                'sample': str
            ----------------------------------------
            Row fields:
                'gene': str
            ----------------------------------------
            Entry fields:
                'expression': int32
            ----------------------------------------
            Column key: ['sample']
            Row key: ['gene']
            ----------------------------------------
            >>> mt.show(n_cols=2)
            +---------+----------------------+----------------------+
            | gene    | 'lung001'.expression | 'lung002'.expression |
            +---------+----------------------+----------------------+
            | str     |                int32 |                int32 |
            +---------+----------------------+----------------------+
            | "LD4"   |                    1 |                    2 |
            | "SCN1A" |                    2 |                    1 |
            | "TITIN" |                    3 |                    0 |
            +---------+----------------------+----------------------+
            showing the first 2 of 6 columns
    
            Notes
            -----
            All fields in `columns` must have the same type.
    
            Parameters
            ----------
            columns : Sequence[str]
                Fields to be used as columns.
            entry_field_name : ``str`` or None
                Field name for the entries of the matrix table.
            col_field_name : ``str``
                Field name for the columns of the matrix table.
    
            Returns
            -------
            ``.MatrixTable``
            """
            if len(columns) == 0:
                raise ValueError('Columns must be non-empty.')
    
            fields = [self[field] for field in columns]
            col_types = set([field.dtype for field in fields])
            if len(col_types) != 1:
                raise ValueError('All columns must have the same type.')
    
            if all([isinstance(col_typ, hl.tstruct) for col_typ in col_types]):
                if entry_field_name is not None:
                    raise ValueError('Cannot both provide struct columns and an entry field name.')
                entries = hl.array(fields)
            else:
                if entry_field_name is None:
                    raise ValueError('Must provide an entry field name.')
                entries = hl.array([hl.struct(**{entry_field_name: field}) for field in fields])
    
            t = self.transmute(entries=entries)
            t = t.annotate_globals(cols=hl.array(columns).map(lambda col: hl.struct(**{col_field_name: col})))
            return t._unlocalize_entries('entries', 'cols', [col_field_name])
    
    
    
        @property
        def globals(self) -> 'StructExpression':
            """Returns a struct expression including all global fields.
    
            Examples
            --------
            The data type of the globals struct:
    
            >>> table1.globals.dtype
            dtype('struct{global_field_1: int32, global_field_2: int32}')
    
            The number of global fields:
    
            >>> len(table1.globals)
            2
    
            Returns
            -------
            ``.StructExpression``
                Struct of all global fields.
            """
            return self._globals
    
        @property
        def row(self) -> 'StructExpression':
            """Returns a struct expression of all row-indexed fields, including keys.
    
            Examples
            --------
            The data type of the row struct:
    
            >>> table1.row.dtype
            dtype('struct{ID: int32, HT: int32, SEX: str, X: int32, Z: int32, C1: int32, C2: int32, C3: int32}')
    
            The number of row fields:
    
            >>> len(table1.row)
            8
    
            Returns
            -------
            ``.StructExpression``
                Struct of all row fields, including key fields.
            """
            return self._row
    
        @property
        def row_value(self) -> 'StructExpression':
            """Returns a struct expression including all non-key row-indexed fields.
    
            Examples
            --------
            The data type of the row struct:
    
            >>> table1.row_value.dtype
            dtype('struct{HT: int32, SEX: str, X: int32, Z: int32, C1: int32, C2: int32, C3: int32}')
    
            The number of row fields:
    
            >>> len(table1.row_value)
            7
    
            Returns
            -------
            ``.StructExpression``
                Struct of all non-key row fields.
            """
            return self._row.drop(*self.key.keys())
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.from_spark)    @staticmethod
        @typecheck(df=pyspark.sql.DataFrame, key=table_key_type)
        def from_spark(df, key=[]) -> 'Table':
            """Convert PySpark SQL DataFrame to a table.
    
            Examples
            --------
    
            >>> t = Table.from_spark(df) # doctest: +SKIP
    
            Notes
            -----
    
            Spark SQL data types are converted to Hail types as follows:
    
            .. code-block:: text
    
              BooleanType => :py``.tbool``
              IntegerType => :py``.tint32``
              LongType => :py``.tint64``
              FloatType => :py``.tfloat32``
              DoubleType => :py``.tfloat64``
              StringType => :py``.tstr``
              BinaryType => ``.TBinary``
              ArrayType => ``.tarray``
              StructType => ``.tstruct``
    
            Unlisted Spark SQL data types are currently unsupported.
    
            Parameters
            ----------
            df : ``.pyspark.sql.DataFrame``
                PySpark DataFrame.
    
            key : ``str`` or ``list`` of ``str``
                Key fields.
    
            Returns
            -------
            ``.Table``
                Table constructed from the Spark SQL DataFrame.
            """
            return Env.spark_backend('from_spark').from_spark(df, key)
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.to_spark)    @typecheck_method(flatten=bool)
        def to_spark(self, flatten=True):
            """Converts this table to a Spark DataFrame.
    
            Because Spark cannot represent complex types, types are
            expanded before flattening or conversion.
    
            Parameters
            ----------
            flatten : ``bool``
                If ``True``, ``flatten`` before converting to Spark DataFrame.
    
            Returns
            -------
            ``.pyspark.sql.DataFrame``
    
            """
            return Env.spark_backend('to_spark').to_spark(self, flatten)
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.to_pandas)    @typecheck_method(flatten=bool, types=dictof(oneof(str, hail_type), str))
        def to_pandas(self, flatten=True, types={}):
            """Converts this table to a Pandas DataFrame.
    
            Parameters
            ----------
            flatten : ``bool``
                If ``True``, ``flatten`` before converting to Pandas DataFrame.
            types : ``dict`` mapping ``str`` or ``.HailType`` to ``str``
                Dictionary defining Pandas DataFrame dtypes.
                If a key is ``str``, a field with the specified key name is mapped to a Pandas dtype.
                If a key is ``.HailType``, all fields with the specified ``.HailType`` are mapped to a Pandas dtype.
            Returns
            -------
            ``.pandas.DataFrame``
    
            """
            table = self.flatten() if flatten else self
            dtypes_struct = table.row.dtype
            collect_dict = {key: hl.agg.collect(value) for key, value in table.row.items()}
            column_struct_array = table.aggregate(hl.struct(**collect_dict))
            columns = list(column_struct_array.keys())
            data_dict = {}
            hl_default_dtypes = {
                hl.tstr: "string",
                hl.tint32: "Int32",
                hl.tint64: "Int64",
                hl.tfloat32: "Float32",
                hl.tfloat64: "Float64",
                hl.tbool: "boolean",
            }
            all_types = {**hl_default_dtypes, **types}
    
            for column in columns:
                hl_dtype = dtypes_struct[column]
                if column in all_types:
                    pd_dtype = all_types[column]
                elif hl_dtype in all_types:
                    pd_dtype = all_types[hl_dtype]
                else:
                    pd_dtype = hl_dtype.to_numpy()
                data_dict[column] = pandas.Series(column_struct_array[column], dtype=pd_dtype)
    
            return pandas.DataFrame(data_dict)
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.from_pandas)    @staticmethod
        @typecheck(df=pandas.DataFrame, key=oneof(str, sequenceof(str)))
        def from_pandas(df, key=[]) -> 'Table':
            """Create table from Pandas DataFrame
    
            Examples
            --------
    
            >>> t = hl.Table.from_pandas(df) # doctest: +SKIP
    
            Parameters
            ----------
            df : ``.pandas.DataFrame``
                Pandas DataFrame.
            key : ``str`` or ``list`` of ``str``
                Key fields.
    
            Returns
            -------
            ``.Table``
            """
    
            key = [key] if isinstance(key, str) else key
            fields = list(df.columns)
            pd_dtypes = df.dtypes
            hl_type_hints = {}
            columns = {fields[col_idx]: df[col].tolist() for col_idx, col in enumerate(df.columns)}
            data = [{} for _ in range(len(columns[fields[0]]))]
    
            for data_idx in range(len(columns[fields[0]])):
                for field in fields:
                    cur_val = columns[field][data_idx]
    
                    # Can't call isna on a collection or it will implicitly broadcast
                    if pandas.api.types.is_numeric_dtype(df[field].dtype) and pandas.isna(cur_val):
                        if isinstance(cur_val, float):
                            fixed_val = cur_val
                        elif isinstance(cur_val, np.floating):
                            fixed_val = cur_val.item()
                        else:
                            fixed_val = None
                    elif isinstance(df[field].dtype, pandas.StringDtype) and pandas.isna(cur_val):  # No NaN to worry about
                        fixed_val = None
                    elif isinstance(cur_val, np.number):
                        fixed_val = cur_val.item()
                    else:
                        fixed_val = cur_val
                    data[data_idx][field] = fixed_val
    
            for data_idx, field in enumerate(fields):
                type_hint = dtypes_from_pandas(pd_dtypes[field])
                if type_hint is not None:
                    hl_type_hints[field] = type_hint
    
            new_table = hl.Table.parallelize(data, partial_type=hl_type_hints)
            return new_table if not key else new_table.key_by(*key)
    
    
    
        @typecheck_method(other=table_type, tolerance=nullable(numeric), absolute=bool, reorder_fields=bool)
        def _same(self, other, tolerance=1e-6, absolute=False, reorder_fields=False):
            from hail.expr.functions import _values_similar
    
            fd_f = set if reorder_fields else list
    
            if fd_f(self.row) != fd_f(other.row):
                print(f'Different row fields: \n  {list(self.row)}\n  {list(other.row)}')
                return False
            if fd_f(self.globals) != fd_f(other.globals):
                print(f'Different globals fields: \n  {list(self.globals)}\n  {list(other.globals)}')
                return False
    
            if reorder_fields:
                globals_order = list(self.globals)
                if list(other.globals) != globals_order:
                    other = other.select_globals(*globals_order)
    
                row_order = list(self.row)
                if list(other.row) != row_order:
                    other = other.select(*row_order)
    
            if self._type != other._type:
                print(f'Table._same: types differ:\n  {self._type}\n  {other._type}')
                return False
    
            left = self
            left = left.select_globals(left_globals=left.globals)
            left = left.group_by(key=left.key).aggregate(left_row=hl.agg.collect(left.row_value))
    
            right = other
            right = right.select_globals(right_globals=right.globals)
            right = right.group_by(key=right.key).aggregate(right_row=hl.agg.collect(right.row_value))
    
            t = left.join(right, how='outer')
    
            mismatched_globals, mismatched_rows = t.aggregate(
                hl.tuple((
                    hl.or_missing(~_values_similar(t.left_globals, t.right_globals, tolerance, absolute), t.globals),
                    hl.agg.filter(
                        ~hl.all(
                            hl.is_defined(t.left_row),
                            hl.is_defined(t.right_row),
                            _values_similar(t.left_row, t.right_row, tolerance, absolute),
                        ),
                        hl.agg.take(t.row, 10),
                    ),
                ))
            )
    
            columns, _ = shutil.get_terminal_size((80, 10))
    
            def pretty(obj):
                pretty_str = pprint.pformat(obj, width=columns)
                return ''.join('        ' + line for line in pretty_str.splitlines(keepends=True))
    
            is_same = True
            if mismatched_globals is not None:
                print(f"""Table._same: globals differ:
        Left:
    {pretty(mismatched_globals.left_globals)}
        Right:
    {pretty(mismatched_globals.right_globals)}""")
                is_same = False
    
            if len(mismatched_rows) > 0:
                print('Table._same: rows differ:')
                for r in mismatched_rows:
                    print(f"""  Row mismatch at key={r.key}:
        Left:
    {pretty(r.left_row)}
        Right:
    {pretty(r.right_row)}""")
                is_same = False
    
            return is_same
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.collect_by_key)    def collect_by_key(self, name: str = 'values') -> 'Table':
            """Collect values for each unique key into an array.
    
            .. include:: _templates/req_keyed_table.rst
    
            Examples
            --------
            >>> t1 = hl.Table.parallelize([
            ...     {'t': 'foo', 'x': 4, 'y': 'A'},
            ...     {'t': 'bar', 'x': 2, 'y': 'B'},
            ...     {'t': 'bar', 'x': -3, 'y': 'C'},
            ...     {'t': 'quam', 'x': 0, 'y': 'D'}],
            ...     hl.tstruct(t=hl.tstr, x=hl.tint32, y=hl.tstr),
            ...     key='t')
    
            >>> t1.show()
            +--------+-------+-----+
            | t      |     x | y   |
            +--------+-------+-----+
            | str    | int32 | str |
            +--------+-------+-----+
            | "bar"  |     2 | "B" |
            | "bar"  |    -3 | "C" |
            | "foo"  |     4 | "A" |
            | "quam" |     0 | "D" |
            +--------+-------+-----+
    
            >>> t1.collect_by_key().show()
            +--------+---------------------------------+
            | t      | values                          |
            +--------+---------------------------------+
            | str    | array<struct{x: int32, y: str}> |
            +--------+---------------------------------+
            | "bar"  | [(2,"B"),(-3,"C")]              |
            | "foo"  | [(4,"A")]                       |
            | "quam" | [(0,"D")]                       |
            +--------+---------------------------------+
    
            Notes
            -----
            The order of the values array is not guaranteed.
    
            Parameters
            ----------
            name : ``str``
                Field name for all values per key.
    
            Returns
            -------
            ``.Table``
            """
    
            from hail.methods import misc
    
            misc.require_key(self, 'collect_by_key')
    
            return Table(ir.TableAggregateByKey(self._tir, hl.struct(**{name: hl.agg.collect(self.row_value)})._ir))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.distinct)    def distinct(self) -> 'Table':
            """Deduplicate keys, keeping exactly one row for each unique key.
    
            .. include:: _templates/req_keyed_table.rst
    
            Examples
            --------
            >>> t1 = hl.Table.parallelize([
            ...     {'a': 'foo', 'b': 1},
            ...     {'a': 'bar', 'b': 5},
            ...     {'a': 'bar', 'b': 2}],
            ...     hl.tstruct(a=hl.tstr, b=hl.tint32),
            ...     key='a')
    
            >>> t1.show()
            +-------+-------+
            | a     |     b |
            +-------+-------+
            | str   | int32 |
            +-------+-------+
            | "bar" |     5 |
            | "bar" |     2 |
            | "foo" |     1 |
            +-------+-------+
    
            >>> t1.distinct().show()
            +-------+-------+
            | a     |     b |
            +-------+-------+
            | str   | int32 |
            +-------+-------+
            | "bar" |     5 |
            | "foo" |     1 |
            +-------+-------+
    
            Notes
            -----
            The row chosen per distinct key is not guaranteed.
    
            Returns
            -------
            ``.Table``
            """
    
            from hail.methods import misc
    
            misc.require_key(self, 'distinct')
    
            return Table(ir.TableDistinct(self._tir))
    
    
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.summarize)    def summarize(self, handler=None):
            """Compute and print summary information about the fields in the table.
    
            .. include:: _templates/experimental.rst
            """
    
            if handler is None:
                handler = hl.utils.default_handler()
            handler(self.row._summarize(top=True))
    
    
    
        @typecheck_method(parts=sequenceof(int), keep=bool)
        def _filter_partitions(self, parts, keep=True) -> 'Table':
            return Table(ir.TableToTableApply(self._tir, {'name': 'TableFilterPartitions', 'parts': parts, 'keep': keep}))
    
        @typecheck_method(entries_field_name=str, cols_field_name=str, col_key=sequenceof(str))
        def _unlocalize_entries(self, entries_field_name, cols_field_name, col_key) -> 'hl.MatrixTable':
            return hl.MatrixTable(ir.CastTableToMatrix(self._tir, entries_field_name, cols_field_name, col_key))
    
    
    
    [[docs]](../../hail.Table.html#hail.Table.multi_way_zip_join)    @staticmethod
        @typecheck(tables=sequenceof(table_type), data_field_name=str, global_field_name=str)
        def multi_way_zip_join(tables, data_field_name, global_field_name) -> 'Table':
            """Combine many tables in a zip join
    
            .. include:: _templates/experimental.rst
    
            Notes
            -----
            The row type of the returned table is a struct with the key fields, and
            one extra field, `data_field_name`, which is an array of structs with
            the non key fields, one per input. The array elements are missing if
            their corresponding input had no row with that key or possibly if there
            is another input with more rows with that key than the corresponding
            input.
    
            The global type of the returned table is an array of structs of the
            global type of all of the inputs.
    
            The types for every input must be identical, not merely compatible,
            including the keys.
    
            A zip join is similar to an outer join however rows are not duplicated
            to create the full Cartesian product of duplicate keys. Instead, there
            is exactly one entry in some `data_field_name` array for every row in
            the inputs.
    
            The ``multi_way_zip_join`` method assumes that inputs have distinct
            keys. If any input has duplicate keys, the row value that is included
            in the result array for that key is undefined.
    
            Parameters
            ----------
            tables : ``list`` of ``Table``
                A list of tables to combine
            data_field_name : ``str``
                The name of the resulting data field
            global_field_name : ``str``
                The name of the resulting global field
    
            """
            if not tables:
                raise ValueError('multi_way_zip_join must have at least one table as an argument')
            head = tables[0]
            if any(head.key.dtype != t.key.dtype for t in tables):
                raise TypeError(
                    'All input tables to multi_way_zip_join must have the same key type:\n  '
                    + '\n  '.join(str(t.key.dtype) for t in tables)
                )
            if any(head.row.dtype != t.row.dtype for t in tables):
                raise TypeError(
                    'All input tables to multi_way_zip_join must have the same row type\n  '
                    + '\n  '.join(str(t.row.dtype) for t in tables)
                )
            if any(head.globals.dtype != t.globals.dtype for t in tables):
                raise TypeError(
                    'All input tables to multi_way_zip_join must have the same global type\n  '
                    + '\n  '.join(str(t.globals.dtype) for t in tables)
                )
            return Table(ir.TableMultiWayZipJoin([t._tir for t in tables], data_field_name, global_field_name))
    
    
    
        def _group_within_partitions(self, name, n):
            def grouping_func(part):
                groups = part.grouped(n)
                key_names = list(self.key)
                return groups.map(lambda group: group[0].select(*key_names, **{name: group}))
    
            return self._map_partitions(grouping_func)
    
        @typecheck_method(f=func_spec(1, expr_stream(expr_struct())))
        def _map_partitions(self, f):
            rows_uid = 'tmp_rows_' + Env.get_uid()
            globals_uid = 'tmp_globals_' + Env.get_uid()
            expr = construct_expr(
                ir.Ref(rows_uid, hl.tstream(self.row.dtype)), hl.tstream(self.row.dtype), self._row_indices
            )
            body = f(expr)
            result_t = body.dtype
            if any(k not in result_t.element_type for k in self.key):
                raise ValueError('Table._map_partitions must preserve key fields')
    
            body_ir = ir.Let('global', ir.Ref(globals_uid, self._global_type), body._ir)
            return Table(ir.TableMapPartitions(self._tir, globals_uid, rows_uid, body_ir, len(self.key), len(self.key)))
    
        def _calculate_new_partitions(self, n_partitions):
            """returns a set of range bounds that can be passed to write"""
            return Env.backend().execute(
                ir.TableToValueApply(
                    self.select().select_globals()._tir,
                    {'name': 'TableCalculateNewPartitions', 'nPartitions': n_partitions},
                )
            )
    
    
    
    
    table_type.set(Table)

---


# Source code for hail.matrixtable

    
    
    import itertools
    import warnings
    from collections import Counter
    from typing import Any, Dict, Iterable, List, Optional, Tuple
    
    from deprecated import deprecated
    
    import hail as hl
    from hail import ir
    from hail.expr.expressions import (
        Expression,
        ExpressionException,
        Indices,
        StructExpression,
        TupleExpression,
        analyze,
        construct_expr,
        construct_reference,
        expr_any,
        expr_bool,
        expr_struct,
        extract_refs_by_indices,
        unify_all,
    )
    from hail.expr.matrix_type import tmatrix
    from hail.expr.types import tarray, tset, types_match
    from hail.table import BaseTable, Table, TableIndexKeyError
    from hail.typecheck import (
        anyfunc,
        anytype,
        dictof,
        enumeration,
        lazy,
        nullable,
        numeric,
        oneof,
        sequenceof,
        typecheck,
        typecheck_method,
    )
    from hail.utils import deduplicate, default_handler, storage_level
    from hail.utils.java import Env, info, warning
    from hail.utils.misc import check_annotate_exprs, get_key_by_exprs, get_select_exprs, process_joins, wrap_to_tuple
    
    
    
    
    [[docs]](../../hail.GroupedMatrixTable.html#hail.GroupedMatrixTable)class GroupedMatrixTable(BaseTable):
        """Matrix table grouped by row or column that can be aggregated into a new matrix table."""
    
        def __init__(
            self,
            parent: 'MatrixTable',
            row_keys=None,
            computed_row_key=None,
            col_keys=None,
            computed_col_key=None,
            entry_fields=None,
            row_fields=None,
            col_fields=None,
            partitions=None,
        ):
            super(GroupedMatrixTable, self).__init__()
            self._parent = parent
            self._copy_fields_from(parent)
            self._row_keys = row_keys
            self._computed_row_key = computed_row_key
            self._col_keys = col_keys
            self._computed_col_key = computed_col_key
            self._entry_fields = entry_fields
            self._row_fields = row_fields
            self._col_fields = col_fields
            self._partitions = partitions
    
        def _copy(
            self,
            *,
            row_keys=None,
            computed_row_key=None,
            col_keys=None,
            computed_col_key=None,
            entry_fields=None,
            row_fields=None,
            col_fields=None,
            partitions=None,
        ):
            return GroupedMatrixTable(
                parent=self._parent,
                row_keys=row_keys if row_keys is not None else self._row_keys,
                computed_row_key=computed_row_key if computed_row_key is not None else self._computed_row_key,
                col_keys=col_keys if col_keys is not None else self._col_keys,
                computed_col_key=computed_col_key if computed_col_key is not None else self._computed_col_key,
                entry_fields=entry_fields if entry_fields is not None else self._entry_fields,
                row_fields=row_fields if row_fields is not None else self._row_fields,
                col_fields=col_fields if col_fields is not None else self._col_fields,
                partitions=partitions if partitions is not None else self._partitions,
            )
    
        def _fixed_indices(self):
            if self._row_keys is None and self._col_keys is None:
                return self._parent._entry_indices
            if self._row_keys is not None and self._col_keys is None:
                return self._parent._col_indices
            if self._row_keys is None and self._col_keys is not None:
                return self._parent._row_indices
            return self._parent._global_indices
    
        @typecheck_method(item=str)
        def __getitem__(self, item):
            return self._get_field(item)
    
    
    
    [[docs]](../../hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.describe)    def describe(self, handler=print):
            """Print information about grouped matrix table."""
    
            if self._row_keys is None:
                rowstr = ""
            else:
                rowstr = "\nRows: \n" + "\n    ".join(["{}: {}".format(k, v._type) for k, v in self._row_keys.items()])
    
            if self._col_keys is None:
                colstr = ""
            else:
                colstr = "\nColumns: \n" + "\n    ".join(["{}: {}".format(k, v) for k, v in self._col_keys.items()])
    
            s = (
                f'----------------------------------------\n'
                f'GroupedMatrixTable grouped by {rowstr}{colstr}\n'
                f'----------------------------------------\n'
                f'Parent MatrixTable:\n'
            )
    
            handler(s)
            self._parent.describe(handler)
    
    
    
    
    
    [[docs]](../../hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.group_rows_by)    @typecheck_method(exprs=oneof(str, Expression), named_exprs=expr_any)
        def group_rows_by(self, *exprs, **named_exprs) -> 'GroupedMatrixTable':
            """Group rows.
    
            Examples
            --------
            Aggregate to a matrix with genes as row keys, computing the number of
            non-reference calls as an entry field:
    
            >>> dataset_result = (dataset.group_rows_by(dataset.gene)
            ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    
            Notes
            -----
            All complex expressions must be passed as named expressions.
    
            Parameters
            ----------
            exprs : args of ``str`` or ``.Expression``
                Row fields to group by.
            named_exprs : keyword args of ``.Expression``
                Row-indexed expressions to group by.
    
            Returns
            -------
            ``.GroupedMatrixTable``
                Grouped matrix. Can be used to call ``.GroupedMatrixTable.aggregate``.
            """
            if self._row_keys is not None:
                raise NotImplementedError("GroupedMatrixTable is already grouped by rows.")
            if self._col_keys is not None:
                raise NotImplementedError("GroupedMatrixTable is already grouped by cols; cannot also group by rows.")
    
            caller = 'group_rows_by'
            row_key, computed_key = get_key_by_exprs(
                caller,
                exprs,
                named_exprs,
                self._parent._row_indices,
                override_protected_indices={self._parent._global_indices, self._parent._col_indices},
            )
    
            self._check_bindings(caller, computed_key, self._parent._row_indices)
            return self._copy(row_keys=row_key, computed_row_key=computed_key)
    
    
    
    
    
    [[docs]](../../hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.group_cols_by)    @typecheck_method(exprs=oneof(str, Expression), named_exprs=expr_any)
        def group_cols_by(self, *exprs, **named_exprs) -> 'GroupedMatrixTable':
            """Group columns.
    
            Examples
            --------
            Aggregate to a matrix with cohort as column keys, computing the call rate
            as an entry field:
    
            >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
            ...                          .aggregate(call_rate = hl.agg.fraction(hl.is_defined(dataset.GT))))
    
            Notes
            -----
            All complex expressions must be passed as named expressions.
    
            Parameters
            ----------
            exprs : args of ``str`` or ``.Expression``
                Column fields to group by.
            named_exprs : keyword args of ``.Expression``
                Column-indexed expressions to group by.
    
            Returns
            -------
            ``.GroupedMatrixTable``
                Grouped matrix, can be used to call ``.GroupedMatrixTable.aggregate``.
            """
            if self._row_keys is not None:
                raise NotImplementedError("GroupedMatrixTable is already grouped by rows; cannot also group by cols.")
            if self._col_keys is not None:
                raise NotImplementedError("GroupedMatrixTable is already grouped by cols.")
    
            caller = 'group_cols_by'
            col_key, computed_key = get_key_by_exprs(
                caller,
                exprs,
                named_exprs,
                self._parent._col_indices,
                override_protected_indices={self._parent._global_indices, self._parent._row_indices},
            )
    
            self._check_bindings(caller, computed_key, self._parent._col_indices)
            return self._copy(col_keys=col_key, computed_col_key=computed_key)
    
    
    
        def _check_bindings(self, caller, new_bindings, indices):
            empty = []
    
            def iter_option(o):
                return o if o is not None else empty
    
            if indices == self._parent._row_indices:
                fixed_fields = [*self._parent.globals, *self._parent.col]
            else:
                assert indices == self._parent._col_indices
                fixed_fields = [*self._parent.globals, *self._parent.row]
    
            bound_fields = set(
                itertools.chain(
                    iter_option(self._row_keys),
                    iter_option(self._col_keys),
                    iter_option(self._col_fields),
                    iter_option(self._row_fields),
                    iter_option(self._entry_fields),
                    fixed_fields,
                )
            )
    
            for k in new_bindings:
                if k in bound_fields:
                    raise ExpressionException(f"{caller!r} cannot assign duplicate field {k!r}")
    
    
    
    [[docs]](../../hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.partition_hint)    def partition_hint(self, n: int) -> 'GroupedMatrixTable':
            """Set the target number of partitions for aggregation.
    
            Examples
            --------
    
            Use `partition_hint` in a ``.MatrixTable.group_rows_by`` /
            ``.GroupedMatrixTable.aggregate`` pipeline:
    
            >>> dataset_result = (dataset.group_rows_by(dataset.gene)
            ...                          .partition_hint(5)
            ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    
            Notes
            -----
            Until Hail's query optimizer is intelligent enough to sample records at all
            stages of a pipeline, it can be necessary in some places to provide some
            explicit hints.
    
            The default number of partitions for ``.GroupedMatrixTable.aggregate`` is
            the number of partitions in the upstream dataset. If the aggregation greatly
            reduces the size of the dataset, providing a hint for the target number of
            partitions can accelerate downstream operations.
    
            Parameters
            ----------
            n : int
                Number of partitions.
    
            Returns
            -------
            ``.GroupedMatrixTable``
                Same grouped matrix table with a partition hint.
            """
    
            self._partitions = n
            return self
    
    
    
    
    
    [[docs]](../../hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate_cols)    @typecheck_method(named_exprs=expr_any)
        def aggregate_cols(self, **named_exprs) -> 'GroupedMatrixTable':
            """Aggregate cols by group.
    
            Examples
            --------
            Aggregate to a matrix with cohort as column keys, computing the mean height
            per cohort as a new column field:
    
            >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
            ...                          .aggregate_cols(mean_height = hl.agg.mean(dataset.pheno.height))
            ...                          .result())
    
            Notes
            -----
            The aggregation scope includes all column fields and global fields.
    
            See Also
            --------
            ``.result``
    
            Parameters
            ----------
            named_exprs : varargs of ``.Expression``
                Aggregation expressions.
    
            Returns
            -------
            ``.GroupedMatrixTable``
            """
            if self._row_keys is not None:
                raise NotImplementedError("GroupedMatrixTable is already grouped by rows. Cannot aggregate over cols.")
            assert self._col_keys is not None
    
            base = self._col_fields if self._col_fields is not None else hl.struct()
            for k, e in named_exprs.items():
                analyze('GroupedMatrixTable.aggregate_cols', e, self._parent._global_indices, {self._parent._col_axis})
    
            self._check_bindings('aggregate_cols', named_exprs, self._parent._col_indices)
            return self._copy(col_fields=base.annotate(**named_exprs))
    
    
    
    
    
    [[docs]](../../hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate_rows)    @typecheck_method(named_exprs=expr_any)
        def aggregate_rows(self, **named_exprs) -> 'GroupedMatrixTable':
            """Aggregate rows by group.
    
            Examples
            --------
            Aggregate to a matrix with genes as row keys, collecting the functional
            consequences per gene as a set as a new row field:
    
            >>> dataset_result = (dataset.group_rows_by(dataset.gene)
            ...                          .aggregate_rows(consequences = hl.agg.collect_as_set(dataset.consequence))
            ...                          .result())
    
            Notes
            -----
            The aggregation scope includes all row fields and global fields.
    
            See Also
            --------
            ``.result``
    
            Parameters
            ----------
            named_exprs : varargs of ``.Expression``
                Aggregation expressions.
    
            Returns
            -------
            ``.GroupedMatrixTable``
            """
            if self._col_keys is not None:
                raise NotImplementedError("GroupedMatrixTable is already grouped by cols. Cannot aggregate over rows.")
            assert self._row_keys is not None
    
            base = self._row_fields if self._row_fields is not None else hl.struct()
            for k, e in named_exprs.items():
                analyze('GroupedMatrixTable.aggregate_rows', e, self._parent._global_indices, {self._parent._row_axis})
    
            self._check_bindings('aggregate_rows', named_exprs, self._parent._row_indices)
            return self._copy(row_fields=base.annotate(**named_exprs))
    
    
    
    
    
    [[docs]](../../hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate_entries)    @typecheck_method(named_exprs=expr_any)
        def aggregate_entries(self, **named_exprs) -> 'GroupedMatrixTable':
            """Aggregate entries by group.
    
            Examples
            --------
            Aggregate to a matrix with genes as row keys, computing the number of
            non-reference calls as an entry field:
    
            >>> dataset_result = (dataset.group_rows_by(dataset.gene)
            ...                          .aggregate_entries(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref()))
            ...                          .result())
    
            See Also
            --------
            ``.aggregate``, ``.result``
    
            Parameters
            ----------
            named_exprs : varargs of ``.Expression``
                Aggregation expressions.
    
            Returns
            -------
            ``.GroupedMatrixTable``
            """
            assert self._row_keys is not None or self._col_keys is not None
    
            base = self._entry_fields if self._entry_fields is not None else hl.struct()
            for k, e in named_exprs.items():
                analyze(
                    'GroupedMatrixTable.aggregate_entries',
                    e,
                    self._fixed_indices(),
                    {self._parent._row_axis, self._parent._col_axis},
                )
    
            self._check_bindings(
                'aggregate_entries',
                named_exprs,
                self._parent._col_indices if self._col_keys is not None else self._parent._row_indices,
            )
            return self._copy(entry_fields=base.annotate(**named_exprs))
    
    
    
    
    
    [[docs]](../../hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.result)    def result(self) -> 'MatrixTable':
            """Return the result of aggregating by group.
    
            Examples
            --------
            Aggregate to a matrix with genes as row keys, collecting the functional
            consequences per gene as a row field and computing the number of
            non-reference calls as an entry field:
    
            >>> dataset_result = (dataset.group_rows_by(dataset.gene)
            ...                          .aggregate_rows(consequences = hl.agg.collect_as_set(dataset.consequence))
            ...                          .aggregate_entries(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref()))
            ...                          .result())
    
            Aggregate to a matrix with cohort as column keys, computing the mean height
            per cohort as a column field and computing the number of non-reference calls
            as an entry field:
    
            >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
            ...                          .aggregate_cols(mean_height = hl.agg.stats(dataset.pheno.height).mean)
            ...                          .aggregate_entries(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref()))
            ...                          .result())
    
            See Also
            --------
            ``.aggregate``
    
            Returns
            -------
            ``.MatrixTable``
                Aggregated matrix table.
            """
            assert self._row_keys is not None or self._col_keys is not None
    
            defined_exprs = []
            for e in [self._row_fields, self._col_fields, self._entry_fields]:
                if e is not None:
                    defined_exprs.append(e)
            for e in [self._computed_row_key, self._computed_col_key]:
                if e is not None:
                    defined_exprs.extend(e.values())
    
            def promote_none(e):
                return hl.struct() if e is None else e
    
            entry_exprs = promote_none(self._entry_fields)
            if len(entry_exprs) == 0:
                warning("'GroupedMatrixTable.result': No entry fields were defined.")
    
            base, cleanup = self._parent._process_joins(*defined_exprs)
    
            if self._col_keys is not None:
                cck = self._computed_col_key or {}
                computed_key_uids = {k: Env.get_uid() for k in cck}
                modified_keys = [computed_key_uids.get(k, k) for k in self._col_keys]
                mt = MatrixTable(
                    ir.MatrixAggregateColsByKey(
                        ir.MatrixMapCols(
                            base._mir,
                            self._parent.col.annotate(**{computed_key_uids[k]: v for k, v in cck.items()})._ir,
                            modified_keys,
                        ),
                        entry_exprs._ir,
                        promote_none(self._col_fields)._ir,
                    )
                )
                if cck:
                    mt = mt.rename({v: k for k, v in computed_key_uids.items()})
            else:
                cck = self._computed_row_key or {}
                computed_key_uids = {k: Env.get_uid() for k in cck}
                modified_keys = [computed_key_uids.get(k, k) for k in self._row_keys]
                mt = MatrixTable(
                    ir.MatrixAggregateRowsByKey(
                        ir.MatrixKeyRowsBy(
                            ir.MatrixMapRows(
                                ir.MatrixKeyRowsBy(base._mir, []),
                                self._parent._rvrow.annotate(**{computed_key_uids[k]: v for k, v in cck.items()})._ir,
                            ),
                            modified_keys,
                        ),
                        entry_exprs._ir,
                        promote_none(self._row_fields)._ir,
                    )
                )
                if cck:
                    mt = mt.rename({v: k for k, v in computed_key_uids.items()})
    
            return cleanup(mt)
    
    
    
    
    
    [[docs]](../../hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate)    @typecheck_method(named_exprs=expr_any)
        def aggregate(self, **named_exprs) -> 'MatrixTable':
            """Aggregate entries by group, used after ``.MatrixTable.group_rows_by``
            or ``.MatrixTable.group_cols_by``.
    
            Examples
            --------
            Aggregate to a matrix with genes as row keys, computing the number of
            non-reference calls as an entry field:
    
            >>> dataset_result = (dataset.group_rows_by(dataset.gene)
            ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    
            Notes
            -----
            Alias for ``aggregate_entries``, ``result``.
    
            See Also
            --------
            ``aggregate_entries``, ``result``
    
            Parameters
            ----------
            named_exprs : varargs of ``.Expression``
                Aggregation expressions.
    
            Returns
            -------
            ``.MatrixTable``
                Aggregated matrix table.
            """
    
            return self.aggregate_entries(**named_exprs).result()
    
    
    
    
    matrix_table_type = lazy()
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable)class MatrixTable(BaseTable):
        """Hail's distributed implementation of a structured matrix.
    
        Use ``.read_matrix_table`` to read a matrix table that was written with
        ``.MatrixTable.write``.
    
        Examples
        --------
    
        Add annotations:
    
        >>> dataset = dataset.annotate_globals(pli = {'SCN1A': 0.999, 'SONIC': 0.014},
        ...                                    populations = ['AFR', 'EAS', 'EUR', 'SAS', 'AMR', 'HIS'])
    
        >>> dataset = dataset.annotate_cols(pop = dataset.populations[hl.int(hl.rand_unif(0, 6))],
        ...                                 sample_gq = hl.agg.mean(dataset.GQ),
        ...                                 sample_dp = hl.agg.mean(dataset.DP))
    
        >>> dataset = dataset.annotate_rows(variant_gq = hl.agg.mean(dataset.GQ),
        ...                                 variant_dp = hl.agg.mean(dataset.GQ),
        ...                                 sas_hets = hl.agg.count_where(dataset.GT.is_het()))
    
        >>> dataset = dataset.annotate_entries(gq_by_dp = dataset.GQ / dataset.DP)
    
        Filter:
    
        >>> dataset = dataset.filter_cols(dataset.pop != 'EUR')
    
        >>> datasetm = dataset.filter_rows((dataset.variant_gq > 10) & (dataset.variant_dp > 5))
    
        >>> dataset = dataset.filter_entries(dataset.gq_by_dp > 1)
    
        Query:
    
        >>> col_stats = dataset.aggregate_cols(hl.struct(pop_counts=hl.agg.counter(dataset.pop),
        ...                                              high_quality=hl.agg.fraction((dataset.sample_gq > 10) & (dataset.sample_dp > 5))))
        >>> print(col_stats.pop_counts)
        >>> print(col_stats.high_quality)
    
        >>> het_dist = dataset.aggregate_rows(hl.agg.stats(dataset.sas_hets))
        >>> print(het_dist)
    
        >>> entry_stats = dataset.aggregate_entries(hl.struct(call_rate=hl.agg.fraction(hl.is_defined(dataset.GT)),
        ...                                                   global_gq_mean=hl.agg.mean(dataset.GQ)))
        >>> print(entry_stats.call_rate)
        >>> print(entry_stats.global_gq_mean)
        """
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.from_parts)    @staticmethod
        @typecheck(
            globals=nullable(dictof(str, anytype)),
            rows=nullable(dictof(str, sequenceof(anytype))),
            cols=nullable(dictof(str, sequenceof(anytype))),
            entries=nullable(dictof(str, sequenceof(sequenceof(anytype)))),
        )
        def from_parts(
            globals: Optional[Dict[str, Any]] = None,
            rows: Optional[Dict[str, Iterable[Any]]] = None,
            cols: Optional[Dict[str, Iterable[Any]]] = None,
            entries: Optional[Dict[str, Iterable[Iterable[Any]]]] = None,
        ) -> 'MatrixTable':
            """Create a `MatrixTable` from its component parts.
    
            Example
            -------
            >>> mt = hl.MatrixTable.from_parts(
            ...     globals={'hello':'world'},
            ...     rows={'foo':[1, 2]},
            ...     cols={'bar':[3, 4]},
            ...     entries={'baz':[[1, 2],[3, 4]]}
            ... )
            >>> mt.describe()
            ----------------------------------------
            Global fields:
                'hello': str
            ----------------------------------------
            Column fields:
                'col_idx': int32
                'bar': int32
            ----------------------------------------
            Row fields:
                'row_idx': int32
                'foo': int32
            ----------------------------------------
            Entry fields:
                'baz': int32
            ----------------------------------------
            Column key: ['col_idx']
            Row key: ['row_idx']
            ----------------------------------------
            >>> mt.row.show()
            +---------+-------+
            | row_idx |   foo |
            +---------+-------+
            |   int32 | int32 |
            +---------+-------+
            |       0 |     1 |
            |       1 |     2 |
            +---------+-------+
            >>> mt.col.show()
            +---------+-------+
            | col_idx |   bar |
            +---------+-------+
            |   int32 | int32 |
            +---------+-------+
            |       0 |     3 |
            |       1 |     4 |
            +---------+-------+
            >>> mt.entry.show()
            +---------+-------+-------+
            | row_idx | 0.baz | 1.baz |
            +---------+-------+-------+
            |   int32 | int32 | int32 |
            +---------+-------+-------+
            |       0 |     1 |     2 |
            |       1 |     3 |     4 |
            +---------+-------+-------+
    
            Notes
            -----
            - Matrix dimensions are inferred from input data.
            - You must provide row and column dimensions by specifying rows or
              entries (inclusive) and cols or entries (inclusive).
            - The respective dimensions of rows, cols and entries must match should
              you provide rows and entries or cols and entries (inclusive).
    
            Parameters
            ----------
            globals : ``dict`` from ``str`` to ``any``
                Global fields by name.
    
            rows: ``dict`` from ``str`` to ``list`` of ``any``
                Row fields by name.
    
            cols: ``dict`` from ``str`` to ``list`` of ``any``
                Column fields by name.
    
            entries: ``dict`` from ``str`` to ``list`` of ``list`` of ``any``
                Matrix entries by name in the form `entry[row_idx][col_idx]`.
    
            Returns
            -------
            ``.MatrixTable``
                A MatrixTable assembled from inputs whose rows are keyed by `row_idx`
                and columns are keyed by `col_idx`.
            """
    
            # General idea: build a `Table` representation matching that returned by
            # `MatrixTable.localize_entries` and then call `_unlocalize_entries`. In
            # this form, the column table is bundled with the globals and the entries
            # for each row is stored on the row.
            def raise_when_mismatched_property_dimensions(kvs: Dict[str, Iterable[Any]]):
                def value_len(entry):
                    return len(entry[1])
    
                kvs = sorted(kvs.items(), key=value_len)
                dims = itertools.groupby(kvs, value_len)
                dims = {size: [k for k, _ in group] for size, group in dims}
                if len(dims) > 1:
                    raise ValueError(f"property matrix dimensions do not match: {dims}.")
    
            def transpose(kvs: Dict[str, Iterable[Any]]) -> List[Dict[str, Any]]:
                raise_when_mismatched_property_dimensions(kvs)
                return [dict(zip(kvs, vs)) for vs in zip(*kvs.values())]
    
            def anyval(kvs):
                return next(iter(kvs.values()))
    
            # In the case rows or cols aren't specified, we need to infer the
            # matrix dimensions from *an* entry. Which one isn't important as we
            # enforce congruence among input dimensions.
            assert not ((rows is None or cols is None) and (entries is None))
            cols = transpose(cols) if cols else [{} for _ in anyval(entries)[0]]
            for i, _ in enumerate(cols):
                cols[i] = hl.struct(col_idx=i, **cols[i])
    
            if globals is None:
                globals = {}
    
            cols_field_name = Env.get_uid()
            globals[cols_field_name] = cols
    
            rows = transpose(rows) if rows else [{} for _ in anyval(entries)]
            entries = [transpose(e) for e in transpose(entries)] if entries else [[{} for _ in cols] for _ in rows]
    
            if len(rows) != len(entries) or len(cols) != len(entries[0]):
                raise ValueError(("mismatched matrix dimensions: number of rows and cols does not match entry dimensions."))
    
            entries_field_name = Env.get_uid()
            for i, (row_props, entry_props) in enumerate(zip(rows, entries)):
                row_entries = [hl.struct(**kvs) for kvs in entry_props]
                rows[i] = hl.Struct(row_idx=i, **row_props, **{entries_field_name: row_entries})
    
            ht = Table.parallelize(rows, key='row_idx', globals=hl.struct(**globals))
            return ht._unlocalize_entries(entries_field_name, cols_field_name, col_key=['col_idx'])
    
    
    
        def __init__(self, mir):
            super(MatrixTable, self).__init__()
    
            self._mir = mir
    
            self._globals = None
            self._col_values = None
    
            self._row_axis = 'row'
            self._col_axis = 'column'
    
            self._global_indices = Indices(self, set())
            self._row_indices = Indices(self, {self._row_axis})
            self._col_indices = Indices(self, {self._col_axis})
            self._entry_indices = Indices(self, {self._row_axis, self._col_axis})
    
            self._type = self._mir.typ
    
            self._global_type = self._type.global_type
            self._col_type = self._type.col_type
            self._row_type = self._type.row_type
            self._entry_type = self._type.entry_type
    
            self._globals = construct_reference('global', self._global_type, indices=self._global_indices)
            self._rvrow = construct_reference('va', self._type.row_type, indices=self._row_indices)
            self._row = hl.struct(**{k: self._rvrow[k] for k in self._row_type.keys()})
            self._col = construct_reference('sa', self._col_type, indices=self._col_indices)
            self._entry = construct_reference('g', self._entry_type, indices=self._entry_indices)
    
            self._indices_from_ref = {
                'global': self._global_indices,
                'va': self._row_indices,
                'sa': self._col_indices,
                'g': self._entry_indices,
            }
    
            self._row_key = hl.struct(**{k: self._row[k] for k in self._type.row_key})
            self._partition_key = self._row_key
            self._col_key = hl.struct(**{k: self._col[k] for k in self._type.col_key})
    
            self._num_samples = None
    
            for k, v in itertools.chain(self._globals.items(), self._row.items(), self._col.items(), self._entry.items()):
                self._set_field(k, v)
    
        @property
        def _schema(self) -> tmatrix:
            return tmatrix(
                self._global_type,
                self._col_type,
                list(self._col_key),
                self._row_type,
                list(self._row_key),
                self._entry_type,
            )
    
        def __getitem__(self, item):
            invalid_usage = TypeError(
                "MatrixTable.__getitem__: invalid index argument(s)\n"
                "  Usage 1: field selection: mt['field']\n"
                "  Usage 2: Entry joining: mt[mt2.row_key, mt2.col_key]\n\n"
                "  To join row or column fields, use one of the following:\n"
                "    rows:\n"
                "       mt.index_rows(mt2.row_key)\n"
                "       mt.rows().index(mt2.row_key)\n"
                "       mt.rows()[mt2.row_key]\n"
                "    cols:\n"
                "       mt.index_cols(mt2.col_key)\n"
                "       mt.cols().index(mt2.col_key)\n"
                "       mt.cols()[mt2.col_key]"
            )
    
            if isinstance(item, str):
                return self._get_field(item)
            if isinstance(item, tuple) and len(item) == 2:
                # this is the join path
                exprs = item
                row_key = wrap_to_tuple(exprs[0])
                col_key = wrap_to_tuple(exprs[1])
    
                try:
                    return self.index_entries(row_key, col_key)
                except TypeError as e:
                    raise invalid_usage from e
            raise invalid_usage
    
        @property
        def _col_key_types(self):
            return [v.dtype for _, v in self.col_key.items()]
    
        @property
        def _row_key_types(self):
            return [v.dtype for _, v in self.row_key.items()]
    
        @property
        def col_key(self) -> 'StructExpression':
            """Column key struct.
    
            Examples
            --------
    
            Get the column key field names:
    
            >>> list(dataset.col_key)
            ['s']
    
            Returns
            -------
            ``.StructExpression``
            """
            return self._col_key
    
        @property
        def row_key(self) -> 'StructExpression':
            """Row key struct.
    
            Examples
            --------
    
            Get the row key field names:
    
            >>> list(dataset.row_key)
            ['locus', 'alleles']
    
            Returns
            -------
            ``.StructExpression``
            """
            return self._row_key
    
        @property
        def globals(self) -> 'StructExpression':
            """Returns a struct expression including all global fields.
    
            Returns
            -------
            ``.StructExpression``
            """
            return self._globals
    
        @property
        def row(self) -> 'StructExpression':
            """Returns a struct expression of all row-indexed fields, including keys.
    
            Examples
            --------
            Get the first five row field names:
    
            >>> list(dataset.row)[:5]
            ['locus', 'alleles', 'rsid', 'qual', 'filters']
    
            Returns
            -------
            ``.StructExpression``
                Struct of all row fields.
            """
            return self._row
    
        @property
        def row_value(self) -> 'StructExpression':
            """Returns a struct expression including all non-key row-indexed fields.
    
            Examples
            --------
            Get the first five non-key row field names:
    
                >>> list(dataset.row_value)[:5]
                ['rsid', 'qual', 'filters', 'info', 'use_as_marker']
    
            Returns
            -------
            ``.StructExpression``
                Struct of all row fields, minus keys.
            """
            return self._row.drop(*self.row_key)
    
        @property
        def col(self) -> 'StructExpression':
            """Returns a struct expression of all column-indexed fields, including keys.
    
            Examples
            --------
            Get all column field names:
    
            >>> list(dataset.col)  # doctest: +SKIP_OUTPUT_CHECK
            ['s', 'sample_qc', 'is_case', 'pheno', 'cov', 'cov1', 'cov2', 'cohorts', 'pop']
    
            Returns
            -------
            ``.StructExpression``
                Struct of all column fields.
            """
            return self._col
    
        @property
        def col_value(self) -> 'StructExpression':
            """Returns a struct expression including all non-key column-indexed fields.
    
            Examples
            --------
            Get all non-key column field names:
    
            >>> list(dataset.col_value)  # doctest: +SKIP_OUTPUT_CHECK
            ['sample_qc', 'is_case', 'pheno', 'cov', 'cov1', 'cov2', 'cohorts', 'pop']
    
            Returns
            -------
            ``.StructExpression``
                Struct of all column fields, minus keys.
            """
            return self._col.drop(*self.col_key)
    
        @property
        def entry(self) -> 'StructExpression':
            """Returns a struct expression including all row-and-column-indexed fields.
    
            Examples
            --------
            Get all entry field names:
    
            >>> list(dataset.entry)
            ['GT', 'AD', 'DP', 'GQ', 'PL']
    
    
            Returns
            -------
            ``.StructExpression``
                Struct of all entry fields.
            """
            return self._entry
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.key_cols_by)    @typecheck_method(keys=oneof(str, Expression), named_keys=expr_any)
        def key_cols_by(self, *keys, **named_keys) -> 'MatrixTable':
            """Key columns by a new set of fields.
    
            See ``.Table.key_by`` for more information on defining a key.
    
            Parameters
            ----------
            keys : varargs of ``str`` or ``.Expression``.
                Column fields to key by.
            named_keys : keyword args of ``.Expression``.
                Column fields to key by.
            Returns
            -------
            ``.MatrixTable``
            """
            key_fields, computed_keys = get_key_by_exprs("MatrixTable.key_cols_by", keys, named_keys, self._col_indices)
    
            if not computed_keys:
                return MatrixTable(ir.MatrixMapCols(self._mir, self._col._ir, key_fields))
            else:
                new_col = self.col.annotate(**computed_keys)
                base, cleanup = self._process_joins(new_col)
    
                return cleanup(MatrixTable(ir.MatrixMapCols(base._mir, new_col._ir, key_fields)))
    
    
    
        @typecheck_method(new_key=str)
        def _key_rows_by_assert_sorted(self, *new_key):
            rk_names = list(self.row_key)
            i = 0
            while i < min(len(new_key), len(rk_names)):
                if new_key[i] != rk_names[i]:
                    break
                i += 1
    
            if i < 1:
                raise ValueError(
                    f'cannot implement an unsafe sort with no shared key:\n  new key: {new_key}\n  old key: {rk_names}'
                )
    
            return MatrixTable(ir.MatrixKeyRowsBy(self._mir, list(new_key), is_sorted=True))
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.key_rows_by)    @typecheck_method(keys=oneof(str, Expression), named_keys=expr_any)
        def key_rows_by(self, *keys, **named_keys) -> 'MatrixTable':
            """Key rows by a new set of fields.
    
            Examples
            --------
    
            >>> dataset_result = dataset.key_rows_by('locus')
            >>> dataset_result = dataset.key_rows_by(dataset['locus'])
            >>> dataset_result = dataset.key_rows_by(**dataset.row_key.drop('alleles'))
    
            All of these expressions key the dataset by the 'locus' field, dropping
            the 'alleles' field from the row key.
    
            >>> dataset_result = dataset.key_rows_by(contig=dataset['locus'].contig,
            ...                                      position=dataset['locus'].position,
            ...                                      alleles=dataset['alleles'])
    
            This keys the dataset by the newly defined fields, 'contig' and 'position',
            and the 'alleles' field. The old row key field, 'locus', is preserved as
            a non-key field.
    
            Notes
            -----
            See ``.Table.key_by`` for more information on defining a key.
    
            Parameters
            ----------
            keys : varargs of ``str`` or ``.Expression``.
                Row fields to key by.
            named_keys : keyword args of ``.Expression``.
                Row fields to key by.
            Returns
            -------
            ``.MatrixTable``
            """
            key_fields, computed_keys = get_key_by_exprs("MatrixTable.key_rows_by", keys, named_keys, self._row_indices)
    
            if not computed_keys:
                return MatrixTable(ir.MatrixKeyRowsBy(self._mir, key_fields))
            else:
                new_row = self._rvrow.annotate(**computed_keys)
                base, cleanup = self._process_joins(new_row)
    
                return cleanup(
                    MatrixTable(
                        ir.MatrixKeyRowsBy(
                            ir.MatrixMapRows(ir.MatrixKeyRowsBy(base._mir, []), new_row._ir), list(key_fields)
                        )
                    )
                )
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.annotate_globals)    @typecheck_method(named_exprs=expr_any)
        def annotate_globals(self, **named_exprs) -> 'MatrixTable':
            """Create new global fields by name.
    
            Examples
            --------
            Add two global fields:
    
            >>> pops_1kg = {'EUR', 'AFR', 'EAS', 'SAS', 'AMR'}
            >>> dataset_result = dataset.annotate_globals(pops_in_1kg = pops_1kg,
            ...                                           gene_list = ['SHH', 'SCN1A', 'SPTA1', 'DISC1'])
    
            Add global fields from another table and matrix table:
    
            >>> dataset_result = dataset.annotate_globals(thing1 = dataset2.index_globals().global_field,
            ...                                           thing2 = v_metadata.index_globals().global_field)
    
            Note
            ----
            This method does not support aggregation.
    
            Notes
            -----
            This method creates new global fields, but can also overwrite existing fields. Only
            same-scope fields can be overwritten: for example, it is not possible to annotate a
            row field `foo` and later create an global field `foo`. However, it would be possible
            to create an global field `foo` and later create another global field `foo`, overwriting
            the first.
    
            The arguments to the method should either be ``.Expression``
            objects, or should be implicitly interpretable as expressions.
    
            Parameters
            ----------
            named_exprs : keyword args of ``.Expression``
                Field names and the expressions to compute them.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix table with new global field(s).
            """
    
            caller = "MatrixTable.annotate_globals"
            check_annotate_exprs(caller, named_exprs, self._global_indices, set())
            return self._select_globals(caller, self.globals.annotate(**named_exprs))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.annotate_rows)    @typecheck_method(named_exprs=expr_any)
        def annotate_rows(self, **named_exprs) -> 'MatrixTable':
            """Create new row-indexed fields by name.
    
            Examples
            --------
            Compute call statistics for high quality samples per variant:
    
            >>> high_quality_calls = hl.agg.filter(dataset.sample_qc.gq_stats.mean > 20,
            ...                                    hl.agg.call_stats(dataset.GT, dataset.alleles))
            >>> dataset_result = dataset.annotate_rows(call_stats = high_quality_calls)
    
            Add functional annotations from a ``.Table``, `v_metadata`, and a
            ``.MatrixTable``, `dataset2_AF`, both keyed by locus and alleles.
    
            >>> dataset_result = dataset.annotate_rows(consequence = v_metadata[dataset.locus, dataset.alleles].consequence,
            ...                                        dataset2_AF = dataset2.index_rows(dataset.row_key).info.AF)
    
            Note
            ----
            This method supports aggregation over columns. For instance, the usage:
    
            >>> dataset_result = dataset.annotate_rows(mean_GQ = hl.agg.mean(dataset.GQ))
    
            will compute the mean per row.
    
            Notes
            -----
            This method creates new row fields, but can also overwrite existing fields. Only
            non-key, same-scope fields can be overwritten: for example, it is not possible
            to annotate a global field `foo` and later create an row field `foo`. However,
            it would be possible to create an row field `foo` and later create another row
            field `foo`, overwriting the first, as long as `foo` is not a row key.
    
            The arguments to the method should either be ``.Expression``
            objects, or should be implicitly interpretable as expressions.
    
            Parameters
            ----------
            named_exprs : keyword args of ``.Expression``
                Field names and the expressions to compute them.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix table with new row-indexed field(s).
            """
    
            caller = "MatrixTable.annotate_rows"
            check_annotate_exprs(caller, named_exprs, self._row_indices, {self._col_axis})
            return self._select_rows(caller, self._rvrow.annotate(**named_exprs))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.annotate_cols)    @typecheck_method(named_exprs=expr_any)
        def annotate_cols(self, **named_exprs) -> 'MatrixTable':
            """Create new column-indexed fields by name.
    
            Examples
            --------
            Compute statistics about the GQ distribution per sample:
    
            >>> dataset_result = dataset.annotate_cols(sample_gq_stats = hl.agg.stats(dataset.GQ))
    
            Add sample metadata from a ``.hail.Table``.
    
            >>> dataset_result = dataset.annotate_cols(population = s_metadata[dataset.s].pop)
    
            Note
            ----
            This method supports aggregation over rows. For instance, the usage:
    
            >>> dataset_result = dataset.annotate_cols(mean_GQ = hl.agg.mean(dataset.GQ))
    
            will compute the mean per column.
    
            Notes
            -----
            This method creates new column fields, but can also overwrite existing fields. Only
            same-scope fields can be overwritten: for example, it is not possible to annotate a
            global field `foo` and later create an column field `foo`. However, it would be possible
            to create an column field `foo` and later create another column field `foo`, overwriting
            the first.
    
            The arguments to the method should either be ``.Expression``
            objects, or should be implicitly interpretable as expressions.
    
            Parameters
            ----------
            named_exprs : keyword args of ``.Expression``
                Field names and the expressions to compute them.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix table with new column-indexed field(s).
            """
            caller = "MatrixTable.annotate_cols"
            check_annotate_exprs(caller, named_exprs, self._col_indices, {self._row_axis})
            return self._select_cols(caller, self.col.annotate(**named_exprs))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.annotate_entries)    @typecheck_method(named_exprs=expr_any)
        def annotate_entries(self, **named_exprs) -> 'MatrixTable':
            """Create new row-and-column-indexed fields by name.
    
            Examples
            --------
            Compute the allele dosage using the PL field:
    
            >>> def get_dosage(pl):
            ...    # convert to linear scale
            ...    linear_scaled = pl.map(lambda x: 10 ** - (x / 10))
            ...
            ...    # normalize to sum to 1
            ...    ls_sum = hl.sum(linear_scaled)
            ...    linear_scaled = linear_scaled.map(lambda x: x / ls_sum)
            ...
            ...    # multiply by [0, 1, 2] and sum
            ...    return hl.sum(linear_scaled * [0, 1, 2])
            >>>
            >>> dataset_result = dataset.annotate_entries(dosage = get_dosage(dataset.PL))
    
            Note
            ----
            This method does not support aggregation.
    
            Notes
            -----
            This method creates new entry fields, but can also overwrite existing fields. Only
            same-scope fields can be overwritten: for example, it is not possible to annotate a
            global field `foo` and later create an entry field `foo`. However, it would be possible
            to create an entry field `foo` and later create another entry field `foo`, overwriting
            the first.
    
            The arguments to the method should either be ``.Expression``
            objects, or should be implicitly interpretable as expressions.
    
            Parameters
            ----------
            named_exprs : keyword args of ``.Expression``
                Field names and the expressions to compute them.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix table with new row-and-column-indexed field(s).
            """
            caller = "MatrixTable.annotate_entries"
            check_annotate_exprs(caller, named_exprs, self._entry_indices, set())
            return self._select_entries(caller, s=self.entry.annotate(**named_exprs))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.select_globals)    def select_globals(self, *exprs, **named_exprs) -> 'MatrixTable':
            """Select existing global fields or create new fields by name, dropping the rest.
    
            Examples
            --------
            Select one existing field and compute a new one:
    
            >>> dataset_result = dataset.select_globals(dataset.global_field_1,
            ...                                         another_global=['AFR', 'EUR', 'EAS', 'AMR', 'SAS'])
    
            Notes
            -----
            This method creates new global fields. If a created field shares its name
            with a differently-indexed field of the table, the method will fail.
    
            Note
            ----
    
            See ``.Table.select`` for more information about using ``select`` methods.
    
            Note
            ----
            This method does not support aggregation.
    
            Parameters
            ----------
            exprs : variable-length args of ``str`` or ``.Expression``
                Arguments that specify field names or nested field reference expressions.
            named_exprs : keyword args of ``.Expression``
                Field names and the expressions to compute them.
    
            Returns
            -------
            ``.MatrixTable``
                MatrixTable with specified global fields.
            """
    
            caller = 'MatrixTable.select_globals'
            new_global = get_select_exprs(caller, exprs, named_exprs, self._global_indices, self._globals)
            return self._select_globals(caller, new_global)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.select_rows)    def select_rows(self, *exprs, **named_exprs) -> 'MatrixTable':
            """Select existing row fields or create new fields by name, dropping all
            other non-key fields.
    
            Examples
            --------
            Select existing fields and compute a new one:
    
            >>> dataset_result = dataset.select_rows(
            ...    dataset.variant_qc.gq_stats.mean,
            ...    high_quality_cases = hl.agg.count_where((dataset.GQ > 20) &
            ...                                         dataset.is_case))
    
            Notes
            -----
            This method creates new row fields. If a created field shares its name
            with a differently-indexed field of the table, or with a row key, the
            method will fail.
    
            Row keys are preserved. To drop or change a row key field, use
            ``MatrixTable.key_rows_by``.
    
            Note
            ----
    
            See ``.Table.select`` for more information about using ``select`` methods.
    
            Note
            ----
            This method supports aggregation over columns. For instance, the usage:
    
            >>> dataset_result = dataset.select_rows(mean_GQ = hl.agg.mean(dataset.GQ))
    
            will compute the mean per row.
    
            Parameters
            ----------
            exprs : variable-length args of ``str`` or ``.Expression``
                Arguments that specify field names or nested field reference expressions.
            named_exprs : keyword args of ``.Expression``
                Field names and the expressions to compute them.
    
            Returns
            -------
            ``.MatrixTable``
                MatrixTable with specified row fields.
            """
            caller = 'MatrixTable.select_rows'
            new_row = get_select_exprs(caller, exprs, named_exprs, self._row_indices, self._rvrow)
            return self._select_rows(caller, new_row)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.select_cols)    def select_cols(self, *exprs, **named_exprs) -> 'MatrixTable':
            """Select existing column fields or create new fields by name, dropping the rest.
    
            Examples
            --------
            Select existing fields and compute a new one:
    
            >>> dataset_result = dataset.select_cols(
            ...     dataset.sample_qc,
            ...     dataset.pheno.age,
            ...     isCohort1 = dataset.pheno.cohort_name == 'Cohort1')
    
            Notes
            -----
            This method creates new column fields. If a created field shares its name
            with a differently-indexed field of the table, the method will fail.
    
            Note
            ----
    
            See ``.Table.select`` for more information about using ``select`` methods.
    
            Note
            ----
            This method supports aggregation over rows. For instance, the usage:
    
            >>> dataset_result = dataset.select_cols(mean_GQ = hl.agg.mean(dataset.GQ))
    
            will compute the mean per column.
    
            Parameters
            ----------
            exprs : variable-length args of ``str`` or ``.Expression``
                Arguments that specify field names or nested field reference expressions.
            named_exprs : keyword args of ``.Expression``
                Field names and the expressions to compute them.
    
            Returns
            -------
            ``.MatrixTable``
                MatrixTable with specified column fields.
            """
            caller = 'MatrixTable.select_cols'
            new_col = get_select_exprs(caller, exprs, named_exprs, self._col_indices, self._col)
            return self._select_cols(caller, new_col)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.select_entries)    def select_entries(self, *exprs, **named_exprs) -> 'MatrixTable':
            """Select existing entry fields or create new fields by name, dropping the rest.
    
            Examples
            --------
            Drop all entry fields aside from `GT`:
    
            >>> dataset_result = dataset.select_entries(dataset.GT)
    
            Notes
            -----
            This method creates new entry fields. If a created field shares its name
            with a differently-indexed field of the table, the method will fail.
    
            Note
            ----
    
            See ``.Table.select`` for more information about using ``select`` methods.
    
            Note
            ----
            This method does not support aggregation.
    
            Parameters
            ----------
            exprs : variable-length args of ``str`` or ``.Expression``
                Arguments that specify field names or nested field reference expressions.
            named_exprs : keyword args of ``.Expression``
                Field names and the expressions to compute them.
    
            Returns
            -------
            ``.MatrixTable``
                MatrixTable with specified entry fields.
            """
            caller = 'MatrixTable.select_entries'
            new_entry = get_select_exprs(caller, exprs, named_exprs, self._entry_indices, self._entry)
            return self._select_entries(caller, new_entry)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.drop)    @typecheck_method(exprs=oneof(str, Expression))
        def drop(self, *exprs) -> 'MatrixTable':
            """Drop fields.
    
            Examples
            --------
    
            Drop fields `PL` (an entry field), `info` (a row field), and `pheno` (a column
            field): using strings:
    
            >>> dataset_result = dataset.drop('PL', 'info', 'pheno')
    
            Drop fields `PL` (an entry field), `info` (a row field), and `pheno` (a column
            field): using field references:
    
            >>> dataset_result = dataset.drop(dataset.PL, dataset.info, dataset.pheno)
    
            Drop a list of fields:
    
            >>> fields_to_drop = ['PL', 'info', 'pheno']
            >>> dataset_result = dataset.drop(*fields_to_drop)
    
            Notes
            -----
    
            This method can be used to drop global, row-indexed, column-indexed, or
            row-and-column-indexed (entry) fields. The arguments can be either strings
            (``'field'``), or top-level field references (``table.field`` or
            ``table['field']``).
    
            Key fields (belonging to either the row key or the column key) cannot be
            dropped using this method. In order to drop a key field, use ``.key_rows_by``
            or ``.key_cols_by`` to remove the field from the key before dropping.
    
            While many operations exist independently for rows, columns, entries, and
            globals, only one is needed for dropping due to the lack of any necessary
            contextual information.
    
            Parameters
            ----------
            exprs : varargs of ``str`` or ``.Expression``
                Names of fields to drop or field reference expressions.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix table without specified fields.
            """
    
            def check_key(name, keys):
                if name in keys:
                    raise ValueError("MatrixTable.drop: cannot drop key field '{}'".format(name))
                return name
    
            all_field_exprs = {e: k for k, e in self._fields.items()}
            fields_to_drop = set()
            for e in exprs:
                if isinstance(e, Expression):
                    if e in all_field_exprs:
                        fields_to_drop.add(all_field_exprs[e])
                    else:
                        raise ExpressionException(
                            "Method 'drop' expects string field names or top-level field expressions"
                            " (e.g. 'foo', matrix.foo, or matrix['foo'])"
                        )
                else:
                    assert isinstance(e, str)
                    if e not in self._fields:
                        raise IndexError("MatrixTable has no field '{}'".format(e))
                    fields_to_drop.add(e)
    
            m = self
            global_fields = [field for field in fields_to_drop if self._fields[field]._indices == self._global_indices]
            if global_fields:
                m = m._select_globals("MatrixTable.drop", m.globals.drop(*global_fields))
    
            row_fields = [
                check_key(field, list(self.row_key))
                for field in fields_to_drop
                if self._fields[field]._indices == self._row_indices
            ]
            if row_fields:
                m = m._select_rows("MatrixTable.drop", row=m.row.drop(*row_fields))
    
            col_fields = [
                check_key(field, list(self.col_key))
                for field in fields_to_drop
                if self._fields[field]._indices == self._col_indices
            ]
            if col_fields:
                m = m._select_cols("MatrixTable.drop", m.col.drop(*col_fields))
    
            entry_fields = [field for field in fields_to_drop if self._fields[field]._indices == self._entry_indices]
            if entry_fields:
                m = m._select_entries("MatrixTable.drop", m.entry.drop(*entry_fields))
    
            return m
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.semi_join_rows)    @typecheck_method(other=Table)
        def semi_join_rows(self, other: 'Table') -> 'MatrixTable':
            """Filters the matrix table to rows whose key appears in `other`.
    
            Parameters
            ----------
            other : ``.Table``
                Table with compatible key field(s).
    
            Returns
            -------
            ``.MatrixTable``
    
            Notes
            -----
            The row key type of the matrix table must match the key type of `other`.
    
            This method does not change the schema of the matrix table; it is
            filtering the matrix table to row keys present in another table.
    
            To discard rows whose key is present in `other`, use
            ``.anti_join_rows``.
    
            Examples
            --------
            >>> ds_result = ds.semi_join_rows(rows_to_keep)
    
            It may be expensive to key the matrix table by the right-side key.
            In this case, it is possible to implement a semi-join using a non-key
            field as follows:
    
            >>> ds_result = ds.filter_rows(hl.is_defined(rows_to_keep.index(ds['locus'], ds['alleles'])))
    
            See Also
            --------
            ``.anti_join_rows``, ``.filter_rows``, ``.semi_join_cols``
            """
            if len(other.key) == 0:
                raise ValueError('semi_join_rows: cannot join with a table with no key')
            if len(other.key) > len(self.row_key) or any(
                t[0].dtype != t[1].dtype for t in zip(self.row_key.values(), other.key.values())
            ):
                raise ValueError(
                    'semi_join_rows: cannot join: table must have a key of the same type(s) and be the same length or shorter:'
                    f'\n  MatrixTable row key: {", ".join(str(x.dtype) for x in self.row_key.values())}'
                    f'\n            Table key: {", ".join(str(x.dtype) for x in other.key.values())}'
                )
            return self.filter_rows(hl.is_defined(other.index(*(self.row_key[i] for i in range(len(other.key))))))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.anti_join_rows)    @typecheck_method(other=Table)
        def anti_join_rows(self, other: 'Table') -> 'MatrixTable':
            """Filters the table to rows whose key does not appear in `other`.
    
            Parameters
            ----------
            other : ``.Table``
                Table with compatible key field(s).
    
            Returns
            -------
            ``.MatrixTable``
    
            Notes
            -----
            The row key type of the matrix table must match the key type of `other`.
    
            This method does not change the schema of the table; it is a method of
            filtering the matrix table to row keys not present in another table.
    
            To restrict to rows whose key is present in `other`, use
            ``.semi_join_rows``.
    
            Examples
            --------
            >>> ds_result = ds.anti_join_rows(rows_to_remove)
    
            It may be expensive to key the matrix table by the right-side key.
            In this case, it is possible to implement an anti-join using a non-key
            field as follows:
    
            >>> ds_result = ds.filter_rows(hl.is_missing(rows_to_remove.index(ds['locus'], ds['alleles'])))
    
            See Also
            --------
            ``.anti_join_rows``, ``.filter_rows``, ``.anti_join_cols``
            """
            if len(other.key) == 0:
                raise ValueError('anti_join_rows: cannot join with a table with no key')
            if len(other.key) > len(self.row_key) or any(
                t[0].dtype != t[1].dtype for t in zip(self.row_key.values(), other.key.values())
            ):
                raise ValueError(
                    'anti_join_rows: cannot join: table must have a key of the same type(s) and be the same length or shorter:'
                    f'\n  MatrixTable row key: {", ".join(str(x.dtype) for x in self.row_key.values())}'
                    f'\n            Table key: {", ".join(str(x.dtype) for x in other.key.values())}'
                )
            return self.filter_rows(hl.is_missing(other.index(*(self.row_key[i] for i in range(len(other.key))))))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.semi_join_cols)    @typecheck_method(other=Table)
        def semi_join_cols(self, other: 'Table') -> 'MatrixTable':
            """Filters the matrix table to columns whose key appears in `other`.
    
            Parameters
            ----------
            other : ``.Table``
                Table with compatible key field(s).
    
            Returns
            -------
            ``.MatrixTable``
    
            Notes
            -----
            The column key type of the matrix table must match the key type of `other`.
    
            This method does not change the schema of the matrix table; it is a
            filtering the matrix table to column keys not present in another table.
    
            To discard collumns whose key is present in `other`, use
            ``.anti_join_cols``.
    
            Examples
            --------
            >>> ds_result = ds.semi_join_cols(cols_to_keep)
    
            It may be inconvenient to key the matrix table by the right-side key.
            In this case, it is possible to implement a semi-join using a non-key
            field as follows:
    
            >>> ds_result = ds.filter_cols(hl.is_defined(cols_to_keep.index(ds['s'])))
    
            See Also
            --------
            ``.anti_join_cols``, ``.filter_cols``, ``.semi_join_rows``
            """
            if len(other.key) == 0:
                raise ValueError('semi_join_cols: cannot join with a table with no key')
            if len(other.key) > len(self.col_key) or any(
                t[0].dtype != t[1].dtype for t in zip(self.col_key.values(), other.key.values())
            ):
                raise ValueError(
                    'semi_join_cols: cannot join: table must have a key of the same type(s) and be the same length or shorter:'
                    f'\n  MatrixTable col key: {", ".join(str(x.dtype) for x in self.col_key.values())}'
                    f'\n            Table key: {", ".join(str(x.dtype) for x in other.key.values())}'
                )
    
            return self.filter_cols(hl.is_defined(other.index(*(self.col_key[i] for i in range(len(other.key))))))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.anti_join_cols)    @typecheck_method(other=Table)
        def anti_join_cols(self, other: 'Table') -> 'MatrixTable':
            """Filters the table to columns whose key does not appear in `other`.
    
            Parameters
            ----------
            other : ``.Table``
                Table with compatible key field(s).
    
            Returns
            -------
            ``.MatrixTable``
    
            Notes
            -----
            The column key type of the matrix table must match the key type of `other`.
    
            This method does not change the schema of the table; it is a method of
            filtering the matrix table to column keys not present in another table.
    
            To restrict to columns whose key is present in `other`, use
            ``.semi_join_cols``.
    
            Examples
            --------
            >>> ds_result = ds.anti_join_cols(cols_to_remove)
    
            It may be inconvenient to key the matrix table by the right-side key.
            In this case, it is possible to implement an anti-join using a non-key
            field as follows:
    
            >>> ds_result = ds.filter_cols(hl.is_missing(cols_to_remove.index(ds['s'])))
    
            See Also
            --------
            ``.semi_join_cols``, ``.filter_cols``, ``.anti_join_rows``
            """
            if len(other.key) == 0:
                raise ValueError('anti_join_cols: cannot join with a table with no key')
            if len(other.key) > len(self.col_key) or any(
                t[0].dtype != t[1].dtype for t in zip(self.col_key.values(), other.key.values())
            ):
                raise ValueError(
                    'anti_join_cols: cannot join: table must have a key of the same type(s) and be the same length or shorter:'
                    f'\n  MatrixTable col key: {", ".join(str(x.dtype) for x in self.col_key.values())}'
                    f'\n            Table key: {", ".join(str(x.dtype) for x in other.key.values())}'
                )
    
            return self.filter_cols(hl.is_missing(other.index(*(self.col_key[i] for i in range(len(other.key))))))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.filter_rows)    @typecheck_method(expr=expr_bool, keep=bool)
        def filter_rows(self, expr, keep: bool = True) -> 'MatrixTable':
            """Filter rows of the matrix.
    
            Examples
            --------
    
            Keep rows where `variant_qc.AF` is below 1%:
    
            >>> dataset_result = dataset.filter_rows(dataset.variant_qc.AF[1] < 0.01, keep=True)
    
            Remove rows where `filters` is non-empty:
    
            >>> dataset_result = dataset.filter_rows(dataset.filters.size() > 0, keep=False)
    
            Notes
            -----
            The expression `expr` will be evaluated for every row of the table. If
            `keep` is ``True``, then rows where `expr` evaluates to ``True`` will be
            kept (the filter removes the rows where the predicate evaluates to
            ``False``). If `keep` is ``False``, then rows where `expr` evaluates to
            ``True`` will be removed (the filter keeps the rows where the predicate
            evaluates to ``False``).
    
            Warning
            -------
            When `expr` evaluates to missing, the row will be removed regardless of `keep`.
    
            Note
            ----
            This method supports aggregation over columns. For instance,
    
            >>> dataset_result = dataset.filter_rows(hl.agg.mean(dataset.GQ) > 20.0)
    
            will remove rows where the mean GQ of all entries in the row is smaller than
            20.
    
            Parameters
            ----------
            expr : bool or ``.BooleanExpression``
                Filter expression.
            keep : bool
                Keep rows where `expr` is true.
    
            Returns
            -------
            ``.MatrixTable``
                Filtered matrix table.
            """
            caller = 'MatrixTable.filter_rows'
            analyze(caller, expr, self._row_indices, {self._col_axis})
    
            if expr._aggregations:
                bool_uid = Env.get_uid()
                mt = self._select_rows(caller, self.row.annotate(**{bool_uid: expr}))
                return mt.filter_rows(mt[bool_uid], keep).drop(bool_uid)
    
            base, cleanup = self._process_joins(expr)
            mt = MatrixTable(ir.MatrixFilterRows(base._mir, ir.filter_predicate_with_keep(expr._ir, keep)))
            return cleanup(mt)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.filter_cols)    @typecheck_method(expr=expr_bool, keep=bool)
        def filter_cols(self, expr, keep: bool = True) -> 'MatrixTable':
            """Filter columns of the matrix.
    
            Examples
            --------
    
            Keep columns where `pheno.is_case` is ``True`` and `pheno.age` is larger
            than 50:
    
            >>> dataset_result = dataset.filter_cols(dataset.pheno.is_case &
            ...                                      (dataset.pheno.age > 50),
            ...                                      keep=True)
    
            Remove columns where `sample_qc.gq_stats.mean` is less than 20:
    
            >>> dataset_result = dataset.filter_cols(dataset.sample_qc.gq_stats.mean < 20,
            ...                                      keep=False)
    
            Remove columns where `s` is found in a Python set:
    
            >>> samples_to_remove = {'NA12878', 'NA12891', 'NA12892'}
            >>> set_to_remove = hl.literal(samples_to_remove)
            >>> dataset_result = dataset.filter_cols(~set_to_remove.contains(dataset['s']))
    
            Notes
            -----
            The expression `expr` will be evaluated for every column of the table.
            If `keep` is ``True``, then columns where `expr` evaluates to ``True``
            will be kept (the filter removes the columns where the predicate
            evaluates to ``False``). If `keep` is ``False``, then columns where
            `expr` evaluates to ``True`` will be removed (the filter keeps the
            columns where the predicate evaluates to ``False``).
    
            Warning
            -------
            When `expr` evaluates to missing, the column will be removed regardless of
            `keep`.
    
            Note
            ----
            This method supports aggregation over rows. For instance,
    
            >>> dataset_result = dataset.filter_cols(hl.agg.mean(dataset.GQ) > 20.0)
    
            will remove columns where the mean GQ of all entries in the column is smaller
            than 20.
    
            Parameters
            ----------
            expr : bool or ``.BooleanExpression``
                Filter expression.
            keep : bool
                Keep columns where `expr` is true.
    
            Returns
            -------
            ``.MatrixTable``
                Filtered matrix table.
            """
            caller = 'MatrixTable.filter_cols'
            analyze(caller, expr, self._col_indices, {self._row_axis})
    
            if expr._aggregations:
                bool_uid = Env.get_uid()
                mt = self._select_cols(caller, self.col.annotate(**{bool_uid: expr}))
                return mt.filter_cols(mt[bool_uid], keep).drop(bool_uid)
    
            base, cleanup = self._process_joins(expr)
            mt = MatrixTable(ir.MatrixFilterCols(base._mir, ir.filter_predicate_with_keep(expr._ir, keep)))
            return cleanup(mt)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.filter_entries)    @typecheck_method(expr=expr_bool, keep=bool)
        def filter_entries(self, expr, keep: bool = True) -> 'MatrixTable':
            """Filter entries of the matrix.
    
            Parameters
            ----------
            expr : bool or ``.BooleanExpression``
                Filter expression.
            keep : bool
                Keep entries where `expr` is true.
    
            Returns
            -------
            ``.MatrixTable``
                Filtered matrix table.
    
            Examples
            --------
    
            Keep entries where the sum of `AD` is greater than 10 and `GQ` is greater than 20:
    
            >>> dataset_result = dataset.filter_entries((hl.sum(dataset.AD) > 10) & (dataset.GQ > 20))
    
            Warning
            -------
            When `expr` evaluates to missing, the entry will be removed regardless of
            `keep`.
    
            Note
            ----
            This method does not support aggregation.
    
            Notes
            -----
            The expression `expr` will be evaluated for every entry of the table.
            If `keep` is ``True``, then entries where `expr` evaluates to ``True``
            will be kept (the filter removes the entries where the predicate
            evaluates to ``False``). If `keep` is ``False``, then entries where
            `expr` evaluates to ``True`` will be removed (the filter keeps the
            entries where the predicate evaluates to ``False``).
    
            Filtered entries are removed entirely from downstream operations. This
            means that the resulting matrix table has sparsity -- that is, that the
            number of entries is **smaller** than the product of ``count_rows``
            and ``count_cols``. To re-densify a filtered matrix table, use the
            ``unfilter_entries`` method to restore filtered entries, populated
            all fields with missing values. Below are some properties of an
            entry-filtered matrix table.
    
            1. Filtered entries are not included in the ``entries`` table.
    
            >>> mt_range = hl.utils.range_matrix_table(10, 10)
            >>> mt_range = mt_range.annotate_entries(x = mt_range.row_idx + mt_range.col_idx)
            >>> mt_range.count()
            (10, 10)
    
            >>> mt_range.entries().count()
            100
    
            >>> mt_filt = mt_range.filter_entries(mt_range.x % 2 == 0)
            >>> mt_filt.count()
            (10, 10)
    
            >>> mt_filt.count_rows() * mt_filt.count_cols()
            100
    
            >>> mt_filt.entries().count()
            50
    
            2. Filtered entries are not included in aggregation.
    
            >>> mt_filt.aggregate_entries(hl.agg.count())
            50
    
            >>> mt_filt = mt_filt.annotate_cols(col_n = hl.agg.count())
            >>> mt_filt.col_n.take(5)
            [5, 5, 5, 5, 5]
    
            >>> mt_filt = mt_filt.annotate_rows(row_n = hl.agg.count())
            >>> mt_filt.row_n.take(5)
            [5, 5, 5, 5, 5]
    
            3. Annotating a new entry field will not annotate filtered entries.
    
            >>> mt_filt = mt_filt.annotate_entries(y = 1)
            >>> mt_filt.aggregate_entries(hl.agg.sum(mt_filt.y))
            50
    
            4. If all the entries in a row or column of a matrix table are
            filtered, the row or column remains.
    
            >>> mt_filt.filter_entries(False).count()
            (10, 10)
    
            See Also
            --------
            ``unfilter_entries``, ``compute_entry_filter_stats``
            """
            base, cleanup = self._process_joins(expr)
            analyze('MatrixTable.filter_entries', expr, self._entry_indices)
    
            m = MatrixTable(ir.MatrixFilterEntries(base._mir, ir.filter_predicate_with_keep(expr._ir, keep)))
            return cleanup(m)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.unfilter_entries)    def unfilter_entries(self):
            """Unfilters filtered entries, populating fields with missing values.
    
            Returns
            -------
            ``MatrixTable``
    
            Notes
            -----
            This method is used in the case that a pipeline downstream of ``filter_entries``
            requires a fully dense (no filtered entries) matrix table.
    
            Generally, if this method is required in a pipeline, the upstream pipeline can
            be rewritten to use annotation instead of entry filtering.
    
            See Also
            --------
            ``filter_entries``, ``compute_entry_filter_stats``
            """
            entry_ir = hl.if_else(
                hl.is_defined(self.entry), self.entry, hl.struct(**{k: hl.missing(v.dtype) for k, v in self.entry.items()})
            )._ir
            return MatrixTable(ir.MatrixMapEntries(self._mir, entry_ir))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.compute_entry_filter_stats)    @typecheck_method(row_field=str, col_field=str)
        def compute_entry_filter_stats(self, row_field='entry_stats_row', col_field='entry_stats_col') -> 'MatrixTable':
            """Compute statistics about the number and fraction of filtered entries.
    
            .. include:: _templates/experimental.rst
    
            Parameters
            ----------
            row_field : ``str``
                Name for computed row field (default: ``entry_stats_row``.
            col_field : ``str``
                Name for computed column field (default: ``entry_stats_col``.
    
            Returns
            -------
            ``.MatrixTable``
    
            Notes
            -----
            Adds a new row field, `row_field`, and a new column field, `col_field`,
            each of which are structs with the following fields:
    
             - *n_filtered* (``.tint64``) - Number of filtered entries per row
               or column.
             - *n_remaining* (``.tint64``) - Number of entries not filtered per
               row or column.
             - *fraction_filtered* (``.tfloat32``) - Number of filtered entries
               divided by the total number of filtered and remaining entries.
    
            See Also
            --------
            ``filter_entries``, ``unfilter_entries``
            """
    
            def result(count):
                return hl.rbind(
                    count,
                    hl.agg.count(),
                    lambda n_tot, n_def: hl.struct(
                        n_filtered=n_tot - n_def, n_remaining=n_def, fraction_filtered=(n_tot - n_def) / n_tot
                    ),
                )
    
            mt = self
            mt = mt.annotate_cols(**{col_field: result(mt.count_rows(_localize=False))})
            mt = mt.annotate_rows(**{row_field: result(mt.count_cols(_localize=False))})
            return mt
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.transmute_globals)    @typecheck_method(named_exprs=expr_any)
        def transmute_globals(self, **named_exprs) -> 'MatrixTable':
            """Similar to ``.MatrixTable.annotate_globals``, but drops referenced fields.
    
            Notes
            -----
            This method adds new global fields according to `named_exprs`, and
            drops all global fields referenced in those expressions. See
            ``.Table.transmute`` for full documentation on how transmute
            methods work.
    
            See Also
            --------
            ``.Table.transmute``, ``.MatrixTable.select_globals``,
            ``.MatrixTable.annotate_globals``
    
            Parameters
            ----------
            named_exprs : keyword args of ``.Expression``
                Annotation expressions.
    
            Returns
            -------
            ``.MatrixTable``
            """
            caller = 'MatrixTable.transmute_globals'
            check_annotate_exprs(caller, named_exprs, self._global_indices, set())
            fields_referenced = extract_refs_by_indices(named_exprs.values(), self._global_indices) - set(
                named_exprs.keys()
            )
            return self._select_globals(caller, self.globals.annotate(**named_exprs).drop(*fields_referenced))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.transmute_rows)    @typecheck_method(named_exprs=expr_any)
        def transmute_rows(self, **named_exprs) -> 'MatrixTable':
            """Similar to ``.MatrixTable.annotate_rows``, but drops referenced fields.
    
            Notes
            -----
            This method adds new row fields according to `named_exprs`, and drops
            all row fields referenced in those expressions. See
            ``.Table.transmute`` for full documentation on how transmute
            methods work.
    
            Note
            ----
            ``transmute_rows`` will not drop key fields.
    
            Note
            ----
            This method supports aggregation over columns.
    
            See Also
            --------
            ``.Table.transmute``, ``.MatrixTable.select_rows``,
            ``.MatrixTable.annotate_rows``
    
            Parameters
            ----------
            named_exprs : keyword args of ``.Expression``
                Annotation expressions.
    
            Returns
            -------
            ``.MatrixTable``
            """
            caller = 'MatrixTable.transmute_rows'
            check_annotate_exprs(caller, named_exprs, self._row_indices, {self._col_axis})
            fields_referenced = extract_refs_by_indices(named_exprs.values(), self._row_indices) - set(named_exprs.keys())
            fields_referenced -= set(self.row_key)
    
            return self._select_rows(caller, self.row.annotate(**named_exprs).drop(*fields_referenced))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.transmute_cols)    @typecheck_method(named_exprs=expr_any)
        def transmute_cols(self, **named_exprs) -> 'MatrixTable':
            """Similar to ``.MatrixTable.annotate_cols``, but drops referenced fields.
    
            Notes
            -----
            This method adds new column fields according to `named_exprs`, and
            drops all column fields referenced in those expressions. See
            ``.Table.transmute`` for full documentation on how transmute
            methods work.
    
            Note
            ----
            ``transmute_cols`` will not drop key fields.
    
            Note
            ----
            This method supports aggregation over rows.
    
            See Also
            --------
            ``.Table.transmute``, ``.MatrixTable.select_cols``,
            ``.MatrixTable.annotate_cols``
    
            Parameters
            ----------
            named_exprs : keyword args of ``.Expression``
                Annotation expressions.
    
            Returns
            -------
            ``.MatrixTable``
            """
            caller = 'MatrixTable.transmute_cols'
            check_annotate_exprs(caller, named_exprs, self._col_indices, {self._row_axis})
            fields_referenced = extract_refs_by_indices(named_exprs.values(), self._col_indices) - set(named_exprs.keys())
            fields_referenced -= set(self.col_key)
    
            return self._select_cols(caller, self.col.annotate(**named_exprs).drop(*fields_referenced))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.transmute_entries)    @typecheck_method(named_exprs=expr_any)
        def transmute_entries(self, **named_exprs) -> 'MatrixTable':
            """Similar to ``.MatrixTable.annotate_entries``, but drops referenced fields.
    
            Notes
            -----
            This method adds new entry fields according to `named_exprs`, and
            drops all entry fields referenced in those expressions. See
            ``.Table.transmute`` for full documentation on how transmute
            methods work.
    
            See Also
            --------
            ``.Table.transmute``, ``.MatrixTable.select_entries``,
            ``.MatrixTable.annotate_entries``
    
            Parameters
            ----------
            named_exprs : keyword args of ``.Expression``
                Annotation expressions.
    
            Returns
            -------
            ``.MatrixTable``
            """
            caller = 'MatrixTable.transmute_entries'
            check_annotate_exprs(caller, named_exprs, self._entry_indices, set())
            fields_referenced = extract_refs_by_indices(named_exprs.values(), self._entry_indices) - set(named_exprs.keys())
    
            return self._select_entries(caller, self.entry.annotate(**named_exprs).drop(*fields_referenced))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.aggregate_rows)    @typecheck_method(expr=expr_any, _localize=bool)
        def aggregate_rows(self, expr, _localize=True) -> Any:
            """Aggregate over rows to a local value.
    
            Examples
            --------
            Aggregate over rows:
    
            >>> dataset.aggregate_rows(hl.struct(n_high_quality=hl.agg.count_where(dataset.qual > 40),
            ...                                  mean_qual=hl.agg.mean(dataset.qual)))
            Struct(n_high_quality=9, mean_qual=140054.73333333334)
    
            Notes
            -----
            Unlike most ``.MatrixTable`` methods, this method does not support
            meaningful references to fields that are not global or indexed by row.
    
            This method should be thought of as a more convenient alternative to
            the following:
    
            >>> rows_table = dataset.rows()
            >>> rows_table.aggregate(hl.struct(n_high_quality=hl.agg.count_where(rows_table.qual > 40),
            ...                                mean_qual=hl.agg.mean(rows_table.qual)))
    
            Note
            ----
            This method supports (and expects!) aggregation over rows.
    
            Parameters
            ----------
            expr : ``.Expression``
                Aggregation expression.
    
            Returns
            -------
            any
                Aggregated value dependent on `expr`.
            """
            base, _ = self._process_joins(expr)
            analyze('MatrixTable.aggregate_rows', expr, self._global_indices, {self._row_axis})
            rows_table = ir.MatrixRowsTable(base._mir)
            subst_query = ir.subst(expr._ir, {}, {'va': ir.Ref('row', rows_table.typ.row_type)})
    
            agg_ir = ir.TableAggregate(rows_table, subst_query)
            if _localize:
                return Env.backend().execute(ir.MakeTuple([agg_ir]))[0]
            else:
                return construct_expr(ir.LiftMeOut(agg_ir), expr.dtype)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.aggregate_cols)    @typecheck_method(expr=expr_any, _localize=bool)
        def aggregate_cols(self, expr, _localize=True) -> Any:
            """Aggregate over columns to a local value.
    
            Examples
            --------
            Aggregate over columns:
    
            >>> dataset.aggregate_cols(
            ...    hl.struct(fraction_female=hl.agg.fraction(dataset.pheno.is_female),
            ...              case_ratio=hl.agg.count_where(dataset.is_case) / hl.agg.count()))
            Struct(fraction_female=0.44, case_ratio=1.0)
    
            Notes
            -----
            Unlike most ``.MatrixTable`` methods, this method does not support
            meaningful references to fields that are not global or indexed by column.
    
            This method should be thought of as a more convenient alternative to
            the following:
    
            >>> cols_table = dataset.cols()
            >>> cols_table.aggregate(
            ...     hl.struct(fraction_female=hl.agg.fraction(cols_table.pheno.is_female),
            ...               case_ratio=hl.agg.count_where(cols_table.is_case) / hl.agg.count()))
    
            Note
            ----
            This method supports (and expects!) aggregation over columns.
    
            Parameters
            ----------
            expr : ``.Expression``
                Aggregation expression.
    
            Returns
            -------
            any
                Aggregated value dependent on `expr`.
            """
            base, _ = self._process_joins(expr)
            analyze('MatrixTable.aggregate_cols', expr, self._global_indices, {self._col_axis})
    
            cols_field = Env.get_uid()
            globals = base.localize_entries(columns_array_field_name=cols_field).index_globals()
            if len(self._col_key) == 0:
                cols = globals[cols_field]
            else:
                if Env.hc()._warn_cols_order:
                    warning(
                        "aggregate_cols(): Aggregates over cols ordered by 'col_key'."
                        "\n    To preserve matrix table column order, "
                        "first unkey columns with 'key_cols_by()'"
                    )
                    Env.hc()._warn_cols_order = False
                cols = hl.sorted(globals[cols_field], key=lambda x: x.select(*self._col_key.keys()))
    
            agg_ir = ir.Let('global', globals.drop(cols_field)._ir, ir.StreamAgg(ir.ToStream(cols._ir), 'sa', expr._ir))
    
            if _localize:
                return Env.backend().execute(ir.MakeTuple([agg_ir]))[0]
            else:
                return construct_expr(agg_ir, expr.dtype)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.aggregate_entries)    @typecheck_method(expr=expr_any, _localize=bool)
        def aggregate_entries(self, expr, _localize=True):
            """Aggregate over entries to a local value.
    
            Examples
            --------
            Aggregate over entries:
    
            >>> dataset.aggregate_entries(hl.struct(global_gq_mean=hl.agg.mean(dataset.GQ),
            ...                                     call_rate=hl.agg.fraction(hl.is_defined(dataset.GT))))
            Struct(global_gq_mean=69.60514541387025, call_rate=0.9933333333333333)
    
            Notes
            -----
            This method should be thought of as a more convenient alternative to
            the following:
    
            >>> entries_table = dataset.entries()
            >>> entries_table.aggregate(hl.struct(global_gq_mean=hl.agg.mean(entries_table.GQ),
            ...                                   call_rate=hl.agg.fraction(hl.is_defined(entries_table.GT))))
    
            Note
            ----
            This method supports (and expects!) aggregation over entries.
    
            Parameters
            ----------
            expr : ``.Expression``
                Aggregation expressions.
    
            Returns
            -------
            any
                Aggregated value dependent on `expr`.
            """
    
            base, _ = self._process_joins(expr)
            analyze('MatrixTable.aggregate_entries', expr, self._global_indices, {self._row_axis, self._col_axis})
            agg_ir = ir.MatrixAggregate(base._mir, expr._ir)
            if _localize:
                return Env.backend().execute(ir.MakeTuple([agg_ir]))[0]
            else:
                return construct_expr(ir.LiftMeOut(agg_ir), expr.dtype)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.explode_rows)    @typecheck_method(field_expr=oneof(str, Expression))
        def explode_rows(self, field_expr) -> 'MatrixTable':
            """Explodes a row field of type array or set, copying the entire row for each element.
    
            Examples
            --------
            Explode rows by annotated genes:
    
            >>> dataset_result = dataset.explode_rows(dataset.gene)
    
            Notes
            -----
            The new matrix table will have `N` copies of each row, where `N` is the number
            of elements that row contains for the field denoted by `field_expr`. The field
            referenced in `field_expr` is replaced in the sequence of duplicated rows by the
            sequence of elements in the array or set. All other fields remain the same,
            including entry fields.
    
            If the field referenced with `field_expr` is missing or empty, the row is
            removed entirely.
    
            Parameters
            ----------
            field_expr : str or ``.Expression``
                Field name or (possibly nested) field reference expression.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix table exploded row-wise for each element of `field_expr`.
            """
            if isinstance(field_expr, str):
                if field_expr not in self._fields:
                    raise KeyError("MatrixTable has no field '{}'".format(field_expr))
                elif self._fields[field_expr]._indices != self._row_indices:
                    raise ExpressionException(
                        "Method 'explode_rows' expects a field indexed by row, found axes '{}'".format(
                            self._fields[field_expr]._indices.axes
                        )
                    )
                root = [field_expr]
                field_expr = self._fields[field_expr]
            else:
                analyze('MatrixTable.explode_rows', field_expr, self._row_indices, set(self._fields.keys()))
                if not field_expr._ir.is_nested_field:
                    raise ExpressionException(
                        "method 'explode_rows' requires a field or subfield, not a complex expression"
                    )
                nested = field_expr._ir
                root = []
                while isinstance(nested, ir.GetField):
                    root.append(nested.name)
                    nested = nested.o
                root = root[::-1]
    
            if not isinstance(field_expr.dtype, (tarray, tset)):
                raise ValueError(f"method 'explode_rows' expects array or set, found: {field_expr.dtype}")
    
            if self.row_key is not None:
                for k in self.row_key.values():
                    if k is field_expr:
                        raise ValueError("method 'explode_rows' cannot explode a key field")
    
            return MatrixTable(ir.MatrixExplodeRows(self._mir, root))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.explode_cols)    @typecheck_method(field_expr=oneof(str, Expression))
        def explode_cols(self, field_expr) -> 'MatrixTable':
            """Explodes a column field of type array or set, copying the entire column for each element.
    
            Examples
            --------
            Explode columns by annotated cohorts:
    
            >>> dataset_result = dataset.explode_cols(dataset.cohorts)
    
            Notes
            -----
            The new matrix table will have `N` copies of each column, where `N` is the
            number of elements that column contains for the field denoted by `field_expr`.
            The field referenced in `field_expr` is replaced in the sequence of duplicated
            columns by the sequence of elements in the array or set. All other fields remain
            the same, including entry fields.
    
            If the field referenced with `field_expr` is missing or empty, the column is
            removed entirely.
    
            Parameters
            ----------
            field_expr : str or ``.Expression``
                Field name or (possibly nested) field reference expression.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix table exploded column-wise for each element of `field_expr`.
            """
    
            if isinstance(field_expr, str):
                if field_expr not in self._fields:
                    raise KeyError("MatrixTable has no field '{}'".format(field_expr))
                elif self._fields[field_expr]._indices != self._col_indices:
                    raise ExpressionException(
                        "Method 'explode_cols' expects a field indexed by col, found axes '{}'".format(
                            self._fields[field_expr]._indices.axes
                        )
                    )
                root = [field_expr]
                field_expr = self._fields[field_expr]
            else:
                analyze('MatrixTable.explode_cols', field_expr, self._col_indices)
                if not field_expr._ir.is_nested_field:
                    raise ExpressionException(
                        "method 'explode_cols' requires a field or subfield, not a complex expression"
                    )
                root = []
                nested = field_expr._ir
                while isinstance(nested, ir.GetField):
                    root.append(nested.name)
                    nested = nested.o
                root = root[::-1]
    
            if not isinstance(field_expr.dtype, (tarray, tset)):
                raise ValueError(f"method 'explode_cols' expects array or set, found: {field_expr.dtype}")
    
            if self.col_key is not None:
                for k in self.col_key.values():
                    if k is field_expr:
                        raise ValueError("method 'explode_cols' cannot explode a key field")
    
            return MatrixTable(ir.MatrixExplodeCols(self._mir, root))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.group_rows_by)    @typecheck_method(exprs=oneof(str, Expression), named_exprs=expr_any)
        def group_rows_by(self, *exprs, **named_exprs) -> 'GroupedMatrixTable':
            """Group rows, used with ``.GroupedMatrixTable.aggregate``.
    
            Examples
            --------
            Aggregate to a matrix with genes as row keys, computing the number of
            non-reference calls as an entry field:
    
            >>> dataset_result = (dataset.group_rows_by(dataset.gene)
            ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    
            Notes
            -----
            All complex expressions must be passed as named expressions.
    
            Parameters
            ----------
            exprs : args of ``str`` or ``.Expression``
                Row fields to group by.
            named_exprs : keyword args of ``.Expression``
                Row-indexed expressions to group by.
    
            Returns
            -------
            ``.GroupedMatrixTable``
                Grouped matrix. Can be used to call ``.GroupedMatrixTable.aggregate``.
            """
    
            return GroupedMatrixTable(self).group_rows_by(*exprs, **named_exprs)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.group_cols_by)    @typecheck_method(exprs=oneof(str, Expression), named_exprs=expr_any)
        def group_cols_by(self, *exprs, **named_exprs) -> 'GroupedMatrixTable':
            """Group columns, used with ``.GroupedMatrixTable.aggregate``.
    
            Examples
            --------
            Aggregate to a matrix with cohort as column keys, computing the call rate
            as an entry field:
    
            >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
            ...                          .aggregate(call_rate = hl.agg.fraction(hl.is_defined(dataset.GT))))
    
            Notes
            -----
            All complex expressions must be passed as named expressions.
    
            Parameters
            ----------
            exprs : args of ``str`` or ``.Expression``
                Column fields to group by.
            named_exprs : keyword args of ``.Expression``
                Column-indexed expressions to group by.
    
            Returns
            -------
            ``.GroupedMatrixTable``
                Grouped matrix, can be used to call ``.GroupedMatrixTable.aggregate``.
            """
            return GroupedMatrixTable(self).group_cols_by(*exprs, **named_exprs)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.collect_cols_by_key)    def collect_cols_by_key(self) -> 'MatrixTable':
            """Collect values for each unique column key into arrays.
    
            Examples
            --------
            >>> mt = hl.utils.range_matrix_table(3, 3)
            >>> col_dict = hl.literal({0: [1], 1: [2, 3], 2: [4, 5, 6]})
            >>> mt = (mt.annotate_cols(foo = col_dict.get(mt.col_idx))
            ...     .explode_cols('foo'))
            >>> mt = mt.annotate_entries(bar = mt.row_idx * mt.foo)
    
            >>> mt.cols().show() # doctest: +SKIP_OUTPUT_CHECK
            +---------+-------+
            | col_idx |   foo |
            +---------+-------+
            |   int32 | int32 |
            +---------+-------+
            |       0 |     1 |
            |       1 |     2 |
            |       1 |     3 |
            |       2 |     4 |
            |       2 |     5 |
            |       2 |     6 |
            +---------+-------+
    
            >>> mt.entries().show() # doctest: +SKIP_OUTPUT_CHECK
            +---------+---------+-------+-------+
            | row_idx | col_idx |   foo |   bar |
            +---------+---------+-------+-------+
            |   int32 |   int32 | int32 | int32 |
            +---------+---------+-------+-------+
            |       0 |       0 |     1 |     0 |
            |       0 |       1 |     2 |     0 |
            |       0 |       1 |     3 |     0 |
            |       0 |       2 |     4 |     0 |
            |       0 |       2 |     5 |     0 |
            |       0 |       2 |     6 |     0 |
            |       1 |       0 |     1 |     1 |
            |       1 |       1 |     2 |     2 |
            |       1 |       1 |     3 |     3 |
            |       1 |       2 |     4 |     4 |
            +---------+---------+-------+-------+
            showing top 10 rows
    
            >>> mt = mt.collect_cols_by_key()
            >>> mt.cols().show()
            +---------+--------------+
            | col_idx | foo          |
            +---------+--------------+
            |   int32 | array<int32> |
            +---------+--------------+
            |       0 | [1]          |
            |       1 | [2,3]        |
            |       2 | [4,5,6]      |
            +---------+--------------+
    
            >>> mt.entries().show() # doctest: +SKIP_OUTPUT_CHECK
            +---------+---------+--------------+--------------+
            | row_idx | col_idx | foo          | bar          |
            +---------+---------+--------------+--------------+
            |   int32 |   int32 | array<int32> | array<int32> |
            +---------+---------+--------------+--------------+
            |       0 |       0 | [1]          | [0]          |
            |       0 |       1 | [2,3]        | [0,0]        |
            |       0 |       2 | [4,5,6]      | [0,0,0]      |
            |       1 |       0 | [1]          | [1]          |
            |       1 |       1 | [2,3]        | [2,3]        |
            |       1 |       2 | [4,5,6]      | [4,5,6]      |
            |       2 |       0 | [1]          | [2]          |
            |       2 |       1 | [2,3]        | [4,6]        |
            |       2 |       2 | [4,5,6]      | [8,10,12]    |
            +---------+---------+--------------+--------------+
    
            Notes
            -----
            Each entry field and each non-key column field of type t is replaced by
            a field of type array<t>. The value of each such field is an array
            containing all values of that field sharing the corresponding column
            key. In each column, the newly collected arrays all have the same
            length, and the values of each pre-collection column are guaranteed to
            be located at the same index in their corresponding arrays.
    
            Note
            -----
            The order of the columns is not guaranteed.
    
            Returns
            -------
            ``.MatrixTable``
            """
    
            return MatrixTable(ir.MatrixCollectColsByKey(self._mir))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.count_rows)    @typecheck_method(_localize=bool)
        def count_rows(self, _localize=True) -> int:
            """Count the number of rows in the matrix.
    
            Examples
            --------
    
            Count the number of rows:
    
            >>> n_rows = dataset.count_rows()
    
            Returns
            -------
            ``int``
                Number of rows in the matrix.
            """
            count_ir = ir.TableCount(ir.MatrixRowsTable(self._mir))
            if _localize:
                return Env.backend().execute(count_ir)
            else:
                return construct_expr(ir.LiftMeOut(count_ir), hl.tint64)
    
    
    
        def _force_count_rows(self):
            return Env.backend().execute(ir.MatrixToValueApply(self._mir, {'name': 'ForceCountMatrixTable'}))
    
        def _force_count_cols(self):
            return self.cols()._force_count()
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.count_cols)    @typecheck_method(_localize=bool)
        def count_cols(self, _localize=True) -> int:
            """Count the number of columns in the matrix.
    
            Examples
            --------
    
            Count the number of columns:
    
            >>> n_cols = dataset.count_cols()
    
            Returns
            -------
            ``int``
                Number of columns in the matrix.
            """
            count_ir = ir.TableCount(ir.MatrixColsTable(self._mir))
            if _localize:
                return Env.backend().execute(count_ir)
            else:
                return construct_expr(ir.LiftMeOut(count_ir), hl.tint64)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.count)    def count(self) -> Tuple[int, int]:
            """Count the number of rows and columns in the matrix.
    
            Examples
            --------
    
            >>> dataset.count()
    
            Returns
            -------
            ``int``, ``int``
                Number of rows, number of cols.
            """
            count_ir = ir.MatrixCount(self._mir)
            return Env.backend().execute(count_ir)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.checkpoint)    @typecheck_method(
            output=str,
            overwrite=bool,
            stage_locally=bool,
            _codec_spec=nullable(str),
            _read_if_exists=bool,
            _intervals=nullable(sequenceof(anytype)),
            _filter_intervals=bool,
            _drop_cols=bool,
            _drop_rows=bool,
        )
        def checkpoint(
            self,
            output: str,
            overwrite: bool = False,
            stage_locally: bool = False,
            _codec_spec: Optional[str] = None,
            _read_if_exists: bool = False,
            _intervals=None,
            _filter_intervals=False,
            _drop_cols=False,
            _drop_rows=False,
        ) -> 'MatrixTable':
            """Checkpoint the matrix table to disk by writing and reading using a fast, but less space-efficient codec.
    
            Parameters
            ----------
            output : str
                Path at which to write.
            stage_locally: bool
                If ``True``, major output will be written to temporary local storage
                before being copied to ``output``
            overwrite : bool
                If ``True``, overwrite an existing file at the destination.
    
            Returns
            -------
            ``MatrixTable``
    
    
            .. include:: _templates/write_warning.rst
    
            Notes
            -----
            An alias for ``write`` followed by ``.read_matrix_table``. It is
            possible to read the file at this path later with
            ``.read_matrix_table``. A faster, but less efficient, codec is used
            or writing the data so the file will be larger than if one used
            ``write``.
    
            Examples
            --------
            >>> dataset = dataset.checkpoint('output/dataset_checkpoint.mt')
            """
            hl.current_backend().validate_file(output)
    
            if not _read_if_exists or not hl.hadoop_exists(f'{output}/_SUCCESS'):
                self.write(output=output, overwrite=overwrite, stage_locally=stage_locally, _codec_spec=_codec_spec)
                _assert_type = self._type
                _load_refs = False
            else:
                _assert_type = None
                _load_refs = True
            return hl.read_matrix_table(
                output,
                _intervals=_intervals,
                _filter_intervals=_filter_intervals,
                _drop_cols=_drop_cols,
                _drop_rows=_drop_rows,
                _assert_type=_assert_type,
                _load_refs=_load_refs,
            )
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.write)    @typecheck_method(
            output=str, overwrite=bool, stage_locally=bool, _codec_spec=nullable(str), _partitions=nullable(expr_any)
        )
        def write(
            self,
            output: str,
            overwrite: bool = False,
            stage_locally: bool = False,
            _codec_spec: Optional[str] = None,
            _partitions=None,
        ):
            """Write to disk.
    
            Examples
            --------
    
            >>> dataset.write('output/dataset.mt')
    
            .. include:: _templates/write_warning.rst
    
            See Also
            --------
            ``.read_matrix_table``
    
            Parameters
            ----------
            output : str
                Path at which to write.
            stage_locally: bool
                If ``True``, major output will be written to temporary local storage
                before being copied to ``output``
            overwrite : bool
                If ``True``, overwrite an existing file at the destination.
            """
    
            hl.current_backend().validate_file(output)
    
            if _partitions is not None:
                _partitions, _partitions_type = hl.utils._dumps_partitions(_partitions, self.row_key.dtype)
            else:
                _partitions_type = None
    
            writer = ir.MatrixNativeWriter(output, overwrite, stage_locally, _codec_spec, _partitions, _partitions_type)
            Env.backend().execute(ir.MatrixWrite(self._mir, writer))
    
    
    
        class _Show:
            def __init__(self, table, n_rows, actual_n_cols, displayed_n_cols, width, truncate, types):
                self.table_show = table._show(n_rows, width, truncate, types)
                self.actual_n_cols = actual_n_cols
                self.displayed_n_cols = displayed_n_cols
    
            def __str__(self):
                s = self.table_show.__str__()
                if self.displayed_n_cols != self.actual_n_cols:
                    s += f"showing the first {self.displayed_n_cols} of {self.actual_n_cols} columns"
                return s
    
            def __repr__(self):
                return self.__str__()
    
            def _repr_html_(self):
                s = self.table_show._repr_html_()
                if self.displayed_n_cols != self.actual_n_cols:
                    s += '<p style="background: #fdd; padding: 0.4em;">'
                    s += f"showing the first {self.displayed_n_cols} of {self.actual_n_cols} columns"
                    s += '</p>\n'
                return s
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.show)    @typecheck_method(
            n_rows=nullable(int),
            n_cols=nullable(int),
            include_row_fields=bool,
            width=nullable(int),
            truncate=nullable(int),
            types=bool,
            handler=nullable(anyfunc),
        )
        def show(
            self, n_rows=None, n_cols=None, include_row_fields=False, width=None, truncate=None, types=True, handler=None
        ):
            """Print the first few rows of the matrix table to the console.
    
            .. include:: _templates/experimental.rst
    
            Notes
            -----
            The output can be passed piped to another output source using the `handler` argument:
    
            >>> mt.show(handler=lambda x: logging.info(x))  # doctest: +SKIP
    
            Parameters
            ----------
            n_rows : ``int``
                Maximum number of rows to show.
            n_cols : ``int``
                Maximum number of columns to show.
            width : ``int``
                Horizontal width at which to break fields.
            truncate : ``int``, optional
                Truncate each field to the given number of characters. If
                ``None``, truncate fields to the given `width`.
            types : ``bool``
                Print an extra header line with the type of each field.
            handler : Callable[[str], Any]
                Handler function for data string.
            """
    
            def estimate_size(struct_expression):
                return sum(max(len(f), len(str(x.dtype))) + 3 for f, x in struct_expression.flatten().items())
    
            if n_cols is None:
                import shutil
    
                (characters, _) = shutil.get_terminal_size((80, 10))
                characters -= 6  # borders
                key_characters = estimate_size(self.row_key)
                characters -= key_characters
                if include_row_fields:
                    characters -= estimate_size(self.row_value)
                characters = max(characters, 0)
                n_cols = characters // (estimate_size(self.entry) + 4)  # 4 for the column index
            actual_n_cols = self.count_cols()
            displayed_n_cols = min(actual_n_cols, n_cols)
    
            t = self.localize_entries('entries', 'cols')
            if len(t.key) > 0:
                t = t.order_by(*t.key)
            col_key_type = self.col_key.dtype
    
            col_headers = [f'<col {i}>' for i in range(0, displayed_n_cols)]
            if len(col_key_type) == 1 and col_key_type[0] in (hl.tstr, hl.tint32, hl.tint64):
                cols = self.col_key[0].take(displayed_n_cols)
                if len(set(cols)) == len(cols):
                    col_headers = [repr(c) for c in cols]
    
            entries = {col_headers[i]: t.entries[i] for i in range(0, displayed_n_cols)}
            t = t.select(
                **{f: t[f] for f in self.row_key}, **{f: t[f] for f in self.row_value if include_row_fields}, **entries
            )
            if handler is None:
                handler = default_handler()
            return handler(MatrixTable._Show(t, n_rows, actual_n_cols, displayed_n_cols, width, truncate, types))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.globals_table)    def globals_table(self) -> Table:
            """Returns a table with a single row with the globals of the matrix table.
    
            Examples
            --------
            Extract the globals table:
    
            >>> globals_table = dataset.globals_table()
    
            Returns
            -------
            ``.Table``
                Table with the globals from the matrix, with a single row.
            """
            return Table.parallelize([hl.eval(self.globals)], self._global_type)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.rows)    def rows(self) -> Table:
            """Returns a table with all row fields in the matrix.
    
            Examples
            --------
            Extract the row table:
    
            >>> rows_table = dataset.rows()
    
            Returns
            -------
            ``.Table``
                Table with all row fields from the matrix, with one row per row of the matrix.
            """
    
            return Table(ir.MatrixRowsTable(self._mir))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.cols)    def cols(self) -> Table:
            """Returns a table with all column fields in the matrix.
    
            Examples
            --------
            Extract the column table:
    
            >>> cols_table = dataset.cols()
    
            Warning
            -------
            Matrix table columns are typically sorted by the order at import, and
            not necessarily by column key. Since tables are always sorted by key,
            the table which results from this command will have its rows sorted by
            the column key (which becomes the table key). To preserve the original
            column order as the table row order, first unkey the columns using
            ``key_cols_by`` with no arguments.
    
            Returns
            -------
            ``.Table``
                Table with all column fields from the matrix, with one row per column of the matrix.
            """
    
            if len(self.col_key) != 0 and Env.hc()._warn_cols_order:
                warning(
                    "cols(): Resulting column table is sorted by 'col_key'."
                    "\n    To preserve matrix table column order, "
                    "first unkey columns with 'key_cols_by()'"
                )
                Env.hc()._warn_cols_order = False
    
            return Table(ir.MatrixColsTable(self._mir))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.entries)    def entries(self) -> Table:
            """Returns a matrix in coordinate table form.
    
            Examples
            --------
            Extract the entry table:
    
            >>> entries_table = dataset.entries()
    
            Notes
            -----
            The coordinate table representation of the source matrix table contains
            one row for each **non-filtered** entry of the matrix -- if a matrix table
            has no filtered entries and contains N rows and M columns, the table will contain
            ``M * N`` rows, which can be **a very large number**.
    
            This representation can be useful for aggregating over both axes of a matrix table
            at the same time -- it is not possible to aggregate over a matrix table using
            ``group_rows_by`` and ``group_cols_by`` at the same time (aggregating
            by population and chromosome from a variant-by-sample genetics representation,
            for instance). After moving to the coordinate representation with ``entries``,
            it is possible to group and aggregate the resulting table much more flexibly,
            albeit with potentially poorer computational performance.
    
            Warning
            -------
            The table returned by this method should be used for aggregation or queries,
            but never exported or written to disk without extensive filtering and field
            selection -- the disk footprint of an entries_table could be 100x (or more!)
            larger than its parent matrix. This means that if you try to export the entries
            table of a 10 terabyte matrix, you could write a petabyte of data!
    
            Warning
            -------
            Matrix table columns are typically sorted by the order at import, and
            not necessarily by column key. Since tables are always sorted by key,
            the table which results from this command will have its rows sorted by
            the compound (row key, column key) which becomes the table key.
            To preserve the original row-major entry order as the table row order,
            first unkey the columns using ``key_cols_by`` with no arguments.
    
            Warning
            -------
            If the matrix table has no row key, but has a column key, this operation
            may require a full shuffle to sort by the column key, depending on the
            pipeline.
    
            Returns
            -------
            ``.Table``
                Table with all non-global fields from the matrix, with **one row per entry of the matrix**.
            """
            if Env.hc()._warn_entries_order and len(self.col_key) > 0:
                warning(
                    "entries(): Resulting entries table is sorted by '(row_key, col_key)'."
                    "\n    To preserve row-major matrix table order, "
                    "first unkey columns with 'key_cols_by()'"
                )
                Env.hc()._warn_entries_order = False
    
            return Table(ir.MatrixEntriesTable(self._mir))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.index_globals)    def index_globals(self) -> Expression:
            """Return this matrix table's global variables for use in another
            expression context.
    
            Examples
            --------
            >>> dataset1 = dataset.annotate_globals(pli={'SCN1A': 0.999, 'SONIC': 0.014})
            >>> pli_dict = dataset1.index_globals().pli
            >>> dataset_result = dataset2.annotate_rows(gene_pli = dataset2.gene.map(lambda x: pli_dict.get(x)))
    
            Returns
            -------
            ``.StructExpression``
            """
            return construct_expr(ir.TableGetGlobals(ir.MatrixRowsTable(self._mir)), self.globals.dtype)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.index_rows)    def index_rows(self, *exprs, all_matches=False) -> 'Expression':
            """Expose the row values as if looked up in a dictionary, indexing
            with `exprs`.
    
            Examples
            --------
            >>> dataset_result = dataset.annotate_rows(qual = dataset2.index_rows(dataset.locus, dataset.alleles).qual)
    
            Or equivalently:
    
            >>> dataset_result = dataset.annotate_rows(qual = dataset2.index_rows(dataset.row_key).qual)
    
            Parameters
            ----------
            exprs : variable-length args of ``.Expression``
                Index expressions.
            all_matches : bool
                Experimental. If ``True``, value of expression is array of all matches.
    
            Notes
            -----
            ``index_rows(exprs)`` is equivalent to ``rows().index(exprs)``
            or ``rows()[exprs]``.
    
            The type of the resulting struct is the same as the type of
            ``.row_value``.
    
            Returns
            -------
            ``.Expression``
            """
            try:
                return self.rows()._index(*exprs, all_matches=all_matches)
            except TableIndexKeyError as err:
                raise ExpressionException(
                    f"Key type mismatch: cannot index matrix table with given expressions:\n"
                    f"  MatrixTable row key: {', '.join(str(t) for t in err.key_type.values()) or '<<<empty key>>>'}\n"
                    f"  Index expressions:   {', '.join(str(e.dtype) for e in err.index_expressions)}"
                )
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.index_cols)    def index_cols(self, *exprs, all_matches=False) -> 'Expression':
            """Expose the column values as if looked up in a dictionary, indexing
            with `exprs`.
    
            Examples
            --------
            >>> dataset_result = dataset.annotate_cols(pheno = dataset2.index_cols(dataset.s).pheno)
    
            Or equivalently:
    
            >>> dataset_result = dataset.annotate_cols(pheno = dataset2.index_cols(dataset.col_key).pheno)
    
            Parameters
            ----------
            exprs : variable-length args of ``.Expression``
                Index expressions.
            all_matches : bool
                Experimental. If ``True``, value of expression is array of all matches.
    
            Notes
            -----
            ``index_cols(cols)`` is equivalent to ``cols().index(exprs)``
            or ``cols()[exprs]``.
    
            The type of the resulting struct is the same as the type of
            ``.col_value``.
    
            Returns
            -------
            ``.Expression``
            """
            try:
                return self.cols()._index(*exprs, all_matches=all_matches)
            except TableIndexKeyError as err:
                raise ExpressionException(
                    f"Key type mismatch: cannot index matrix table with given expressions:\n"
                    f"  MatrixTable col key: {', '.join(str(t) for t in err.key_type.values()) or '<<<empty key>>>'}\n"
                    f"  Index expressions:   {', '.join(str(e.dtype) for e in err.index_expressions)}"
                )
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.index_entries)    def index_entries(self, row_exprs, col_exprs):
            """Expose the entries as if looked up in a dictionary, indexing
            with `exprs`.
    
            Examples
            --------
            >>> dataset_result = dataset.annotate_entries(GQ2 = dataset2.index_entries(dataset.row_key, dataset.col_key).GQ)
    
            Or equivalently:
    
            >>> dataset_result = dataset.annotate_entries(GQ2 = dataset2[dataset.row_key, dataset.col_key].GQ)
    
            Parameters
            ----------
            row_exprs : tuple of ``.Expression``
                Row index expressions.
            col_exprs : tuple of ``.Expression``
                Column index expressions.
    
            Notes
            -----
            The type of the resulting struct is the same as the type of
            ``.entry``.
    
            Note
            ----
            There is a shorthand syntax for ``.MatrixTable.index_entries`` using
            square brackets (the Python ``__getitem__`` syntax). This syntax is
            preferred.
    
            >>> dataset_result = dataset.annotate_entries(GQ2 = dataset2[dataset.row_key, dataset.col_key].GQ)
    
            Returns
            -------
            ``.StructExpression``
            """
            row_exprs = wrap_to_tuple(row_exprs)
            col_exprs = wrap_to_tuple(col_exprs)
            if len(row_exprs) == 0 or len(col_exprs) == 0:
                raise ValueError("'MatrixTable.index_entries:' 'row_exprs' and 'col_exprs' must not be empty")
            row_non_exprs = list(filter(lambda e: not isinstance(e, Expression), row_exprs))
            if row_non_exprs:
                raise TypeError(f"'MatrixTable.index_entries': row_exprs expects expressions, found {row_non_exprs}")
            col_non_exprs = list(filter(lambda e: not isinstance(e, Expression), col_exprs))
            if col_non_exprs:
                raise TypeError(f"'MatrixTable.index_entries': col_exprs expects expressions, found {col_non_exprs}")
    
            if not types_match(self.row_key.values(), row_exprs):
                if (
                    len(row_exprs) == 1
                    and isinstance(row_exprs[0], TupleExpression)
                    and types_match(self.row_key.values(), row_exprs[0])
                ):
                    return self.index_entries(tuple(row_exprs[0]), col_exprs)
                elif (
                    len(row_exprs) == 1
                    and isinstance(row_exprs[0], StructExpression)
                    and types_match(self.row_key.values(), row_exprs[0].values())
                ):
                    return self.index_entries(tuple(row_exprs[0].values()), col_exprs)
                elif len(row_exprs) != len(self.row_key):
                    raise ExpressionException(
                        f'Key mismatch: matrix table has {len(self.row_key)} row key fields, '
                        f'found {len(row_exprs)} index expressions'
                    )
                else:
                    raise ExpressionException(
                        f"Key type mismatch: Cannot index matrix table with given expressions\n"
                        f"  MatrixTable row key:   {', '.join(str(t) for t in self.row_key.dtype.values())}\n"
                        f"  Row index expressions: {', '.join(str(e.dtype) for e in row_exprs)}"
                    )
    
            if not types_match(self.col_key.values(), col_exprs):
                if (
                    len(col_exprs) == 1
                    and isinstance(col_exprs[0], TupleExpression)
                    and types_match(self.col_key.values(), col_exprs[0])
                ):
                    return self.index_entries(row_exprs, tuple(col_exprs[0]))
                elif (
                    len(col_exprs) == 1
                    and isinstance(col_exprs[0], StructExpression)
                    and types_match(self.col_key.values(), col_exprs[0].values())
                ):
                    return self.index_entries(row_exprs, tuple(col_exprs[0].values()))
                elif len(col_exprs) != len(self.col_key):
                    raise ExpressionException(
                        f'Key mismatch: matrix table has {len(self.col_key)} col key fields, '
                        f'found {len(col_exprs)} index expressions.'
                    )
                else:
                    raise ExpressionException(
                        f"Key type mismatch: cannot index matrix table with given expressions:\n"
                        f"  MatrixTable col key:   {', '.join(str(t) for t in self.col_key.dtype.values())}\n"
                        f"  Col index expressions: {', '.join(str(e.dtype) for e in col_exprs)}"
                    )
    
            indices, aggregations = unify_all(*(row_exprs + col_exprs))
            src = indices.source
            if aggregations:
                raise ExpressionException('Cannot join using an aggregated field')
    
            uid = Env.get_uid()
            uids = [uid]
    
            if isinstance(src, Table):
                # join table with matrix.entries_table()
                return self.entries().index(*(row_exprs + col_exprs))
            else:
                assert isinstance(src, MatrixTable)
                row_uid = Env.get_uid()
                uids.append(row_uid)
                col_uid = Env.get_uid()
                uids.append(col_uid)
    
                def joiner(left: MatrixTable):
                    localized = self._localize_entries(row_uid, col_uid)
                    src_cols_indexed = self.add_col_index(col_uid).cols()
                    src_cols_indexed = src_cols_indexed.annotate(**{col_uid: hl.int32(src_cols_indexed[col_uid])})
                    left = left._annotate_all(
                        row_exprs={row_uid: localized.index(*row_exprs)[row_uid]},
                        col_exprs={col_uid: src_cols_indexed.index(*col_exprs)[col_uid]},
                    )
                    return left.annotate_entries(**{uid: left[row_uid][left[col_uid]]})
    
                join_ir = ir.Join(
                    ir.ProjectedTopLevelReference('g', uid, self.entry.dtype), uids, [*row_exprs, *col_exprs], joiner
                )
                return construct_expr(join_ir, self.entry.dtype, indices, aggregations)
    
    
    
        @typecheck_method(entries_field_name=str, cols_field_name=str)
        def _localize_entries(self, entries_field_name, cols_field_name) -> 'Table':
            assert entries_field_name not in self.row
            assert cols_field_name not in self.globals
            return Table(ir.CastMatrixToTable(self._mir, entries_field_name, cols_field_name))
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.localize_entries)    @typecheck_method(entries_array_field_name=nullable(str), columns_array_field_name=nullable(str))
        def localize_entries(self, entries_array_field_name=None, columns_array_field_name=None) -> 'Table':
            """Convert the matrix table to a table with entries localized as an array of structs.
    
            Examples
            --------
            Build a numpy ndarray from a small ``.MatrixTable``:
    
            >>> mt = hl.utils.range_matrix_table(3,3)
            >>> mt = mt.select_entries(x = mt.row_idx * mt.col_idx)
            >>> mt.show()
            +---------+-------+-------+-------+
            | row_idx |   0.x |   1.x |   2.x |
            +---------+-------+-------+-------+
            |   int32 | int32 | int32 | int32 |
            +---------+-------+-------+-------+
            |       0 |     0 |     0 |     0 |
            |       1 |     0 |     1 |     2 |
            |       2 |     0 |     2 |     4 |
            +---------+-------+-------+-------+
    
            >>> t = mt.localize_entries('entry_structs', 'columns')
            >>> t.describe()
            ----------------------------------------
            Global fields:
                'columns': array<struct {
                    col_idx: int32
                }>
            ----------------------------------------
            Row fields:
                'row_idx': int32
                'entry_structs': array<struct {
                    x: int32
                }>
            ----------------------------------------
            Key: ['row_idx']
            ----------------------------------------
    
            >>> t = t.select(entries = t.entry_structs.map(lambda entry: entry.x))
            >>> import numpy as np
            >>> np.array(t.entries.collect())
            array([[0, 0, 0],
                   [0, 1, 2],
                   [0, 2, 4]])
    
            Notes
            -----
            Both of the added fields are arrays of length equal to
            ``mt.count_cols()``. Missing entries are represented as missing structs
            in the entries array.
    
            Parameters
            ----------
            entries_array_field_name : ``str``
                The name of the table field containing the array of entry structs
                for the given row.
            columns_array_field_name : ``str``
                The name of the global field containing the array of column
                structs.
    
            Returns
            -------
            ``.Table``
                A table whose fields are the row fields of this matrix table plus
                one field named ``entries_array_field_name``. The global fields of
                this table are the global fields of this matrix table plus one field
                named ``columns_array_field_name``.
            """
            entries = entries_array_field_name or Env.get_uid()
            cols = columns_array_field_name or Env.get_uid()
            if entries in self.row:
                raise ValueError(
                    f"'localize_entries': cannot localize entries to field {entries!r}, which is already a row field"
                )
            if cols in self.globals:
                raise ValueError(
                    f"'localize_entries': cannot localize columns to field {cols!r}, which is already a global field"
                )
    
            t = self._localize_entries(entries, cols)
            if entries_array_field_name is None:
                t = t.drop(entries)
            if columns_array_field_name is None:
                t = t.drop(cols)
            return t
    
    
    
        @typecheck_method(
            row_exprs=dictof(str, expr_any),
            col_exprs=dictof(str, expr_any),
            entry_exprs=dictof(str, expr_any),
            global_exprs=dictof(str, expr_any),
        )
        def _annotate_all(
            self,
            row_exprs={},
            col_exprs={},
            entry_exprs={},
            global_exprs={},
        ) -> 'MatrixTable':
            all_exprs = list(
                itertools.chain(row_exprs.values(), col_exprs.values(), entry_exprs.values(), global_exprs.values())
            )
    
            for field_name in list(
                itertools.chain(row_exprs.keys(), col_exprs.keys(), entry_exprs.keys(), global_exprs.keys())
            ):
                if field_name in self._fields:
                    raise RuntimeError(f'field {field_name!r} already in matrix table, cannot use _annotate_all')
    
            base, cleanup = self._process_joins(*all_exprs)
            mir = base._mir
    
            if row_exprs:
                row_struct = ir.InsertFields.construct_with_deduplication(
                    base.row._ir, [(n, e._ir) for (n, e) in row_exprs.items()], None
                )
                mir = ir.MatrixMapRows(mir, row_struct)
            if col_exprs:
                col_struct = ir.InsertFields.construct_with_deduplication(
                    base.col._ir, [(n, e._ir) for (n, e) in col_exprs.items()], None
                )
                mir = ir.MatrixMapCols(mir, col_struct, None)
            if entry_exprs:
                entry_struct = ir.InsertFields.construct_with_deduplication(
                    base.entry._ir, [(n, e._ir) for (n, e) in entry_exprs.items()], None
                )
                mir = ir.MatrixMapEntries(mir, entry_struct)
            if global_exprs:
                globals_struct = ir.InsertFields.construct_with_deduplication(
                    base.globals._ir, [(n, e._ir) for (n, e) in global_exprs.items()], None
                )
                mir = ir.MatrixMapGlobals(mir, globals_struct)
    
            return cleanup(MatrixTable(mir))
    
        @typecheck_method(
            row_exprs=dictof(str, expr_any),
            row_key=nullable(sequenceof(str)),
            col_exprs=dictof(str, expr_any),
            col_key=nullable(sequenceof(str)),
            entry_exprs=dictof(str, expr_any),
            global_exprs=dictof(str, expr_any),
        )
        def _select_all(
            self,
            row_exprs={},
            row_key=None,
            col_exprs={},
            col_key=None,
            entry_exprs={},
            global_exprs={},
        ) -> 'MatrixTable':
            all_names = list(itertools.chain(row_exprs.keys(), col_exprs.keys(), entry_exprs.keys(), global_exprs.keys()))
            uids = {k: Env.get_uid() for k in all_names}
    
            mt = self._annotate_all(
                {uids[k]: v for k, v in row_exprs.items()},
                {uids[k]: v for k, v in col_exprs.items()},
                {uids[k]: v for k, v in entry_exprs.items()},
                {uids[k]: v for k, v in global_exprs.items()},
            )
    
            keep = set()
            if row_key is not None:
                old_key = list(mt.row_key)
                mt = mt.key_rows_by(*(uids[k] for k in row_key)).drop(*old_key)
            else:
                keep = keep.union(set(mt.row_key))
    
            if col_key is not None:
                old_key = list(mt.col_key)
                mt = mt.key_cols_by(*(uids[k] for k in col_key)).drop(*old_key)
            else:
                keep = keep.union(set(mt.col_key))
    
            keep = keep.union(uids.values())
            return mt.drop(*(f for f in mt._fields if f not in keep)).rename({
                uid: original for original, uid in uids.items()
            })
    
        def _process_joins(self, *exprs) -> 'MatrixTable':
            return process_joins(self, exprs)
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.describe)    def describe(self, handler=print, *, widget=False):
            """Print information about the fields in the matrix table.
    
            Note
            ----
            The `widget` argument is **experimental**.
    
            Parameters
            ----------
            handler : Callable[[str], None]
                Handler function for returned string.
            widget : bool
                Create an interactive IPython widget.
            """
            if widget:
                from hail.experimental.interact import interact
    
                return interact(self)
    
            def format_type(typ):
                return typ.pretty(indent=4).lstrip()
    
            if len(self.globals) == 0:
                global_fields = '\n    None'
            else:
                global_fields = ''.join(
                    "\n    '{name}': {type}".format(name=f, type=format_type(t)) for f, t in self.globals.dtype.items()
                )
    
            if len(self.row) == 0:
                row_fields = '\n    None'
            else:
                row_fields = ''.join(
                    "\n    '{name}': {type}".format(name=f, type=format_type(t)) for f, t in self.row.dtype.items()
                )
    
            row_key = '[' + ', '.join("'{name}'".format(name=f) for f in self.row_key) + ']' if self.row_key else None
    
            if len(self.col) == 0:
                col_fields = '\n    None'
            else:
                col_fields = ''.join(
                    "\n    '{name}': {type}".format(name=f, type=format_type(t)) for f, t in self.col.dtype.items()
                )
    
            col_key = '[' + ', '.join("'{name}'".format(name=f) for f in self.col_key) + ']' if self.col_key else None
    
            if len(self.entry) == 0:
                entry_fields = '\n    None'
            else:
                entry_fields = ''.join(
                    "\n    '{name}': {type}".format(name=f, type=format_type(t)) for f, t in self.entry.dtype.items()
                )
    
            s = (
                '----------------------------------------\n'
                'Global fields:{g}\n'
                '----------------------------------------\n'
                'Column fields:{c}\n'
                '----------------------------------------\n'
                'Row fields:{r}\n'
                '----------------------------------------\n'
                'Entry fields:{e}\n'
                '----------------------------------------\n'
                'Column key: {ck}\n'
                'Row key: {rk}\n'
                '----------------------------------------'.format(
                    g=global_fields, rk=row_key, r=row_fields, ck=col_key, c=col_fields, e=entry_fields
                )
            )
            handler(s)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.choose_cols)    @typecheck_method(indices=sequenceof(int))
        def choose_cols(self, indices: List[int]) -> 'MatrixTable':
            """Choose a new set of columns from a list of old column indices.
    
            Examples
            --------
    
            Randomly shuffle column order:
    
            >>> import random
            >>> indices = list(range(dataset.count_cols()))
            >>> random.shuffle(indices)
            >>> dataset_reordered = dataset.choose_cols(indices)
    
            Take the first ten columns:
    
            >>> dataset_result = dataset.choose_cols(list(range(10)))
    
            Parameters
            ----------
            indices : ``list`` of ``int``
                List of old column indices.
    
            Returns
            -------
            ``.MatrixTable``
            """
            n_cols = self.count_cols()
            for i in indices:
                if not 0 <= i < n_cols:
                    raise ValueError(f"'choose_cols': expect indices between 0 and {n_cols}, found {i}")
            return MatrixTable(ir.MatrixChooseCols(self._mir, indices))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.n_partitions)    def n_partitions(self) -> int:
            """Number of partitions.
    
            Notes
            -----
    
            The data in a dataset is divided into chunks called partitions, which
            may be stored together or across a network, so that each partition may
            be read and processed in parallel by available cores. Partitions are a
            core concept of distributed computation in Spark, see `here
            <http://spark.apache.org/docs/latest/programming-guide.html#resilient-distributed-datasets-rdds>`__
            for details.
    
            Returns
            -------
            int
                Number of partitions.
            """
            return Env.backend().execute(ir.MatrixToValueApply(self._mir, {'name': 'NPartitionsMatrixTable'}))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.repartition)    @typecheck_method(n_partitions=int, shuffle=bool)
        def repartition(self, n_partitions: int, shuffle: bool = True) -> 'MatrixTable':
            """Change the number of partitions.
    
            Examples
            --------
    
            Repartition to 500 partitions:
    
            >>> dataset_result = dataset.repartition(500)
    
            Notes
            -----
    
            Check the current number of partitions with ``.n_partitions``.
    
            The data in a dataset is divided into chunks called partitions, which
            may be stored together or across a network, so that each partition may
            be read and processed in parallel by available cores. When a matrix with
            ``M`` rows is first imported, each of the ``k`` partitions will
            contain about ``M/k`` of the rows. Since each partition has some
            computational overhead, decreasing the number of partitions can improve
            performance after significant filtering. Since it's recommended to have
            at least 2 - 4 partitions per core, increasing the number of partitions
            can allow one to take advantage of more cores. Partitions are a core
            concept of distributed computation in Spark, see `their documentation
            <http://spark.apache.org/docs/latest/programming-guide.html#resilient-distributed-datasets-rdds>`__
            for details.
    
            When ``shuffle=True``, Hail does a full shuffle of the data
            and creates equal sized partitions.  When ``shuffle=False``,
            Hail combines existing partitions to avoid a full
            shuffle. These algorithms correspond to the `repartition` and
            `coalesce` commands in Spark, respectively. In particular,
            when ``shuffle=False``, ``n_partitions`` cannot exceed current
            number of partitions.
    
            Parameters
            ----------
            n_partitions : int
                Desired number of partitions.
            shuffle : bool
                If ``True``, use full shuffle to repartition.
    
            Returns
            -------
            ``.MatrixTable``
                Repartitioned dataset.
            """
            if hl.current_backend().requires_lowering:
                tmp = hl.utils.new_temp_file()
    
                if len(self.row_key) == 0:
                    uid = Env.get_uid()
                    tmp2 = hl.utils.new_temp_file()
                    self.checkpoint(tmp2)
                    ht = hl.read_matrix_table(tmp2).add_row_index(uid).key_rows_by(uid)
                    ht.checkpoint(tmp)
                    return hl.read_matrix_table(tmp, _n_partitions=n_partitions).drop(uid)
                else:
                    # checkpoint rather than write to use fast codec
                    self.checkpoint(tmp)
                    return hl.read_matrix_table(tmp, _n_partitions=n_partitions)
    
            return MatrixTable(
                ir.MatrixRepartition(
                    self._mir, n_partitions, ir.RepartitionStrategy.SHUFFLE if shuffle else ir.RepartitionStrategy.COALESCE
                )
            )
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.naive_coalesce)    @typecheck_method(max_partitions=int)
        def naive_coalesce(self, max_partitions: int) -> 'MatrixTable':
            """Naively decrease the number of partitions.
    
            Example
            -------
            Naively repartition to 10 partitions:
    
            >>> dataset_result = dataset.naive_coalesce(10)
    
            Warning
            -------
            ``.naive_coalesce`` simply combines adjacent partitions to achieve
            the desired number. It does not attempt to rebalance, unlike
            ``.repartition``, so it can produce a heavily unbalanced dataset. An
            unbalanced dataset can be inefficient to operate on because the work is
            not evenly distributed across partitions.
    
            Parameters
            ----------
            max_partitions : int
                Desired number of partitions. If the current number of partitions is
                less than or equal to `max_partitions`, do nothing.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix table with at most `max_partitions` partitions.
            """
            return MatrixTable(ir.MatrixRepartition(self._mir, max_partitions, ir.RepartitionStrategy.NAIVE_COALESCE))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.cache)    def cache(self) -> 'MatrixTable':
            """Persist the dataset in memory.
    
            Examples
            --------
            Persist the dataset in memory:
    
            >>> dataset = dataset.cache() # doctest: +SKIP
    
            Notes
            -----
    
            This method is an alias for ``persist("MEMORY_ONLY") <hail.MatrixTable.persist>``.
    
            Returns
            -------
            ``.MatrixTable``
                Cached dataset.
            """
            return self.persist('MEMORY_ONLY')
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.persist)    @typecheck_method(storage_level=storage_level)
        def persist(self, storage_level: str = 'MEMORY_AND_DISK') -> 'MatrixTable':
            """Persist this table in memory or on disk.
    
            Examples
            --------
            Persist the dataset to both memory and disk:
    
            >>> dataset = dataset.persist() # doctest: +SKIP
    
            Notes
            -----
    
            The ``.MatrixTable.persist`` and ``.MatrixTable.cache``
            methods store the current dataset on disk or in memory temporarily to
            avoid redundant computation and improve the performance of Hail
            pipelines. This method is not a substitution for ``.Table.write``,
            which stores a permanent file.
    
            Most users should use the "MEMORY_AND_DISK" storage level. See the `Spark
            documentation
            <http://spark.apache.org/docs/latest/programming-guide.html#rdd-persistence>`__
            for a more in-depth discussion of persisting data.
    
            Parameters
            ----------
            storage_level : str
                Storage level.  One of: NONE, DISK_ONLY,
                DISK_ONLY_2, MEMORY_ONLY, MEMORY_ONLY_2, MEMORY_ONLY_SER,
                MEMORY_ONLY_SER_2, MEMORY_AND_DISK, MEMORY_AND_DISK_2,
                MEMORY_AND_DISK_SER, MEMORY_AND_DISK_SER_2, OFF_HEAP
    
            Returns
            -------
            ``.MatrixTable``
                Persisted dataset.
            """
            return Env.backend().persist(self)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.unpersist)    def unpersist(self) -> 'MatrixTable':
            """
            Unpersists this dataset from memory/disk.
    
            Notes
            -----
            This function will have no effect on a dataset that was not previously
            persisted.
    
            Returns
            -------
            ``.MatrixTable``
                Unpersisted dataset.
            """
            return Env.backend().unpersist(self)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.add_row_index)    @typecheck_method(name=str)
        def add_row_index(self, name: str = 'row_idx') -> 'MatrixTable':
            """Add the integer index of each row as a new row field.
    
            Examples
            --------
    
            >>> dataset_result = dataset.add_row_index()
    
            Notes
            -----
            The field added is type :py``.tint64``.
    
            The row index is 0-indexed; the values are found in the range
            ``[0, N)``, where ``N`` is the total number of rows.
    
            Parameters
            ----------
            name : ``str``
                Name for row index field.
    
            Returns
            -------
            ``.MatrixTable``
                Dataset with new field.
            """
            return self.annotate_rows(**{name: hl.scan.count()})
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.add_col_index)    @typecheck_method(name=str)
        def add_col_index(self, name: str = 'col_idx') -> 'MatrixTable':
            """Add the integer index of each column as a new column field.
    
            Examples
            --------
    
            >>> dataset_result = dataset.add_col_index()
    
            Notes
            -----
            The field added is type :py``.tint32``.
    
            The column index is 0-indexed; the values are found in the range
            ``[0, N)``, where ``N`` is the total number of columns.
    
            Parameters
            ----------
            name: ``str``
                Name for column index field.
    
            Returns
            -------
            ``.MatrixTable``
                Dataset with new field.
            """
            return self.annotate_cols(**{name: hl.scan.count()})
    
    
    
        @typecheck_method(other=matrix_table_type, tolerance=numeric, absolute=bool, reorder_fields=bool)
        def _same(self, other, tolerance=1e-6, absolute=False, reorder_fields=False) -> bool:
            entries_name = Env.get_uid('entries_')
            cols_name = Env.get_uid('columns_')
    
            fd_f = set if reorder_fields else list
    
            if fd_f(self.row) != fd_f(other.row):
                print(f'Different row fields: \n  {list(self.row)}\n  {list(other.row)}')
                return False
            if fd_f(self.globals) != fd_f(other.globals):
                print(f'Different globals fields: \n  {list(self.globals)}\n  {list(other.globals)}')
                return False
            if fd_f(self.col) != fd_f(other.col):
                print(f'Different col fields: \n  {list(self.col)}\n  {list(other.col)}')
                return False
            if fd_f(self.entry) != fd_f(other.entry):
                print(f'Different row fields: \n  {list(self.entry)}\n  {list(other.entry)}')
                return False
    
            if reorder_fields:
                entry_order = list(self.entry)
                if list(other.entry) != entry_order:
                    other = other.select_entries(*entry_order)
    
                globals_order = list(self.globals)
                if list(other.globals) != globals_order:
                    other = other.select_globals(*globals_order)
    
                col_order = list(self.col)
                if list(other.col) != col_order:
                    other = other.select_cols(*col_order)
    
                row_order = list(self.row)
                if list(other.row) != row_order:
                    other = other.select_rows(*row_order)
    
            if list(self.col_key) != list(other.col_key):
                print(f'different col keys:\n  {list(self.col_key)}\n  {list(other.col_key)}')
                return False
    
            return self._localize_entries(entries_name, cols_name)._same(
                other._localize_entries(entries_name, cols_name), tolerance, absolute
            )
    
        @typecheck_method(caller=str, s=expr_struct())
        def _select_entries(self, caller, s) -> 'MatrixTable':
            base, cleanup = self._process_joins(s)
            analyze(caller, s, self._entry_indices)
            return cleanup(MatrixTable(ir.MatrixMapEntries(base._mir, s._ir)))
    
        @typecheck_method(caller=str, row=expr_struct())
        def _select_rows(self, caller, row) -> 'MatrixTable':
            analyze(caller, row, self._row_indices, {self._col_axis})
            base, cleanup = self._process_joins(row)
            return cleanup(MatrixTable(ir.MatrixMapRows(base._mir, row._ir)))
    
        @typecheck_method(caller=str, col=expr_struct(), new_key=nullable(sequenceof(str)))
        def _select_cols(self, caller, col, new_key=None) -> 'MatrixTable':
            analyze(caller, col, self._col_indices, {self._row_axis})
            base, cleanup = self._process_joins(col)
            return cleanup(MatrixTable(ir.MatrixMapCols(base._mir, col._ir, new_key)))
    
        @typecheck_method(caller=str, s=expr_struct())
        def _select_globals(self, caller, s) -> 'MatrixTable':
            base, cleanup = self._process_joins(s)
            analyze(caller, s, self._global_indices)
            return cleanup(MatrixTable(ir.MatrixMapGlobals(base._mir, s._ir)))
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.union_rows)    @typecheck(datasets=matrix_table_type, _check_cols=bool)
        def union_rows(*datasets: 'MatrixTable', _check_cols=True) -> 'MatrixTable':
            """Take the union of dataset rows.
    
            Examples
            --------
    
            .. testsetup::
    
                dataset_to_union_1 = dataset
                dataset_to_union_2 = dataset
    
            Union the rows of two datasets:
    
            >>> dataset_result = dataset_to_union_1.union_rows(dataset_to_union_2)
    
            Given a list of datasets, take the union of all rows:
    
            >>> all_datasets = [dataset_to_union_1, dataset_to_union_2]
    
            The following three syntaxes are equivalent:
    
            >>> dataset_result = dataset_to_union_1.union_rows(dataset_to_union_2)
            >>> dataset_result = all_datasets[0].union_rows(*all_datasets[1:])
            >>> dataset_result = hl.MatrixTable.union_rows(*all_datasets)
    
            Notes
            -----
    
            In order to combine two datasets, three requirements must be met:
    
             - The column keys must be identical, both in type, value, and ordering.
             - The row key schemas and row schemas must match.
             - The entry schemas must match.
    
            The column fields in the resulting dataset are the column fields from
            the first dataset; the column schemas do not need to match.
    
            This method does not deduplicate; if a row exists identically in two
            datasets, then it will be duplicated in the result.
    
            Warning
            -------
            This method can trigger a shuffle, if partitions from two datasets
            overlap.
    
            Parameters
            ----------
            datasets : varargs of ``.MatrixTable``
                Datasets to combine.
    
            Returns
            -------
            ``.MatrixTable``
                Dataset with rows from each member of `datasets`.
            """
            if len(datasets) == 0:
                raise ValueError('Expected at least one argument')
            elif len(datasets) == 1:
                return datasets[0]
            else:
                error_msg = "'MatrixTable.union_rows' expects {} for all datasets to be the same. Found:    \ndataset {}: {}    \ndataset {}: {}"
                first = datasets[0]
                for i, next in enumerate(datasets[1:]):
                    if first.row_key.keys() != next.row_key.keys():
                        raise ValueError(error_msg.format("row keys", 0, first.row_key.keys(), i + 1, next.row_key.keys()))
                    if first.row.dtype != next.row.dtype:
                        raise ValueError(error_msg.format("row types", 0, first.row.dtype, i + 1, next.row.dtype))
                    if first.entry.dtype != next.entry.dtype:
                        raise ValueError(
                            error_msg.format("entry field types", 0, first.entry.dtype, i + 1, next.entry.dtype)
                        )
                    if first.col_key.dtype != next.col_key.dtype:
                        raise ValueError(
                            error_msg.format("col key types", 0, first.col_key.dtype, i + 1, next.col_key.dtype)
                        )
                if _check_cols:
                    wrong_keys = hl.eval(
                        hl.rbind(
                            first.col_key.collect(_localize=False),
                            lambda first_keys: (
                                hl.enumerate([mt.col_key.collect(_localize=False) for mt in datasets[1:]]).find(
                                    lambda x: ~(x[1] == first_keys)
                                )[0]
                            ),
                        )
                    )
                    if wrong_keys is not None:
                        raise ValueError(
                            f"'MatrixTable.union_rows' expects all datasets to have the same columns. "
                            f"Datasets 0 and {wrong_keys + 1} have different columns (or possibly different order)."
                        )
                return MatrixTable(ir.MatrixUnionRows(*[d._mir for d in datasets]))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.union_cols)    @typecheck_method(other=matrix_table_type, row_join_type=enumeration('inner', 'outer'), drop_right_row_fields=bool)
        def union_cols(
            self, other: 'MatrixTable', row_join_type: str = 'inner', drop_right_row_fields: bool = True
        ) -> 'MatrixTable':
            """Take the union of dataset columns.
    
            Warning
            -------
    
            This method does not preserve the global fields from the other matrix table.
    
            Examples
            --------
    
            Union the columns of two datasets:
    
            >>> dataset_result = dataset_to_union_1.union_cols(dataset_to_union_2)
    
            Notes
            -----
    
            In order to combine two datasets, three requirements must be met:
    
             - The row keys must match.
             - The column key schemas and column schemas must match.
             - The entry schemas must match.
    
            The row fields in the resulting dataset are the row fields from the
            first dataset; the row schemas do not need to match.
    
            This method creates a ``.MatrixTable`` which contains all columns
            from both input datasets. The set of rows included in the result is
            determined by the `row_join_type` parameter.
    
            - With the default value of ``'inner'``, an inner join is performed
              on rows, so that only rows whose row key exists in both input datasets
              are included. In this case, the entries for each row are the
              concatenation of all entries of the corresponding rows in the input
              datasets.
            - With `row_join_type` set to  ``'outer'``, an outer join is perfomed on
              rows, so that row keys which exist in only one input dataset are also
              included. For those rows, the entry fields for the columns coming
              from the other dataset will be missing.
    
            Only distinct row keys from each dataset are included (equivalent to
            calling ``.distinct_by_row`` on each dataset first).
    
            This method does not deduplicate; if a column key exists identically in
            two datasets, then it will be duplicated in the result.
    
            Parameters
            ----------
            other : ``.MatrixTable``
                Dataset to concatenate.
            row_join_type : ``.str``
                If `outer`, perform an outer join on rows; if 'inner', perform an
                inner join. Default `inner`.
            drop_right_row_fields : ``.bool``
                If true, non-key row fields of `other` are dropped. Otherwise,
                non-key row fields in the two datasets must have distinct names,
                and the result contains the union of the row fields.
    
            Returns
            -------
            ``.MatrixTable``
                Dataset with columns from both datasets.
            """
            if self.entry.dtype != other.entry.dtype:
                raise ValueError(f'entry types differ:\n    left: {self.entry.dtype}\n    right: {other.entry.dtype}')
            if self.col.dtype != other.col.dtype:
                raise ValueError(f'column types differ:\n    left: {self.col.dtype}\n    right: {other.col.dtype}')
            if self.col_key.keys() != other.col_key.keys():
                raise ValueError(
                    f'column key fields differ:\n'
                    f'    left: {", ".join(self.col_key.keys())}\n'
                    f'    right: {", ".join(other.col_key.keys())}'
                )
            if list(self.row_key.dtype.values()) != list(other.row_key.dtype.values()):
                raise ValueError(
                    f'row key types differ:\n'
                    f'    left: {", ".join(self.row_key.dtype.values())}\n'
                    f'    right: {", ".join(other.row_key.dtype.values())}'
                )
    
            if drop_right_row_fields:
                other = other.select_rows()
            else:
                left_fields = set(self.row_value)
                other_fields = set(other.row_value) - set(other.row_key)
                renames, _ = deduplicate(other_fields, max_attempts=100, already_used=left_fields)
    
                if renames:
                    renames = dict(renames)
                    other = other.rename(renames)
                    info(
                        'Table.union_cols: renamed the following fields on the right to avoid name conflicts:'
                        + ''.join(f'\n    {k!r} -> {v!r}' for k, v in renames.items())
                    )
    
            return MatrixTable(ir.MatrixUnionCols(self._mir, other._mir, row_join_type))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.head)    @typecheck_method(n_rows=nullable(int), n_cols=nullable(int), n=nullable(int))
        def head(self, n_rows: Optional[int], n_cols: Optional[int] = None, *, n: Optional[int] = None) -> 'MatrixTable':
            """Subset matrix to first `n_rows` rows and `n_cols` cols.
    
            Examples
            --------
            >>> mt_range = hl.utils.range_matrix_table(100, 100)
    
            Passing only one argument will take the first `n_rows` rows:
    
            >>> mt_range.head(10).count()
            (10, 100)
    
            Passing two arguments refers to rows and columns, respectively:
    
            >>> mt_range.head(10, 20).count()
            (10, 20)
    
            Either argument may be ``None`` to indicate no filter.
    
            First 10 rows, all columns:
    
            >>> mt_range.head(10, None).count()
            (10, 100)
    
            All rows, first 10 columns:
    
            >>> mt_range.head(None, 10).count()
            (100, 10)
    
            Notes
            -----
            The number of partitions in the new matrix is equal to the number of
            partitions containing the first `n_rows` rows.
    
            Parameters
            ----------
            n_rows : ``int``
                Number of rows to include (all rows included if ``None``).
            n_cols : ``int``, optional
                Number of cols to include (all cols included if ``None``).
            n : ``int``
                Deprecated in favor of n_rows.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix including the first `n_rows` rows and first `n_cols` cols.
    
            """
            if n_rows is not None and n is not None:
                raise ValueError('Both n and n_rows specified. Only one may be specified.')
    
            if n_rows is not None:
                n_rows_name = 'n_rows'
            else:
                warnings.warn("MatrixTable.head: the 'n' parameter is deprecated in favor of 'n_rows'.")
                n_rows = n
                n_rows_name = 'n'
            del n
    
            mt = self
            if n_rows is not None:
                if n_rows < 0:
                    raise ValueError(
                        f"MatrixTable.head: expect '{n_rows_name}' to be non-negative or None, found '{n_rows}'"
                    )
                mt = MatrixTable(ir.MatrixRowsHead(mt._mir, n_rows))
            if n_cols is not None:
                if n_cols < 0:
                    raise ValueError(f"MatrixTable.head: expect 'n_cols' to be non-negative or None, found '{n_cols}'")
                mt = MatrixTable(ir.MatrixColsHead(mt._mir, n_cols))
            return mt
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.tail)    @typecheck_method(n_rows=nullable(int), n_cols=nullable(int), n=nullable(int))
        def tail(self, n_rows: Optional[int], n_cols: Optional[int] = None, *, n: Optional[int] = None) -> 'MatrixTable':
            """Subset matrix to last `n` rows.
    
            Examples
            --------
            >>> mt_range = hl.utils.range_matrix_table(100, 100)
    
            Passing only one argument will take the last `n` rows:
    
            >>> mt_range.tail(10).count()
            (10, 100)
    
            Passing two arguments refers to rows and columns, respectively:
    
            >>> mt_range.tail(10, 20).count()
            (10, 20)
    
            Either argument may be ``None`` to indicate no filter.
    
            Last 10 rows, all columns:
    
            >>> mt_range.tail(10, None).count()
            (10, 100)
    
            All rows, last 10 columns:
    
            >>> mt_range.tail(None, 10).count()
            (100, 10)
    
            Notes
            -----
            For backwards compatibility, the `n` parameter is not named `n_rows`,
            but the parameter refers to the number of rows to keep.
    
            The number of partitions in the new matrix is equal to the number of
            partitions containing the last `n` rows.
    
            Parameters
            ----------
            n_rows : ``int``
                Number of rows to include (all rows included if ``None``).
            n_cols : ``int``, optional
                Number of cols to include (all cols included if ``None``).
            n : ``int``
                Deprecated in favor of n_rows.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix including the last `n` rows and last `n_cols` cols.
            """
            if n_rows is not None and n is not None:
                raise ValueError('Both n and n_rows specified. Only one may be specified.')
    
            if n_rows is not None:
                n_rows_name = 'n_rows'
            else:
                warnings.warn("MatrixTable.tail: the 'n' parameter is deprecated in favor of 'n_rows'.")
                n_rows = n
                n_rows_name = 'n'
            del n
    
            mt = self
            if n_rows is not None:
                if n_rows < 0:
                    raise ValueError(
                        f"MatrixTable.tail: expect '{n_rows_name}' to be non-negative or None, found '{n_rows}'"
                    )
                mt = MatrixTable(ir.MatrixRowsTail(mt._mir, n_rows))
            if n_cols is not None:
                if n_cols < 0:
                    raise ValueError(f"MatrixTable.tail: expect 'n_cols' to be non-negative or None, found '{n_cols}'")
                mt = MatrixTable(ir.MatrixColsTail(mt._mir, n_cols))
            return mt
    
    
    
        @typecheck_method(parts=sequenceof(int), keep=bool)
        def _filter_partitions(self, parts, keep=True) -> 'MatrixTable':
            return MatrixTable(
                ir.MatrixToMatrixApply(self._mir, {'name': 'MatrixFilterPartitions', 'parts': parts, 'keep': keep})
            )
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.from_rows_table)    @classmethod
        @typecheck_method(table=Table)
        def from_rows_table(cls, table: Table) -> 'MatrixTable':
            """Construct matrix table with no columns from a table.
    
            .. include:: _templates/experimental.rst
    
            Examples
            --------
            Import a text table and construct a rows-only matrix table:
    
            >>> table = hl.import_table('data/variant-lof.tsv')
            >>> table = table.transmute(**hl.parse_variant(table['v'])).key_by('locus', 'alleles')
            >>> sites_mt = hl.MatrixTable.from_rows_table(table)
    
            Notes
            -----
            All fields in the table become row-indexed fields in the
            result.
    
            Parameters
            ----------
            table : ``.Table``
                The table to be converted.
    
            Returns
            -------
            ``.MatrixTable``
            """
            col_values_uid = Env.get_uid()
            entries_uid = Env.get_uid()
            return (
                table.annotate_globals(**{col_values_uid: hl.empty_array(hl.tstruct())})
                .annotate(**{entries_uid: hl.empty_array(hl.tstruct())})
                ._unlocalize_entries(entries_uid, col_values_uid, [])
            )
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.sample_rows)    @typecheck_method(p=numeric, seed=nullable(int))
        def sample_rows(self, p: float, seed=None) -> 'MatrixTable':
            """Downsample the matrix table by keeping each row with probability ``p``.
    
            Examples
            --------
            Downsample the dataset to approximately 1% of its rows.
    
            >>> small_dataset = dataset.sample_rows(0.01)
    
            Notes
            -----
            Although the ``MatrixTable`` returned by this method may be
            small, it requires a full pass over the rows of the sampled object.
    
            Parameters
            ----------
            p : ``float``
                Probability of keeping each row.
            seed : ``int``
                Random seed.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix table with approximately ``p * n_rows`` rows.
            """
    
            if not 0 <= p <= 1:
                raise ValueError("Requires 'p' in [0,1]. Found p={}".format(p))
    
            return self.filter_rows(hl.rand_bool(p, seed))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.sample_cols)    @typecheck_method(p=numeric, seed=nullable(int))
        def sample_cols(self, p: float, seed=None) -> 'MatrixTable':
            """Downsample the matrix table by keeping each column with probability ``p``.
    
            Examples
            --------
            Downsample the dataset to approximately 1% of its columns.
    
            >>> small_dataset = dataset.sample_cols(0.01)
    
            Parameters
            ----------
            p : ``float``
                Probability of keeping each column.
            seed : ``int``
                Random seed.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix table with approximately ``p * n_cols`` column.
            """
    
            if not 0 <= p <= 1:
                raise ValueError("Requires 'p' in [0,1]. Found p={}".format(p))
    
            return self.filter_cols(hl.rand_bool(p, seed))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.rename)    @typecheck_method(fields=dictof(str, str))
        def rename(self, fields: Dict[str, str]) -> 'MatrixTable':
            """Rename fields of a matrix table.
    
            Examples
            --------
    
            Rename column key `s` to `SampleID`, still keying by `SampleID`.
    
            >>> dataset_result = dataset.rename({'s': 'SampleID'})
    
            You can rename a field to a field name that already exists, as long as
            that field also gets renamed (no name collisions). Here, we rename the
            column key `s` to `info`, and the row field `info` to `vcf_info`:
    
            >>> dataset_result = dataset.rename({'s': 'info', 'info': 'vcf_info'})
    
            Parameters
            ----------
            fields : ``dict`` from ``str`` to ``str``
                Mapping from old field names to new field names.
    
            Returns
            -------
            ``.MatrixTable``
                Matrix table with renamed fields.
            """
    
            seen = {}
    
            row_map = {}
            col_map = {}
            entry_map = {}
            global_map = {}
    
            for k, v in fields.items():
                if v in seen:
                    raise ValueError(
                        "Cannot rename two fields to the same name: attempted to rename {} and {} both to {}".format(
                            repr(seen[v]), repr(k), repr(v)
                        )
                    )
                if v in self._fields and v not in fields:
                    raise ValueError("Cannot rename {} to {}: field already exists.".format(repr(k), repr(v)))
                seen[v] = k
                if self[k]._indices == self._row_indices:
                    row_map[k] = v
                elif self[k]._indices == self._col_indices:
                    col_map[k] = v
                elif self[k]._indices == self._entry_indices:
                    entry_map[k] = v
                elif self[k]._indices == self._global_indices:
                    global_map[k] = v
    
            return MatrixTable(ir.MatrixRename(self._mir, global_map, col_map, row_map, entry_map))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.distinct_by_row)    def distinct_by_row(self) -> 'MatrixTable':
            """Remove rows with a duplicate row key, keeping exactly one row for each unique key.
    
            Returns
            -------
            ``.MatrixTable``
            """
            return MatrixTable(ir.MatrixDistinctByRow(self._mir))
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.distinct_by_col)    def distinct_by_col(self) -> 'MatrixTable':
            """Remove columns with a duplicate row key, keeping exactly one column for each unique key.
    
            Returns
            -------
            ``.MatrixTable``
            """
            index_uid = Env.get_uid()
    
            col_key_fields = list(self.col_key)
            t = self.key_cols_by().cols()
    
            t = t.add_index(index_uid)
            unique_cols = t.aggregate(
                hl.agg.group_by(hl.struct(**{f: t[f] for f in col_key_fields}), hl.agg.take(t[index_uid], 1))
            )
            unique_cols = sorted([v[0] for _, v in unique_cols.items()])
    
            return self.choose_cols(unique_cols)
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.make_table)    @deprecated(version="0.2.129")
        @typecheck_method(separator=str)
        def make_table(self, separator='.') -> Table:
            """Make a table from a matrix table with one field per sample.
    
            .. deprecated:: 0.2.129
                use ``.localize_entries`` instead because it supports more
                columns
    
            Parameters
            ----------
            separator : ``str``
                Separator between sample IDs and entry field names.
    
            Returns
            -------
            ``.Table``
    
            See Also
            --------
            ``.localize_entries``
    
            Notes
            -----
            The table has one row for each row of the input matrix.  The
            per sample and entry fields are formed by concatenating the
            sample ID with the entry field name using `separator`.  If the
            entry field name is empty, the separator is omitted.
    
            The table inherits the globals from the matrix table.
    
    
            Examples
            --------
            Consider a matrix table with the following schema:
    
            .. code-block:: text
    
              Global fields:
                  'batch': str
              Column fields:
                  's': str
              Row fields:
                  'locus': locus<GRCh37>
                  'alleles': array<str>
              Entry fields:
                  'GT': call
                  'GQ': int32
              Column key:
                  's': str
              Row key:
                  'locus': locus<GRCh37>
                  'alleles': array<str>
    
            and three sample IDs: `A`, `B` and `C`.  Then the result of
            ``.make_table``:
    
            >>> ht = mt.make_table() # doctest: +SKIP
    
            has the original row fields along with 6 additional fields,
            one for each sample and entry field:
    
            .. code-block:: text
    
              Global fields:
                  'batch': str
              Row fields:
                  'locus': locus<GRCh37>
                  'alleles': array<str>
                  'A.GT': call
                  'A.GQ': int32
                  'B.GT': call
                  'B.GQ': int32
                  'C.GT': call
                  'C.GQ': int32
              Key:
                  'locus': locus<GRCh37>
                  'alleles': array<str>
            """
            if not (len(self.col_key) == 1 and self.col_key[0].dtype == hl.tstr):
                raise ValueError("column key must be a single field of type str")
    
            col_keys = self.col_key[0].collect()
    
            counts = Counter(col_keys)
            if counts[None] > 0:
                raise ValueError(
                    "'make_table' encountered a missing column key; ensure all identifiers are defined.\n"
                    "  To fill in key index, run:\n"
                    "    mt = mt.key_cols_by(ck = hl.coalesce(mt.COL_KEY_NAME, 'missing_' + hl.str(hl.scan.count())))"
                )
    
            duplicates = [k for k, count in counts.items() if count > 1]
            if duplicates:
                raise ValueError(f"column keys must be unique, found duplicates: {', '.join(duplicates)}")
    
            entries_uid = Env.get_uid()
            cols_uid = Env.get_uid()
    
            t = self
            t = t._localize_entries(entries_uid, cols_uid)
    
            def fmt(f, col_key):
                if f:
                    return col_key + separator + f
                else:
                    return col_key
    
            t = t.annotate(**{
                fmt(f, col_keys[i]): t[entries_uid][i][j] for i in range(len(col_keys)) for j, f in enumerate(self.entry)
            })
            t = t.drop(cols_uid, entries_uid)
    
            return t
    
    
    
    
    
    [[docs]](../../hail.MatrixTable.html#hail.MatrixTable.summarize)    @typecheck_method(rows=bool, cols=bool, entries=bool, handler=nullable(anyfunc))
        def summarize(self, *, rows=True, cols=True, entries=True, handler=None):
            """Compute and print summary information about the fields in the matrix table.
    
            .. include:: _templates/experimental.rst
    
            Parameters
            ----------
            rows : ``bool``
                Compute summary for the row fields.
            cols : ``bool``
                Compute summary for the column fields.
            entries : ``bool``
                Compute summary for the entry fields.
            """
    
            if handler is None:
                handler = default_handler()
            if cols:
                handler(self.col._summarize(header='Columns', top=True))
            if rows:
                handler(self.row._summarize(header='Rows', top=True))
            if entries:
                handler(self.entry._summarize(header='Entries', top=True))
    
    
    
        def _write_block_matrix(self, path, overwrite, entry_field, block_size):
            mt = self
            mt = mt._select_all(entry_exprs={entry_field: mt[entry_field]})
    
            writer = ir.MatrixBlockMatrixWriter(path, overwrite, entry_field, block_size)
            Env.backend().execute(ir.MatrixWrite(self._mir, writer))
    
        def _calculate_new_partitions(self, n_partitions):
            """returns a set of range bounds that can be passed to write"""
            ht = self.rows()
            ht = ht.select().select_globals()
            return Env.backend().execute(
                ir.TableToValueApply(ht._tir, {'name': 'TableCalculateNewPartitions', 'nPartitions': n_partitions})
            )
    
    
    
    
    matrix_table_type.set(MatrixTable)

---


# Source code for hail.expr.expressions.expression_utils

    
    
    from typing import Dict, Set
    
    from hail.typecheck import setof, typecheck
    
    from ...ir import MakeTuple
    from ..expressions import Expression, ExpressionException, expr_any
    from .indices import Aggregation, Indices
    
    
    @typecheck(caller=str, expr=Expression, expected_indices=Indices, aggregation_axes=setof(str), broadcast=bool)
    def analyze(caller: str, expr: Expression, expected_indices: Indices, aggregation_axes: Set = set(), broadcast=True):
        from hail.utils import error, warning
    
        indices = expr._indices
        source = indices.source
        axes = indices.axes
        aggregations = expr._aggregations
    
        warnings = []
        errors = []
    
        expected_source = expected_indices.source
        expected_axes = expected_indices.axes
    
        if source is not None and source is not expected_source:
            bad_refs = []
            for name, inds in get_refs(expr).items():
                if inds.source is not expected_source:
                    bad_refs.append(name)
            errors.append(
                ExpressionException(
                    "'{caller}': source mismatch\n"
                    "  Expected an expression from source {expected}\n"
                    "  Found expression derived from source {actual}\n"
                    "  Problematic field(s): {bad_refs}\n\n"
                    "  This error is commonly caused by chaining methods together:\n"
                    "    >>> ht.distinct().select(ht.x)\n\n"
                    "  Correct usage:\n"
                    "    >>> ht = ht.distinct()\n"
                    "    >>> ht = ht.select(ht.x)".format(
                        caller=caller, expected=expected_source, actual=source, bad_refs=list(bad_refs)
                    )
                )
            )
    
        # check for stray indices by subtracting expected axes from observed
        if broadcast:
            unexpected_axes = axes - expected_axes
            strictness = ''
        else:
            unexpected_axes = axes if axes != expected_axes else set()
            strictness = 'strictly '
    
        if unexpected_axes:
            # one or more out-of-scope fields
            refs = get_refs(expr)
            bad_refs = []
            for name, inds in refs.items():
                if broadcast:
                    bad_axes = inds.axes.intersection(unexpected_axes)
                    if bad_axes:
                        bad_refs.append((name, inds))
                elif inds.axes != expected_axes:
                    bad_refs.append((name, inds))
    
            assert len(bad_refs) > 0
            errors.append(
                ExpressionException(
                    "scope violation: '{caller}' expects an expression {strictness}indexed by {expected}"
                    "\n    Found indices {axes}, with unexpected indices {stray}. Invalid fields:{fields}{agg}".format(
                        caller=caller,
                        strictness=strictness,
                        expected=list(expected_axes),
                        axes=list(indices.axes),
                        stray=list(unexpected_axes),
                        fields=''.join(
                            "\n        '{}' (indices {})".format(name, list(inds.axes)) for name, inds in bad_refs
                        ),
                        agg=''
                        if (unexpected_axes - aggregation_axes)
                        else "\n    '{}' supports aggregation over axes {}, "
                        "so these fields may appear inside an aggregator function.".format(caller, list(aggregation_axes)),
                    )
                )
            )
    
        if aggregations:
            if aggregation_axes:
                # the expected axes of aggregated expressions are the expected axes + axes aggregated over
                expected_agg_axes = expected_axes.union(aggregation_axes)
    
                for agg in aggregations:
                    assert isinstance(agg, Aggregation)
                    refs = get_refs(*agg.exprs)
                    agg_axes = agg.agg_axes()
    
                    # check for stray indices
                    unexpected_agg_axes = agg_axes - expected_agg_axes
                    if unexpected_agg_axes:
                        # one or more out-of-scope fields
                        bad_refs = []
                        for name, inds in refs.items():
                            bad_axes = inds.axes.intersection(unexpected_agg_axes)
                            if bad_axes:
                                bad_refs.append((name, inds))
    
                        assert len(bad_refs) > 0
    
                        errors.append(
                            ExpressionException(
                                "scope violation: '{caller}' supports aggregation over indices {expected}"
                                "\n    Found indices {axes}, with unexpected indices {stray}. Invalid fields:{fields}".format(
                                    caller=caller,
                                    expected=list(aggregation_axes),
                                    axes=list(agg_axes),
                                    stray=list(unexpected_agg_axes),
                                    fields=''.join(
                                        "\n        '{}' (indices {})".format(name, list(inds.axes))
                                        for name, inds in bad_refs
                                    ),
                                )
                            )
                        )
            else:
                errors.append(ExpressionException("'{}' does not support aggregation".format(caller)))
    
        for w in warnings:
            warning('{}'.format(w.msg))
        if errors:
            for e in errors:
                error('{}'.format(e.msg))
            raise errors[0]
    
    
    @typecheck(expression=expr_any)
    def eval_timed(expression):
        """Evaluate a Hail expression, returning the result and the times taken for
        each stage in the evaluation process.
    
        Parameters
        ----------
        expression : ``.Expression``
            Any expression, or a Python value that can be implicitly interpreted as an expression.
    
        Returns
        -------
        (Any, dict)
            Result of evaluating `expression` and a dictionary of the timings
        """
    
        from hail.utils.java import Env
    
        analyze('eval', expression, Indices(expression._indices.source))
        if expression._indices.source is None:
            ir_type = expression._ir.typ
            expression_type = expression.dtype
            if ir_type != expression.dtype:
                raise ExpressionException(f'Expression type and IR type differed: \n{ir_type}\n vs \n{expression_type}')
            ir = expression._ir
        else:
            uid = Env.get_uid()
            ir = expression._indices.source.select_globals(**{uid: expression}).index_globals()[uid]._ir
    
        (value, timings) = Env.backend().execute(MakeTuple([ir]), timed=True)
        return value[0], timings
    
    
    
    
    [[docs]](../../../../expressions.html#hail.expr.eval)@typecheck(expression=expr_any)
    def eval(expression):
        """Evaluate a Hail expression, returning the result.
    
        This method is extremely useful for learning about Hail expressions and
        understanding how to compose them.
    
        The expression must have no indices, but can refer to the globals
        of a ``.Table`` or ``.MatrixTable``.
    
        Examples
        --------
        Evaluate a conditional:
    
        >>> x = 6
        >>> hl.eval(hl.if_else(x % 2 == 0, 'Even', 'Odd'))
        'Even'
    
        Parameters
        ----------
        expression : ``.Expression``
            Any expression, or a Python value that can be implicitly interpreted as an expression.
    
        Returns
        -------
        Any
        """
        return eval_timed(expression)[0]
    
    
    
    
    @typecheck(expression=expr_any)
    def eval_typed(expression):
        """Evaluate a Hail expression, returning the result and the type of the result.
    
        This method is extremely useful for learning about Hail expressions and understanding
        how to compose them.
    
        The expression must have no indices, but can refer to the globals
        of a ``.hail.Table`` or ``.hail.MatrixTable``.
    
        Examples
        --------
        Evaluate a conditional:
    
        >>> x = 6
        >>> hl.eval_typed(hl.if_else(x % 2 == 0, 'Even', 'Odd'))
        ('Even', dtype('str'))
    
        Parameters
        ----------
        expression : ``.Expression``
            Any expression, or a Python value that can be implicitly interpreted as an expression.
    
        Returns
        -------
        (any, ``.HailType``)
            Result of evaluating `expression`, and its type.
    
        """
        return eval(expression), expression.dtype
    
    
    def _get_refs(expr: Expression, builder: Dict[str, Indices]) -> None:
        from hail.ir import GetField, TopLevelReference
    
        for ir in expr._ir.search(
            lambda a: (isinstance(a, GetField) and not a.name.startswith('__uid') and isinstance(a.o, TopLevelReference))
        ):
            src = expr._indices.source
            builder[ir.name] = src._indices_from_ref[ir.o.name]
    
    
    def extract_refs_by_indices(exprs, indices):
        """Returns a set of references in `exprs` with indices `indices`.
    
        Parameters
        ----------
        expr : Expression
        indices : Indices
    
        Returns
        -------
        Set[str]
        """
        s = set()
        for e in exprs:
            for name, inds in get_refs(e).items():
                if inds == indices:
                    s.add(name)
        return s
    
    
    def get_refs(*exprs: Expression) -> Dict[str, Indices]:
        builder = {}
        for e in exprs:
            _get_refs(e, builder)
        return builder
    
    
    @typecheck(caller=str, expr=Expression)
    def matrix_table_source(caller, expr):
        from hail import MatrixTable
    
        source = expr._indices.source
        if not isinstance(source, MatrixTable):
            raise ValueError(
                "{}: Expect an expression of 'MatrixTable', found {}".format(
                    caller, "expression of '{}'".format(source.__class__) if source is not None else 'scalar expression'
                )
            )
        return source
    
    
    @typecheck(caller=str, expr=Expression)
    def table_source(caller, expr):
        from hail import Table
    
        source = expr._indices.source
        if not isinstance(source, Table):
            raise ValueError(
                "{}: Expect an expression of 'Table', found {}".format(
                    caller, "expression of '{}'".format(source.__class__) if source is not None else 'scalar expression'
                )
            )
        return source
    
    
    @typecheck(caller=str, expr=Expression)
    def raise_unless_entry_indexed(caller, expr):
        if expr._indices.source is None:
            raise ExpressionException(f"{caller}: expression must be entry-indexed, found no indices (no source)")
        if expr._indices != expr._indices.source._entry_indices:
            raise ExpressionException(
                f"{caller}: expression must be entry-indexed, found indices {list(expr._indices.axes)}."
            )
    
    
    @typecheck(caller=str, expr=Expression)
    def raise_unless_row_indexed(caller, expr):
        if expr._indices.source is None:
            raise ExpressionException(f"{caller}: expression must be row-indexed, found no indices (no source).")
        if expr._indices != expr._indices.source._row_indices:
            raise ExpressionException(
                f"{caller}: expression must be row-indexed, found indices {list(expr._indices.axes)}."
            )
    
    
    @typecheck(caller=str, expr=Expression)
    def raise_unless_column_indexed(caller, expr):
        if expr._indices.source is None:
            raise ExpressionException(f"{caller}: expression must be column-indexed, found no indices (no source).")
        if expr._indices != expr._indices.source._col_indices:
            raise ExpressionException(
                f"{caller}: expression must be column-indexed, found indices ({list(expr._indices.axes)})."
            )

---

# Source code for hail.expr.types

    
    
    import abc
    import builtins
    import json
    import math
    import pprint
    from collections.abc import Mapping, Sequence
    from typing import ClassVar, Union
    
    import numpy as np
    import pandas as pd
    
    import hail as hl
    from hailtop.frozendict import frozendict
    from hailtop.hail_frozenlist import frozenlist
    
    from .. import genetics
    from ..genetics.reference_genome import reference_genome_type
    from ..typecheck import nullable, oneof, transformed, typecheck, typecheck_method
    from ..utils.byte_reader import ByteReader, ByteWriter
    from ..utils.java import escape_parsable
    from ..utils.misc import lookup_bit
    from ..utils.struct import Struct
    from .nat import NatBase, NatLiteral
    from .type_parsing import type_grammar, type_grammar_str, type_node_visitor
    
    __all__ = [
        'HailType',
        'dtype',
        'dtypes_from_pandas',
        'hail_type',
        'hts_entry_schema',
        'is_compound',
        'is_container',
        'is_numeric',
        'is_primitive',
        'tarray',
        'tbool',
        'tcall',
        'tdict',
        'tfloat',
        'tfloat32',
        'tfloat64',
        'tint',
        'tint32',
        'tint64',
        'tinterval',
        'tlocus',
        'tndarray',
        'tset',
        'tstr',
        'tstream',
        'tstruct',
        'ttuple',
        'tunion',
        'tvariable',
        'tvoid',
        'types_match',
    ]
    
    
    def summary_type(t):
        if isinstance(t, hl.tdict):
            return f'dict<{summary_type(t.key_type)}, {summary_type(t.value_type)}>'
        elif isinstance(t, hl.tset):
            return f'set<{summary_type(t.element_type)}>'
        elif isinstance(t, hl.tarray):
            return f'array<{summary_type(t.element_type)}>'
        elif isinstance(t, hl.tstruct):
            return f'struct with {len(t)} fields'
        elif isinstance(t, hl.ttuple):
            return f'tuple with {len(t)} fields'
        elif isinstance(t, hl.tinterval):
            return f'interval<{summary_type(t.point_type)}>'
        else:
            return str(t)
    
    
    
    
    [[docs]](../../../types.html#hail.expr.types.dtype)def dtype(type_str) -> 'HailType':
        tree = type_grammar.parse(type_str)
        return type_node_visitor.visit(tree)
    
    
    
    
    dtype.__doc__ = f"""\
    Parse a type from its string representation.
    
    Examples
    --------
    
    >>> hl.dtype('int')
    dtype('int32')
    
    >>> hl.dtype('float')
    dtype('float64')
    
    >>> hl.dtype('array<int32>')
    dtype('array<int32>')
    
    >>> hl.dtype('dict<str, bool>')
    dtype('dict<str, bool>')
    
    >>> hl.dtype('struct{{a: int32, `field with spaces`: int64}}')
    dtype('struct{{a: int32, `field with spaces`: int64}}')
    
    Notes
    -----
    This function is able to reverse ``str(t)`` on a ``.HailType``.
    
    The grammar is defined as follows:
    
    .. code-block:: text
        {type_grammar_str}
    
    Parameters
    ----------
    type_str : ``str``
        String representation of type.
    
    Returns
    -------
    ``.HailType``
    """
    
    
    class HailTypeContext(object):
        def __init__(self, references=set()):
            self.references = references
    
        @property
        def is_empty(self):
            return len(self.references) == 0
    
        def _to_json_context(self):
            if self._json is None:
                self._json = {'reference_genomes': {r: hl.get_reference(r)._config for r in self.references}}
            return self._json
    
        @classmethod
        def union(cls, *types):
            ctxs = [t.get_context() for t in types if not t.get_context().is_empty]
            if len(ctxs) == 0:
                return _empty_context
            if len(ctxs) == 1:
                return ctxs[0]
            refs = ctxs[0].references.union(*[ctx.references for ctx in ctxs[1:]])
            return HailTypeContext(refs)
    
    
    _empty_context = HailTypeContext()
    
    
    
    
    [[docs]](../../../types.html#hail.expr.types.HailType)class HailType(object):
        """
        Hail type superclass.
        """
    
        def __init__(self):
            super(HailType, self).__init__()
            self._context = None
    
        def __repr__(self):
            s = str(self).replace("'", "\\'")
            return "dtype('{}')".format(s)
    
        @abc.abstractmethod
        def _eq(self, other):
            raise NotImplementedError
    
        def __eq__(self, other):
            return isinstance(other, HailType) and self._eq(other)
    
        @abc.abstractmethod
        def __str__(self):
            raise NotImplementedError
    
        def __hash__(self):
            # FIXME this is a bit weird
            return 43 + hash(str(self))
    
        def pretty(self, indent=0, increment=4):
            """Returns a prettily formatted string representation of the type.
    
            Parameters
            ----------
            indent : ``int``
                Spaces to indent.
    
            Returns
            -------
            ``str``
            """
            b = []
            b.append(' ' * indent)
            self._pretty(b, indent, increment)
            return ''.join(b)
    
        def _pretty(self, b, indent, increment):
            b.append(str(self))
    
        @abc.abstractmethod
        def _parsable_string(self) -> str:
            raise NotImplementedError
    
        def typecheck(self, value):
            """Check that `value` matches a type.
    
            Parameters
            ----------
            value
                Value to check.
    
            Raises
            ------
            ``TypeError``
            """
    
            def check(t, obj):
                t._typecheck_one_level(obj)
                return True
    
            self._traverse(value, check)
    
        @abc.abstractmethod
        def _typecheck_one_level(self, annotation):
            raise NotImplementedError
    
        def _to_json(self, x):
            converted = self._convert_to_json_na(x)
            return json.dumps(converted)
    
        def _convert_to_json_na(self, x):
            if x is None:
                return x
            else:
                return self._convert_to_json(x)
    
        def _convert_to_json(self, x):
            return x
    
        def _from_json(self, s):
            x = json.loads(s)
            return self._convert_from_json_na(x)
    
        def _convert_from_json_na(self, x, _should_freeze: bool = False):
            if x is None:
                return x
            else:
                return self._convert_from_json(x, _should_freeze)
    
        def _convert_from_json(self, x, _should_freeze: bool = False):
            return x
    
        def _from_encoding(self, encoding):
            return self._convert_from_encoding(ByteReader(memoryview(encoding)))
    
        def _to_encoding(self, value) -> bytes:
            buf = bytearray()
            self._convert_to_encoding(ByteWriter(buf), value)
            return bytes(buf)
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False):
            raise ValueError("Not implemented yet")
    
        def _convert_to_encoding(self, byte_writer, value):
            raise ValueError("Not implemented yet")
    
        @staticmethod
        def _missing(value):
            return value is None or value is pd.NA
    
        def _traverse(self, obj, f):
            """Traverse a nested type and object.
    
            Parameters
            ----------
            obj : Any
            f : Callable[[HailType, Any], bool]
                Function to evaluate on the type and object. Traverse children if
                the function returns ``True``.
            """
            f(self, obj)
    
        @abc.abstractmethod
        def unify(self, t):
            raise NotImplementedError
    
        @abc.abstractmethod
        def subst(self):
            raise NotImplementedError
    
        @abc.abstractmethod
        def clear(self):
            raise NotImplementedError
    
        def _get_context(self):
            return _empty_context
    
        def get_context(self):
            if self._context is None:
                self._context = self._get_context()
            return self._context
    
        def to_numpy(self):
            return object
    
    
    
    
    hail_type = oneof(HailType, transformed((str, dtype)), type(None))
    
    
    class _tvoid(HailType):
        def __init__(self):
            super(_tvoid, self).__init__()
    
        def __str__(self):
            return "void"
    
        def _eq(self, other):
            return isinstance(other, _tvoid)
    
        def _parsable_string(self):
            return "Void"
    
        def unify(self, t):
            return t == tvoid
    
        def subst(self):
            return self
    
        def clear(self):
            pass
    
        def _convert_from_encoding(self, *_):
            raise ValueError("Cannot decode void type")
    
        def _convert_to_encoding(self, *_):
            raise ValueError("Cannot encode void type")
    
    
    class _tint32(HailType):
        """Hail type for signed 32-bit integers.
    
        Their values can range from ``-2^{31}`` to ``2^{31} - 1``
        (approximately 2.15 billion).
    
        In Python, these are represented as ``int``.
        """
    
        def __init__(self):
            super(_tint32, self).__init__()
    
        def _typecheck_one_level(self, annotation):
            if annotation is not None:
                if not is_int32(annotation):
                    raise TypeError("type 'tint32' expected Python 'int', but found type '%s'" % type(annotation))
                elif not self.min_value <= annotation <= self.max_value:
                    raise TypeError(
                        f"Value out of range for 32-bit integer: "
                        f"expected [{self.min_value}, {self.max_value}], found {annotation}"
                    )
    
        def __str__(self):
            return "int32"
    
        def _eq(self, other):
            return isinstance(other, _tint32)
    
        def _parsable_string(self):
            return "Int32"
    
        @property
        def min_value(self):
            return -(1 << 31)
    
        @property
        def max_value(self):
            return (1 << 31) - 1
    
        def unify(self, t):
            return t == tint32
    
        def subst(self):
            return self
    
        def clear(self):
            pass
    
        def to_numpy(self):
            return np.int32
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> int:
            return byte_reader.read_int32()
    
        def _convert_to_encoding(self, byte_writer: ByteWriter, value):
            byte_writer.write_int32(value)
    
        def _byte_size(self):
            return 4
    
    
    class _tint64(HailType):
        """Hail type for signed 64-bit integers.
    
        Their values can range from ``-2^{63}`` to ``2^{63} - 1``.
    
        In Python, these are represented as ``int``.
        """
    
        def __init__(self):
            super(_tint64, self).__init__()
    
        def _typecheck_one_level(self, annotation):
            if annotation is not None:
                if not is_int64(annotation):
                    raise TypeError("type 'int64' expected Python 'int', but found type '%s'" % type(annotation))
                if not self.min_value <= annotation <= self.max_value:
                    raise TypeError(
                        f"Value out of range for 64-bit integer: "
                        f"expected [{self.min_value}, {self.max_value}], found {annotation}"
                    )
    
        def __str__(self):
            return "int64"
    
        def _eq(self, other):
            return isinstance(other, _tint64)
    
        def _parsable_string(self):
            return "Int64"
    
        @property
        def min_value(self):
            return -(1 << 63)
    
        @property
        def max_value(self):
            return (1 << 63) - 1
    
        def unify(self, t):
            return t == tint64
    
        def subst(self):
            return self
    
        def clear(self):
            pass
    
        def to_numpy(self):
            return np.int64
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> int:
            return byte_reader.read_int64()
    
        def _convert_to_encoding(self, byte_writer: ByteWriter, value):
            byte_writer.write_int64(value)
    
        def _byte_size(self):
            return 8
    
    
    class _tfloat32(HailType):
        """Hail type for 32-bit floating point numbers.
    
        In Python, these are represented as ``float``.
        """
    
        def __init__(self):
            super(_tfloat32, self).__init__()
    
        def _typecheck_one_level(self, annotation):
            if annotation is not None and not is_float32(annotation):
                raise TypeError("type 'float32' expected Python 'float', but found type '%s'" % type(annotation))
    
        def __str__(self):
            return "float32"
    
        def _eq(self, other):
            return isinstance(other, _tfloat32)
    
        def _parsable_string(self):
            return "Float32"
    
        def _convert_from_json(self, x, _should_freeze: bool = False):
            return float(x)
    
        def _convert_to_json(self, x):
            if math.isfinite(x):
                return x
            else:
                return str(x)
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> float:
            return byte_reader.read_float32()
    
        def _convert_to_encoding(self, byte_writer: ByteWriter, value):
            byte_writer.write_float32(value)
    
        def unify(self, t):
            return t == tfloat32
    
        def subst(self):
            return self
    
        def clear(self):
            pass
    
        def to_numpy(self):
            return np.float32
    
        def _byte_size(self):
            return 4
    
    
    class _tfloat64(HailType):
        """Hail type for 64-bit floating point numbers.
    
        In Python, these are represented as ``float``.
        """
    
        def __init__(self):
            super(_tfloat64, self).__init__()
    
        def _typecheck_one_level(self, annotation):
            if annotation is not None and not is_float64(annotation):
                raise TypeError("type 'float64' expected Python 'float', but found type '%s'" % type(annotation))
    
        def __str__(self):
            return "float64"
    
        def _eq(self, other):
            return isinstance(other, _tfloat64)
    
        def _parsable_string(self):
            return "Float64"
    
        def _convert_from_json(self, x, _should_freeze: bool = False):
            return float(x)
    
        def _convert_to_json(self, x):
            if math.isfinite(x):
                return x
            else:
                return str(x)
    
        def unify(self, t):
            return t == tfloat64
    
        def subst(self):
            return self
    
        def clear(self):
            pass
    
        def to_numpy(self):
            return np.float64
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> float:
            return byte_reader.read_float64()
    
        def _convert_to_encoding(self, byte_writer: ByteWriter, value):
            byte_writer.write_float64(value)
    
        def _byte_size(self):
            return 8
    
    
    class _tstr(HailType):
        """Hail type for text strings.
    
        In Python, these are represented as strings.
        """
    
        def __init__(self):
            super(_tstr, self).__init__()
    
        def _typecheck_one_level(self, annotation):
            if annotation and not isinstance(annotation, str):
                raise TypeError("type 'str' expected Python 'str', but found type '%s'" % type(annotation))
    
        def __str__(self):
            return "str"
    
        def _eq(self, other):
            return isinstance(other, _tstr)
    
        def _parsable_string(self):
            return "String"
    
        def unify(self, t):
            return t == tstr
    
        def subst(self):
            return self
    
        def clear(self):
            pass
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> str:
            length = byte_reader.read_int32()
            str_literal = byte_reader.read_bytes(length).decode('utf-8')
    
            return str_literal
    
        def _convert_to_encoding(self, byte_writer: ByteWriter, value):
            value_bytes = value.encode('utf-8')
            byte_writer.write_int32(len(value_bytes))
            byte_writer.write_bytes(value_bytes)
    
    
    class _tbool(HailType):
        """Hail type for Boolean (``True`` or ``False``) values.
    
        In Python, these are represented as ``bool``.
        """
    
        def __init__(self):
            super(_tbool, self).__init__()
    
        def _typecheck_one_level(self, annotation):
            if annotation is not None and not isinstance(annotation, bool):
                raise TypeError("type 'bool' expected Python 'bool', but found type '%s'" % type(annotation))
    
        def __str__(self):
            return "bool"
    
        def _eq(self, other):
            return isinstance(other, _tbool)
    
        def _parsable_string(self):
            return "Boolean"
    
        def unify(self, t):
            return t == tbool
    
        def subst(self):
            return self
    
        def clear(self):
            pass
    
        def to_numpy(self):
            return bool
    
        def _byte_size(self):
            return 1
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> bool:
            return byte_reader.read_bool()
    
        def _convert_to_encoding(self, byte_writer: ByteWriter, value):
            byte_writer.write_bool(value)
    
    
    class _trngstate(HailType):
        def __init__(self):
            super(_trngstate, self).__init__()
    
        def __str__(self):
            return "rng_state"
    
        def _eq(self, other):
            return isinstance(other, _trngstate)
    
        def _parsable_string(self):
            return "RNGState"
    
        def unify(self, t):
            return t == trngstate
    
        def subst(self):
            return self
    
        def clear(self):
            pass
    
    
    
    
    [[docs]](../../../types.html#hail.expr.types.tndarray)class tndarray(HailType):
        """Hail type for n-dimensional arrays.
    
        .. include:: _templates/experimental.rst
    
        In Python, these are represented as NumPy ``numpy.ndarray``.
    
        Notes
        -----
    
        NDArrays contain elements of only one type, which is parameterized by
        `element_type`.
    
        Parameters
        ----------
        element_type : ``.HailType``
            Element type of array.
        ndim : int32
            Number of dimensions.
    
        See Also
        --------
        ``.NDArrayExpression``, ``.nd.array``
        """
    
        @typecheck_method(element_type=hail_type, ndim=oneof(NatBase, int))
        def __init__(self, element_type, ndim):
            self._element_type = element_type
            self._ndim = NatLiteral(ndim) if isinstance(ndim, int) else ndim
            super(tndarray, self).__init__()
    
        @property
        def element_type(self):
            """NDArray element type.
    
            Returns
            -------
            ``.HailType``
                Element type.
            """
            return self._element_type
    
        @property
        def ndim(self):
            """NDArray number of dimensions.
    
            Returns
            -------
            ``int``
                Number of dimensions.
            """
            assert isinstance(self._ndim, NatLiteral), "tndarray must be realized with a concrete number of dimensions"
            return self._ndim.n
    
        def _traverse(self, obj, f):
            if f(self, obj):
                for elt in np.nditer(obj, ['zerosize_ok']):
                    self.element_type._traverse(elt.item(), f)
    
        def _typecheck_one_level(self, annotation):
            if annotation is not None and not isinstance(annotation, np.ndarray):
                raise TypeError("type 'ndarray' expected Python 'numpy.ndarray', but found type '%s'" % type(annotation))
    
        def __str__(self):
            return "ndarray<{}, {}>".format(self.element_type, self.ndim)
    
        def _eq(self, other):
            return isinstance(other, tndarray) and self.element_type == other.element_type and self.ndim == other.ndim
    
        def _pretty(self, b, indent, increment):
            b.append('ndarray<')
            self._element_type._pretty(b, indent, increment)
            b.append(', ')
            b.append(str(self.ndim))
            b.append('>')
    
        def _parsable_string(self):
            return f'NDArray[{self._element_type._parsable_string()},{self.ndim}]'
    
        def _convert_from_json(self, x, _should_freeze: bool = False) -> np.ndarray:
            if is_numeric(self._element_type):
                np_type = self.element_type.to_numpy()
                return np.ndarray(shape=x['shape'], buffer=np.array(x['data'], dtype=np_type), dtype=np_type)
            else:
                raise TypeError("Hail cannot currently return ndarrays of non-numeric or boolean type.")
    
        def _convert_to_json(self, x):
            data = x.flatten("C").tolist()
    
            strides = []
            axis_one_step_byte_size = x.itemsize
            for dimension_size in x.shape:
                strides.append(axis_one_step_byte_size)
                axis_one_step_byte_size *= dimension_size if dimension_size > 0 else 1
    
            json_dict = {"shape": x.shape, "data": data}
            return json_dict
    
        def clear(self):
            self._element_type.clear()
            self._ndim.clear()
    
        def unify(self, t):
            return isinstance(t, tndarray) and self._element_type.unify(t._element_type) and self._ndim.unify(t._ndim)
    
        def subst(self):
            return tndarray(self._element_type.subst(), self._ndim.subst())
    
        def _get_context(self):
            return self.element_type.get_context()
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> np.ndarray:
            shape = [byte_reader.read_int64() for i in range(self.ndim)]
            total_num_elements = np.prod(shape, dtype=np.int64)
    
            if self.element_type in _numeric_types:
                element_byte_size = self.element_type._byte_size
                bytes_to_read = element_byte_size * total_num_elements
                buffer = byte_reader.read_bytes_view(bytes_to_read)
                return np.frombuffer(buffer, self.element_type.to_numpy, count=total_num_elements).reshape(shape)
            else:
                elements = [
                    self.element_type._convert_from_encoding(byte_reader, _should_freeze) for i in range(total_num_elements)
                ]
                np_type = self.element_type.to_numpy()
                return np.ndarray(shape=shape, buffer=np.array(elements, dtype=np_type), dtype=np_type, order="F")
    
        def _convert_to_encoding(self, byte_writer, value: np.ndarray):
            for dim in value.shape:
                byte_writer.write_int64(dim)
    
            if value.size > 0:
                if self.element_type in _numeric_types:
                    byte_writer.write_bytes(value.data)
                else:
                    for elem in np.nditer(value, order='F'):
                        self.element_type._convert_to_encoding(byte_writer, elem)
    
    
    
    
    
    
    [[docs]](../../../types.html#hail.expr.types.tarray)class tarray(HailType):
        """Hail type for variable-length arrays of elements.
    
        In Python, these are represented as ``list``.
    
        Notes
        -----
        Arrays contain elements of only one type, which is parameterized by
        `element_type`.
    
        Parameters
        ----------
        element_type : ``.HailType``
            Element type of array.
    
        See Also
        --------
        ``.ArrayExpression``, ``.CollectionExpression``,
        ``~hail.expr.functions.array``, ``sec-collection-functions``
        """
    
        @typecheck_method(element_type=hail_type)
        def __init__(self, element_type):
            self._element_type = element_type
            super(tarray, self).__init__()
    
        @property
        def element_type(self):
            """Array element type.
    
            Returns
            -------
            ``.HailType``
                Element type.
            """
            return self._element_type
    
        def _traverse(self, obj, f):
            if f(self, obj):
                for elt in obj:
                    self.element_type._traverse(elt, f)
    
        def _typecheck_one_level(self, annotation):
            if annotation is not None:
                if not isinstance(annotation, Sequence):
                    raise TypeError("type 'array' expected Python 'list', but found type '%s'" % type(annotation))
    
        def __str__(self):
            return "array<{}>".format(self.element_type)
    
        def _eq(self, other):
            return isinstance(other, tarray) and self.element_type == other.element_type
    
        def _pretty(self, b, indent, increment):
            b.append('array<')
            self.element_type._pretty(b, indent, increment)
            b.append('>')
    
        def _parsable_string(self):
            return "Array[" + self.element_type._parsable_string() + "]"
    
        def _convert_from_json(self, x, _should_freeze: bool = False) -> Union[list, frozenlist]:
            ls = [self.element_type._convert_from_json_na(elt, _should_freeze) for elt in x]
            if _should_freeze:
                return frozenlist(ls)
            return ls
    
        def _convert_to_json(self, x):
            return [self.element_type._convert_to_json_na(elt) for elt in x]
    
        def _propagate_jtypes(self, jtype):
            self._element_type._add_jtype(jtype.elementType())
    
        def unify(self, t):
            return isinstance(t, tarray) and self.element_type.unify(t.element_type)
    
        def subst(self):
            return tarray(self.element_type.subst())
    
        def clear(self):
            self.element_type.clear()
    
        def _get_context(self):
            return self.element_type.get_context()
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> Union[list, frozenlist]:
            length = byte_reader.read_int32()
    
            num_missing_bytes = math.ceil(length / 8)
            missing_bytes = byte_reader.read_bytes_view(num_missing_bytes)
    
            decoded = []
            i = 0
            current_missing_byte = None
            while i < length:
                which_missing_bit = i % 8
                if which_missing_bit == 0:
                    current_missing_byte = missing_bytes[i // 8]
    
                if lookup_bit(current_missing_byte, which_missing_bit):
                    decoded.append(None)
                else:
                    element_decoded = self.element_type._convert_from_encoding(byte_reader, _should_freeze)
                    decoded.append(element_decoded)
                i += 1
            if _should_freeze:
                return frozenlist(decoded)
            return decoded
    
        def _convert_to_encoding(self, byte_writer: ByteWriter, value):
            length = len(value)
            byte_writer.write_int32(length)
            i = 0
            while i < length:
                missing_byte = 0
                for j in range(min(8, length - i)):
                    if HailType._missing(value[i + j]):
                        missing_byte |= 1 << j
                byte_writer.write_byte(missing_byte)
                i += 8
    
            for element in value:
                if not HailType._missing(element):
                    self.element_type._convert_to_encoding(byte_writer, element)
    
    
    
    
    class tstream(HailType):
        @typecheck_method(element_type=hail_type)
        def __init__(self, element_type):
            self._element_type = element_type
            super(tstream, self).__init__()
    
        @property
        def element_type(self):
            return self._element_type
    
        def _traverse(self, obj, f):
            if f(self, obj):
                for elt in obj:
                    self.element_type._traverse(elt, f)
    
        def _typecheck_one_level(self, annotation):
            raise TypeError("type 'stream' is not realizable in Python")
    
        def __str__(self):
            return "stream<{}>".format(self.element_type)
    
        def _eq(self, other):
            return isinstance(other, tstream) and self.element_type == other.element_type
    
        def _pretty(self, b, indent, increment):
            b.append('stream<')
            self.element_type._pretty(b, indent, increment)
            b.append('>')
    
        def _parsable_string(self):
            return "Stream[" + self.element_type._parsable_string() + "]"
    
        def _convert_from_json(self, x, _should_freeze: bool = False) -> Union[list, frozenlist]:
            ls = [self.element_type._convert_from_json_na(elt, _should_freeze) for elt in x]
            if _should_freeze:
                return frozenlist(ls)
            return ls
    
        def _convert_to_json(self, x):
            return [self.element_type._convert_to_json_na(elt) for elt in x]
    
        def _propagate_jtypes(self, jtype):
            self._element_type._add_jtype(jtype.elementType())
    
        def unify(self, t):
            return isinstance(t, tstream) and self.element_type.unify(t.element_type)
    
        def subst(self):
            return tstream(self.element_type.subst())
    
        def clear(self):
            self.element_type.clear()
    
        def _get_context(self):
            return self.element_type.get_context()
    
    
    def is_setlike(maybe_setlike):
        return isinstance(maybe_setlike, (set, frozenset))
    
    
    
    
    [[docs]](../../../types.html#hail.expr.types.tset)class tset(HailType):
        """Hail type for collections of distinct elements.
    
        In Python, these are represented as ``set``.
    
        Notes
        -----
        Sets contain elements of only one type, which is parameterized by
        `element_type`.
    
        Parameters
        ----------
        element_type : ``.HailType``
            Element type of set.
    
        See Also
        --------
        ``.SetExpression``, ``.CollectionExpression``,
        ``.set``, ``sec-collection-functions``
        """
    
        @typecheck_method(element_type=hail_type)
        def __init__(self, element_type):
            self._element_type = element_type
            self._array_repr = tarray(element_type)
            super(tset, self).__init__()
    
        @property
        def element_type(self):
            """Set element type.
    
            Returns
            -------
            ``.HailType``
                Element type.
            """
            return self._element_type
    
        def _traverse(self, obj, f):
            if f(self, obj):
                for elt in obj:
                    self.element_type._traverse(elt, f)
    
        def _typecheck_one_level(self, annotation):
            if annotation is not None:
                if not is_setlike(annotation):
                    raise TypeError("type 'set' expected Python 'set', but found type '%s'" % type(annotation))
    
        def __str__(self):
            return "set<{}>".format(self.element_type)
    
        def _eq(self, other):
            return isinstance(other, tset) and self.element_type == other.element_type
    
        def _pretty(self, b, indent, increment):
            b.append('set<')
            self.element_type._pretty(b, indent, increment)
            b.append('>')
    
        def _parsable_string(self):
            return "Set[" + self.element_type._parsable_string() + "]"
    
        def _convert_from_json(self, x, _should_freeze: bool = False) -> Union[set, frozenset]:
            s = {self.element_type._convert_from_json_na(elt, _should_freeze=True) for elt in x}
            if _should_freeze:
                return frozenset(s)
            return s
    
        def _convert_to_json(self, x):
            return [self.element_type._convert_to_json_na(elt) for elt in x]
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> Union[set, frozenset]:
            s = self._array_repr._convert_from_encoding(byte_reader, _should_freeze=True)
            if _should_freeze:
                return frozenset(s)
            return set(s)
    
        def _convert_to_encoding(self, byte_writer: ByteWriter, value):
            self._array_repr._convert_to_encoding(byte_writer, list(value))
    
        def _propagate_jtypes(self, jtype):
            self._element_type._add_jtype(jtype.elementType())
    
        def unify(self, t):
            return isinstance(t, tset) and self.element_type.unify(t.element_type)
    
        def subst(self):
            return tset(self.element_type.subst())
    
        def clear(self):
            self.element_type.clear()
    
        def _get_context(self):
            return self.element_type.get_context()
    
    
    
    
    class _freeze_this_type(HailType):
        def __init__(self, t):
            self.t = t
    
        def _convert_from_json_na(self, x, _should_freeze: bool = False):
            return self.t._convert_from_json_na(x, _should_freeze=True)
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False):
            return self.t._convert_from_encoding(byte_reader, _should_freeze=True)
    
        def _convert_to_encoding(self, byte_writer, x):
            return self.t._convert_to_encoding(byte_writer, x)
    
    
    
    
    [[docs]](../../../types.html#hail.expr.types.tdict)class tdict(HailType):
        """Hail type for key-value maps.
    
        In Python, these are represented as ``dict``.
    
        Notes
        -----
        Dicts parameterize the type of both their keys and values with
        `key_type` and `value_type`.
    
        Parameters
        ----------
        key_type: ``.HailType``
            Key type.
        value_type: ``.HailType``
            Value type.
    
        See Also
        --------
        ``.DictExpression``, ``.dict``, ``sec-collection-functions``
        """
    
        @typecheck_method(key_type=hail_type, value_type=hail_type)
        def __init__(self, key_type, value_type):
            self._key_type = key_type
            self._value_type = value_type
            self._array_repr = tarray(tstruct(key=_freeze_this_type(key_type), value=value_type))
            super(tdict, self).__init__()
    
        @property
        def key_type(self):
            """Dict key type.
    
            Returns
            -------
            ``.HailType``
                Key type.
            """
            return self._key_type
    
        @property
        def value_type(self):
            """Dict value type.
    
            Returns
            -------
            ``.HailType``
                Value type.
            """
            return self._value_type
    
        @property
        def element_type(self):
            return tstruct(key=self._key_type, value=self._value_type)
    
        def _traverse(self, obj, f):
            if f(self, obj):
                for k, v in obj.items():
                    self.key_type._traverse(k, f)
                    self.value_type._traverse(v, f)
    
        def _typecheck_one_level(self, annotation):
            if annotation is not None:
                if not isinstance(annotation, Mapping):
                    raise TypeError("type 'dict' expected Python 'Mapping', but found type '%s'" % type(annotation))
    
        def __str__(self):
            return "dict<{}, {}>".format(self.key_type, self.value_type)
    
        def _eq(self, other):
            return isinstance(other, tdict) and self.key_type == other.key_type and self.value_type == other.value_type
    
        def _pretty(self, b, indent, increment):
            b.append('dict<')
            self.key_type._pretty(b, indent, increment)
            b.append(', ')
            self.value_type._pretty(b, indent, increment)
            b.append('>')
    
        def _parsable_string(self):
            return "Dict[{},{}]".format(self.key_type._parsable_string(), self.value_type._parsable_string())
    
        def _convert_from_json(self, x, _should_freeze: bool = False) -> Union[dict, frozendict]:
            d = {
                self.key_type._convert_from_json_na(elt['key'], _should_freeze=True): self.value_type._convert_from_json_na(
                    elt['value'], _should_freeze=_should_freeze
                )
                for elt in x
            }
            if _should_freeze:
                return frozendict(d)
            return d
    
        def _convert_to_json(self, x):
            return [
                {'key': self.key_type._convert_to_json(k), 'value': self.value_type._convert_to_json(v)}
                for k, v in x.items()
            ]
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> Union[dict, frozendict]:
            # NB: We ensure the key is always frozen with a wrapper on the key_type in the _array_repr.
            d = {}
            length = byte_reader.read_int32()
            for _ in range(length):
                element = self._array_repr.element_type._convert_from_encoding(byte_reader, _should_freeze)
                d[element.key] = element.value
    
            if _should_freeze:
                return frozendict(d)
            return d
    
        def _convert_to_encoding(self, byte_writer: ByteWriter, value):
            length = len(value)
            byte_writer.write_int32(length)
            for k, v in value.items():
                self._array_repr.element_type._convert_to_encoding(byte_writer, {'key': k, 'value': v})
    
        def _propagate_jtypes(self, jtype):
            self._key_type._add_jtype(jtype.keyType())
            self._value_type._add_jtype(jtype.valueType())
    
        def unify(self, t):
            return isinstance(t, tdict) and self.key_type.unify(t.key_type) and self.value_type.unify(t.value_type)
    
        def subst(self):
            return tdict(self._key_type.subst(), self._value_type.subst())
    
        def clear(self):
            self.key_type.clear()
            self.value_type.clear()
    
        def _get_context(self):
            return HailTypeContext.union(self.key_type, self.value_type)
    
    
    
    
    
    
    [[docs]](../../../types.html#hail.expr.types.tstruct)class tstruct(HailType, Mapping):
        """Hail type for structured groups of heterogeneous fields.
    
        In Python, these are represented as ``.Struct``.
    
        Hail's ``.tstruct`` type is commonly used to compose types together to form nested
        structures. Structs can contain any combination of types, and are ordered mappings
        from field name to field type. Each field name must be unique.
    
        Structs are very common in Hail. Each component of a ``.Table`` and ``.MatrixTable``
        is a struct:
    
        - ``.Table.row``
        - ``.Table.globals``
        - ``.MatrixTable.row``
        - ``.MatrixTable.col``
        - ``.MatrixTable.entry``
        - ``.MatrixTable.globals``
    
        Structs appear below the top-level component types as well. Consider the following join:
    
        >>> new_table = table1.annotate(table2_fields = table2.index(table1.key))
    
        This snippet adds a field to ``table1`` called ``table2_fields``. In the new table,
        ``table2_fields`` will be a struct containing all the non-key fields from ``table2``.
    
        Parameters
        ----------
        field_types : keyword args of ``.HailType``
            Fields.
    
        See Also
        --------
        ``.StructExpression``, ``.Struct``
        """
    
        @typecheck_method(field_types=hail_type)
        def __init__(*args, **field_types):
            if len(args) < 1:
                raise TypeError("__init__() missing 1 required positional argument: 'self'")
            if len(args) > 1:
                raise TypeError(f"__init__() takes 1 positional argument but {len(args)} were given")
            self = args[0]
            self._field_types = field_types
            self._fields = tuple(field_types)
            super(tstruct, self).__init__()
    
        @property
        def types(self):
            """Struct field types.
    
            Returns
            -------
            ``tuple`` of ``.HailType``
            """
            return tuple(self._field_types.values())
    
        @property
        def fields(self):
            """Struct field names.
    
            Returns
            -------
            ``tuple`` of ``str``
                Tuple of struct field names.
            """
            return self._fields
    
        def _traverse(self, obj, f):
            if f(self, obj):
                for k, v in obj.items():
                    t = self[k]
                    t._traverse(v, f)
    
        def _typecheck_one_level(self, annotation):
            if annotation:
                if isinstance(annotation, Mapping):
                    s = set(self)
                    for f in annotation:
                        if f not in s:
                            raise TypeError(
                                "type '%s' expected fields '%s', but found fields '%s'"
                                % (self, list(self), list(annotation))
                            )
                else:
                    raise TypeError(
                        "type 'struct' expected type Mapping (e.g. dict or hail.utils.Struct), but found '%s'"
                        % type(annotation)
                    )
    
        @typecheck_method(item=oneof(int, str))
        def __getitem__(self, item):
            if not isinstance(item, str):
                item = self._fields[item]
            return self._field_types[item]
    
        def __iter__(self):
            return iter(self._field_types)
    
        def __len__(self):
            return len(self._fields)
    
        def __str__(self):
            return "struct{{{}}}".format(', '.join('{}: {}'.format(escape_parsable(f), str(t)) for f, t in self.items()))
    
        def items(self):
            return self._field_types.items()
    
        def _eq(self, other):
            return (
                isinstance(other, tstruct)
                and self._fields == other._fields
                and all(self[f] == other[f] for f in self._fields)
            )
    
        def _pretty(self, b, indent, increment):
            if not self._fields:
                b.append('struct {}')
                return
    
            pre_indent = indent
            indent += increment
            b.append('struct {')
            for i, (f, t) in enumerate(self.items()):
                if i > 0:
                    b.append(', ')
                b.append('\n')
                b.append(' ' * indent)
                b.append('{}: '.format(escape_parsable(f)))
                t._pretty(b, indent, increment)
            b.append('\n')
            b.append(' ' * pre_indent)
            b.append('}')
    
        def _parsable_string(self):
            return "Struct{{{}}}".format(
                ','.join('{}:{}'.format(escape_parsable(f), t._parsable_string()) for f, t in self.items())
            )
    
        def _convert_from_json(self, x, _should_freeze: bool = False) -> Struct:
            return Struct(**{f: t._convert_from_json_na(x.get(f), _should_freeze) for f, t in self._field_types.items()})
    
        def _convert_to_json(self, x):
            return {f: t._convert_to_json_na(x[f]) for f, t in self.items()}
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> Struct:
            num_missing_bytes = math.ceil(len(self) / 8)
            missing_bytes = byte_reader.read_bytes_view(num_missing_bytes)
    
            kwargs = {}
    
            current_missing_byte = None
            for i, (f, t) in enumerate(self._field_types.items()):
                which_missing_bit = i % 8
                if which_missing_bit == 0:
                    current_missing_byte = missing_bytes[i // 8]
    
                if lookup_bit(current_missing_byte, which_missing_bit):
                    kwargs[f] = None
                else:
                    field_decoded = t._convert_from_encoding(byte_reader, _should_freeze)
                    kwargs[f] = field_decoded
    
            return Struct(**kwargs)
    
        def _convert_to_encoding(self, byte_writer: ByteWriter, value):
            keys = list(self.keys())
            length = len(keys)
            i = 0
            while i < length:
                missing_byte = 0
                for j in range(min(8, length - i)):
                    if HailType._missing(value[keys[i + j]]):
                        missing_byte |= 1 << j
                byte_writer.write_byte(missing_byte)
                i += 8
    
            for f, t in self.items():
                if not HailType._missing(value[f]):
                    t._convert_to_encoding(byte_writer, value[f])
    
        def _is_prefix_of(self, other):
            return (
                isinstance(other, tstruct)
                and len(self._fields) <= len(other._fields)
                and all(x == y for x, y in zip(self._field_types.values(), other._field_types.values()))
            )
    
        def _concat(self, other):
            new_field_types = {}
            new_field_types.update(self._field_types)
            new_field_types.update(other._field_types)
            return tstruct(**new_field_types)
    
        def _insert(self, path, t):
            if not path:
                return t
    
            key = path[0]
            keyt = self.get(key)
            if not (keyt and isinstance(keyt, tstruct)):
                keyt = tstruct()
            return self._insert_fields(**{key: keyt._insert(path[1:], t)})
    
        def _insert_field(self, field, typ):
            return self._insert_fields(**{field: typ})
    
        def _insert_fields(self, **new_fields):
            new_field_types = {}
            new_field_types.update(self._field_types)
            new_field_types.update(new_fields)
            return tstruct(**new_field_types)
    
        def _drop_fields(self, fields):
            return tstruct(**{f: t for f, t in self.items() if f not in fields})
    
        def _select_fields(self, fields):
            return tstruct(**{f: self[f] for f in fields})
    
        def _index_path(self, path):
            t = self
            for p in path:
                t = t[p]
            return t
    
        def _rename(self, map):
            seen = {}
            new_field_types = {}
    
            for f0, t in self.items():
                f = map.get(f0, f0)
                if f in seen:
                    raise ValueError(
                        "Cannot rename two fields to the same name: attempted to rename {} and {} both to {}".format(
                            repr(seen[f]), repr(f0), repr(f)
                        )
                    )
                else:
                    seen[f] = f0
                    new_field_types[f] = t
    
            return tstruct(**new_field_types)
    
        def unify(self, t):
            if not (isinstance(t, tstruct) and len(self) == len(t)):
                return False
            for (f1, t1), (f2, t2) in zip(self.items(), t.items()):
                if not (f1 == f2 and t1.unify(t2)):
                    return False
            return True
    
        def subst(self):
            return tstruct(**{f: t.subst() for f, t in self.items()})
    
        def clear(self):
            for f, t in self.items():
                t.clear()
    
        def _get_context(self):
            return HailTypeContext.union(*self.values())
    
    
    
    
    class tunion(HailType, Mapping):
        @typecheck_method(case_types=hail_type)
        def __init__(self, **case_types):
            """Tagged union type.  Values of type union represent one of several
            heterogenous, named cases.
    
            Parameters
            ----------
            cases : keyword args of ``.HailType``
                The union cases.
    
            """
    
            super(tunion, self).__init__()
            self._case_types = case_types
            self._cases = tuple(case_types)
    
        @property
        def cases(self):
            """Return union case names.
    
            Returns
            -------
            ``tuple`` of ``str``
                Tuple of union case names
            """
            return self._cases
    
        @typecheck_method(item=oneof(int, str))
        def __getitem__(self, item):
            if isinstance(item, int):
                item = self._cases[item]
            return self._case_types[item]
    
        def __iter__(self):
            return iter(self._case_types)
    
        def __len__(self):
            return len(self._cases)
    
        def __str__(self):
            return "union{{{}}}".format(', '.join('{}: {}'.format(escape_parsable(f), str(t)) for f, t in self.items()))
    
        def _eq(self, other):
            return (
                isinstance(other, tunion) and self._cases == other._cases and all(self[c] == other[c] for c in self._cases)
            )
    
        def _pretty(self, b, indent, increment):
            if not self._cases:
                b.append('union {}')
                return
    
            pre_indent = indent
            indent += increment
            b.append('union {')
            for i, (f, t) in enumerate(self.items()):
                if i > 0:
                    b.append(', ')
                b.append('\n')
                b.append(' ' * indent)
                b.append('{}: '.format(escape_parsable(f)))
                t._pretty(b, indent, increment)
            b.append('\n')
            b.append(' ' * pre_indent)
            b.append('}')
    
        def _parsable_string(self):
            return "Union{{{}}}".format(
                ','.join('{}:{}'.format(escape_parsable(f), t._parsable_string()) for f, t in self.items())
            )
    
        def unify(self, t):
            if not (isinstance(t, tunion) and len(self) == len(t)):
                return False
            for (f1, t1), (f2, t2) in zip(self.items(), t.items()):
                if not (f1 == f2 and t1.unify(t2)):
                    return False
            return True
    
        def subst(self):
            return tunion(**{f: t.subst() for f, t in self.items()})
    
        def clear(self):
            for f, t in self.items():
                t.clear()
    
        def _get_context(self):
            return HailTypeContext.union(*self.values())
    
    
    
    
    [[docs]](../../../types.html#hail.expr.types.ttuple)class ttuple(HailType, Sequence):
        """Hail type for tuples.
    
        In Python, these are represented as ``tuple``.
    
        Parameters
        ----------
        types: varargs of ``.HailType``
            Element types.
    
        See Also
        --------
        ``.TupleExpression``
        """
    
        @typecheck_method(types=hail_type)
        def __init__(self, *types):
            self._types = types
            super(ttuple, self).__init__()
    
        @property
        def types(self):
            """Tuple element types.
    
            Returns
            -------
            ``tuple`` of ``.HailType``
            """
            return self._types
    
        def _traverse(self, obj, f):
            if f(self, obj):
                for t, elt in zip(self.types, obj):
                    t._traverse(elt, f)
    
        def _typecheck_one_level(self, annotation):
            if annotation:
                if not isinstance(annotation, tuple):
                    raise TypeError("type 'tuple' expected Python tuple, but found '%s'" % type(annotation))
                if len(annotation) != len(self.types):
                    raise TypeError("%s expected tuple of size '%i', but found '%s'" % (self, len(self.types), annotation))
    
        @typecheck_method(item=int)
        def __getitem__(self, item):
            return self._types[item]
    
        def __iter__(self):
            for i in range(len(self)):
                yield self[i]
    
        def __len__(self):
            return len(self._types)
    
        def __str__(self):
            return "tuple({})".format(", ".join([str(t) for t in self.types]))
    
        def _eq(self, other):
            from operator import eq
    
            return (
                isinstance(other, ttuple) and len(self.types) == len(other.types) and all(map(eq, self.types, other.types))
            )
    
        def _pretty(self, b, indent, increment):
            pre_indent = indent
            indent += increment
            b.append('tuple (')
            for i, t in enumerate(self.types):
                if i > 0:
                    b.append(', ')
                b.append('\n')
                b.append(' ' * indent)
                t._pretty(b, indent, increment)
            b.append('\n')
            b.append(' ' * pre_indent)
            b.append(')')
    
        def _parsable_string(self):
            return "Tuple[{}]".format(",".join([t._parsable_string() for t in self.types]))
    
        def _convert_from_json(self, x, _should_freeze: bool = False) -> tuple:
            return tuple(self.types[i]._convert_from_json_na(x[i], _should_freeze) for i in range(len(self.types)))
    
        def _convert_to_json(self, x):
            return [self.types[i]._convert_to_json_na(x[i]) for i in range(len(self.types))]
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> tuple:
            num_missing_bytes = math.ceil(len(self) / 8)
            missing_bytes = byte_reader.read_bytes_view(num_missing_bytes)
    
            answer = []
            current_missing_byte = None
            for i, t in enumerate(self.types):
                which_missing_bit = i % 8
                if which_missing_bit == 0:
                    current_missing_byte = missing_bytes[i // 8]
    
                if lookup_bit(current_missing_byte, which_missing_bit):
                    answer.append(None)
                else:
                    field_decoded = t._convert_from_encoding(byte_reader, _should_freeze)
                    answer.append(field_decoded)
    
            return tuple(answer)
    
        def _convert_to_encoding(self, byte_writer, value):
            length = len(self)
            i = 0
            while i < length:
                missing_byte = 0
                for j in range(min(8, length - i)):
                    if HailType._missing(value[i + j]):
                        missing_byte |= 1 << j
                byte_writer.write_byte(missing_byte)
                i += 8
            for i, t in enumerate(self.types):
                if not HailType._missing(value[i]):
                    t._convert_to_encoding(byte_writer, value[i])
    
        def unify(self, t):
            if not (isinstance(t, ttuple) and len(self.types) == len(t.types)):
                return False
            for t1, t2 in zip(self.types, t.types):
                if not t1.unify(t2):
                    return False
            return True
    
        def subst(self):
            return ttuple(*[t.subst() for t in self.types])
    
        def clear(self):
            for t in self.types:
                t.clear()
    
        def _get_context(self):
            return HailTypeContext.union(*self.types)
    
    
    
    
    def allele_pair(j: int, k: int):
        assert j >= 0 and j <= 0xFFFF
        assert k >= 0 and k <= 0xFFFF
        return j | (k << 16)
    
    
    def allele_pair_sqrt(i):
        k = int(math.sqrt(8 * float(i) + 1) / 2 - 0.5)
        assert k * (k + 1) // 2 <= i
        j = i - k * (k + 1) // 2
        # TODO another assert
        return allele_pair(j, k)
    
    
    small_allele_pair = [
        allele_pair(0, 0),
        allele_pair(0, 1),
        allele_pair(1, 1),
        allele_pair(0, 2),
        allele_pair(1, 2),
        allele_pair(2, 2),
        allele_pair(0, 3),
        allele_pair(1, 3),
        allele_pair(2, 3),
        allele_pair(3, 3),
        allele_pair(0, 4),
        allele_pair(1, 4),
        allele_pair(2, 4),
        allele_pair(3, 4),
        allele_pair(4, 4),
        allele_pair(0, 5),
        allele_pair(1, 5),
        allele_pair(2, 5),
        allele_pair(3, 5),
        allele_pair(4, 5),
        allele_pair(5, 5),
        allele_pair(0, 6),
        allele_pair(1, 6),
        allele_pair(2, 6),
        allele_pair(3, 6),
        allele_pair(4, 6),
        allele_pair(5, 6),
        allele_pair(6, 6),
        allele_pair(0, 7),
        allele_pair(1, 7),
        allele_pair(2, 7),
        allele_pair(3, 7),
        allele_pair(4, 7),
        allele_pair(5, 7),
        allele_pair(6, 7),
        allele_pair(7, 7),
    ]
    
    
    class _tcall(HailType):
        """Hail type for a diploid genotype.
    
        In Python, these are represented by ``.Call``.
        """
    
        def __init__(self):
            super(_tcall, self).__init__()
    
        def _typecheck_one_level(self, annotation):
            if annotation is not None and not isinstance(annotation, genetics.Call):
                raise TypeError("type 'call' expected Python hail.genetics.Call, but found %s'" % type(annotation))
    
        def __str__(self):
            return "call"
    
        def _eq(self, other):
            return isinstance(other, _tcall)
    
        def _parsable_string(self):
            return "Call"
    
        def _convert_from_json(self, x, _should_freeze: bool = False) -> genetics.Call:
            if x == '-':
                return genetics.Call([])
            if x == '|-':
                return genetics.Call([], phased=True)
            if x[0] == '|':
                return genetics.Call([int(x[1:])], phased=True)
    
            n = len(x)
            i = 0
            while i < n:
                c = x[i]
                if c in '|/':
                    break
                i += 1
    
            if i == n:
                return genetics.Call([int(x)])
    
            return genetics.Call([int(x[0:i]), int(x[i + 1 :])], phased=(c == '|'))
    
        def _convert_to_json(self, x):
            return str(x)
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> genetics.Call:
            int_rep = byte_reader.read_int32()
            int_rep = int_rep if int_rep >= 0 else int_rep + 2**32
    
            ploidy = (int_rep >> 1) & 0x3
            phased = (int_rep & 1) == 1
    
            def allele_repr(c):
                return c >> 3
    
            def ap_j(p):
                return p & 0xFFFF
    
            def ap_k(p):
                return (p >> 16) & 0xFFFF
    
            def gt_allele_pair(i):
                assert i >= 0, "allele pair value should never be negative"
                if i < len(small_allele_pair):
                    return small_allele_pair[i]
                return allele_pair_sqrt(i)
    
            def call_allele_pair(i):
                if phased:
                    rep = allele_repr(i)
                    p = gt_allele_pair(rep)
                    j = ap_j(p)
                    k = ap_k(p)
                    return allele_pair(j, k - j)
                else:
                    rep = allele_repr(i)
                    return gt_allele_pair(rep)
    
            if ploidy == 0:
                alleles = []
            elif ploidy == 1:
                alleles = [allele_repr(int_rep)]
            elif ploidy == 2:
                p = call_allele_pair(int_rep)
                alleles = [ap_j(p), ap_k(p)]
            else:
                raise ValueError("Unsupported Ploidy")
    
            return genetics.Call(alleles, phased)
    
        def _convert_to_encoding(self, byte_writer, value: genetics.Call):
            int_rep = 0
    
            int_rep |= value.ploidy << 1
            if value.phased:
                int_rep |= 1
    
            def diploid_gt_index(j: int, k: int):
                assert j <= k
                return k * (k + 1) // 2 + j
    
            def allele_pair_rep(c: genetics.Call):
                [j, k] = c.alleles
                if c.phased:
                    return diploid_gt_index(j, j + k)
                return diploid_gt_index(j, k)
    
            assert value.ploidy <= 2
            if value.ploidy == 1:
                int_rep |= value.alleles[0] << 3
            elif value.ploidy == 2:
                int_rep |= allele_pair_rep(value) << 3
            int_rep = int_rep if 0 <= int_rep < 2**31 - 1 else int_rep - 2**32
    
            byte_writer.write_int32(int_rep)
    
        def unify(self, t):
            return t == tcall
    
        def subst(self):
            return self
    
        def clear(self):
            pass
    
    
    
    
    [[docs]](../../../types.html#hail.expr.types.tlocus)class tlocus(HailType):
        """Hail type for a genomic coordinate with a contig and a position.
    
        In Python, these are represented by ``.Locus``.
    
        Parameters
        ----------
        reference_genome: ``.ReferenceGenome`` or ``str``
            Reference genome to use.
    
        See Also
        --------
        ``.LocusExpression``, ``.locus``, ``.parse_locus``,
        ``.Locus``
        """
    
        struct_repr = tstruct(contig=_tstr(), pos=_tint32())
    
        @classmethod
        @typecheck_method(reference_genome=nullable(reference_genome_type))
        def _schema_from_rg(cls, reference_genome='default'):
            # must match TLocus.schemaFromRG
            if reference_genome is None:
                return hl.tstruct(contig=hl.tstr, position=hl.tint32)
            return cls(reference_genome)
    
        @typecheck_method(reference_genome=reference_genome_type)
        def __init__(self, reference_genome='default'):
            self._rg = reference_genome
            super(tlocus, self).__init__()
    
        def _typecheck_one_level(self, annotation):
            if annotation is not None:
                if not isinstance(annotation, genetics.Locus):
                    raise TypeError(
                        "type '{}' expected Python hail.genetics.Locus, but found '{}'".format(self, type(annotation))
                    )
                if not self.reference_genome == annotation.reference_genome:
                    raise TypeError(
                        "type '{}' encountered Locus with reference genome {}".format(
                            self, repr(annotation.reference_genome)
                        )
                    )
    
        def __str__(self):
            return "locus<{}>".format(escape_parsable(str(self.reference_genome)))
    
        def _parsable_string(self):
            return "Locus({})".format(escape_parsable(str(self.reference_genome)))
    
        def _eq(self, other):
            return isinstance(other, tlocus) and self.reference_genome == other.reference_genome
    
        @property
        def reference_genome(self):
            """Reference genome.
    
            Returns
            -------
            ``.ReferenceGenome``
                Reference genome.
            """
            if self._rg is None:
                self._rg = hl.default_reference()
            return self._rg
    
        def _pretty(self, b, indent, increment):
            b.append('locus<{}>'.format(escape_parsable(self.reference_genome.name)))
    
        def _convert_from_json(self, x, _should_freeze: bool = False) -> genetics.Locus:
            return genetics.Locus(x['contig'], x['position'], reference_genome=self.reference_genome)
    
        def _convert_to_json(self, x):
            return {'contig': x.contig, 'position': x.position}
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False) -> genetics.Locus:
            as_struct = tlocus.struct_repr._convert_from_encoding(byte_reader)
            return genetics.Locus(as_struct.contig, as_struct.pos, self.reference_genome)
    
        def _convert_to_encoding(self, byte_writer, value: genetics.Locus):
            tlocus.struct_repr._convert_to_encoding(byte_writer, {'contig': value.contig, 'pos': value.position})
    
        def unify(self, t):
            return isinstance(t, tlocus) and self.reference_genome == t.reference_genome
    
        def subst(self):
            return self
    
        def clear(self):
            pass
    
        def _get_context(self):
            return HailTypeContext(references={self.reference_genome.name})
    
    
    
    
    
    
    [[docs]](../../../types.html#hail.expr.types.tinterval)class tinterval(HailType):
        """Hail type for intervals of ordered values.
    
        In Python, these are represented by ``.Interval``.
    
        Parameters
        ----------
        point_type: ``.HailType``
            Interval point type.
    
        See Also
        --------
        ``.IntervalExpression``, ``.Interval``, ``.interval``,
        ``.parse_locus_interval``
        """
    
        @typecheck_method(point_type=hail_type)
        def __init__(self, point_type):
            self._point_type = point_type
            self._struct_repr = tstruct(start=point_type, end=point_type, includes_start=hl.tbool, includes_end=hl.tbool)
            super(tinterval, self).__init__()
    
        @property
        def point_type(self):
            """Interval point type.
    
            Returns
            -------
            ``.HailType``
                Interval point type.
            """
            return self._point_type
    
        def _traverse(self, obj, f):
            if f(self, obj):
                self.point_type._traverse(obj.start, f)
                self.point_type._traverse(obj.end, f)
    
        def _typecheck_one_level(self, annotation):
            from hail.utils import Interval
    
            if annotation is not None:
                if not isinstance(annotation, Interval):
                    raise TypeError(
                        "type '{}' expected Python hail.utils.Interval, but found {}".format(self, type(annotation))
                    )
                if annotation.point_type != self.point_type:
                    raise TypeError(
                        "type '{}' encountered Interval with point type {}".format(self, repr(annotation.point_type))
                    )
    
        def __str__(self):
            return "interval<{}>".format(str(self.point_type))
    
        def _eq(self, other):
            return isinstance(other, tinterval) and self.point_type == other.point_type
    
        def _pretty(self, b, indent, increment):
            b.append('interval<')
            self.point_type._pretty(b, indent, increment)
            b.append('>')
    
        def _parsable_string(self):
            return "Interval[{}]".format(self.point_type._parsable_string())
    
        def _convert_from_json(self, x, _should_freeze: bool = False):
            from hail.utils import Interval
    
            return Interval(
                self.point_type._convert_from_json_na(x['start'], _should_freeze),
                self.point_type._convert_from_json_na(x['end'], _should_freeze),
                x['includeStart'],
                x['includeEnd'],
                point_type=self.point_type,
            )
    
        def _convert_to_json(self, x):
            return {
                'start': self.point_type._convert_to_json_na(x.start),
                'end': self.point_type._convert_to_json_na(x.end),
                'includeStart': x.includes_start,
                'includeEnd': x.includes_end,
            }
    
        def _convert_from_encoding(self, byte_reader, _should_freeze: bool = False):
            interval_as_struct = self._struct_repr._convert_from_encoding(byte_reader, _should_freeze)
            return hl.Interval(
                interval_as_struct.start,
                interval_as_struct.end,
                interval_as_struct.includes_start,
                interval_as_struct.includes_end,
                point_type=self.point_type,
            )
    
        def _convert_to_encoding(self, byte_writer, value):
            interval_dict = {
                'start': value.start,
                'end': value.end,
                'includes_start': value.includes_start,
                'includes_end': value.includes_end,
            }
            self._struct_repr._convert_to_encoding(byte_writer, interval_dict)
    
        def unify(self, t):
            return isinstance(t, tinterval) and self.point_type.unify(t.point_type)
    
        def subst(self):
            return tinterval(self.point_type.subst())
    
        def clear(self):
            self.point_type.clear()
    
        def _get_context(self):
            return self.point_type.get_context()
    
    
    
    
    class Box(object):
        named_boxes: ClassVar = {}
    
        @staticmethod
        def from_name(name):
            if name in Box.named_boxes:
                return Box.named_boxes[name]
            b = Box()
            Box.named_boxes[name] = b
            return b
    
        def __init__(self):
            pass
    
        def unify(self, v):
            if hasattr(self, 'value'):
                return self.value == v
            self.value = v
            return True
    
        def clear(self):
            if hasattr(self, 'value'):
                del self.value
    
        def get(self):
            assert hasattr(self, 'value')
            return self.value
    
    
    tvoid = _tvoid()
    
    
    tint32 = _tint32()
    """Hail type for signed 32-bit integers.
    
    Their values can range from ``-2^{31}`` to ``2^{31} - 1``
    (approximately 2.15 billion).
    
    In Python, these are represented as ``int``.
    
    See Also
    --------
    ``.Int32Expression``, ``.int``, ``.int32``
    """
    
    
    tint64 = _tint64()
    """Hail type for signed 64-bit integers.
    
    Their values can range from ``-2^{63}`` to ``2^{63} - 1``.
    
    In Python, these are represented as ``int``.
    
    See Also
    --------
    ``.Int64Expression``, ``.int64``
    """
    
    tint = tint32
    """Alias for :py``.tint32``."""
    
    tfloat32 = _tfloat32()
    """Hail type for 32-bit floating point numbers.
    
    In Python, these are represented as ``float``.
    
    See Also
    --------
    ``.Float32Expression``, ``.float64``
    """
    
    tfloat64 = _tfloat64()
    """Hail type for 64-bit floating point numbers.
    
    In Python, these are represented as ``float``.
    
    See Also
    --------
    ``.Float64Expression``, ``.float``, ``.float64``
    """
    
    tfloat = tfloat64
    """Alias for :py``.tfloat64``."""
    
    tstr = _tstr()
    """Hail type for text strings.
    
    In Python, these are represented as strings.
    
    See Also
    --------
    ``.StringExpression``, ``.str``
    """
    
    tbool = _tbool()
    """Hail type for Boolean (``True`` or ``False``) values.
    
    In Python, these are represented as ``bool``.
    
    See Also
    --------
    ``.BooleanExpression``, ``.bool``
    """
    
    trngstate = _trngstate()
    
    tcall = _tcall()
    """Hail type for a diploid genotype.
    
    In Python, these are represented by ``.Call``.
    
    See Also
    --------
    ``.CallExpression``, ``.Call``, ``.call``, ``.parse_call``,
    ``.unphased_diploid_gt_index_call``
    """
    
    hts_entry_schema = tstruct(GT=tcall, AD=tarray(tint32), DP=tint32, GQ=tint32, PL=tarray(tint32))
    
    _numeric_types = {_tbool, _tint32, _tint64, _tfloat32, _tfloat64}
    _primitive_types = _numeric_types.union({_tstr})
    _interned_types = _primitive_types.union({_tcall})
    
    
    @typecheck(t=HailType)
    def is_numeric(t) -> bool:
        return t.__class__ in _numeric_types
    
    
    @typecheck(t=HailType)
    def is_primitive(t) -> bool:
        return t.__class__ in _primitive_types
    
    
    @typecheck(t=HailType)
    def is_container(t) -> bool:
        return isinstance(t, (tarray, tset, tdict))
    
    
    @typecheck(t=HailType)
    def is_compound(t) -> bool:
        return is_container(t) or isinstance(t, (tstruct, tunion, ttuple, tndarray))
    
    
    def types_match(left, right) -> bool:
        return len(left) == len(right) and all(map(lambda lr: lr[0].dtype == lr[1].dtype, zip(left, right)))
    
    
    def is_int32(x):
        return isinstance(x, (builtins.int, np.int32))
    
    
    def is_int64(x):
        return isinstance(x, (builtins.int, np.int64))
    
    
    def is_float32(x):
        return isinstance(x, (builtins.float, builtins.int, np.float32))
    
    
    def is_float64(x):
        return isinstance(x, (builtins.float, builtins.int, np.float64))
    
    
    def from_numpy(np_dtype):
        if np_dtype == np.int32:
            return tint32
        if np_dtype == np.int64:
            return tint64
        if np_dtype == np.float32:
            return tfloat32
        if np_dtype == np.float64:
            return tfloat64
        if np_dtype == np.bool:
            return tbool
        raise ValueError(f"numpy type {np_dtype} could not be converted to a hail type.")
    
    
    def dtypes_from_pandas(pd_dtype):
        if pd_dtype is pd.StringDtype:
            return hl.tstr
        if pd_dtype == np.int64:
            return hl.tint64
        if pd_dtype == np.uint64:
            # Hail does *not* support unsigned integers but the next condition,
            # pd.api.types.is_integer_dtype(pd_dtype) would return true on unsigned 64-bit ints
            return None
        # For some reason pandas doesn't have `is_int32_dtype`, so we use `is_integer_dtype` if first branch failed.
        if pd.api.types.is_integer_dtype(pd_dtype):
            return hl.tint32
        if pd_dtype == np.float32:
            return hl.tfloat32
        if pd_dtype == np.float64:
            return hl.tfloat64
        if pd_dtype == np.bool:
            return hl.tbool
        return None
    
    
    class tvariable(HailType):
        _cond_map: ClassVar = {
            'numeric': is_numeric,
            'int32': lambda x: x == tint32,
            'int64': lambda x: x == tint64,
            'float32': lambda x: x == tfloat32,
            'float64': lambda x: x == tfloat64,
            'locus': lambda x: isinstance(x, tlocus),
            'struct': lambda x: isinstance(x, tstruct),
            'union': lambda x: isinstance(x, tunion),
            'tuple': lambda x: isinstance(x, ttuple),
        }
    
        def __init__(self, name, cond):
            self.name = name
            self.cond = cond
            self.condf = tvariable._cond_map[cond] if cond else None
            self.box = Box.from_name(name)
    
        def unify(self, t):
            if self.condf and not self.condf(t):
                return False
            return self.box.unify(t)
    
        def clear(self):
            self.box.clear()
    
        def subst(self):
            return self.box.get()
    
        def __str__(self):
            s = '?' + self.name
            if self.cond:
                s = s + ':' + self.cond
            return s
    
    
    _old_printer = pprint.PrettyPrinter
    
    
    class TypePrettyPrinter(pprint.PrettyPrinter):
        def _format(self, object, stream, indent, allowance, context, level):
            if isinstance(object, HailType):
                stream.write(object.pretty(self._indent_per_level))
            else:
                return _old_printer._format(self, object, stream, indent, allowance, context, level)
    
    
    pprint.PrettyPrinter = TypePrettyPrinter  # monkey-patch pprint

---


# Source code for hail.plot.plots

    
    
    import collections
    import math
    import warnings
    from typing import Any, Callable, Dict, List, Optional, Sequence, Set, Tuple, Union
    
    import bokeh
    import bokeh.io
    import bokeh.models
    import bokeh.palettes
    import bokeh.plotting
    import numpy as np
    import pandas as pd
    from bokeh.layouts import gridplot
    from bokeh.models import (
        BasicTicker,
        CategoricalColorMapper,
        CDSView,
        ColorBar,
        ColorMapper,
        Column,
        ColumnDataSource,
        CustomJS,
        DataRange1d,
        GridPlot,
        GroupFilter,
        HoverTool,
        IntersectionFilter,
        Label,
        Legend,
        LegendItem,
        LinearColorMapper,
        LogColorMapper,
        LogTicker,
        Plot,
        Renderer,
        Select,
        Slope,
        Span,
    )
    from bokeh.plotting import figure
    from bokeh.transform import transform
    
    import hail
    from hail.expr import aggregators
    from hail.expr.expressions import (
        Expression,
        Float32Expression,
        Float64Expression,
        Int32Expression,
        Int64Expression,
        LocusExpression,
        NumericExpression,
        StringExpression,
        expr_any,
        expr_float64,
        expr_locus,
        expr_numeric,
        expr_str,
        raise_unless_row_indexed,
    )
    from hail.expr.functions import _error_from_cdf_python
    from hail.matrixtable import MatrixTable
    from hail.table import Table
    from hail.typecheck import dictof, nullable, numeric, oneof, sequenceof, sized_tupleof, typecheck
    from hail.utils.java import warning
    from hail.utils.struct import Struct
    
    palette = ['#1f77b4', '#ff7f0e', '#2ca02c', '#d62728', '#9467bd', '#8c564b', '#e377c2', '#7f7f7f', '#bcbd22', '#17becf']
    
    
    
    
    [[docs]](../../../plot.html#hail.plot.output_notebook)def output_notebook():
        """Configure the Bokeh output state to generate output in notebook
        cells when ``bokeh.io.show`` is called.  Calls
        ``bokeh.io.output_notebook``.
    
        """
        bokeh.io.output_notebook()
    
    
    
    
    def show(obj, interact=None):
        """Immediately display a Bokeh object or application.  Calls
        ``bokeh.io.show``.
    
        Parameters
        ----------
        obj
            A Bokeh object to display.
        interact
            A handle returned by a plotting method with `interactive=True`.
        """
        if interact is None:
            bokeh.io.show(obj)
        else:
            handle = bokeh.io.show(obj, notebook_handle=True)
            interact(handle)
    
    
    
    
    [[docs]](../../../plot.html#hail.plot.cdf)def cdf(data, k=350, legend=None, title=None, normalize=True, log=False) -> figure:
        """Create a cumulative density plot.
    
        Parameters
        ----------
        data : ``.Struct`` or ``.Float64Expression``
            Sequence of data to plot.
        k : int
            Accuracy parameter (passed to ``~.approx_cdf``).
        legend : str
            Label of data on the x-axis.
        title : str
            Title of the histogram.
        normalize: bool
            Whether or not the cumulative data should be normalized.
        log: bool
            Whether or not the y-axis should be of type log.
    
        Returns
        -------
        ``bokeh.plotting.figure``
        """
        if isinstance(data, Expression):
            if data._indices is None:
                raise ValueError('Invalid input')
            agg_f = data._aggregation_method()
            data = agg_f(aggregators.approx_cdf(data, k))
    
        if legend is None:
            legend = ""
    
        if normalize:
            y_axis_label = 'Quantile'
        else:
            y_axis_label = 'Rank'
        if log:
            y_axis_type = 'log'
        else:
            y_axis_type = 'linear'
        p = figure(
            title=title,
            x_axis_label=legend,
            y_axis_label=y_axis_label,
            y_axis_type=y_axis_type,
            width=600,
            height=400,
            background_fill_color='#EEEEEE',
            tools='xpan,xwheel_zoom,reset,save',
            active_scroll='xwheel_zoom',
        )
        p.add_tools(HoverTool(tooltips=[("value", "$x"), ("rank", "@top")], mode='vline'))
    
        ranks = np.array(data.ranks)
        values = np.array(data['values'])
        if normalize:
            ranks = ranks / ranks[-1]
    
        # invisible, there to support tooltips
        p.quad(top=ranks[1:-1], bottom=ranks[1:-1], left=values[:-1], right=values[1:], fill_alpha=0, line_alpha=0)
        p.step(x=[*values, values[-1]], y=ranks, line_width=2, line_color='black', legend_label=legend)
        return p
    
    
    
    
    
    
    [[docs]](../../../plot.html#hail.plot.pdf)def pdf(
        data, k=1000, confidence=5, legend=None, title=None, log=False, interactive=False
    ) -> Union[figure, Tuple[figure, Callable]]:
        if isinstance(data, Expression):
            if data._indices is None:
                raise ValueError('Invalid input')
            agg_f = data._aggregation_method()
            data = agg_f(aggregators.approx_cdf(data, k))
    
        if legend is None:
            legend = ""
    
        y_axis_label = 'Frequency'
        if log:
            y_axis_type = 'log'
        else:
            y_axis_type = 'linear'
        fig = figure(
            title=title,
            x_axis_label=legend,
            y_axis_label=y_axis_label,
            y_axis_type=y_axis_type,
            width=600,
            height=400,
            tools='xpan,xwheel_zoom,reset,save',
            active_scroll='xwheel_zoom',
            background_fill_color='#EEEEEE',
        )
    
        y = np.array(data['ranks'][1:-1]) / data['ranks'][-1]
        x = np.array(data['values'][1:-1])
        min_x = data['values'][0]
        max_x = data['values'][-1]
        err = _error_from_cdf_python(data, 10 ** (-confidence), all_quantiles=True)
    
        new_y, keep = _max_entropy_cdf(min_x, max_x, x, y, err)
        slopes = np.diff([0, *new_y[keep], 1]) / np.diff([min_x, *x[keep], max_x])
        if log:
            plot = fig.step(x=[min_x, *x[keep], max_x], y=[*slopes, slopes[-1]], mode='after')
        else:
            plot = fig.quad(left=[min_x, *x[keep]], right=[*x[keep], max_x], bottom=0, top=slopes, legend_label=legend)
    
        if interactive:
    
            def mk_interact(handle):
                def update(confidence=confidence):
                    err = _error_from_cdf_python(data, 10 ** (-confidence), all_quantiles=True) / 1.8
                    new_y, keep = _max_entropy_cdf(min_x, max_x, x, y, err)
                    slopes = np.diff([0, *new_y[keep], 1]) / np.diff([min_x, *x[keep], max_x])
                    if log:
                        new_data = {'x': [min_x, *x[keep], max_x], 'y': [*slopes, slopes[-1]]}
                    else:
                        new_data = {
                            'left': [min_x, *x[keep]],
                            'right': [*x[keep], max_x],
                            'bottom': np.full(len(slopes), 0),
                            'top': slopes,
                        }
                    plot.data_source.data = new_data
                    bokeh.io.push_notebook(handle=handle)
    
                from ipywidgets import interact
    
                interact(update, confidence=(1, 10, 0.01))
    
            return fig, mk_interact
        else:
            return fig
    
    
    
    
    def _max_entropy_cdf(min_x, max_x, x, y, e):
        def compare(x1, y1, x2, y2):
            return x1 * y2 - x2 * y1
    
        new_y = np.full_like(x, 0.0, dtype=np.float64)
        keep = np.full_like(x, False, dtype=np.bool_)
    
        fx = min_x  # fixed x
        fy = 0  # fixed y
        li = 0  # index of lower slope
        ui = 0  # index of upper slope
        ldx = x[li] - fx
        udx = x[ui] - fx
        ldy = y[li + 1] - e - fy
        udy = y[ui] + e - fy
        j = 1
        while ui < len(x) and li < len(x):
            if j == len(x):
                ub = 1
                lb = 1
                xj = max_x
            else:
                ub = y[j] + e
                lb = y[j + 1] - e
                xj = x[j]
            dx = xj - fx
            judy = ub - fy
            jldy = lb - fy
            if compare(ldx, ldy, dx, judy) < 0:
                # line must bend down at j
                fx = x[li]
                fy = y[li + 1] - e
                new_y[li] = fy
                keep[li] = True
                j = li + 1
                if j >= len(x):
                    break
                li = j
                ldx = x[li] - fx
                ldy = y[li + 1] - e - fy
                ui = j
                udx = x[ui] - fx
                udy = y[ui] + e - fy
                j += 1
                continue
            elif compare(udx, udy, dx, jldy) > 0:
                # line must bend up at j
                fx = x[ui]
                fy = y[ui] + e
                new_y[ui] = fy
                keep[ui] = True
                j = ui + 1
                if j >= len(x):
                    break
                li = j
                ldx = x[li] - fx
                ldy = y[li + 1] - e - fy
                ui = j
                udx = x[ui] - fx
                udy = y[ui] + e - fy
                j += 1
                continue
            if j >= len(x):
                break
            if compare(udx, udy, dx, judy) < 0:
                ui = j
                udx = x[ui] - fx
                udy = y[ui] + e - fy
            if compare(ldx, ldy, dx, jldy) > 0:
                li = j
                ldx = x[li] - fx
                ldy = y[li + 1] - e - fy
            j += 1
        return new_y, keep
    
    
    
    
    [[docs]](../../../plot.html#hail.plot.smoothed_pdf)def smoothed_pdf(
        data, k=350, smoothing=0.5, legend=None, title=None, log=False, interactive=False, figure=None
    ) -> Union[figure, Tuple[figure, Callable]]:
        """Create a density plot.
    
        Parameters
        ----------
        data : ``.Struct`` or ``.Float64Expression``
            Sequence of data to plot.
        k : int
            Accuracy parameter.
        smoothing : float
            Degree of smoothing.
        legend : str
            Label of data on the x-axis.
        title : str
            Title of the histogram.
        log : bool
            Plot the log10 of the bin counts.
        interactive : bool
            If `True`, return a handle to pass to ``bokeh.io.show``.
        figure : ``bokeh.plotting.figure``
            If not None, add density plot to figure. Otherwise, create a new figure.
    
        Returns
        -------
        ``bokeh.plotting.figure``
        """
        if isinstance(data, Expression):
            if data._indices is None:
                raise ValueError('Invalid input')
            agg_f = data._aggregation_method()
            data = agg_f(aggregators.approx_cdf(data, k))
    
        if legend is None:
            legend = ""
    
        y_axis_label = 'Frequency'
        if log:
            y_axis_type = 'log'
        else:
            y_axis_type = 'linear'
    
        if figure is None:
            p = bokeh.plotting.figure(
                title=title,
                x_axis_label=legend,
                y_axis_label=y_axis_label,
                y_axis_type=y_axis_type,
                width=600,
                height=400,
                tools='xpan,xwheel_zoom,reset,save',
                active_scroll='xwheel_zoom',
                background_fill_color='#EEEEEE',
            )
        else:
            p = figure
    
        n = data['ranks'][-1]
        weights = np.diff(data['ranks'][1:-1])
        min = data['values'][0]
        max = data['values'][-1]
        values = np.array(data['values'][1:-1])
        slope = 1 / (max - min)
    
        def f(x, prev, smoothing=smoothing):
            inv_scale = (np.sqrt(n * slope) / smoothing) * np.sqrt(prev / weights)
            diff = x[:, np.newaxis] - values
            grid = (3 / (4 * n)) * weights * np.maximum(0, inv_scale - np.power(diff, 2) * np.power(inv_scale, 3))
            return np.sum(grid, axis=1)
    
        round1 = f(values, np.full(len(values), slope))
        x_d = np.linspace(min, max, 1000)
        final = f(x_d, round1)
    
        line = p.line(x_d, final, line_width=2, line_color='black', legend_label=legend)
    
        if interactive:
    
            def mk_interact(handle):
                def update(smoothing=smoothing):
                    final = f(x_d, round1, smoothing)
                    line.data_source.data = {'x': x_d, 'y': final}
                    bokeh.io.push_notebook(handle=handle)
    
                from ipywidgets import interact
    
                interact(update, smoothing=(0.02, 0.8, 0.005))
    
            return p, mk_interact
        else:
            return p
    
    
    
    
    
    
    [[docs]](../../../plot.html#hail.plot.histogram)@typecheck(
        data=oneof(Struct, expr_float64),
        range=nullable(sized_tupleof(numeric, numeric)),
        bins=int,
        legend=nullable(str),
        title=nullable(str),
        log=bool,
        interactive=bool,
    )
    def histogram(
        data, range=None, bins=50, legend=None, title=None, log=False, interactive=False
    ) -> Union[figure, Tuple[figure, Callable]]:
        """Create a histogram.
    
        Notes
        -----
        `data` can be a ``.Float64Expression``, or the result of the ``~.aggregators.hist``
        or ``~.aggregators.approx_cdf`` aggregators.
    
        Parameters
        ----------
        data : ``.Struct`` or ``.Float64Expression``
            Sequence of data to plot.
        range : Tuple[float]
            Range of x values in the histogram.
        bins : int
            Number of bins in the histogram.
        legend : str
            Label of data on the x-axis.
        title : str
            Title of the histogram.
        log : bool
            Plot the log10 of the bin counts.
    
        Returns
        -------
        ``bokeh.plotting.figure``
        """
        if isinstance(data, Expression):
            if data._indices.source is not None:
                if interactive:
                    raise ValueError("'interactive' flag can only be used on data from 'approx_cdf'.")
                agg_f = data._aggregation_method()
                if range is not None:
                    start = range[0]
                    end = range[1]
                else:
                    finite_data = hail.bind(lambda x: hail.case().when(hail.is_finite(x), x).or_missing(), data)
                    start, end = agg_f((aggregators.min(finite_data), aggregators.max(finite_data)))
                    if start is None and end is None:
                        raise ValueError("'data' contains no values that are defined and finite")
                data = agg_f(aggregators.hist(data, start, end, bins))
            else:
                raise ValueError('Invalid input')
        elif 'values' in data:
            cdf = data
            hist, edges = np.histogram(cdf['values'], bins=bins, weights=np.diff(cdf.ranks), density=True)
            data = Struct(bin_freq=hist, bin_edges=edges, n_larger=0, n_smaller=0)
    
        if legend is None:
            legend = ""
    
        if log:
            bin_freq = []
            count_problems = 0
            for x in data.bin_freq:
                if x == 0.0:
                    count_problems += 1
                    bin_freq.append(x)
                else:
                    bin_freq.append(math.log10(x))
    
            if count_problems > 0:
                warning(
                    f"There were {count_problems} bins with height 0, those cannot be log transformed and were left as 0s."
                )
    
            changes = {
                "bin_freq": bin_freq,
                "n_larger": math.log10(data.n_larger) if data.n_larger > 0.0 else data.n_larger,
                "n_smaller": math.log10(data.n_smaller) if data.n_smaller > 0.0 else data.n_smaller,
            }
            data = data.annotate(**changes)
            y_axis_label = 'log10 Frequency'
        else:
            y_axis_label = 'Frequency'
    
        x_span = data.bin_edges[-1] - data.bin_edges[0]
        x_start = data.bin_edges[0] - 0.05 * x_span
        x_end = data.bin_edges[-1] + 0.05 * x_span
        p = figure(
            title=title,
            x_axis_label=legend,
            y_axis_label=y_axis_label,
            background_fill_color='#EEEEEE',
            x_range=(x_start, x_end),
        )
        q = p.quad(
            bottom=0,
            top=data.bin_freq,
            left=data.bin_edges[:-1],
            right=data.bin_edges[1:],
            legend_label=legend,
            line_color='black',
        )
        if data.n_larger > 0:
            p.quad(
                bottom=0,
                top=data.n_larger,
                left=data.bin_edges[-1],
                right=(data.bin_edges[-1] + (data.bin_edges[1] - data.bin_edges[0])),
                line_color='black',
                fill_color='green',
                legend_label='Outliers Above',
            )
        if data.n_smaller > 0:
            p.quad(
                bottom=0,
                top=data.n_smaller,
                left=data.bin_edges[0] - (data.bin_edges[1] - data.bin_edges[0]),
                right=data.bin_edges[0],
                line_color='black',
                fill_color='red',
                legend_label='Outliers Below',
            )
        if interactive:
    
            def mk_interact(handle):
                def update(bins=bins, phase=0):
                    if phase > 0 and phase < 1:
                        bins = bins + 1
                        delta = (cdf['values'][-1] - cdf['values'][0]) / bins
                        edges = np.linspace(cdf['values'][0] - (1 - phase) * delta, cdf['values'][-1] + phase * delta, bins)
                    else:
                        edges = np.linspace(cdf['values'][0], cdf['values'][-1], bins)
                    hist, edges = np.histogram(cdf['values'], bins=edges, weights=np.diff(cdf.ranks), density=True)
                    new_data = {'top': hist, 'left': edges[:-1], 'right': edges[1:], 'bottom': np.full(len(hist), 0)}
                    q.data_source.data = new_data
                    bokeh.io.push_notebook(handle=handle)
    
                from ipywidgets import interact
    
                interact(update, bins=(0, 5 * bins), phase=(0, 1, 0.01))
    
            return p, mk_interact
        else:
            return p
    
    
    
    
    
    
    [[docs]](../../../plot.html#hail.plot.cumulative_histogram)@typecheck(
        data=oneof(Struct, expr_float64),
        range=nullable(sized_tupleof(numeric, numeric)),
        bins=int,
        legend=nullable(str),
        title=nullable(str),
        normalize=bool,
        log=bool,
    )
    def cumulative_histogram(data, range=None, bins=50, legend=None, title=None, normalize=True, log=False) -> figure:
        """Create a cumulative histogram.
    
        Parameters
        ----------
        data : ``.Struct`` or ``.Float64Expression``
            Sequence of data to plot.
        range : Tuple[float]
            Range of x values in the histogram.
        bins : int
            Number of bins in the histogram.
        legend : str
            Label of data on the x-axis.
        title : str
            Title of the histogram.
        normalize: bool
            Whether or not the cumulative data should be normalized.
        log: bool
            Whether or not the y-axis should be of type log.
    
        Returns
        -------
        ``bokeh.plotting.figure``
        """
        if isinstance(data, Expression):
            if data._indices.source is not None:
                agg_f = data._aggregation_method()
                if range is not None:
                    start = range[0]
                    end = range[1]
                else:
                    start, end = agg_f((aggregators.min(data), aggregators.max(data)))
                data = agg_f(aggregators.hist(data, start, end, bins))
            else:
                raise ValueError('Invalid input')
    
        if legend is None:
            legend = ""
    
        cumulative_data = np.cumsum(data.bin_freq) + data.n_smaller
        np.append(cumulative_data, [cumulative_data[-1] + data.n_larger])
        num_data_points = max(cumulative_data)
    
        if normalize:
            cumulative_data = cumulative_data / num_data_points
        if title is not None:
            title = f'{title} ({num_data_points:,} data points)'
        if log:
            p = figure(
                title=title,
                x_axis_label=legend,
                y_axis_label='Frequency',
                background_fill_color='#EEEEEE',
                y_axis_type='log',
            )
        else:
            p = figure(title=title, x_axis_label=legend, y_axis_label='Frequency', background_fill_color='#EEEEEE')
        p.line(data.bin_edges[:-1], cumulative_data, line_color='#036564', line_width=3)
        return p
    
    
    
    
    @typecheck(p=figure, font_size=str)
    def set_font_size(p, font_size: str = '12pt'):
        """Set most of the font sizes in a bokeh figure
    
        Parameters
        ----------
        p : ``bokeh.plotting.figure``
            Input figure.
        font_size : str
            String of font size in points (e.g. '12pt').
    
        Returns
        -------
        ``bokeh.plotting.figure``
        """
        p.legend.label_text_font_size = font_size
        p.xaxis.axis_label_text_font_size = font_size
        p.yaxis.axis_label_text_font_size = font_size
        p.xaxis.major_label_text_font_size = font_size
        p.yaxis.major_label_text_font_size = font_size
        if hasattr(p.title, 'text_font_size'):
            p.title.text_font_size = font_size
        if hasattr(p.xaxis, 'group_text_font_size'):
            p.xaxis.group_text_font_size = font_size
        return p
    
    
    
    
    [[docs]](../../../plot.html#hail.plot.histogram2d)@typecheck(
        x=expr_numeric,
        y=expr_numeric,
        bins=oneof(int, sequenceof(int)),
        range=nullable(sized_tupleof(nullable(sized_tupleof(numeric, numeric)), nullable(sized_tupleof(numeric, numeric)))),
        title=nullable(str),
        width=int,
        height=int,
        colors=sequenceof(str),
        log=bool,
    )
    def histogram2d(
        x: NumericExpression,
        y: NumericExpression,
        bins: int = 40,
        range: Optional[Tuple[int, int]] = None,
        title: Optional[str] = None,
        width: int = 600,
        height: int = 600,
        colors: Sequence[str] = bokeh.palettes.all_palettes['Blues'][7][::-1],
        log: bool = False,
    ) -> figure:
        """Plot a two-dimensional histogram.
    
        ``x`` and ``y`` must both be a ``.NumericExpression`` from the same ``.Table``.
    
        If ``x_range`` or ``y_range`` are not provided, the function will do a pass through the data to determine
        min and max of each variable.
    
        Examples
        --------
    
        >>> ht = hail.utils.range_table(1000).annotate(x=hail.rand_norm(), y=hail.rand_norm())
        >>> p_hist = hail.plot.histogram2d(ht.x, ht.y)
    
        >>> ht = hail.utils.range_table(1000).annotate(x=hail.rand_norm(), y=hail.rand_norm())
        >>> p_hist = hail.plot.histogram2d(ht.x, ht.y, bins=10, range=((0, 1), None))
    
        Parameters
        ----------
        x : ``.NumericExpression``
            Expression for x-axis (from a Hail table).
        y : ``.NumericExpression``
            Expression for y-axis (from the same Hail table as ``x``).
        bins : int or [int, int]
            The bin specification:
            -   If int, the number of bins for the two dimensions (nx = ny = bins).
            -   If [int, int], the number of bins in each dimension (nx, ny = bins).
            The default value is 40.
        range : None or ((float, float), (float, float))
            The leftmost and rightmost edges of the bins along each dimension:
            ((xmin, xmax), (ymin, ymax)). All values outside of this range will be considered outliers
            and not tallied in the histogram. If this value is None, or either of the inner lists is None,
            the range will be computed from the data.
        width : int
            Plot width (default 600px).
        height : int
            Plot height (default 600px).
        title : str
            Title of the plot.
        colors : Sequence[str]
            List of colors (hex codes, or strings as described
            `here <https://bokeh.pydata.org/en/latest/docs/reference/colors.html>`__). Compatible with one of the many
            built-in palettes available `here <https://bokeh.pydata.org/en/latest/docs/reference/palettes.html>`__.
        log : bool
            Plot the log10 of the bin counts.
    
        Returns
        -------
        ``bokeh.plotting.figure``
        """
        data = _generate_hist2d_data(x, y, bins, range).to_pandas()
    
        # Use python prettier float -> str function
        data['x'] = data['x'].apply(lambda e: str(float(e)))
        data['y'] = data['y'].apply(lambda e: str(float(e)))
    
        mapper: ColorMapper
        if log:
            mapper = LogColorMapper(palette=colors, low=data.c.min(), high=data.c.max())
        else:
            mapper = LinearColorMapper(palette=colors, low=data.c.min(), high=data.c.max())
    
        x_axis = sorted(set(data.x), key=lambda z: float(z))
        y_axis = sorted(set(data.y), key=lambda z: float(z))
        p = figure(
            title=title,
            x_range=x_axis,
            y_range=y_axis,
            x_axis_location="above",
            width=width,
            height=height,
            tools="hover,save,pan,box_zoom,reset,wheel_zoom",
            toolbar_location='below',
        )
    
        p.grid.grid_line_color = None
        p.axis.axis_line_color = None
        p.axis.major_tick_line_color = None
        p.axis.major_label_standoff = 0
        import math
    
        p.xaxis.major_label_orientation = math.pi / 3
    
        p.rect(
            x='x', y='y', width=1, height=1, source=data, fill_color={'field': 'c', 'transform': mapper}, line_color=None
        )
    
        color_bar = ColorBar(
            color_mapper=mapper,
            ticker=LogTicker(desired_num_ticks=len(colors)) if log else BasicTicker(desired_num_ticks=len(colors)),
            label_standoff=12 if log else 6,
            border_line_color=None,
            location=(0, 0),
        )
        p.add_layout(color_bar, 'right')
    
        hovertool = p.select_one(HoverTool)
        assert hovertool is not None
        hovertool.tooltips = [
            ('x', '@x'),
            (
                'y',
                '@y',
            ),
            ('count', '@c'),
        ]
    
        return p
    
    
    
    
    @typecheck(
        x=expr_numeric,
        y=expr_numeric,
        bins=oneof(int, sequenceof(int)),
        range=nullable(sized_tupleof(nullable(sized_tupleof(numeric, numeric)), nullable(sized_tupleof(numeric, numeric)))),
    )
    def _generate_hist2d_data(x, y, bins, range):
        source = x._indices.source
        y_source = y._indices.source
        if source is None or y_source is None:
            raise ValueError("histogram_2d expects two expressions of 'Table', found scalar expression")
        if isinstance(source, hail.MatrixTable):
            raise ValueError("histogram_2d requires source to be Table, not MatrixTable")
        if source != y_source:
            raise ValueError(f"histogram_2d expects two expressions from the same 'Table', found {source} and {y_source}")
        raise_unless_row_indexed('histogram_2d', x)
        raise_unless_row_indexed('histogram_2d', y)
        if isinstance(bins, int):
            x_bins = y_bins = bins
        else:
            x_bins, y_bins = bins
        if range is None:
            x_range = y_range = None
        else:
            x_range, y_range = range
        if x_range is None or y_range is None:
            warning('At least one range was not defined in histogram_2d. Doing two passes...')
            ranges = source.aggregate(hail.struct(x_stats=hail.agg.stats(x), y_stats=hail.agg.stats(y)))
            if x_range is None:
                x_range = (ranges.x_stats.min, ranges.x_stats.max)
            if y_range is None:
                y_range = (ranges.y_stats.min, ranges.y_stats.max)
        else:
            warning(
                'If x_range or y_range are specified in histogram_2d, and there are points '
                'outside of these ranges, they will not be plotted'
            )
        x_range = list(map(float, x_range))
        y_range = list(map(float, y_range))
        x_spacing = (x_range[1] - x_range[0]) / x_bins
        y_spacing = (y_range[1] - y_range[0]) / y_bins
    
        def frange(start, stop, step):
            from itertools import count, takewhile
    
            return takewhile(lambda x: x <= stop, count(start, step))
    
        x_levels = hail.literal(list(frange(x_range[0], x_range[1], x_spacing))[::-1])
        y_levels = hail.literal(list(frange(y_range[0], y_range[1], y_spacing))[::-1])
        grouped_ht = source.group_by(
            x=hail.str(x_levels.find(lambda w: x >= w)), y=hail.str(y_levels.find(lambda w: y >= w))
        ).aggregate(c=hail.agg.count())
        data = grouped_ht.filter(
            hail.is_defined(grouped_ht.x)
            & (grouped_ht.x != str(x_range[1]))
            & hail.is_defined(grouped_ht.y)
            & (grouped_ht.y != str(y_range[1]))
        )
        return data
    
    
    def _collect_scatter_plot_data(
        x: Tuple[str, NumericExpression],
        y: Tuple[str, NumericExpression],
        fields: Optional[Dict[str, Expression]] = None,
        n_divisions: Optional[int] = None,
        missing_label: str = 'NA',
    ) -> pd.DataFrame:
        expressions = dict()
        if fields is not None:
            expressions.update({
                k: hail.or_else(v, missing_label) if isinstance(v, StringExpression) else v for k, v in fields.items()
            })
    
        if n_divisions is None:
            collect_expr = hail.struct(**dict((k, v) for k, v in (x, y)), **expressions)
            plot_data = [point for point in collect_expr.collect() if point[x[0]] is not None and point[y[0]] is not None]
            source_pd = pd.DataFrame(plot_data)
        else:
            # FIXME: remove the type conversion logic if/when downsample supports continuous values for labels
            # Save all numeric types to cast in DataFrame
            numeric_expr = {k: 'int32' for k, v in expressions.items() if isinstance(v, Int32Expression)}
            numeric_expr.update({k: 'int64' for k, v in expressions.items() if isinstance(v, Int64Expression)})
            numeric_expr.update({k: 'float32' for k, v in expressions.items() if isinstance(v, Float32Expression)})
            numeric_expr.update({k: 'float64' for k, v in expressions.items() if isinstance(v, Float64Expression)})
    
            # Cast non-string types to string
            expressions = {k: hail.str(v) if not isinstance(v, StringExpression) else v for k, v in expressions.items()}
    
            agg_f = x[1]._aggregation_method()
            res = agg_f(
                hail.agg.downsample(
                    x[1], y[1], label=list(expressions.values()) if expressions else None, n_divisions=n_divisions
                )
            )
            source_pd = pd.DataFrame([
                dict(
                    **{x[0]: point[0], y[0]: point[1]},
                    **(dict(zip(expressions, point[2])) if point[2] is not None else {}),
                )
                for point in res
            ])
            source_pd = source_pd.astype(numeric_expr, copy=False)
    
        return source_pd
    
    
    def _get_categorical_palette(factors: List[str]) -> ColorMapper:
        n = max(3, len(factors))
        _palette: Sequence[str]
        if n < len(palette):
            _palette = palette
        elif n < 21:
            from bokeh.palettes import Category20
    
            _palette = Category20[n]
        else:
            from bokeh.palettes import viridis
    
            _palette = viridis(n)
    
        return CategoricalColorMapper(factors=factors, palette=_palette)
    
    
    def _get_scatter_plot_elements(
        sp: Plot,
        source_pd: pd.DataFrame,
        x_col: str,
        y_col: str,
        label_cols: List[str],
        colors: Optional[Dict[str, ColorMapper]] = None,
        size: int = 4,
        hover_cols: Optional[Set[str]] = None,
    ) -> Union[
        Tuple[Plot, Dict[str, List[LegendItem]], Legend, ColorBar, Dict[str, ColorMapper], List[Renderer]],
        Tuple[Plot, None, None, None, None, None],
    ]:
        if not source_pd.shape[0]:
            print("WARN: No data to plot.")
            return sp, None, None, None, None, None
    
        possible_tooltips = [(x_col, f'@{x_col}'), (y_col, f'@{y_col}')] + [
            (c, f'@{c}') for c in source_pd.columns if c not in [x_col, y_col]
        ]
    
        if hover_cols is not None:
            possible_tooltips = [x for x in possible_tooltips if x[0] in hover_cols]
        sp.tools.append(HoverTool(tooltips=possible_tooltips))
    
        cds = ColumnDataSource(source_pd)
    
        if not label_cols:
            sp.scatter(x_col, y_col, source=cds, size=size)
            return sp, None, None, None, None, None
        continuous_cols = [
            col
            for col in label_cols
            if (str(source_pd.dtypes[col]).startswith('float') or str(source_pd.dtypes[col]).startswith('int'))
        ]
        factor_cols = [col for col in label_cols if col not in continuous_cols]
    
        #  Assign color mappers to columns
        if colors is None:
            colors = {}
        color_mappers: Dict[str, ColorMapper] = {}
    
        for col in continuous_cols:
            low = np.nanmin(source_pd[col])
            if np.isnan(low):
                low = 0
                high = 0
            else:
                high = np.nanmax(source_pd[col])
            color_mappers[col] = colors[col] if col in colors else LinearColorMapper(palette='Magma256', low=low, high=high)
    
        for col in factor_cols:
            if col in colors:
                color_mappers[col] = colors[col]
            else:
                factors = list(set(source_pd[col]))
                color_mappers[col] = _get_categorical_palette(factors)
    
        # Create initial glyphs
        initial_col = label_cols[0]
        initial_mapper = color_mappers[initial_col]
        legend_items: Dict[str, List[LegendItem]] = {}
    
        if not factor_cols:
            all_renderers = [sp.scatter(x_col, y_col, color=transform(initial_col, initial_mapper), source=cds, size=size)]
    
        else:
            all_renderers = []
            legend_items_by_key_by_factor = {col: collections.defaultdict(list) for col in factor_cols}
            for key in source_pd.groupby(factor_cols).groups.keys():
                _key = key if len(factor_cols) > 1 else [key]
                cds_view = CDSView(
                    filter=IntersectionFilter(
                        operands=[
                            GroupFilter(column_name=factor_cols[i], group=_key[i]) for i in range(0, len(factor_cols))
                        ]
                    )
                )
                renderer = sp.scatter(
                    x_col,
                    y_col,
                    color=transform(initial_col, initial_mapper),
                    source=cds,
                    view=cds_view,
                    size=size,
                )
                all_renderers.append(renderer)
                for i in range(0, len(factor_cols)):
                    legend_items_by_key_by_factor[factor_cols[i]][_key[i]].append(renderer)
    
            legend_items = {
                factor: [LegendItem(label=key, renderers=renderers) for key, renderers in key_renderers.items()]
                for factor, key_renderers in legend_items_by_key_by_factor.items()
            }
    
        # Add legend / color bar
        legend = (
            Legend(visible=False, click_policy='hide', orientation='vertical')
            if initial_col not in factor_cols
            else Legend(items=legend_items[initial_col], click_policy='hide', orientation='vertical')
        )
        color_bar = ColorBar(color_mapper=color_mappers[initial_col])
        if initial_col not in continuous_cols:
            color_bar.visible = False
        sp.add_layout(legend, 'left')
        sp.add_layout(color_bar, 'left')
    
        return sp, legend_items, legend, color_bar, color_mappers, all_renderers
    
    
    def _downsampling_factor(fname: str, n_divisions: Optional[int], collect_all: Optional[bool]) -> Optional[int]:
        if collect_all is not None:
            warnings.warn(f'{fname}: `collect_all` has been deprecated. Use `n_divisions` instead.')
            if n_divisions is not None and collect_all is not None:
                raise ValueError('At most one of `collect_all` or `n_divisions` must be specified.')
    
        n_divisions = None if collect_all else n_divisions
    
        if n_divisions is not None and n_divisions < 1:
            raise ValueError('`n_divisions` must be a positive whole number or `None`')
    
        return n_divisions
    
    
    
    
    [[docs]](../../../plot.html#hail.plot.scatter)@typecheck(
        x=oneof(expr_numeric, sized_tupleof(str, expr_numeric)),
        y=oneof(expr_numeric, sized_tupleof(str, expr_numeric)),
        label=nullable(oneof(dictof(str, expr_any), expr_any)),
        title=nullable(str),
        xlabel=nullable(str),
        ylabel=nullable(str),
        size=int,
        legend=bool,
        hover_fields=nullable(dictof(str, expr_any)),
        colors=nullable(oneof(bokeh.models.mappers.ColorMapper, dictof(str, bokeh.models.mappers.ColorMapper))),
        width=int,
        height=int,
        collect_all=nullable(bool),
        n_divisions=nullable(int),
        missing_label=str,
    )
    def scatter(
        x: Union[NumericExpression, Tuple[str, NumericExpression]],
        y: Union[NumericExpression, Tuple[str, NumericExpression]],
        label: Optional[Union[Expression, Dict[str, Expression]]] = None,
        title: Optional[str] = None,
        xlabel: Optional[str] = None,
        ylabel: Optional[str] = None,
        size: int = 4,
        legend: bool = True,
        hover_fields: Optional[Dict[str, Expression]] = None,
        colors: Optional[Union[ColorMapper, Dict[str, ColorMapper]]] = None,
        width: int = 800,
        height: int = 800,
        collect_all: Optional[bool] = None,
        n_divisions: Optional[int] = 500,
        missing_label: str = 'NA',
    ) -> Union[Plot, Column]:
        """Create an interactive scatter plot.
    
        ``x`` and ``y`` must both be either:
        - a ``.NumericExpression`` from the same ``.Table``.
        - a tuple (str, ``.NumericExpression``) from the same ``.Table``. If passed as a tuple the first element is used as the hover label.
    
        If no label or a single label is provided, then returns ``bokeh.plotting.figure``
        Otherwise returns a ``bokeh.models.layouts.Column`` containing:
        - a ``bokeh.models.widgets.inputs.Select`` dropdown selection widget for labels
        - a ``bokeh.plotting.figure`` containing the interactive scatter plot
    
        Points will be colored by one of the labels defined in the ``label`` using the color scheme defined in
        the corresponding entry of ``colors`` if provided (otherwise a default scheme is used). To specify your color
        mapper, check `the bokeh documentation <https://bokeh.pydata.org/en/latest/docs/reference/colors.html>`__
        for CategoricalMapper for categorical labels, and for LinearColorMapper and LogColorMapper
        for continuous labels.
        For categorical labels, clicking on one of the items in the legend will hide/show all points with the corresponding label.
        Note that using many different labelling schemes in the same plots, particularly if those labels contain many
        different classes could slow down the plot interactions.
    
        Hovering on points will display their coordinates, labels and any additional fields specified in ``hover_fields``.
    
        Parameters
        ----------
        x : ``.NumericExpression`` or (str, ``.NumericExpression``)
            List of x-values to be plotted.
        y : ``.NumericExpression`` or (str, ``.NumericExpression``)
            List of y-values to be plotted.
        label : ``.Expression`` or Dict[str, ``.Expression``]], optional
            Either a single expression (if a single label is desired), or a
            dictionary of label name -> label value for x and y values.
            Used to color each point w.r.t its label.
            When multiple labels are given, a dropdown will be displayed with the different options.
            Can be used with categorical or continuous expressions.
        title : str, optional
            Title of the scatterplot.
        xlabel : str, optional
            X-axis label.
        ylabel : str, optional
            Y-axis label.
        size : int
            Size of markers in screen space units.
        legend: bool
            Whether or not to show the legend in the resulting figure.
        hover_fields : Dict[str, ``.Expression``], optional
            Extra fields to be displayed when hovering over a point on the plot.
        colors : ``bokeh.models.mappers.ColorMapper`` or Dict[str, ``bokeh.models.mappers.ColorMapper``], optional
            If a single label is used, then this can be a color mapper, if multiple labels are used, then this should
            be a Dict of label name -> color mapper.
            Used to set colors for the labels defined using ``label``.
            If not used at all, or label names not appearing in this dict will be colored using a default color scheme.
        width: int
            Plot width
        height: int
            Plot height
        collect_all : bool, optional
            Deprecated. Use `n_divisions` instead.
        n_divisions : int, optional
            Factor by which to downsample (default value = 500).
            A lower input results in fewer output datapoints.
            Use `None` to collect all points.
        missing_label: str
            Label to use when a point is missing data for a categorical label
    
        Returns
        -------
        ``bokeh.models.Plot`` if no label or a single label was given, otherwise ``bokeh.models.layouts.Column``
        """
        hover_fields = {} if hover_fields is None else hover_fields
    
        label_by_col: Dict[str, Expression]
        if label is None:
            label_by_col = {}
        elif isinstance(label, Expression):
            label_by_col = {'label': label}
        else:
            assert isinstance(label, dict)
            label_by_col = label
    
        if isinstance(colors, ColorMapper):
            colors_by_col = {'label': colors}
        else:
            colors_by_col = colors
    
        label_cols = list(label_by_col.keys())
        if isinstance(x, NumericExpression):
            _x = ('x', x)
        else:
            _x = x
    
        if isinstance(y, NumericExpression):
            _y = ('y', y)
        else:
            _y = y
    
        source_pd = _collect_scatter_plot_data(
            _x,
            _y,
            fields={**hover_fields, **label_by_col},
            n_divisions=_downsampling_factor('scatter', n_divisions, collect_all),
            missing_label=missing_label,
        )
        sp = figure(title=title, x_axis_label=xlabel, y_axis_label=ylabel, height=height, width=width)
        sp, sp_legend_items, sp_legend, sp_color_bar, sp_color_mappers, sp_scatter_renderers = _get_scatter_plot_elements(
            sp, source_pd, _x[0], _y[0], label_cols, colors_by_col, size, hover_cols={'x', 'y'} | set(hover_fields)
        )
    
        if not legend:
            assert sp_legend is not None
            assert sp_color_bar is not None
            sp_legend.visible = False
            sp_color_bar.visible = False
    
        # If multiple labels, create JS call back selector
        if len(label_cols) > 1:
            callback_args: Dict[str, Any]
            callback_args = dict(color_mappers=sp_color_mappers, scatter_renderers=sp_scatter_renderers)
            callback_code = """
            for (var i = 0; i < scatter_renderers.length; i++){
                scatter_renderers[i].glyph.fill_color = {field: cb_obj.value, transform: color_mappers[cb_obj.value]}
                scatter_renderers[i].glyph.line_color = {field: cb_obj.value, transform: color_mappers[cb_obj.value]}
                scatter_renderers[i].visible = true
            }
    
            """
    
            if legend:
                callback_args.update(dict(legend_items=sp_legend_items, legend=sp_legend, color_bar=sp_color_bar))
                callback_code += """
            if (cb_obj.value in legend_items){
                legend.items=legend_items[cb_obj.value]
                legend.visible=true
                color_bar.visible=false
            }else{
                legend.visible=false
                color_bar.visible=true
            }
    
            """
    
            callback = CustomJS(args=callback_args, code=callback_code)
            select = Select(title="Color by", value=label_cols[0], options=label_cols)
            select.js_on_change('value', callback)
            return Column(children=[select, sp])
    
        return sp
    
    
    
    
    @typecheck(
        x=oneof(expr_numeric, sized_tupleof(str, expr_numeric)),
        y=oneof(expr_numeric, sized_tupleof(str, expr_numeric)),
        label=nullable(oneof(dictof(str, expr_any), expr_any)),
        title=nullable(str),
        xlabel=nullable(str),
        ylabel=nullable(str),
        size=int,
        legend=bool,
        hover_fields=nullable(dictof(str, expr_any)),
        colors=nullable(oneof(bokeh.models.mappers.ColorMapper, dictof(str, bokeh.models.mappers.ColorMapper))),
        width=int,
        height=int,
        collect_all=nullable(bool),
        n_divisions=nullable(int),
        missing_label=str,
    )
    def joint_plot(
        x: Union[NumericExpression, Tuple[str, NumericExpression]],
        y: Union[NumericExpression, Tuple[str, NumericExpression]],
        label: Optional[Union[Expression, Dict[str, Expression]]] = None,
        title: Optional[str] = None,
        xlabel: Optional[str] = None,
        ylabel: Optional[str] = None,
        size: int = 4,
        legend: bool = True,
        hover_fields: Optional[Dict[str, StringExpression]] = None,
        colors: Optional[Union[ColorMapper, Dict[str, ColorMapper]]] = None,
        width: int = 800,
        height: int = 800,
        collect_all: Optional[bool] = None,
        n_divisions: Optional[int] = 500,
        missing_label: str = 'NA',
    ) -> GridPlot:
        """Create an interactive scatter plot with marginal densities on the side.
    
        ``x`` and ``y`` must both be either:
        - a ``.NumericExpression`` from the same ``.Table``.
        - a tuple (str, ``.NumericExpression``) from the same ``.Table``. If passed as a tuple the first element is used as the hover label.
    
        This function returns a ``bokeh.models.layouts.Column`` containing two ``figure.Row``:
        - The first row contains the X-axis marginal density and a selection widget if multiple entries are specified in the ``label``
        - The second row contains the scatter plot and the y-axis marginal density
    
        Points will be colored by one of the labels defined in the ``label`` using the color scheme defined in
        the corresponding entry of ``colors`` if provided (otherwise a default scheme is used). To specify your color
        mapper, check `the bokeh documentation <https://bokeh.pydata.org/en/latest/docs/reference/colors.html>`__
        for CategoricalMapper for categorical labels, and for LinearColorMapper and LogColorMapper
        for continuous labels.
        For categorical labels, clicking on one of the items in the legend will hide/show all points with the corresponding label in the scatter plot.
        Note that using many different labelling schemes in the same plots, particularly if those labels contain many
        different classes could slow down the plot interactions.
    
        Hovering on points in the scatter plot displays their coordinates, labels and any additional fields specified in ``hover_fields``.
    
         Parameters
         ----------
         ----------
         x : ``.NumericExpression`` or (str, ``.NumericExpression``)
             List of x-values to be plotted.
         y : ``.NumericExpression`` or (str, ``.NumericExpression``)
             List of y-values to be plotted.
         label : ``.Expression`` or Dict[str, ``.Expression``]], optional
             Either a single expression (if a single label is desired), or a
             dictionary of label name -> label value for x and y values.
             Used to color each point w.r.t its label.
             When multiple labels are given, a dropdown will be displayed with the different options.
             Can be used with categorical or continuous expressions.
         title : str, optional
             Title of the scatterplot.
         xlabel : str, optional
             X-axis label.
         ylabel : str, optional
             Y-axis label.
         size : int
             Size of markers in screen space units.
         legend: bool
             Whether or not to show the legend in the resulting figure.
         hover_fields : Dict[str, ``.Expression``], optional
             Extra fields to be displayed when hovering over a point on the plot.
         colors : ``bokeh.models.mappers.ColorMapper`` or Dict[str, ``bokeh.models.mappers.ColorMapper``], optional
             If a single label is used, then this can be a color mapper, if multiple labels are used, then this should
             be a Dict of label name -> color mapper.
             Used to set colors for the labels defined using ``label``.
             If not used at all, or label names not appearing in this dict will be colored using a default color scheme.
         width: int
             Plot width
         height: int
             Plot height
         collect_all : bool, optional
             Deprecated. Use `n_divisions` instead.
         n_divisions : int, optional
             Factor by which to downsample (default value = 500).
             A lower input results in fewer output datapoints.
             Use `None` to collect all points.
         missing_label: str
             Label to use when a point is missing data for a categorical label
    
    
         Returns
         -------
         ``.GridPlot``
        """
        # Collect data
        hover_fields = {} if hover_fields is None else hover_fields
    
        label_by_col: Dict[str, Expression]
        if label is None:
            label_by_col = {}
        elif isinstance(label, Expression):
            label_by_col = {'label': label}
        else:
            assert isinstance(label, dict)
            label_by_col = label
    
        if isinstance(colors, ColorMapper):
            colors_by_col = {'label': colors}
        else:
            colors_by_col = colors
        if isinstance(x, NumericExpression):
            _x = ('x', x)
        else:
            _x = x
    
        if isinstance(y, NumericExpression):
            _y = ('y', y)
        else:
            _y = y
    
        label_cols = list(label_by_col.keys())
        source_pd = _collect_scatter_plot_data(
            _x,
            _y,
            fields={**hover_fields, **label_by_col},
            n_divisions=_downsampling_factor('join_plot', n_divisions, collect_all),
            missing_label=missing_label,
        )
        sp = figure(title=title, x_axis_label=xlabel, y_axis_label=ylabel, height=height, width=width)
        sp, sp_legend_items, sp_legend, sp_color_bar, sp_color_mappers, sp_scatter_renderers = _get_scatter_plot_elements(
            sp, source_pd, _x[0], _y[0], label_cols, colors_by_col, size, hover_cols={'x', 'y'} | set(hover_fields)
        )
    
        continuous_cols = [
            col
            for col in label_cols
            if (str(source_pd.dtypes[col]).startswith('float') or str(source_pd.dtypes[col]).startswith('int'))
        ]
        factor_cols = [col for col in label_cols if col not in continuous_cols]
    
        # Density plots
        def get_density_plot_items(
            source_pd,
            data_col,
            p,
            x_axis,
            colors: Optional[Dict[str, ColorMapper]],
            continuous_cols: List[str],
            factor_cols: List[str],
        ):
            density_renderers = []
            max_densities = {}
            if not factor_cols or continuous_cols:
                dens, edges = np.histogram(source_pd[data_col], density=True)
                edges = edges[:-1]
                xy = (edges, dens) if x_axis else (dens, edges)
                cds = ColumnDataSource({'x': xy[0], 'y': xy[1]})
                line = p.line('x', 'y', source=cds)
                density_renderers.extend([(col, "", line) for col in continuous_cols])
                max_densities = {col: np.max(dens) for col in continuous_cols}
    
            for factor_col in factor_cols:
                assert colors is not None, (colors, factor_cols)
                factor_colors = colors.get(factor_col, _get_categorical_palette(list(set(source_pd[factor_col]))))
                factor_colors = dict(zip(factor_colors.factors, factor_colors.palette))
                density_data = (
                    source_pd[[factor_col, data_col]]
                    .groupby(factor_col)
                    .apply(lambda df: np.histogram(df['x' if x_axis else 'y'], density=True))
                )
                for factor, (dens, edges) in density_data.iteritems():
                    _edges = edges[:-1]
                    xy = (_edges, dens) if x_axis else (dens, _edges)
                    cds = ColumnDataSource({'x': xy[0], 'y': xy[1]})
                    density_renderers.append((
                        factor_col,
                        factor,
                        p.line('x', 'y', color=factor_colors.get(factor, 'gray'), source=cds),
                    ))
                    max_densities[factor_col] = np.max([*list(dens), max_densities.get(factor_col, 0)])
    
            p.grid.visible = False
            p.outline_line_color = None
            return p, density_renderers, max_densities
    
        xp = figure(title=title, height=int(height / 3), width=width, x_range=sp.x_range)
        xp, x_renderers, x_max_densities = get_density_plot_items(
            source_pd,
            _x[0],
            xp,
            x_axis=True,
            colors=sp_color_mappers,
            continuous_cols=continuous_cols,
            factor_cols=factor_cols,
        )
        xp.xaxis.visible = False
        yp = figure(height=height, width=int(width / 3), y_range=sp.y_range)
        yp, y_renderers, y_max_densities = get_density_plot_items(
            source_pd,
            _y[0],
            yp,
            x_axis=False,
            colors=sp_color_mappers,
            continuous_cols=continuous_cols,
            factor_cols=factor_cols,
        )
        yp.yaxis.visible = False
        density_renderers = x_renderers + y_renderers
        first_row = [xp]
    
        if not legend:
            assert sp_legend is not None
            assert sp_color_bar is not None
            sp_legend.visible = False
            sp_color_bar.visible = False
    
        # If multiple labels, create JS call back selector
        if len(label_cols) > 1:
            for factor_col, _, renderer in density_renderers:
                renderer.visible = factor_col == label_cols[0]
    
            if label_cols[0] in factor_cols:
                xp.y_range.start = 0
                xp.y_range.end = x_max_densities[label_cols[0]]
                yp.x_range.start = 0
                yp.x_range.end = y_max_densities[label_cols[0]]
    
            callback_args: Dict[str, Any]
            callback_args = dict(
                scatter_renderers=sp_scatter_renderers,
                color_mappers=sp_color_mappers,
                density_renderers=x_renderers + y_renderers,
                x_range=xp.y_range,
                x_max_densities=x_max_densities,
                y_range=yp.x_range,
                y_max_densities=y_max_densities,
            )
    
            callback_code = """
                    for (var i = 0; i < scatter_renderers.length; i++){
                        scatter_renderers[i].glyph.fill_color = {field: cb_obj.value, transform: color_mappers[cb_obj.value]}
                        scatter_renderers[i].glyph.line_color = {field: cb_obj.value, transform: color_mappers[cb_obj.value]}
                        scatter_renderers[i].visible = true
                    }
    
                    for (var i = 0; i < density_renderers.length; i++){
                        density_renderers[i][2].visible = density_renderers[i][0] == cb_obj.value
                    }
    
                    x_range.start = 0
                    y_range.start = 0
                    x_range.end = x_max_densities[cb_obj.value]
                    y_range.end = y_max_densities[cb_obj.value]
    
                    """
    
            if legend:
                callback_args.update(dict(legend_items=sp_legend_items, legend=sp_legend, color_bar=sp_color_bar))
                callback_code += """
                    if (cb_obj.value in legend_items){
                        legend.items=legend_items[cb_obj.value]
                        legend.visible=true
                        color_bar.visible=false
                    }else{
                        legend.visible=false
                        color_bar.visible=true
                    }
    
                    """
    
            callback = CustomJS(args=callback_args, code=callback_code)
            select = Select(title="Color by", value=label_cols[0], options=label_cols)
            select.js_on_change('value', callback)
            first_row.append(select)
    
        return gridplot([first_row, [sp, yp]])
    
    
    
    
    [[docs]](../../../plot.html#hail.plot.qq)@typecheck(
        pvals=expr_numeric,
        label=nullable(oneof(dictof(str, expr_any), expr_any)),
        title=nullable(str),
        xlabel=nullable(str),
        ylabel=nullable(str),
        size=int,
        legend=bool,
        hover_fields=nullable(dictof(str, expr_any)),
        colors=nullable(oneof(bokeh.models.mappers.ColorMapper, dictof(str, bokeh.models.mappers.ColorMapper))),
        width=int,
        height=int,
        collect_all=nullable(bool),
        n_divisions=nullable(int),
        missing_label=str,
    )
    def qq(
        pvals: NumericExpression,
        label: Optional[Union[Expression, Dict[str, Expression]]] = None,
        title: Optional[str] = 'Q-Q plot',
        xlabel: Optional[str] = 'Expected -log10(p)',
        ylabel: Optional[str] = 'Observed -log10(p)',
        size: int = 6,
        legend: bool = True,
        hover_fields: Optional[Dict[str, Expression]] = None,
        colors: Optional[Union[ColorMapper, Dict[str, ColorMapper]]] = None,
        width: int = 800,
        height: int = 800,
        collect_all: Optional[bool] = None,
        n_divisions: Optional[int] = 500,
        missing_label: str = 'NA',
    ) -> Union[figure, Column]:
        """Create a Quantile-Quantile plot. (https://en.wikipedia.org/wiki/Q-Q_plot)
    
        If no label or a single label is provided, then returns ``bokeh.plotting.figure``
        Otherwise returns a ``bokeh.models.layouts.Column`` containing:
        - a ``bokeh.models.widgets.inputs.Select`` dropdown selection widget for labels
        - a ``bokeh.plotting.figure`` containing the interactive qq plot
    
        Points will be colored by one of the labels defined in the ``label`` using the color scheme defined in
        the corresponding entry of ``colors`` if provided (otherwise a default scheme is used). To specify your color
        mapper, check `the bokeh documentation <https://bokeh.pydata.org/en/latest/docs/reference/colors.html>`__
        for CategoricalMapper for categorical labels, and for LinearColorMapper and LogColorMapper
        for continuous labels.
        For categorical labels, clicking on one of the items in the legend will hide/show all points with the corresponding label.
        Note that using many different labelling schemes in the same plots, particularly if those labels contain many
        different classes could slow down the plot interactions.
    
        Hovering on points will display their coordinates, labels and any additional fields specified in ``hover_fields``.
    
        Parameters
        ----------
        pvals : ``.NumericExpression``
            List of x-values to be plotted.
        label : ``.Expression`` or Dict[str, ``.Expression``]]
            Either a single expression (if a single label is desired), or a
            dictionary of label name -> label value for x and y values.
            Used to color each point w.r.t its label.
            When multiple labels are given, a dropdown will be displayed with the different options.
            Can be used with categorical or continuous expressions.
        title : str, optional
            Title of the scatterplot.
        xlabel : str, optional
            X-axis label.
        ylabel : str, optional
            Y-axis label.
        size : int
            Size of markers in screen space units.
        legend: bool
            Whether or not to show the legend in the resulting figure.
        hover_fields : Dict[str, ``.Expression``], optional
            Extra fields to be displayed when hovering over a point on the plot.
        colors : ``bokeh.models.mappers.ColorMapper`` or Dict[str, ``bokeh.models.mappers.ColorMapper``], optional
            If a single label is used, then this can be a color mapper, if multiple labels are used, then this should
            be a Dict of label name -> color mapper.
            Used to set colors for the labels defined using ``label``.
            If not used at all, or label names not appearing in this dict will be colored using a default color scheme.
        width: int
            Plot width
        height: int
            Plot height
        collect_all : bool
            Deprecated. Use `n_divisions` instead.
        n_divisions : int, optional
            Factor by which to downsample (default value = 500).
            A lower input results in fewer output datapoints.
            Use `None` to collect all points.
        missing_label: str
            Label to use when a point is missing data for a categorical label
    
        Returns
        -------
        ``bokeh.plotting.figure`` if no label or a single label was given, otherwise ``bokeh.models.layouts.Column``
        """
        hover_fields = {} if hover_fields is None else hover_fields
        label_by_col: Dict[str, Expression]
        if label is None:
            label_by_col = {}
        elif isinstance(label, Expression):
            label_by_col = {'label': label}
        else:
            assert isinstance(label, dict)
            label_by_col = label
    
        source = pvals._indices.source
        if isinstance(source, Table):
            ht = source.select(p_value=pvals, **hover_fields, **label_by_col)
        else:
            assert isinstance(source, MatrixTable)
            ht = source.select_rows(p_value=pvals, **hover_fields, **label_by_col).rows()
        ht = ht.key_by().select('p_value', *hover_fields, *label_by_col).key_by('p_value')
        n = ht.aggregate(aggregators.count(), _localize=False)
        ht = ht.annotate(observed_p=-hail.log10(ht['p_value']), expected_p=-hail.log10((hail.scan.count() + 1) / n))
        if 'p' not in hover_fields:
            hover_fields['p_value'] = ht['p_value']
        p = scatter(
            ht.expected_p,
            ht.observed_p,
            label={x: ht[x] for x in label_by_col},
            title=title,
            xlabel=xlabel,
            ylabel=ylabel,
            size=size,
            legend=legend,
            hover_fields={x: ht[x] for x in hover_fields},
            colors=colors,
            width=width,
            height=height,
            n_divisions=_downsampling_factor('qq', n_divisions, collect_all),
            missing_label=missing_label,
        )
        from hail.methods.statgen import _lambda_gc_agg
    
        lambda_gc, max_p = ht.aggregate((
            _lambda_gc_agg(ht['p_value']),
            hail.agg.max(hail.max(ht.observed_p, ht.expected_p)),
        ))
        if isinstance(p, Column):
            qq = p.children[1]
        else:
            qq = p
        qq.x_range = DataRange1d(start=0, end=max_p + 1)
        qq.y_range = DataRange1d(start=0, end=max_p + 1)
        qq.add_layout(Slope(gradient=1, y_intercept=0, line_color='red'))
    
        label_color = 'red' if lambda_gc > 1.25 else 'orange' if lambda_gc > 1.1 else 'black'
        lgc_label = Label(
            x=max_p * 0.85,
            y=1,
            text=f'λ GC: {lambda_gc:.2f}',
            text_font_style='bold',
            text_color=label_color,
            text_font_size='14pt',
        )
        p.add_layout(lgc_label)
    
        return p
    
    
    
    
    
    
    [[docs]](../../../plot.html#hail.plot.manhattan)@typecheck(
        pvals=expr_float64,
        locus=nullable(expr_locus()),
        title=nullable(str),
        size=int,
        hover_fields=nullable(dictof(str, expr_any)),
        collect_all=nullable(bool),
        n_divisions=nullable(int),
        significance_line=nullable(numeric),
    )
    def manhattan(
        pvals: 'Float64Expression',
        locus: 'Optional[LocusExpression]' = None,
        title: 'Optional[str]' = None,
        size: int = 4,
        hover_fields: 'Optional[Dict[str, Expression]]' = None,
        collect_all: 'Optional[bool]' = None,
        n_divisions: 'Optional[int]' = 500,
        significance_line: 'Optional[Union[int, float]]' = 5e-8,
    ) -> Plot:
        """Create a Manhattan plot. (https://en.wikipedia.org/wiki/Manhattan_plot)
    
        Parameters
        ----------
        pvals : ``.Float64Expression``
            P-values to be plotted.
        locus : ``.LocusExpression``, optional
            Locus values to be plotted.
        title : str, optional
            Title of the plot.
        size : int
            Size of markers in screen space units.
        hover_fields : Dict[str, ``.Expression``], optional
            Dictionary of field names and values to be shown in the HoverTool of the plot.
        collect_all : bool, optional
            Deprecated - use `n_divisions` instead.
        n_divisions : int, optional.
            Factor by which to downsample (default value = 500).
            A lower input results in fewer output datapoints.
            Use `None` to collect all points.
        significance_line : float, optional
            p-value at which to add a horizontal, dotted red line indicating
            genome-wide significance.  If ``None``, no line is added.
    
        Returns
        -------
        ``bokeh.models.Plot``
        """
        if locus is None:
            locus = pvals._indices.source.locus
    
        ref = locus.dtype.reference_genome
    
        if hover_fields is None:
            hover_fields = {}
    
        hover_fields['locus'] = hail.str(locus)
    
        pvals = -hail.log10(pvals)
    
        source_pd = _collect_scatter_plot_data(
            ('_global_locus', locus.global_position()),
            ('_pval', pvals),
            fields=hover_fields,
            n_divisions=_downsampling_factor('manhattan', n_divisions, collect_all),
        )
        source_pd['p_value'] = [10 ** (-p) for p in source_pd['_pval']]
        source_pd['_contig'] = [locus.split(":")[0] for locus in source_pd['locus']]
    
        observed_contigs = [contig for contig in ref.contigs.copy() if contig in set(source_pd['_contig'])]
    
        contig_ticks = [ref._contig_global_position(contig) + ref.contig_length(contig) // 2 for contig in observed_contigs]
        color_mapper = CategoricalColorMapper(factors=ref.contigs, palette=palette[:2] * int((len(ref.contigs) + 1) / 2))
    
        p = figure(title=title, x_axis_label='Chromosome', y_axis_label='P-value (-log10 scale)', width=1000)
        p, _, legend, _, _, _ = _get_scatter_plot_elements(
            p,
            source_pd,
            x_col='_global_locus',
            y_col='_pval',
            label_cols=['_contig'],
            colors={'_contig': color_mapper},
            size=size,
            hover_cols={'locus', 'p_value'} | set(hover_fields),
        )
        assert legend is not None
        legend.visible = False
        p.xaxis.ticker = contig_ticks
        p.xaxis.major_label_overrides = dict(zip(contig_ticks, [contig.replace("chr", "") for contig in observed_contigs]))
    
        if significance_line is not None:
            p.renderers.append(
                Span(
                    location=-math.log10(significance_line),
                    dimension='width',
                    line_color='red',
                    line_dash='dashed',
                    line_width=1.5,
                )
            )
    
        return p
    
    
    
    
    
    
    [[docs]](../../../plot.html#hail.plot.visualize_missingness)@typecheck(
        entry_field=expr_any,
        row_field=nullable(oneof(expr_numeric, expr_locus())),
        column_field=nullable(expr_str),
        window=nullable(int),
        plot_width=int,
        plot_height=int,
    )
    def visualize_missingness(
        entry_field, row_field=None, column_field=None, window=6000000, plot_width=1800, plot_height=900
    ) -> figure:
        """Visualize missingness in a MatrixTable.
    
        Inspired by `naniar <https://cran.r-project.org/web/packages/naniar/index.html>`__.
    
        Row field is windowed by default, and missingness is aggregated over this window to generate a proportion defined.
        This windowing is set to 6,000,000 by default, so that the human genome is divided into ~500 rows.
        With ~2,000 columns, this function returns a sensibly-sized plot with this windowing.
    
        Warning
        -------
        Generating a plot with more than ~1M points takes a long time for Bokeh to render. Consider windowing carefully.
    
        Parameters
        ----------
        entry_field : ``.Expression``
            Field for which to check missingness.
        row_field : ``.NumericExpression`` or ``.LocusExpression``
            Row field to use for y-axis (can be windowed). If not provided, the row key will be used.
        column_field : ``.StringExpression``
            Column field to use for x-axis. If not provided, the column key will be used.
        window : int, optional
            Size of window to summarize by ``row_field``. If set to None, each field will be used individually.
        plot_width : int
            Plot width in px.
        plot_height : int
            Plot height in px.
    
        Returns
        -------
        ``bokeh.plotting.figure``
        """
        mt = entry_field._indices.source
        if row_field is None:
            if isinstance(mt.row_key.dtype, hail.tstruct) and len(mt.row_key) == 1:
                row_field = mt.row_key[0]
            else:
                row_field = mt.row_key
        if column_field is None:
            column_field = hail.str(mt.col_key)
        row_source = row_field._indices.source
        column_source = column_field._indices.source
        if mt is None or row_source is None or column_source is None:
            raise ValueError("visualize_missingness expects expressions of 'MatrixTable', found scalar expression")
        if isinstance(mt, hail.Table):
            raise ValueError("visualize_missingness requires source to be MatrixTable, not Table")
        columns = column_field.collect()
        if not (mt == row_source == column_source):
            raise ValueError(
                f"visualize_missingness expects expressions from the same 'MatrixTable', "
                f"found {mt} and {row_source} and {column_source}"
            )
        # raise_unless_row_indexed('visualize_missingness', row_source)
        if window:
            row_field_is_locus = isinstance(row_field.dtype, hail.tlocus)
            row_field_is_numeric = row_field.dtype in (hail.tint32, hail.tint64, hail.tfloat32, hail.tfloat64)
            if row_field_is_locus:
                grouping = hail.locus_from_global_position(
                    hail.int64(window) * hail.int64(row_field.global_position() / window)
                )
            elif row_field_is_numeric:
                grouping = hail.int64(window) * hail.int64(row_field / window)
            else:
                raise ValueError(
                    f'When window is not None and row key must be numeric, but row key type was {mt.row_key.dtype}.'
                )
            mt = (
                mt.group_rows_by(_new_row_key=grouping)
                .partition_hint(100)
                .aggregate(is_defined=hail.agg.fraction(hail.is_defined(entry_field)))
            )
        else:
            mt = mt._select_all(
                row_exprs={'_new_row_key': row_field}, entry_exprs={'is_defined': hail.is_defined(entry_field)}
            )
        ht = mt.localize_entries('entry_fields', 'phenos')
        ht = ht.select(entry_fields=ht.entry_fields.map(lambda entry: entry.is_defined))
        data = ht.entry_fields.collect()
        if len(data) > 200:
            warning(
                f'Missingness dataset has {len(data)} rows. '
                f'This may take {"a very long time" if len(data) > 1000 else "a few minutes"} to plot.'
            )
        rows = hail.str(ht._new_row_key).collect()
    
        df = pd.DataFrame(data)
        df = df.rename(columns=dict(enumerate(columns))).rename(index=dict(enumerate(rows)))
        df.index.name = 'row'
        df.columns.name = 'column'
    
        df = pd.DataFrame(df.stack(), columns=['defined']).reset_index()
    
        p = figure(
            x_range=columns,
            y_range=list(reversed(rows)),
            x_axis_location="above",
            width=plot_width,
            height=plot_height,
            toolbar_location='below',
            tooltips=[('defined', '@defined'), ('row', '@row'), ('column', '@column')],
        )
    
        p.grid.grid_line_color = None
        p.axis.axis_line_color = None
        p.axis.major_tick_line_color = None
        p.axis.major_label_text_font_size = "5pt"
        p.axis.major_label_standoff = 0
        colors = ["#75968f", "#a5bab7", "#c9d9d3", "#e2e2e2", "#dfccce", "#ddb7b1", "#cc7878", "#933b41", "#550b1d"]
        from bokeh.models import BasicTicker, ColorBar, LinearColorMapper, PrintfTickFormatter
    
        mapper = LinearColorMapper(palette=colors, low=df.defined.min(), high=df.defined.max())
    
        p.rect(
            x='column',
            y='row',
            width=1,
            height=1,
            source=df,
            fill_color={'field': 'defined', 'transform': mapper},
            line_color=None,
        )
    
        color_bar = ColorBar(
            color_mapper=mapper,
            major_label_text_font_size="5pt",
            ticker=BasicTicker(desired_num_ticks=len(colors)),
            formatter=PrintfTickFormatter(format="%d"),
            label_standoff=6,
            border_line_color=None,
            location=(0, 0),
        )
        p.add_layout(color_bar, 'right')
        return p

---


# Source code for hail.expr.aggregators.aggregators

    
    
    import difflib
    from functools import update_wrapper, wraps
    
    import hail as hl
    from hail import ir
    from hail.expr import (
        Aggregation,
        ArrayExpression,
        BooleanExpression,
        DictExpression,
        Expression,
        ExpressionException,
        Float64Expression,
        Indices,
        Int64Expression,
        NDArrayNumericExpression,
        NumericExpression,
        SetExpression,
        StringExpression,
        StructExpression,
        cast_expr,
        construct_expr,
        expr_any,
        expr_array,
        expr_bool,
        expr_call,
        expr_float64,
        expr_int32,
        expr_int64,
        expr_ndarray,
        expr_numeric,
        expr_oneof,
        expr_set,
        expr_str,
        to_expr,
        unify_all,
        unify_types,
    )
    from hail.expr.expressions.typed_expressions import construct_variable
    from hail.expr.functions import _quantile_from_cdf, _result_from_raw_cdf, float32, rbind
    from hail.expr.types import (
        hail_type,
        tarray,
        tbool,
        tcall,
        tdict,
        tfloat32,
        tfloat64,
        tint32,
        tint64,
        tset,
        tstr,
        tstruct,
        ttuple,
    )
    from hail.typecheck import TypeChecker, func_spec, identity, nullable, oneof, sequenceof, typecheck, typecheck_method
    from hail.utils import wrap_to_list
    from hail.utils.java import Env
    
    
    class AggregableChecker(TypeChecker):
        def __init__(self, coercer):
            self.coercer = coercer
            super(AggregableChecker, self).__init__()
    
        def expects(self):
            return self.coercer.expects()
    
        def format(self, arg):
            return self.coercer.format(arg)
    
        def check(self, x, caller, param):
            x = self.coercer.check(x, caller, param)
            if len(x._ir.search(lambda node: isinstance(node, ir.BaseApplyAggOp))) == 0:
                raise ExpressionException(
                    f"{caller} must be placed outside of an aggregation. See "
                    "https://discuss.hail.is/t/breaking-change-redesign-of-aggregator-interface/701"
                )
            return x
    
    
    agg_expr = AggregableChecker
    
    
    class AggFunc(object):
        def __init__(self):
            self._as_scan = False
            self._agg_bindings = set()
    
        def correct_prefix(self):
            return "scan" if self._as_scan else "agg"
    
        def incorrect_prefix(self):
            return "agg" if self._as_scan else "scan"
    
        def correct_plural(self):
            return "scans" if self._as_scan else "aggregations"
    
        def incorrect_plural(self):
            return "aggregations" if self._as_scan else "scans"
    
        def check_scan_agg_compatibility(self, caller, node):
            if self._as_scan != isinstance(node, ir.ApplyScanOp):
                raise ExpressionException(
                    "'{correct}.{caller}' cannot contain {incorrect}".format(
                        correct=self.correct_prefix(), caller=caller, incorrect=self.incorrect_plural()
                    )
                )
    
        @typecheck_method(
            agg_op=str, seq_op_args=sequenceof(expr_any), ret_type=hail_type, init_op_args=sequenceof(expr_any)
        )
        def __call__(self, agg_op, seq_op_args, ret_type, init_op_args=()):
            indices, aggregations = unify_all(*seq_op_args, *init_op_args)
            if aggregations:
                raise ExpressionException('Cannot aggregate an already-aggregated expression')
            for a in seq_op_args + init_op_args:
                _check_agg_bindings(a, self._agg_bindings)
    
            if self._as_scan:
                x = ir.ApplyScanOp(agg_op, [expr._ir for expr in init_op_args], [expr._ir for expr in seq_op_args])
                aggs = aggregations
            else:
                x = ir.ApplyAggOp(agg_op, [expr._ir for expr in init_op_args], [expr._ir for expr in seq_op_args])
                aggs = aggregations.push(Aggregation(*seq_op_args, *init_op_args))
            return construct_expr(x, ret_type, Indices(indices.source, set()), aggs)
    
        def _fold(self, initial_value, seq_op, comb_op):
            indices, aggregations = unify_all(initial_value)
            accum_name = Env.get_uid()
            other_accum_name = Env.get_uid()
    
            accum_ref = construct_variable(accum_name, initial_value.dtype, indices, aggregations)
            other_accum_ref = construct_variable(other_accum_name, initial_value.dtype, indices, aggregations)
            seq_op_expr = to_expr(seq_op(accum_ref))
            comb_op_expr = to_expr(comb_op(accum_ref, other_accum_ref))
    
            # Tricky, all of initial_value, seq_op, comb_op need to be same type. Need to see if any change the others.
            unified_type = unify_types(initial_value.dtype, seq_op_expr.dtype)
            if unified_type is None:
                raise ExpressionException(
                    "'hl.agg.fold' initial value and seq_op could not be resolved to same expression type."
                    f"   initial_value.dtype: {initial_value.dtype}\n"
                    f"   seq_op.dtype: {seq_op_expr.dtype}\n"
                )
    
            accum_ref = construct_variable(accum_name, unified_type, indices, aggregations)
            other_accum_ref = construct_variable(other_accum_name, unified_type, indices, aggregations)
            seq_op_expr = to_expr(seq_op(accum_ref))
            comb_op_expr = to_expr(comb_op(accum_ref, other_accum_ref))
    
            # Now, that might have changed comb_op type? Could be more general than other 2.
            unified_type = unify_types(unified_type, seq_op_expr.dtype, comb_op_expr.dtype)
            if unified_type is None:
                raise ExpressionException(
                    "'hl.agg.fold' initial value, seq_op, and comb_op could not be resolved to same expression type."
                    f"   initial_value.dtype: {initial_value.dtype}\n"
                    f"   seq_op.dtype: {seq_op_expr.dtype}\n"
                    f"   comb_op.dtype: {comb_op_expr.dtype}"
                )
    
            accum_ref = construct_variable(accum_name, unified_type, indices, aggregations)
            other_accum_ref = construct_variable(other_accum_name, unified_type, indices, aggregations)
            initial_value_casted = cast_expr(initial_value, unified_type)
            seq_op_expr = to_expr(seq_op(accum_ref))
            comb_op_expr = to_expr(comb_op(accum_ref, other_accum_ref))
    
            return construct_expr(
                ir.AggFold(
                    initial_value_casted._ir, seq_op_expr._ir, comb_op_expr._ir, accum_name, other_accum_name, self._as_scan
                ),
                unified_type,
                indices,
                aggregations,
            )
    
        @typecheck_method(f=func_spec(1, expr_any), array_agg_expr=expr_oneof(expr_array(), expr_set()))
        def explode(self, f, array_agg_expr):
            if array_agg_expr._aggregations:
                raise ExpressionException(
                    "'{}.explode' does not support an already-aggregated expression as the argument to 'collection'".format(
                        self.correct_prefix()
                    )
                )
            _check_agg_bindings(array_agg_expr, self._agg_bindings)
    
            if isinstance(array_agg_expr.dtype, tset):
                array_agg_expr = hl.array(array_agg_expr)
            elt = array_agg_expr.dtype.element_type
            var = Env.get_uid()
            ref = construct_expr(ir.Ref(var, elt), elt, array_agg_expr._indices)
            self._agg_bindings.add(var)
            aggregated = f(ref)
            _check_agg_bindings(aggregated, self._agg_bindings)
            self._agg_bindings.remove(var)
    
            if not self._as_scan and not aggregated._aggregations:
                raise ExpressionException(
                    "'{}.explode' must take mapping that contains aggregation expression.".format(self.correct_prefix())
                )
    
            indices, _ = unify_all(array_agg_expr, aggregated)
            aggregations = hl.utils.LinkedList(Aggregation)
            if not self._as_scan:
                aggregations = aggregations.push(Aggregation(array_agg_expr, aggregated))
            return construct_expr(
                ir.AggExplode(ir.toStream(array_agg_expr._ir), var, aggregated._ir, self._as_scan),
                aggregated.dtype,
                Indices(indices.source, aggregated._indices.axes),
                aggregations,
            )
    
        @typecheck_method(condition=expr_bool, aggregation=agg_expr(expr_any))
        def filter(self, condition, aggregation):
            if condition._aggregations:
                raise ExpressionException(
                    f"'hl.{self.correct_prefix()}.filter' does not "
                    f"support an already-aggregated expression as the argument to 'condition'"
                )
            if not self._as_scan and not aggregation._aggregations:
                raise ExpressionException(
                    f"'hl.{self.correct_prefix()}.filter' must have aggregation in argument to 'aggregation'"
                )
    
            _check_agg_bindings(condition, self._agg_bindings)
            _check_agg_bindings(aggregation, self._agg_bindings)
            indices, _ = unify_all(condition, aggregation)
    
            aggregations = hl.utils.LinkedList(Aggregation)
            if not self._as_scan:
                aggregations = aggregations.push(Aggregation(condition, aggregation))
            return construct_expr(
                ir.AggFilter(condition._ir, aggregation._ir, self._as_scan),
                aggregation.dtype,
                Indices(indices.source, aggregation._indices.axes),
                aggregations,
            )
    
        def group_by(self, group, aggregation):
            if group._aggregations:
                raise ExpressionException(
                    f"'hl.{self.correct_prefix()}.group_by' "
                    f"does not support an already-aggregated expression as the argument to 'group'"
                )
            if not self._as_scan and not aggregation._aggregations:
                raise ExpressionException(
                    f"'hl.{self.correct_prefix()}.group_by' must have aggregation in argument to 'aggregation'"
                )
    
            _check_agg_bindings(group, self._agg_bindings)
            _check_agg_bindings(aggregation, self._agg_bindings)
            indices, _ = unify_all(group, aggregation)
    
            aggregations = hl.utils.LinkedList(Aggregation)
            if not self._as_scan:
                aggregations = aggregations.push(Aggregation(aggregation))
    
            return construct_expr(
                ir.AggGroupBy(group._ir, aggregation._ir, self._as_scan),
                tdict(group.dtype, aggregation.dtype),
                Indices(indices.source, aggregation._indices.axes),
                aggregations,
            )
    
        def array_agg(self, array, f):
            if array._aggregations:
                raise ExpressionException(
                    f"'hl.{self.correct_prefix()}.array_agg' "
                    f"does not support an already-aggregated expression as the argument to 'array'"
                )
            _check_agg_bindings(array, self._agg_bindings)
    
            elt = array.dtype.element_type
            var = Env.get_uid()
            ref = construct_expr(ir.Ref(var, elt), elt, array._indices)
            self._agg_bindings.add(var)
            aggregated = f(ref)
            _check_agg_bindings(aggregated, self._agg_bindings)
            self._agg_bindings.remove(var)
    
            if not self._as_scan and not aggregated._aggregations:
                raise ExpressionException(
                    f"'hl.{self.correct_prefix()}.array_agg' must take mapping that contains aggregation expression."
                )
    
            indices, _ = unify_all(array, aggregated)
            aggregations = hl.utils.LinkedList(Aggregation)
            if not self._as_scan:
                aggregations = aggregations.push(Aggregation(array, aggregated))
            return construct_expr(
                ir.AggArrayPerElement(array._ir, var, 'unused', aggregated._ir, self._as_scan),
                tarray(aggregated.dtype),
                Indices(indices.source, aggregated._indices.axes),
                aggregations,
            )
    
        @property
        def context(self):
            if self._as_scan:
                return 'scan'
            else:
                return 'agg'
    
    
    def _aggregate_local_array(array, f):
        """Compute a summary of an array using aggregators. Useful for accessing
        functionality that exists in `hl.agg` but not elsewhere, like `hl.agg.call_stats`.
    
        Parameters
        ----------
        array
        f
    
        Returns
        -------
        Aggregation result.
        """
        elt = array.dtype.element_type
    
        var = Env.get_uid(base='agg')
        ref = construct_expr(ir.Ref(var, elt), elt, array._indices)
        aggregated = f(ref)
    
        if not aggregated._aggregations:
            raise ExpressionException(
                "'hl.aggregate_local_array' must take a mapping that contains aggregation expression."
            )
    
        indices, _ = unify_all(array, aggregated)
        if isinstance(array.dtype, tarray):
            stream = ir.toStream(array._ir)
        else:
            stream = array._ir
        return construct_expr(
            ir.StreamAgg(stream, var, aggregated._ir),
            aggregated.dtype,
            Indices(indices.source, indices.axes),
            array._aggregations,
        )
    
    
    _agg_func = AggFunc()
    
    
    def _check_agg_bindings(expr, bindings):
        bound_references = {
            ref.name
            for ref in expr._ir.search(
                lambda x: (
                    isinstance(x, ir.Ref)
                    and not isinstance(x, ir.TopLevelReference)
                    and not x.name.startswith('__uid_scan')
                    and not x.name.startswith('__uid_agg')
                    and not x.name == '__rng_state'
                )
            )
        }
        free_variables = bound_references - expr._ir.bound_variables - bindings
        if free_variables:
            raise ExpressionException(
                "dynamic variables created by 'hl.bind' or lambda methods like 'hl.map' may not be aggregated"
            )
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.approx_cdf)@typecheck(expr=expr_numeric, k=int, _raw=bool)
    def approx_cdf(expr, k=100, *, _raw=False):
        """Produce a summary of the distribution of values.
    
        Notes
        -----
        This method returns a struct containing two arrays: `values` and `ranks`.
        The `values` array contains an ordered sample of values seen. The `ranks`
        array is one longer, and contains the approximate ranks for the
        corresponding values.
    
        These represent a summary of the CDF of the distribution of values. In
        particular, for any value `x = values(i)` in the summary, we estimate that
        there are `ranks(i)` values strictly less than `x`, and that there are
        `ranks(i+1)` values less than or equal to `x`. For any value `y` (not
        necessarily in the summary), we estimate CDF(y) to be `ranks(i)`, where `i`
        is such that `values(i-1) < y ≤ values(i)`.
    
        An alternative intuition is that the summary encodes a compressed
        approximation to the sorted list of values. For example, values=[0,2,5,6,9]
        and ranks=[0,3,4,5,8,10] represents the approximation [0,0,0,2,5,6,6,6,9,9],
        with the value `values(i)` occupying indices `ranks(i)` (inclusive) to
        `ranks(i+1)` (exclusive).
    
        The returned struct also contains an array `_compaction_counts`, which is
        used internally to support downstream error estimation.
    
        Warning
        -------
        This is an approximate and nondeterministic method.
    
        Parameters
        ----------
        expr : ``.Expression``
            Expression to collect.
        k : ``int``
            Parameter controlling the accuracy vs. memory usage tradeoff.
    
        Returns
        -------
        ``.StructExpression``
            Struct containing `values` and `ranks` arrays.
        """
        raw_res = _agg_func(
            'ApproxCDF',
            [hl.float64(expr)],
            tstruct(levels=tarray(tint32), items=tarray(tfloat64), _compaction_counts=tarray(tint32)),
            init_op_args=[k],
        )
        conv = {
            tint32: lambda x: x.map(hl.int),
            tint64: lambda x: x.map(hl.int64),
            tfloat32: lambda x: x.map(hl.float32),
            tfloat64: identity,
        }
        if _raw:
            return raw_res
        else:
            raw_res = raw_res.annotate(items=conv[expr.dtype](raw_res['items']))
            return _result_from_raw_cdf(raw_res)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.approx_quantiles)@typecheck(expr=expr_numeric, qs=expr_oneof(expr_numeric, expr_array(expr_numeric)), k=int)
    def approx_quantiles(expr, qs, k=100) -> Expression:
        """Compute an array of approximate quantiles.
    
        Examples
        --------
        Estimate the median of the `HT` field.
    
        >>> table1.aggregate(hl.agg.approx_quantiles(table1.HT, 0.5)) # doctest: +SKIP_OUTPUT_CHECK
        64
    
        Estimate the quartiles of the `HT` field.
    
        >>> table1.aggregate(hl.agg.approx_quantiles(table1.HT, [0, 0.25, 0.5, 0.75, 1])) # doctest: +SKIP_OUTPUT_CHECK
        [50, 60, 64, 71, 86]
    
        Warning
        -------
        This is an approximate and nondeterministic method.
    
        Parameters
        ----------
        expr : ``.Expression``
            Expression to collect.
        qs : ``.NumericExpression`` or ``.ArrayNumericExpression``
            Number or array of numbers between 0 and 1.
        k : ``int``
            Parameter controlling the accuracy vs. memory usage tradeoff. Increasing k increases both memory use and accuracy.
    
        Returns
        -------
        ``.NumericExpression`` or ``.ArrayNumericExpression``
            If `qs` is a single number, returns the estimated quantile.
            If `qs` is an array, returns the array of estimated quantiles.
        """
        if isinstance(qs.dtype, tarray):
            return rbind(approx_cdf(expr, k), lambda cdf: qs.map(lambda q: _quantile_from_cdf(cdf, float32(q))))
        else:
            return _quantile_from_cdf(approx_cdf(expr, k), qs)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.approx_median)@typecheck(expr=expr_numeric, k=int)
    def approx_median(expr, k=100) -> Expression:
        """Compute the approximate median. This function is a shorthand for `approx_quantiles(expr, .5, k)`
    
        Examples
        --------
        Estimate the median of the `HT` field.
    
        >>> table1.aggregate(hl.agg.approx_median(table1.HT)) # doctest: +SKIP_OUTPUT_CHECK
        64
    
        Warning
        -------
        This is an approximate and nondeterministic method.
    
        Parameters
        ----------
        expr : ``.Expression``
            Expression to collect.
        k : ``int``
            Parameter controlling the accuracy vs. memory usage tradeoff. Increasing k increases both memory use and accuracy.
    
        See Also
        --------
        ``approx_quantiles``
    
        Returns
        -------
        ``.NumericExpression``
            The estimated median.
        """
    
        return approx_quantiles(expr, 0.5, k)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.collect)@typecheck(expr=expr_any)
    def collect(expr) -> ArrayExpression:
        """Collect records into an array.
    
        Examples
        --------
        Collect the `ID` field where `HT` is greater than 68:
    
        >>> table1.aggregate(hl.agg.filter(table1.HT > 68, hl.agg.collect(table1.ID)))
        [2, 3]
    
        Notes
        -----
        The element order of the resulting array is not guaranteed, and in some
        cases is non-deterministic.
    
        Use ``collect_as_set`` to collect unique items.
    
        Warning
        -------
        Collecting a large number of items can cause out-of-memory exceptions.
    
        Parameters
        ----------
        expr : ``.Expression``
            Expression to collect.
    
        Returns
        -------
        ``.ArrayExpression``
            Array of all `expr` records.
        """
        return _agg_func('Collect', [expr], tarray(expr.dtype))
    
    
    
    
    @typecheck(len=expr_int32, expr=expr_array(expr_any))
    def _densify(len, expr) -> ArrayExpression:
        return _agg_func('Densify', [expr], expr.dtype, init_op_args=[len])
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.collect_as_set)@typecheck(expr=expr_any)
    def collect_as_set(expr) -> SetExpression:
        """Collect records into a set.
    
        Examples
        --------
        Collect the unique `ID` field where `HT` is greater than 68:
    
        >>> table1.aggregate(hl.agg.filter(table1.HT > 68, hl.agg.collect_as_set(table1.ID)))
        {2, 3}
    
        Note that when collecting a set-typed field with ``.collect_as_set``, the values become
        ``.frozenset`` s because Python does not permit the keys of a dictionary to be mutable:
    
        >>> table1.aggregate(hl.agg.filter(table1.HT > 68, hl.agg.collect_as_set(hl.set({table1.ID}))))
        {frozenset({3}), frozenset({2})}
    
        Warning
        -------
        Collecting a large number of items can cause out-of-memory exceptions.
    
        Parameters
        ----------
        expr : ``.Expression``
            Expression to collect.
    
        Returns
        -------
        ``.SetExpression``
            Set of unique `expr` records.
    
        """
        return _agg_func('CollectAsSet', [expr], tset(expr.dtype))
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.count)@typecheck()
    def count() -> Int64Expression:
        """Count the number of records.
    
        Examples
        --------
        Group by the `SEX` field and count the number of rows in each category:
    
        >>> (table1.group_by(table1.SEX)
        ...        .aggregate(n=hl.agg.count())
        ...        .show())
        +-----+-------+
        | SEX |     n |
        +-----+-------+
        | str | int64 |
        +-----+-------+
        | "F" |     2 |
        | "M" |     2 |
        +-----+-------+
    
        Returns
        -------
        ``.Expression`` of type :py``.tint64``
            Total number of records.
        """
        return _agg_func('Count', [], tint64)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.count_where)@typecheck(condition=expr_bool)
    def count_where(condition) -> Int64Expression:
        """Count the number of records where a predicate is ``True``.
    
        Examples
        --------
    
        Count the number of individuals with `HT` greater than 68:
    
        >>> table1.aggregate(hl.agg.count_where(table1.HT > 68))
        2
    
        Parameters
        ----------
        condition : ``.BooleanExpression``
            Criteria for inclusion.
    
        Returns
        -------
        ``.Expression`` of type :py``.tint64``
            Total number of records where `condition` is ``True``.
        """
    
        return _agg_func('Sum', [hl.int64(condition)], tint64)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.any)@typecheck(condition=expr_bool)
    def any(condition) -> BooleanExpression:
        """Returns ``True`` if `condition` is ``True`` for any record.
    
        Examples
        --------
    
        >>> (table1.group_by(table1.SEX)
        ... .aggregate(any_over_70 = hl.agg.any(table1.HT > 70))
        ... .show())
        +-----+-------------+
        | SEX | any_over_70 |
        +-----+-------------+
        | str | bool        |
        +-----+-------------+
        | "F" | False       |
        | "M" | True        |
        +-----+-------------+
    
        Notes
        -----
        If there are no records to aggregate, the result is ``False``.
    
        Missing records are not considered. If every record is missing,
        the result is also ``False``.
    
        Parameters
        ----------
        condition : ``.BooleanExpression``
            Condition to test.
    
        Returns
        -------
        ``.BooleanExpression``
        """
        return count_where(condition) > 0
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.all)@typecheck(condition=expr_bool)
    def all(condition) -> BooleanExpression:
        """Returns ``True`` if `condition` is ``True`` for every record.
    
        Examples
        --------
    
        >>> (table1.group_by(table1.SEX)
        ... .aggregate(all_under_70 = hl.agg.all(table1.HT < 70))
        ... .show())
        +-----+--------------+
        | SEX | all_under_70 |
        +-----+--------------+
        | str | bool         |
        +-----+--------------+
        | "F" | False        |
        | "M" | False        |
        +-----+--------------+
    
        Notes
        -----
        If there are no records to aggregate, the result is ``True``.
    
        Missing records are not considered. If every record is missing,
        the result is also ``True``.
    
        Parameters
        ----------
        condition : ``.BooleanExpression``
            Condition to test.
    
        Returns
        -------
        ``.BooleanExpression``
        """
        return count_where(~condition) == 0
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.counter)@typecheck(expr=expr_any, weight=nullable(expr_numeric))
    def counter(expr, *, weight=None) -> DictExpression:
        """Count the occurrences of each unique record and return a dictionary.
    
        Examples
        --------
        Count the number of individuals for each unique `SEX` value:
    
        >>> table1.aggregate(hl.agg.counter(table1.SEX))
        {'F': 2, 'M': 2}
        <BLANKLINE>
    
        Compute the total height for each unique `SEX` value:
    
        >>> table1.aggregate(hl.agg.counter(table1.SEX, weight=table1.HT))
        {'F': 130, 'M': 137}
        <BLANKLINE>
    
        Note that when counting a set-typed field, the values become ``.frozenset`` s because
        Python does not permit the keys of a dictionary to be mutable:
    
        >>> table1.aggregate(hl.agg.counter(hl.set({table1.SEX}), weight=table1.HT))
        {frozenset({'F'}): 130, frozenset({'M'}): 137}
        <BLANKLINE>
    
        Notes
        -----
        If you need a more complex grouped aggregation than ``counter``
        supports, try using ``group_by``.
    
        This aggregator method returns a dict expression whose key type is the
        same type as `expr` and whose value type is ``.Expression`` of type :py``.tint64``.
        This dict contains a key for each unique value of `expr`, and the value
        is the number of times that key was observed.
    
        Ensure that the result can be stored in memory on a single machine.
    
        Warning
        -------
        Using ``counter`` with a large number of unique items can cause
        out-of-memory exceptions.
    
        Parameters
        ----------
        expr : ``.Expression``
            Expression to count by key.
        weight : ``.NumericExpression``, optional
            Expression by which to weight each occurence (when unspecified,
            it is effectively ``1``)
    
        Returns
        -------
        ``.DictExpression``
            Dictionary with the number of occurrences of each unique record.
    
        """
        if weight is None:
            return _agg_func.group_by(expr, count())
        return _agg_func.group_by(expr, hl.agg.sum(weight))
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.take)@typecheck(expr=expr_any, n=int, ordering=nullable(oneof(expr_any, func_spec(1, expr_any))))
    def take(expr, n, ordering=None) -> ArrayExpression:
        """Take `n` records of `expr`, optionally ordered by `ordering`.
    
        Examples
        --------
        Take 3 elements of field `X`:
    
        >>> table1.aggregate(hl.agg.take(table1.X, 3))
        [5, 6, 7]
    
        Take the `ID` and `HT` fields, ordered by `HT` (descending):
    
        >>> table1.aggregate(hl.agg.take(hl.struct(ID=table1.ID, HT=table1.HT),
        ...                              3,
        ...                              ordering=-table1.HT))
        [Struct(ID=2, HT=72), Struct(ID=3, HT=70), Struct(ID=1, HT=65)]
    
        Notes
        -----
        The resulting array can include fewer than `n` elements if there are fewer
        than `n` total records.
    
        The `ordering` argument may be an expression, a function, or ``None``.
    
        If `ordering` is an expression, this expression's type should be one with
        a natural ordering (e.g. numeric).
    
        If `ordering` is a function, it will be evaluated on each record of `expr`
        to compute the value used for ordering. In the above example,
        ``ordering=-table1.HT`` and ``ordering=lambda x: -x.HT`` would be
        equivalent.
    
        If `ordering` is ``None``, then there is no guaranteed ordering on the
        elements taken, and and the results may be non-deterministic.
    
        Missing values are always sorted **last**.
    
        Parameters
        ----------
        expr : ``.Expression``
            Expression to store.
        n : ``.Expression`` of type :py``.tint32``
            Number of records to take.
        ordering : ``.Expression`` or function ((arg) -> ``.Expression``) or None
            Optional ordering on records.
    
        Returns
        -------
        ``.ArrayExpression``
            Array of up to `n` records of `expr`.
    
        """
        n = to_expr(n)
        if ordering is None:
            return _agg_func('Take', [expr], tarray(expr.dtype), [n])
        else:
            return _agg_func('TakeBy', [expr, ordering], tarray(expr.dtype), [n])
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.min)@typecheck(expr=expr_numeric)
    def min(expr) -> NumericExpression:
        """Compute the minimum `expr`.
    
        Examples
        --------
        Compute the minimum value of `HT`:
    
        >>> table1.aggregate(hl.agg.min(table1.HT))
        60
    
        Notes
        -----
        This function returns the minimum non-missing value. If there are no
        non-missing values, then the result is missing.
    
        For back-compatibility reasons, this function also ignores NaN, in contrast
        with ``.functions.min``. The behavior is similar to
        ``.functions.nanmin``.
    
        Parameters
        ----------
        expr : ``.NumericExpression``
            Numeric expression.
    
        Returns
        -------
        ``.NumericExpression``
            Minimum value of all `expr` records, same type as `expr`.
        """
        return _agg_func('Min', [expr], expr.dtype)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.max)@typecheck(expr=expr_numeric)
    def max(expr) -> NumericExpression:
        """Compute the maximum `expr`.
    
        Examples
        --------
        Compute the maximum value of `HT`:
    
        >>> table1.aggregate(hl.agg.max(table1.HT))
        72
    
        Notes
        -----
        This function returns the maximum non-missing value. If there are no
        non-missing values, then the result is missing.
    
        For back-compatibility reasons, this function also ignores NaN, in contrast
        with ``.functions.max``. The behavior is similar to
        ``.functions.nanmax``.
    
        Parameters
        ----------
        expr : ``.NumericExpression``
            Numeric expression.
    
        Returns
        -------
        ``.NumericExpression``
            Maximum value of all `expr` records, same type as `expr`.
        """
        return _agg_func('Max', [expr], expr.dtype)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.sum)@typecheck(expr=expr_oneof(expr_int64, expr_float64))
    def sum(expr):
        """Compute the sum of all records of `expr`.
    
        Examples
        --------
        Compute the sum of field `C1`:
    
        >>> table1.aggregate(hl.agg.sum(table1.C1))
        25
    
        Notes
        -----
        Missing values are ignored (treated as zero).
    
        If `expr` is an expression of type :py``.tint32``, :py``.tint64``,
        or :py``.tbool``, then the result is an expression of type
        :py``.tint64``. If `expr` is an expression of type :py``.tfloat32``
        or :py``.tfloat64``, then the result is an expression of type
        :py``.tfloat64``.
    
        Warning
        -------
        Boolean values are cast to integers before computing the sum.
    
        Parameters
        ----------
        expr : ``.NumericExpression``
            Numeric expression.
    
        Returns
        -------
        ``.Expression`` of type :py``.tint64`` or :py``.tfloat64``
            Sum of records of `expr`.
        """
        return _agg_func('Sum', [expr], expr.dtype)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.array_sum)@typecheck(expr=expr_array(expr_oneof(expr_int64, expr_float64)))
    def array_sum(expr) -> ArrayExpression:
        """Compute the coordinate-wise sum of all records of `expr`.
    
        Examples
        --------
        Compute the sum of `C1` and `C2`:
    
        >>> table1.aggregate(hl.agg.array_sum([table1.C1, table1.C2]))
        [25, 282]
    
        Notes
        ------
        All records must have the same length. Each coordinate is summed
        independently as described in ``sum``.
    
        Parameters
        ----------
        expr : ``.ArrayNumericExpression``
    
        Returns
        -------
        ``.ArrayExpression`` with element type :py``.tint64`` or :py``.tfloat64``
        """
        return array_agg(hl.agg.sum, expr)
    
    
    
    
    @typecheck(expr=expr_ndarray())
    def ndarray_sum(expr) -> NDArrayNumericExpression:
        """Compute the sum of all records of `expr` of the same shape.
    
        :param expr:
        :return:
        """
        return _agg_func("NDArraySum", [expr], expr.dtype)
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.mean)@typecheck(expr=expr_float64)
    def mean(expr) -> Float64Expression:
        """Compute the mean value of records of `expr`.
    
        Examples
        --------
        Compute the mean of field `HT`:
    
        >>> table1.aggregate(hl.agg.mean(table1.HT))
        66.75
    
        Notes
        -----
        Missing values are ignored.
    
        Parameters
        ----------
        expr : ``.NumericExpression``
            Numeric expression.
    
        Returns
        -------
        ``.Expression`` of type :py``.tfloat64``
            Mean value of records of `expr`.
        """
        return hl.bind(lambda expr: sum(expr) / count_where(hl.is_defined(expr)), expr, _ctx=_agg_func.context)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.stats)@typecheck(expr=expr_float64)
    def stats(expr) -> StructExpression:
        """Compute a number of useful statistics about `expr`.
    
        Examples
        --------
        Compute statistics about field `HT`:
    
        >>> table1.aggregate(hl.agg.stats(table1.HT))  #doctest: +SKIP
        Struct(mean=66.75, stdev=4.656984002549289, min=60.0, max=72.0, n=4, sum=267.0)
    
        Notes
        -----
        Computes a struct with the following fields:
    
        - `min` (:py``.tfloat64``) - Minimum value.
        - `max` (:py``.tfloat64``) - Maximum value.
        - `mean` (:py``.tfloat64``) - Mean value,
        - `stdev` (:py``.tfloat64``) - Standard deviation.
        - `n` (:py``.tint64``) - Number of non-missing records.
        - `sum` (:py``.tfloat64``) - Sum.
    
        Parameters
        ----------
        expr : ``.NumericExpression``
            Numeric expression.
    
        Returns
        -------
        ``.StructExpression``
            Struct expression with fields `mean`, `stdev`, `min`, `max`,
            `n`, and `sum`.
        """
    
        return hl.bind(
            lambda expr: hl.bind(
                lambda aggs: hl.bind(
                    lambda mean: hl.struct(
                        mean=mean,
                        stdev=hl.sqrt(hl.float64(aggs.sumsq - mean * aggs.sum) / aggs.n_def),
                        min=hl.float64(aggs.min),
                        max=hl.float64(aggs.max),
                        n=aggs.n_def,
                        sum=hl.float64(aggs.sum),
                    ),
                    hl.float64(aggs.sum) / aggs.n_def,
                ),
                hl.struct(
                    n_def=count_where(hl.is_defined(expr)),
                    sum=sum(expr),
                    sumsq=sum(expr**2),
                    min=min(expr),
                    max=max(expr),
                ),
            ),
            expr,
            _ctx=_agg_func.context,
        )
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.product)@typecheck(expr=expr_oneof(expr_int64, expr_float64))
    def product(expr):
        """Compute the product of all records of `expr`.
    
        Examples
        --------
        Compute the product of field `C1`:
    
        >>> table1.aggregate(hl.agg.product(table1.C1))
        440
    
        Notes
        -----
        Missing values are ignored (treated as one).
    
        If `expr` is an expression of type :py``.tint32``, :py``.tint64`` or
        :py``.tbool``, then the result is an expression of type
        :py``.tint64``. If `expr` is an expression of type :py``.tfloat32``
        or :py``.tfloat64``, then the result is an expression of type
        :py``.tfloat64``.
    
        Warning
        -------
        Boolean values are cast to integers before computing the product.
    
        Parameters
        ----------
        expr : ``.NumericExpression``
            Numeric expression.
    
        Returns
        -------
        ``.Expression`` of type :py``.tint64`` or :py``.tfloat64``
            Product of records of `expr`.
        """
    
        return _agg_func('Product', [expr], expr.dtype)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.fraction)@typecheck(predicate=expr_bool)
    def fraction(predicate) -> Float64Expression:
        """Compute the fraction of records where `predicate` is ``True``.
    
        Examples
        --------
        Compute the fraction of rows where `SEX` is "F" and `HT` > 65:
    
        >>> table1.aggregate(hl.agg.fraction((table1.SEX == 'F') & (table1.HT > 65)))
        0.25
    
        Notes
        -----
        Missing values for `predicate` are treated as ``False``.
    
        Parameters
        ----------
        predicate : ``.BooleanExpression``
            Boolean predicate.
    
        Returns
        -------
        ``.Expression`` of type :py``.tfloat64``
            Fraction of records where `predicate` is ``True``.
        """
        return hl.bind(
            lambda n: hl.if_else(n == 0, hl.missing(hl.tfloat64), hl.float64(filter(predicate, count())) / n), count()
        )
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.hardy_weinberg_test)@typecheck(expr=expr_call, one_sided=expr_bool)
    def hardy_weinberg_test(expr, one_sided=False) -> StructExpression:
        """Performs test of Hardy-Weinberg equilibrium.
    
        Examples
        --------
        Test each row of a dataset:
    
        >>> dataset_result = dataset.annotate_rows(hwe = hl.agg.hardy_weinberg_test(dataset.GT))
    
        Test each row on a sub-population:
    
        >>> dataset_result = dataset.annotate_rows(
        ...     hwe_eas = hl.agg.filter(dataset.pop == 'EAS',
        ...                             hl.agg.hardy_weinberg_test(dataset.GT)))
    
        Notes
        -----
        This method performs the test described in ``.functions.hardy_weinberg_test`` based solely on
        the counts of homozygous reference, heterozygous, and homozygous variant calls.
    
        The resulting struct expression has two fields:
    
        - `het_freq_hwe` (:py``.tfloat64``) - Expected frequency
          of heterozygous calls under Hardy-Weinberg equilibrium.
    
        - `p_value` (:py``.tfloat64``) - p-value from test of Hardy-Weinberg
          equilibrium.
    
        By default, Hail computes the exact p-value with mid-p-value correction, i.e. the
        probability of a less-likely outcome plus one-half the probability of an
        equally-likely outcome. See this `document <_static/LeveneHaldane.pdf>`__ for
        details on the Levene-Haldane distribution and references.
    
        To perform one-sided exact test of excess heterozygosity with mid-p-value
        correction instead, set `one_sided=True` and the p-value returned will be
        from the one-sided exact test.
    
        Warning
        -------
        Non-diploid calls (``ploidy != 2``) are ignored in the counts. While the
        counts are defined for multiallelic variants, this test is only statistically
        rigorous in the biallelic setting; use ``~hail.methods.split_multi``
        to split multiallelic variants beforehand.
    
        Parameters
        ----------
        expr : ``.CallExpression``
            Call to test for Hardy-Weinberg equilibrium.
        one_sided: ``bool``
            ``False`` by default. When ``True``, perform one-sided test for excess heterozygosity.
    
        Returns
        -------
        ``.StructExpression``
            Struct expression with fields `het_freq_hwe` and `p_value`.
        """
        return hl.rbind(
            hl.rbind(
                expr,
                lambda call: filter(
                    call.ploidy == 2,
                    counter(call.n_alt_alleles()).map_values(
                        lambda i: hl.case()
                        .when(i < 1 << 31, hl.int(i))
                        .or_error('hardy_weinberg_test: count greater than MAX_INT')
                    ),
                ),
                _ctx=_agg_func.context,
            ),
            lambda counts: hl.hardy_weinberg_test(
                counts.get(0, 0), counts.get(1, 0), counts.get(2, 0), one_sided=one_sided
            ),
        )
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.explode)@typecheck(f=func_spec(1, agg_expr(expr_any)), array_agg_expr=expr_oneof(expr_array(), expr_set()))
    def explode(f, array_agg_expr) -> Expression:
        """Explode an array or set expression to aggregate the elements of all records.
    
        Examples
        --------
        Compute the mean of all elements in fields `C1`, `C2`, and `C3`:
    
        >>> table1.aggregate(hl.agg.explode(lambda elt: hl.agg.mean(elt), [table1.C1, table1.C2, table1.C3]))
        24.833333333333332
    
        Compute the set of all observed elements in the `filters` field (``Set[String]``):
    
        >>> dataset.aggregate_rows(hl.agg.explode(lambda elt: hl.agg.collect_as_set(elt), dataset.filters))
        set()
    
        Notes
        -----
        This method can be used with aggregator functions to aggregate the elements
        of collection types (``.tarray`` and ``.tset``).
    
        Parameters
        ----------
        f : Function from ``.Expression`` to ``.Expression``
            Aggregation function to apply to each element of the exploded array.
        array_agg_expr : ``.CollectionExpression``
            Expression of type ``.tarray`` or ``.tset``.
    
        Returns
        -------
        ``.Expression``
            Aggregation expression.
        """
        return _agg_func.explode(f, array_agg_expr)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.filter)@typecheck(condition=expr_bool, aggregation=agg_expr(expr_any))
    def filter(condition, aggregation) -> Expression:
        """Filter records according to a predicate.
    
        Examples
        --------
        Collect the `ID` field where `HT` >= 70:
    
        >>> table1.aggregate(hl.agg.filter(table1.HT >= 70, hl.agg.collect(table1.ID)))
        [2, 3]
    
        Notes
        -----
        This method can be used with aggregator functions to remove records from
        aggregation.
    
        Parameters
        ----------
        condition : ``.BooleanExpression``
            Filter expression.
        aggregation : ``.Expression``
            Aggregation expression to filter.
    
        Returns
        -------
        ``.Expression``
            Aggregable expression.
        """
    
        return _agg_func.filter(condition, aggregation)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.inbreeding)@typecheck(expr=expr_call, prior=expr_float64)
    def inbreeding(expr, prior) -> StructExpression:
        """Compute inbreeding statistics on calls.
    
        Examples
        --------
        Compute inbreeding statistics per column:
    
        >>> dataset_result = dataset.annotate_cols(IB = hl.agg.inbreeding(dataset.GT, dataset.variant_qc.AF[1]))
        >>> dataset_result.IB.show(width=100)
        +------------------+-----------+-------------+------------------+------------------+
        | s                | IB.f_stat | IB.n_called | IB.expected_homs | IB.observed_homs |
        +------------------+-----------+-------------+------------------+------------------+
        | str              |   float64 |       int64 |          float64 |            int64 |
        +------------------+-----------+-------------+------------------+------------------+
        | "C1046::HG02024" |  2.79e-01 |           9 |         7.61e+00 |                8 |
        | "C1046::HG02025" | -4.41e-01 |           9 |         7.61e+00 |                7 |
        | "C1046::HG02026" | -4.41e-01 |           9 |         7.61e+00 |                7 |
        | "C1047::HG00731" |  2.79e-01 |           9 |         7.61e+00 |                8 |
        | "C1047::HG00732" |  2.79e-01 |           9 |         7.61e+00 |                8 |
        | "C1047::HG00733" |  2.79e-01 |           9 |         7.61e+00 |                8 |
        | "C1048::HG02024" | -4.41e-01 |           9 |         7.61e+00 |                7 |
        | "C1048::HG02025" | -4.41e-01 |           9 |         7.61e+00 |                7 |
        | "C1048::HG02026" | -4.41e-01 |           9 |         7.61e+00 |                7 |
        | "C1049::HG00731" |  2.79e-01 |           9 |         7.61e+00 |                8 |
        +------------------+-----------+-------------+------------------+------------------+
        showing top 10 rows
        <BLANKLINE>
    
        Notes
        -----
    
        ``E`` is total number of expected homozygous calls, given by the sum of
        ``1 - 2.0 * prior * (1 - prior)`` across records.
    
        ``O`` is the observed number of homozygous calls across records.
    
        ``N`` is the number of non-missing calls.
    
        ``F`` is the inbreeding coefficient, and is computed by: ``(O - E) / (N - E)``.
    
        This method returns a struct expression with four fields:
    
         - `f_stat` (:py``.tfloat64``): ``F``, the inbreeding coefficient.
         - `n_called` (:py``.tint64``): ``N``, the number of non-missing calls.
         - `expected_homs` (:py``.tfloat64``): ``E``, the expected number of homozygotes.
         - `observed_homs` (:py``.tint64``): ``O``, the number of observed homozygotes.
    
        Parameters
        ----------
        expr : ``.CallExpression``
            Call expression.
        prior : ``.Expression`` of type :py``.tfloat64``
            Alternate allele frequency prior.
    
        Returns
        -------
        ``.StructExpression``
            Struct expression with fields `f_stat`, `n_called`, `expected_homs`, `observed_homs`.
        """
        return hl.rbind(
            prior,
            expr,
            lambda af, call: hl.rbind(
                hl.agg.filter(
                    hl.is_defined(af) & hl.is_defined(call),
                    hl.struct(
                        n_called=hl.agg.count(),
                        expected_homs=hl.agg.sum(1 - (2 * af * (1 - af))),
                        observed_homs=hl.agg.count_where(
                            hl.case()
                            .when((call.ploidy == 2) & (call.unphased_diploid_gt_index() <= 2), ~call.is_het())
                            .or_error("'inbreeding' does not support non-diploid or multiallelic genotypes")
                        ),
                    ),
                ),
                lambda r: hl.struct(
                    f_stat=(r['observed_homs'] - r['expected_homs']) / (r['n_called'] - r['expected_homs']), **r
                ),
            ),
            _ctx=_agg_func.context,
        )
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.call_stats)@typecheck(call=expr_call, alleles=expr_oneof(expr_int32, expr_array(expr_str)))
    def call_stats(call, alleles) -> StructExpression:
        """Compute useful call statistics.
    
        Examples
        --------
        Compute call statistics per row:
    
        >>> dataset_result = dataset.annotate_rows(gt_stats = hl.agg.call_stats(dataset.GT, dataset.alleles))
        >>> dataset_result.rows().key_by('locus').select('gt_stats').show(width=120)
        +---------------+--------------+---------------------+-------------+---------------------------+
        | locus         | gt_stats.AC  | gt_stats.AF         | gt_stats.AN | gt_stats.homozygote_count |
        +---------------+--------------+---------------------+-------------+---------------------------+
        | locus<GRCh37> | array<int32> | array<float64>      |       int32 | array<int32>              |
        +---------------+--------------+---------------------+-------------+---------------------------+
        | 20:10579373   | [199,1]      | [9.95e-01,5.00e-03] |         200 | [99,0]                    |
        | 20:10579398   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [99,1]                    |
        | 20:10627772   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [98,0]                    |
        | 20:10633237   | [108,92]     | [5.40e-01,4.60e-01] |         200 | [31,23]                   |
        | 20:10636995   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [98,0]                    |
        | 20:10639222   | [175,25]     | [8.75e-01,1.25e-01] |         200 | [78,3]                    |
        | 20:13763601   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [98,0]                    |
        | 20:16223922   | [87,101]     | [4.63e-01,5.37e-01] |         188 | [28,35]                   |
        | 20:17479617   | [191,9]      | [9.55e-01,4.50e-02] |         200 | [91,0]                    |
        +---------------+--------------+---------------------+-------------+---------------------------+
        <BLANKLINE>
    
        Notes
        -----
        This method is meaningful for computing call metrics per variant, but not
        especially meaningful for computing metrics per sample.
    
        This method returns a struct expression with three fields:
    
         - `AC` (``.tarray`` of :py``.tint32``) - Allele counts. One element
           for each allele, including the reference.
         - `AF` (``.tarray`` of :py``.tfloat64``) - Allele frequencies. One
           element for each allele, including the reference.
         - `AN` (:py``.tint32``) - Allele number. The total number of called
           alleles, or the number of non-missing calls * 2.
         - `homozygote_count` (``.tarray`` of :py``.tint32``) - Homozygote
           genotype counts for each allele, including the reference. Only **diploid**
           genotype calls are counted.
    
        Parameters
        ----------
        call : ``.CallExpression``
        alleles : ``.ArrayExpression`` of strings or ``.Int32Expression``
            Variant alleles array, or number of alleles (including reference).
    
        Returns
        -------
        ``.StructExpression``
            Struct expression with fields `AC`, `AF`, `AN`, and `homozygote_count`.
        """
        if alleles.dtype == tint32:
            n_alleles = alleles
        else:
            n_alleles = hl.len(alleles)
        t = tstruct(AC=tarray(tint32), AF=tarray(tfloat64), AN=tint32, homozygote_count=tarray(tint32))
    
        return _agg_func('CallStats', [call], t, init_op_args=[n_alleles])
    
    
    
    
    _bin_idx_f = None
    _result_from_hist_agg_f = None
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.hist)@typecheck(expr=expr_float64, start=expr_float64, end=expr_float64, bins=expr_int32)
    def hist(expr, start, end, bins) -> StructExpression:
        """Compute binned counts of a numeric expression.
    
        Examples
        --------
        Compute a histogram of field `GQ`:
    
        >>> dataset.aggregate_entries(hl.agg.hist(dataset.GQ, 0, 100, 10))  # doctest: +SKIP_OUTPUT_CHECK
        Struct(bin_edges=[0.0, 10.0, 20.0, 30.0, 40.0, 50.0, 60.0, 70.0, 80.0, 90.0, 100.0],
               bin_freq=[2194L, 637L, 2450L, 1081L, 518L, 402L, 11168L, 1918L, 1379L, 11973L]),
               n_smaller=0,
               n_greater=0)
    
        Notes
        -----
        This method returns a struct expression with four fields:
    
         - `bin_edges` (``.tarray`` of :py``.tfloat64``): Bin edges. Bin `i`
           contains values in the left-inclusive, right-exclusive range
           ``[ bin_edges[i], bin_edges[i+1] )``.
         - `bin_freq` (``.tarray`` of :py``.tint64``): Bin
           frequencies. The number of records found in each bin.
         - `n_smaller` (:py``.tint64``): The number of records smaller than the start
           of the first bin.
         - `n_larger` (:py``.tint64``): The number of records larger than the end
           of the last bin.
    
        Parameters
        ----------
        expr : ``.NumericExpression``
            Target numeric expression.
        start : ``int`` or ``float``
            Start of histogram range.
        end : ``int`` or ``float``
            End of histogram range.
        bins : ``int``
            Number of bins.
    
        Returns
        -------
        ``.StructExpression``
            Struct expression with fields `bin_edges`, `bin_freq`, `n_smaller`, and `n_larger`.
        """
        global _bin_idx_f, _result_from_hist_agg_f
        if _bin_idx_f is None:
            _bin_idx_f = hl.experimental.define_function(
                lambda s, e, nbins, binsize, v: (
                    hl.case()
                    .when(v < s, -1)
                    .when(v > e, nbins)
                    .when(v == e, nbins - 1)
                    .default(hl.int32(hl.floor((v - s) / binsize)))
                ),
                hl.tfloat64,
                hl.tfloat64,
                hl.tint32,
                hl.tfloat64,
                hl.tfloat64,
            )
    
        freq_dict = hl.bind(
            lambda expr: hl.agg.filter(
                hl.is_defined(expr) & ~hl.is_nan(expr),
                hl.agg.group_by(_bin_idx_f(start, end, bins, hl.float64(end - start) / bins, expr), hl.agg.count()),
            ),
            expr,
            _ctx=_agg_func.context,
        )
    
        def result(s, nbins, bs, freq_dict):
            return hl.struct(
                bin_edges=hl.range(0, nbins + 1).map(lambda i: s + i * bs),
                bin_freq=hl.range(0, nbins).map(lambda i: freq_dict.get(i, 0)),
                n_smaller=freq_dict.get(-1, 0),
                n_larger=freq_dict.get(nbins, 0),
            )
    
        def wrap_errors(s, e, nbins, freq_dict):
            return (
                hl.case()
                .when(
                    nbins > 0,
                    hl.bind(
                        lambda bs: hl.case()
                        .when((bs > 0) & hl.is_finite(bs), result(s, nbins, bs, freq_dict))
                        .or_error(
                            "'hist': start="
                            + hl.str(s)
                            + " end="
                            + hl.str(e)
                            + " bins="
                            + hl.str(nbins)
                            + " requires positive bin size."
                        ),
                        hl.float64(e - s) / nbins,
                    ),
                )
                .or_error(hl.literal("'hist' requires positive 'bins', but bins=") + hl.str(nbins))
            )
    
        if _result_from_hist_agg_f is None:
            _result_from_hist_agg_f = hl.experimental.define_function(
                wrap_errors, hl.tfloat64, hl.tfloat64, hl.tint32, hl.tdict(hl.tint32, hl.tint64)
            )
    
        return _result_from_hist_agg_f(start, end, bins, freq_dict)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.downsample)@typecheck(x=expr_float64, y=expr_float64, label=nullable(oneof(expr_str, expr_array(expr_str))), n_divisions=int)
    def downsample(x, y, label=None, n_divisions=500) -> ArrayExpression:
        """Downsample (x, y) coordinate datapoints.
    
        Parameters
        ----------
        x : ``.NumericExpression``
            X-values to be downsampled.
        y : ``.NumericExpression``
            Y-values to be downsampled.
        label : ``.StringExpression`` or ``.ArrayExpression``
            Additional data for each (x, y) coordinate. Can pass in multiple fields in an ``.ArrayExpression``.
        n_divisions : ``int``
            Factor by which to downsample (default value = 500). A lower input results in fewer output datapoints.
    
        Returns
        -------
        ``.ArrayExpression``
            Expression for downsampled coordinate points (x, y). The element type of the array is
            ``.ttuple`` of :py``.tfloat64``, :py``.tfloat64``, and ``.tarray`` of :py``.tstr``
        """
        if label is None:
            label = hl.missing(hl.tarray(hl.tstr))
        elif isinstance(label, StringExpression):
            label = hl.array([label])
        return _agg_func(
            'Downsample', [x, y, label], tarray(ttuple(tfloat64, tfloat64, tarray(tstr))), init_op_args=[n_divisions]
        )
    
    
    
    
    @typecheck(expr=expr_any, n=expr_int32)
    def _reservoir_sample(expr, n):
        return _agg_func('ReservoirSample', [expr], tarray(expr.dtype), [n])
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.info_score)@typecheck(gp=expr_array(expr_float64))
    def info_score(gp) -> StructExpression:
        r"""Compute the IMPUTE information score.
    
        Examples
        --------
        Calculate the info score per variant:
    
        >>> gen_mt = hl.import_gen('data/example.gen', sample_file='data/example.sample')
        >>> gen_mt = gen_mt.annotate_rows(info_score = hl.agg.info_score(gen_mt.GP))
    
        Calculate group-specific info scores per variant:
    
        >>> gen_mt = hl.import_gen('data/example.gen', sample_file='data/example.sample')
        >>> gen_mt = gen_mt.annotate_cols(is_case = hl.rand_bool(0.5))
        >>> gen_mt = gen_mt.annotate_rows(info_score = hl.agg.group_by(gen_mt.is_case, hl.agg.info_score(gen_mt.GP)))
    
        Notes
        -----
        The result of ``.info_score`` is a struct with two fields:
    
            - `score` (``float64``) -- Info score.
            - `n_included` (``int32``) -- Number of non-missing samples included in the
              calculation.
    
        We implemented the IMPUTE info measure as described in the supplementary
        information from `Marchini & Howie. Genotype imputation for genome-wide
        association studies. Nature Reviews Genetics (2010)
        <http://www.nature.com/nrg/journal/v11/n7/extref/nrg2796-s3.pdf>`__. To
        calculate the info score ``I_{A}`` for one SNP:
    
        .. math::
    
            I_{A} =
            \begin{cases}
            1 - \frac{\sum_{i=1}^{N}(f_{i} - e_{i}^2)}{2N\hat{\theta}(1 - \hat{\theta})} & \text{when } \hat{\theta} \in (0, 1) \\
            1 & \text{when } \hat{\theta} = 0, \hat{\theta} = 1\\
            \end{cases}
    
        - ``N`` is the number of samples with imputed genotype probabilities
          [``p_{ik} = P(G_{i} = k)`` where ``k \in \{0, 1, 2\}``]
        - ``e_{i} = p_{i1} + 2p_{i2}`` is the expected genotype per sample
        - ``f_{i} = p_{i1} + 4p_{i2}``
        - ``\hat{\theta} = \frac{\sum_{i=1}^{N}e_{i}}{2N}`` is the MLE for the
          population minor allele frequency
    
        Hail will not generate identical results to `QCTOOL
        <http://www.well.ox.ac.uk/~gav/qctool/#overview>`__ for the following
        reasons:
    
        - Hail automatically removes genotype probability distributions that do not
          meet certain requirements on data import with ``.import_gen`` and
          ``.import_bgen``.
        - Hail does not use the population frequency to impute genotype
          probabilities when a genotype probability distribution has been set to
          missing.
        - Hail calculates the same statistic for sex chromosomes as autosomes while
          QCTOOL incorporates sex information.
        - The floating point number Hail stores for each genotype probability is
          slightly different than the original data due to rounding and
          normalization of probabilities.
    
        Warning
        -------
        - The info score Hail reports will be extremely different from QCTOOL when
          a SNP has a high missing rate.
        - If the `gp` array must contain 3 elements, and its elements may not be
          missing.
        - If the genotype data was not imported using the ``.import_gen`` or
          ``.import_bgen`` functions, then the results for all variants will be
          ``score = NA`` and ``n_included = 0``.
        - It only makes semantic sense to compute the info score per variant. While
          the aggregator will run in any context if its arguments are the right
          type, the results are only meaningful in a narrow context.
    
        Parameters
        ----------
        gp : ``.ArrayNumericExpression``
            Genotype probability array. Must have 3 elements, all of which must be
            defined.
    
        Returns
        -------
        ``.StructExpression``
            Struct with fields `score` and `n_included`.
        """
        return hl.rbind(
            gp,
            lambda unchecked_gp: hl.agg.filter(
                hl.is_defined(unchecked_gp),
                hl.rbind(
                    hl.case()
                    .when(hl.len(unchecked_gp) == 3, unchecked_gp)
                    .or_error(f"'info_score': expected 'gp' to have length 3, found length {hl.str(hl.len(unchecked_gp))}"),
                    lambda gp: hl.rbind(
                        gp[1],
                        gp[2],
                        lambda gp1, gp2: hl.rbind(
                            gp1 + 2 * gp2,
                            lambda mean: hl.rbind(
                                hl.agg.sum(gp1 + 4 * gp2 - (mean * mean)),
                                hl.agg.sum(mean),
                                hl.agg.sum(gp1 + gp2 + gp[0]),
                                hl.agg.count(),
                                lambda sum_variance, expected_ac, total_dosage, n: hl.rbind(
                                    hl.if_else(total_dosage != 0, expected_ac / total_dosage, hl.missing(hl.tfloat64)),
                                    lambda theta: hl.struct(
                                        score=hl.case()
                                        .when(n == 0, hl.missing(hl.tfloat64))
                                        .when((theta == 0.0) | (theta == 1.0), 1.0)
                                        .default(1.0 - ((sum_variance / n) / (2 * theta * (1 - theta)))),
                                        n_included=hl.int32(n),
                                    ),
                                ),
                            ),
                            _ctx=_agg_func.context,
                        ),
                        _ctx=_agg_func.context,
                    ),
                    _ctx=_agg_func.context,
                ),
            ),
            _ctx=_agg_func.context,
        )
    
    
    
    
    _result_from_linreg_agg_f = None
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.linreg)@typecheck(
        y=expr_float64, x=oneof(expr_float64, sequenceof(expr_float64)), nested_dim=int, weight=nullable(expr_float64)
    )
    def linreg(y, x, nested_dim=1, weight=None) -> StructExpression:
        """Compute multivariate linear regression statistics.
    
        Examples
        --------
        Regress HT against an intercept (1), SEX, and C1:
    
        >>> table1.aggregate(hl.agg.linreg(table1.HT, [1, table1.SEX == 'F', table1.C1]))  # doctest: +SKIP_OUTPUT_CHECK
        Struct(beta=[88.50000000000014, 81.50000000000057, -10.000000000000068],
               standard_error=[14.430869689661844, 59.70552738231206, 7.000000000000016],
               t_stat=[6.132686518775844, 1.365032746099571, -1.428571428571435],
               p_value=[0.10290201427537926, 0.40250974549499974, 0.3888002244284281],
               multiple_standard_error=4.949747468305833,
               multiple_r_squared=0.7175792507204611,
               adjusted_r_squared=0.1527377521613834,
               f_stat=1.2704081632653061,
               multiple_p_value=0.5314327326007864,
               n=4)
    
        Regress blood pressure against an intercept (1), genotype, age, and
        the interaction of genotype and age:
    
        >>> ds_ann = ds.annotate_rows(linreg =
        ...     hl.agg.linreg(ds.pheno.blood_pressure,
        ...                   [1,
        ...                    ds.GT.n_alt_alleles(),
        ...                    ds.pheno.age,
        ...                    ds.GT.n_alt_alleles() * ds.pheno.age]))
    
        Warning
        -------
        As in the example, the intercept covariate ``1`` must be included
        **explicitly** if desired.
    
        Notes
        -----
        In relation to
        `lm.summary <https://stat.ethz.ch/R-manual/R-devel/library/stats/html/summary.lm.html>`__
        in R, ``linreg(y, x = [1, mt.x1, mt.x2])`` computes
        ``summary(lm(y ~ x1 + x2))`` and
        ``linreg(y, x = [mt.x1, mt.x2], nested_dim=0)`` computes
        ``summary(lm(y ~ x1 + x2 - 1))``.
    
        More generally, `nested_dim` defines the number of effects to fit in the
        nested (null) model, with the effects on the remaining covariates fixed
        to zero.
    
        The returned struct has ten fields:
         - `beta` (``.tarray`` of :py``.tfloat64``):
           Estimated regression coefficient for each covariate.
         - `standard_error` (``.tarray`` of :py``.tfloat64``):
           Estimated standard error for each covariate.
         - `t_stat` (``.tarray`` of :py``.tfloat64``):
           t-statistic for each covariate.
         - `p_value` (``.tarray`` of :py``.tfloat64``):
           p-value for each covariate.
         - `multiple_standard_error` (:py``.tfloat64``):
           Estimated standard deviation of the random error.
         - `multiple_r_squared` (:py``.tfloat64``):
           Coefficient of determination for nested models.
         - `adjusted_r_squared` (:py``.tfloat64``):
           Adjusted `multiple_r_squared` taking into account degrees of
           freedom.
         - `f_stat` (:py``.tfloat64``):
           F-statistic for nested models.
         - `multiple_p_value` (:py``.tfloat64``):
           p-value for the
           `F-test <https://en.wikipedia.org/wiki/F-test#Regression_problems>`__ of
           nested models.
         - `n` (:py``.tint64``):
           Number of samples included in the regression. A sample is included if and
           only if `y`, all elements of `x`, and `weight` (if set) are non-missing.
    
        All but the last field are missing if `n` is less than or equal to the
        number of covariates or if the covariates are linearly dependent.
    
        If set, the `weight` parameter generalizes the model to `weighted least
        squares <https://en.wikipedia.org/wiki/Weighted_least_squares>`__, useful
        for heteroscedastic (diagonal but non-constant) variance.
    
        Warning
        -------
        If any weight is negative, the resulting statistics will be ``nan``.
    
        Parameters
        ----------
        y : ``.Float64Expression``
            Response (dependent variable).
        x : ``.Float64Expression`` or ``list`` of ``.Float64Expression``
            Covariates (independent variables).
        nested_dim : ``int``
            The null model includes the first `nested_dim` covariates.
            Must be between 0 and `k` (the length of `x`).
        weight : ``.Float64Expression``, optional
            Non-negative weight for weighted least squares.
    
        Returns
        -------
        ``.StructExpression``
            Struct of regression results.
        """
        x = wrap_to_list(x)
        if len(x) == 0:
            raise ValueError("linreg: must have at least one covariate in `x`")
    
        hl.methods.statgen._warn_if_no_intercept('linreg', x)
    
        if weight is not None:
            sqrt_weight = hl.sqrt(weight)
            y = sqrt_weight * y
            x = [sqrt_weight * xi for xi in x]
    
        k = len(x)
        x = hl.array(x)
    
        res_type = hl.tstruct(
            xty=hl.tarray(hl.tfloat64),
            beta=hl.tarray(hl.tfloat64),
            diag_inv=hl.tarray(hl.tfloat64),
            beta0=hl.tarray(hl.tfloat64),
        )
    
        temp = _agg_func('LinearRegression', [y, x], res_type, [k, hl.int32(nested_dim)])
    
        k0 = nested_dim
        covs_defined = hl.all(lambda cov: hl.is_defined(cov), x)
        tup = hl.agg.filter(covs_defined, hl.tuple([hl.agg.count_where(hl.is_defined(y)), hl.agg.sum(y * y)]))
        n = tup[0]
        yty = tup[1]
    
        def result_from_agg(linreg_res, n, k, k0, yty):
            xty = linreg_res.xty
            beta = linreg_res.beta
            diag_inv = linreg_res.diag_inv
            beta0 = linreg_res.beta0
    
            def dot(a, b):
                return hl.sum(a * b)
    
            d = n - k
            rss = yty - dot(xty, beta)
            rse2 = rss / d  # residual standard error squared
            se = (rse2 * diag_inv) ** 0.5
            t = beta / se
            p = t.map(lambda ti: 2 * hl.pT(-hl.abs(ti), d, True, False))
            rse = hl.sqrt(rse2)
    
            d0 = k - k0
            xty0 = xty[:k0]
            rss0 = yty - dot(xty0, beta0)
            r2 = 1 - rss / rss0
            r2adj = 1 - (1 - r2) * (n - k0) / d
            f = (rss0 - rss) * d / (rss * d0)
            p0 = hl.pF(f, d0, d, False, False)
    
            return hl.struct(
                beta=beta,
                standard_error=se,
                t_stat=t,
                p_value=p,
                multiple_standard_error=rse,
                multiple_r_squared=r2,
                adjusted_r_squared=r2adj,
                f_stat=f,
                multiple_p_value=p0,
                n=n,
            )
    
        global _result_from_linreg_agg_f
        if _result_from_linreg_agg_f is None:
            _result_from_linreg_agg_f = hl.experimental.define_function(
                result_from_agg, res_type, hl.tint64, hl.tint32, hl.tint32, hl.tfloat64, _name="linregResFromAgg"
            )
    
        return _result_from_linreg_agg_f(temp, n, k, k0, yty)
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.corr)@typecheck(x=expr_float64, y=expr_float64)
    def corr(x, y) -> Float64Expression:
        """Computes the
        `Pearson correlation coefficient <https://en.wikipedia.org/wiki/Pearson_correlation_coefficient>`__
        between `x` and `y`.
    
        Examples
        --------
        >>> ds.aggregate_cols(hl.agg.corr(ds.pheno.age, ds.pheno.blood_pressure))  # doctest: +SKIP_OUTPUT_CHECK
        0.16592876044845484
    
        Notes
        -----
        Only records where both `x` and `y` are non-missing will be included in the
        calculation.
    
        In the case that there are no non-missing pairs, the result will be missing.
    
        See Also
        --------
        ``linreg``
    
        Parameters
        ----------
        x : ``.Expression`` of type ``tfloat64``
        y : ``.Expression`` of type ``tfloat64``
    
        Returns
        -------
        ``.Float64Expression``
        """
        return hl.bind(
            lambda x, y: hl.bind(
                lambda a: (a.n * a.xy - a.x * a.y) / hl.sqrt((a.n * a.xsq - a.x**2) * (a.n * a.ysq - a.y**2)),
                hl.agg.filter(
                    hl.is_defined(x) & hl.is_defined(y),
                    hl.struct(
                        x=hl.agg.sum(x),
                        y=hl.agg.sum(y),
                        xsq=hl.agg.sum(x**2),
                        ysq=hl.agg.sum(y**2),
                        xy=hl.agg.sum(x * y),
                        n=hl.agg.count(),
                    ),
                ),
            ),
            x,
            y,
            _ctx=_agg_func.context,
        )
    
    
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.group_by)@typecheck(group=expr_any, agg_expr=agg_expr(expr_any))
    def group_by(group, agg_expr) -> DictExpression:
        """Compute aggregation statistics stratified by one or more groups.
    
        Examples
        --------
        Compute linear regression statistics stratified by SEX:
    
        >>> table1.aggregate(hl.agg.group_by(table1.SEX,
        ...                                  hl.agg.linreg(table1.HT, table1.C1, nested_dim=0)))  # doctest: +SKIP_OUTPUT_CHECK
        {
        'F': Struct(beta=[6.153846153846154],
                    standard_error=[0.7692307692307685],
                    t_stat=[8.000000000000009],
                    p_value=[0.07916684832113098],
                    multiple_standard_error=11.4354374979373,
                    multiple_r_squared=0.9846153846153847,
                    adjusted_r_squared=0.9692307692307693,
                    f_stat=64.00000000000014,
                    multiple_p_value=0.07916684832113098,
                    n=2),
        'M': Struct(beta=[34.25],
                    standard_error=[1.75],
                    t_stat=[19.571428571428573],
                    p_value=[0.03249975499062629],
                    multiple_standard_error=4.949747468305833,
                    multiple_r_squared=0.9973961101073441,
                    adjusted_r_squared=0.9947922202146882,
                    f_stat=383.0408163265306,
                    multiple_p_value=0.03249975499062629,
                    n=2)
        }
    
        Compute call statistics stratified by population group and case status:
    
        >>> ann = ds.annotate_rows(call_stats=hl.agg.group_by(hl.struct(pop=ds.pop, is_case=ds.is_case),
        ...                                                   hl.agg.call_stats(ds.GT, ds.alleles)))
    
        Parameters
        ----------
        group : ``.Expression`` or ``list`` of ``.Expression``
            Group to stratify the result by.
        agg_expr : ``.Expression``
            Aggregation or scan expression to compute per grouping.
    
        Returns
        -------
        ``.DictExpression``
            Dictionary where the keys are `group` and the values are the result of computing
            `agg_expr` for each unique value of `group`.
        """
    
        return _agg_func.group_by(group, agg_expr)
    
    
    
    
    @typecheck(expr=expr_any)
    def _prev_nonnull(expr) -> ArrayExpression:
        wrap = expr.dtype in {tint32, tint64, tfloat32, tfloat64, tbool, tcall}
        if wrap:
            expr = hl.or_missing(hl.is_defined(expr), hl.tuple([expr]))
        r = _agg_func('PrevNonnull', [expr], expr.dtype, [])
        if wrap:
            r = r[0]
        return r
    
    
    
    
    [[docs]](../../../../aggregators.html#hail.expr.aggregators.array_agg)@typecheck(f=func_spec(1, expr_any), array=expr_array())
    def array_agg(f, array):
        """Aggregate an array element-wise using a user-specified aggregation function.
    
        Examples
        --------
        Start with a range table with an array of random boolean values:
    
        >>> ht = hl.utils.range_table(100)
        >>> ht = ht.annotate(arr = hl.range(0, 5).map(lambda _: hl.rand_bool(0.5)))
    
        Aggregate to compute the fraction ``True`` per element:
    
        >>> ht.aggregate(hl.agg.array_agg(lambda element: hl.agg.fraction(element), ht.arr))  # doctest: +SKIP_OUTPUT_CHECK
        [0.54, 0.55, 0.46, 0.52, 0.48]
    
        Notes
        -----
        This function requires that all values of `array` have the same length. If
        two values have different lengths, then an exception will be thrown.
    
        The `f` argument should be a function taking one argument, an expression of
        the element type of `array`, and returning an expression including
        aggregation(s). The type of the aggregated expression returned by
        ``array_agg`` is an array of elements of the return type of `f`.
    
        Parameters
        ----------
        f :
            Aggregation function to apply to each element of the exploded array.
        array : ``.ArrayExpression``
            Array to aggregate.
    
        Returns
        -------
        ``.ArrayExpression``
        """
        return _agg_func.array_agg(array, f)
    
    
    
    
    @typecheck(expr=expr_str)
    def _impute_type(expr):
        ret_type = hl.dtype(
            'struct{anyNonMissing: bool,'
            'allDefined: bool,'
            'supportsBool: bool,'
            'supportsInt32: bool,'
            'supportsInt64: bool,'
            'supportsFloat64: bool}'
        )
    
        return _agg_func('ImputeType', [expr], ret_type, [])
    
    
    class ScanFunctions(object):
        def __init__(self, scope):
            self._functions = {name: self._scan_decorator(f) for name, f in scope.items()}
    
        def _scan_decorator(self, f):
            @wraps(f)
            def wrapper(*args, **kwargs):
                func = getattr(f, '__wrapped__')
                af = func.__globals__['_agg_func']
                as_scan = getattr(af, '_as_scan')
                setattr(af, '_as_scan', True)
                try:
                    res = f(*args, **kwargs)
                except Exception as e:
                    setattr(af, '_as_scan', as_scan)
                    raise e
                setattr(af, '_as_scan', as_scan)
                return res
    
            update_wrapper(wrapper, f)
            return wrapper
    
        def __getattr__(self, field):
            if field in self._functions:
                return self._functions[field]
            else:
                field_matches = difflib.get_close_matches(field, self._functions.keys(), n=5)
                raise AttributeError(
                    "hl.scan.{} does not exist. Did you mean:\n    {}".format(field, "\n    ".join(field_matches))
                )
    
    
    @typecheck(initial_value=expr_any, seq_op=func_spec(1, expr_any), comb_op=func_spec(2, expr_any))
    def fold(initial_value, seq_op, comb_op):
        """
        Perform an arbitrary aggregation in terms of python functions.
    
        Examples
        --------
    
        Start with a range table with its default `idx` field:
    
        >>> ht = hl.utils.range_table(100)
    
        Now, using fold, can reimplement `hl.agg.sum` (for non-missing values) as:
    
        >>> ht.aggregate(hl.agg.fold(0, lambda accum: accum + ht.idx, lambda comb_left, comb_right: comb_left + comb_right))
        4950
    
        Parameters
        ----------
        initial_value : ``.Expression``
            The initial value to start the aggregator with. This is a value of type `A`.
        seq_op : function ( (``.Expression``) -> ``.Expression``)
            The function used to combine the current aggregator state with the next element you're aggregating over. Type is
            `A => A`
        comb_op : function ( (``.Expression``, ``.Expression``) -> ``.Expression``)
            The function used to combine two aggregator states together and produce final result. Type is `(A, A) => A`.
        """
    
        return _agg_func._fold(initial_value, seq_op, comb_op)

---
