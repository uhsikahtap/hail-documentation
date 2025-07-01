## Plot

  
Warning

Plotting functionality is in early stages and is experimental. Interfaces will
change regularly.

Plotting in Hail is easy. Hail’s plot functions utilize Bokeh plotting
libraries to create attractive, interactive figures. Plotting functions in
this module return a Bokeh Figure, so you can call a method to plot your data
and then choose to extend the plot however you like by interacting directly
with Bokeh. See the GWAS tutorial for examples.

Plot functions in Hail accept data in the form of either Python objects or
```Table``` and
```MatrixTable```
fields.

`cdf` | Create a cumulative density plot.  
---|---  
`pdf` |   
`smoothed_pdf` | Create a density plot.  
`histogram` | Create a histogram.  
`cumulative_histogram` | Create a cumulative histogram.  
`histogram2d` | Plot a two-dimensional histogram.  
`scatter` | Create an interactive scatter plot.  
`qq` | Create a Quantile-Quantile plot.  
`manhattan` | Create a Manhattan plot.  
`output_notebook` | Configure the Bokeh output state to generate output in notebook cells when [`bokeh.io.show()`](https://docs.bokeh.org/en/3.1.0/docs/reference/io.html#bokeh.io.show "\(in Bokeh v3.1.0\)") is called.  
`visualize_missingness` | Visualize missingness in a MatrixTable.  
  
hail.plot.cdf(_data_ , _k =350_, _legend =None_, _title =None_, _normalize
=True_, _log =False_)[[source]](_modules/hail/plot/plots.html#cdf)

    

Create a cumulative density plot.

Parameters:

    

  * **data** (```Struct``` or ```Float64Expression```) – Sequence of data to plot.

  * **k** (_int_) – Accuracy parameter (passed to ```approx_cdf()```).

  * **legend** (_str_) – Label of data on the x-axis.

  * **title** (_str_) – Title of the histogram.

  * **normalize** (_bool_) – Whether or not the cumulative data should be normalized.

  * **log** (_bool_) – Whether or not the y-axis should be of type log.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

hail.plot.pdf(_data_ , _k =1000_, _confidence =5_, _legend =None_, _title
=None_, _log =False_, _interactive
=False_)[[source]](_modules/hail/plot/plots.html#pdf)

    

hail.plot.smoothed_pdf(_data_ , _k =350_, _smoothing =0.5_, _legend =None_,
_title =None_, _log =False_, _interactive =False_, _figure
=None_)[[source]](_modules/hail/plot/plots.html#smoothed_pdf)

    

Create a density plot.

Parameters:

    

  * **data** (```Struct``` or ```Float64Expression```) – Sequence of data to plot.

  * **k** (_int_) – Accuracy parameter.

  * **smoothing** (_float_) – Degree of smoothing.

  * **legend** (_str_) – Label of data on the x-axis.

  * **title** (_str_) – Title of the histogram.

  * **log** (_bool_) – Plot the log10 of the bin counts.

  * **interactive** (_bool_) – If True, return a handle to pass to [`bokeh.io.show()`](https://docs.bokeh.org/en/3.1.0/docs/reference/io.html#bokeh.io.show "\(in Bokeh v3.1.0\)").

  * **figure** ([`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure "\(in Bokeh v3.1.0\)")) – If not None, add density plot to figure. Otherwise, create a new figure.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

hail.plot.histogram(_data_ , _range =None_, _bins =50_, _legend =None_, _title
=None_, _log =False_, _interactive
=False_)[[source]](_modules/hail/plot/plots.html#histogram)

    

Create a histogram.

Notes

data can be a
[`Float64Expression`](hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression"), or the result of the
[`hist()`](aggregators.html#hail.expr.aggregators.hist
"hail.expr.aggregators.hist") or
[`approx_cdf()`](aggregators.html#hail.expr.aggregators.approx_cdf
"hail.expr.aggregators.approx_cdf") aggregators.

Parameters:

    

  * **data** (```Struct``` or ```Float64Expression```) – Sequence of data to plot.

  * **range** (_Tuple[float]_) – Range of x values in the histogram.

  * **bins** (_int_) – Number of bins in the histogram.

  * **legend** (_str_) – Label of data on the x-axis.

  * **title** (_str_) – Title of the histogram.

  * **log** (_bool_) – Plot the log10 of the bin counts.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

hail.plot.cumulative_histogram(_data_ , _range =None_, _bins =50_, _legend
=None_, _title =None_, _normalize =True_, _log
=False_)[[source]](_modules/hail/plot/plots.html#cumulative_histogram)

    

Create a cumulative histogram.

Parameters:

    

  * **data** (```Struct``` or ```Float64Expression```) – Sequence of data to plot.

  * **range** (_Tuple[float]_) – Range of x values in the histogram.

  * **bins** (_int_) – Number of bins in the histogram.

  * **legend** (_str_) – Label of data on the x-axis.

  * **title** (_str_) – Title of the histogram.

  * **normalize** (_bool_) – Whether or not the cumulative data should be normalized.

  * **log** (_bool_) – Whether or not the y-axis should be of type log.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

hail.plot.histogram2d(_x_ , _y_ , _bins =40_, _range =None_, _title =None_,
_width =600_, _height =600_, _colors =('#eff3ff', '#c6dbef', '#9ecae1',
'#6baed6', '#4292c6', '#2171b5', '#084594')_, _log
=False_)[[source]](_modules/hail/plot/plots.html#histogram2d)

    

Plot a two-dimensional histogram.

`x` and `y` must both be a
[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") from the same
```Table```.

If `x_range` or `y_range` are not provided, the function will do a pass
through the data to determine min and max of each variable.

Examples

    
    
    >>> ht = hail.utils.range_table(1000).annotate(x=hail.rand_norm(), y=hail.rand_norm())
    >>> p_hist = hail.plot.histogram2d(ht.x, ht.y)
    
    
    
    >>> ht = hail.utils.range_table(1000).annotate(x=hail.rand_norm(), y=hail.rand_norm())
    >>> p_hist = hail.plot.histogram2d(ht.x, ht.y, bins=10, range=((0, 1), None))
    

Parameters:

    

  * **x** (```NumericExpression```) – Expression for x-axis (from a Hail table).

  * **y** (```NumericExpression```) – Expression for y-axis (from the same Hail table as `x`).

  * **bins** (_int or [int, int]_) – The bin specification: \- If int, the number of bins for the two dimensions (nx = ny = bins). \- If [int, int], the number of bins in each dimension (nx, ny = bins). The default value is 40.

  * **range** (_None or ((float, float), (float, float))_) – The leftmost and rightmost edges of the bins along each dimension: ((xmin, xmax), (ymin, ymax)). All values outside of this range will be considered outliers and not tallied in the histogram. If this value is None, or either of the inner lists is None, the range will be computed from the data.

  * **width** (_int_) – Plot width (default 600px).

  * **height** (_int_) – Plot height (default 600px).

  * **title** (_str_) – Title of the plot.

  * **colors** (_Sequence[str]_) – List of colors (hex codes, or strings as described [here](https://bokeh.pydata.org/en/latest/docs/reference/colors.html)). Compatible with one of the many built-in palettes available [here](https://bokeh.pydata.org/en/latest/docs/reference/palettes.html).

  * **log** (_bool_) – Plot the log10 of the bin counts.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

hail.plot.scatter(_x_ , _y_ , _label =None_, _title =None_, _xlabel =None_,
_ylabel =None_, _size =4_, _legend =True_, _hover_fields =None_, _colors
=None_, _width =800_, _height =800_, _collect_all =None_, _n_divisions =500_,
_missing_label ='NA'_)[[source]](_modules/hail/plot/plots.html#scatter)

    

Create an interactive scatter plot.

`x` and `y` must both be either: \- a
[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") from the same
```Table```. \- a tuple (str,
[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) from the same
```Table```. If passed as a tuple the
first element is used as the hover label.

If no label or a single label is provided, then returns
[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)") Otherwise returns a
[`bokeh.models.layouts.Column`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/layouts.html#bokeh.models.Column
"\(in Bokeh v3.1.0\)") containing: \- a
[`bokeh.models.widgets.inputs.Select`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/widgets/inputs.html#bokeh.models.Select
"\(in Bokeh v3.1.0\)") dropdown selection widget for labels \- a
[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)") containing the interactive scatter plot

Points will be colored by one of the labels defined in the `label` using the
color scheme defined in the corresponding entry of `colors` if provided
(otherwise a default scheme is used). To specify your color mapper, check [the
bokeh
documentation](https://bokeh.pydata.org/en/latest/docs/reference/colors.html)
for CategoricalMapper for categorical labels, and for LinearColorMapper and
LogColorMapper for continuous labels. For categorical labels, clicking on one
of the items in the legend will hide/show all points with the corresponding
label. Note that using many different labelling schemes in the same plots,
particularly if those labels contain many different classes could slow down
the plot interactions.

Hovering on points will display their coordinates, labels and any additional
fields specified in `hover_fields`.

Parameters:

    

  * **x** (```NumericExpression``` or (str, ```NumericExpression```)) – List of x-values to be plotted.

  * **y** (```NumericExpression``` or (str, ```NumericExpression```)) – List of y-values to be plotted.

  * **label** (```Expression``` or Dict``str, [`Expression```]], optional) – Either a single expression (if a single label is desired), or a dictionary of label name -> label value for x and y values. Used to color each point w.r.t its label. When multiple labels are given, a dropdown will be displayed with the different options. Can be used with categorical or continuous expressions.

  * **title** (_str, optional_) – Title of the scatterplot.

  * **xlabel** (_str, optional_) – X-axis label.

  * **ylabel** (_str, optional_) – Y-axis label.

  * **size** (_int_) – Size of markers in screen space units.

  * **legend** (_bool_) – Whether or not to show the legend in the resulting figure.

  * **hover_fields** (Dict``str, [`Expression```], optional) – Extra fields to be displayed when hovering over a point on the plot.

  * **colors** ([`bokeh.models.mappers.ColorMapper`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/mappers.html#bokeh.models.ColorMapper "\(in Bokeh v3.1.0\)") or Dict[str, [`bokeh.models.mappers.ColorMapper`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/mappers.html#bokeh.models.ColorMapper "\(in Bokeh v3.1.0\)")], optional) – If a single label is used, then this can be a color mapper, if multiple labels are used, then this should be a Dict of label name -> color mapper. Used to set colors for the labels defined using `label`. If not used at all, or label names not appearing in this dict will be colored using a default color scheme.

  * **width** (_int_) – Plot width

  * **height** (_int_) – Plot height

  * **collect_all** (_bool, optional_) – Deprecated. Use n_divisions instead.

  * **n_divisions** (_int, optional_) – Factor by which to downsample (default value = 500). A lower input results in fewer output datapoints. Use None to collect all points.

  * **missing_label** (_str_) – Label to use when a point is missing data for a categorical label

Returns:

    

[`bokeh.models.Plot`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/plots.html#bokeh.models.Plot
"\(in Bokeh v3.1.0\)") if no label or a single label was given, otherwise
[`bokeh.models.layouts.Column`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/layouts.html#bokeh.models.Column
"\(in Bokeh v3.1.0\)")

hail.plot.qq(_pvals_ , _label =None_, _title ='Q-Q plot'_, _xlabel ='Expected
-log10(p)'_, _ylabel ='Observed -log10(p)'_, _size =6_, _legend =True_,
_hover_fields =None_, _colors =None_, _width =800_, _height =800_,
_collect_all =None_, _n_divisions =500_, _missing_label
='NA'_)[[source]](_modules/hail/plot/plots.html#qq)

    

Create a Quantile-Quantile plot. (<https://en.wikipedia.org/wiki/Q-Q_plot>)

If no label or a single label is provided, then returns
[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)") Otherwise returns a
[`bokeh.models.layouts.Column`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/layouts.html#bokeh.models.Column
"\(in Bokeh v3.1.0\)") containing: \- a
[`bokeh.models.widgets.inputs.Select`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/widgets/inputs.html#bokeh.models.Select
"\(in Bokeh v3.1.0\)") dropdown selection widget for labels \- a
[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)") containing the interactive qq plot

Points will be colored by one of the labels defined in the `label` using the
color scheme defined in the corresponding entry of `colors` if provided
(otherwise a default scheme is used). To specify your color mapper, check [the
bokeh
documentation](https://bokeh.pydata.org/en/latest/docs/reference/colors.html)
for CategoricalMapper for categorical labels, and for LinearColorMapper and
LogColorMapper for continuous labels. For categorical labels, clicking on one
of the items in the legend will hide/show all points with the corresponding
label. Note that using many different labelling schemes in the same plots,
particularly if those labels contain many different classes could slow down
the plot interactions.

Hovering on points will display their coordinates, labels and any additional
fields specified in `hover_fields`.

Parameters:

    

  * **pvals** (```NumericExpression```) – List of x-values to be plotted.

  * **label** (```Expression``` or Dict``str, [`Expression```]]) – Either a single expression (if a single label is desired), or a dictionary of label name -> label value for x and y values. Used to color each point w.r.t its label. When multiple labels are given, a dropdown will be displayed with the different options. Can be used with categorical or continuous expressions.

  * **title** (_str, optional_) – Title of the scatterplot.

  * **xlabel** (_str, optional_) – X-axis label.

  * **ylabel** (_str, optional_) – Y-axis label.

  * **size** (_int_) – Size of markers in screen space units.

  * **legend** (_bool_) – Whether or not to show the legend in the resulting figure.

  * **hover_fields** (Dict``str, [`Expression```], optional) – Extra fields to be displayed when hovering over a point on the plot.

  * **colors** ([`bokeh.models.mappers.ColorMapper`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/mappers.html#bokeh.models.ColorMapper "\(in Bokeh v3.1.0\)") or Dict[str, [`bokeh.models.mappers.ColorMapper`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/mappers.html#bokeh.models.ColorMapper "\(in Bokeh v3.1.0\)")], optional) – If a single label is used, then this can be a color mapper, if multiple labels are used, then this should be a Dict of label name -> color mapper. Used to set colors for the labels defined using `label`. If not used at all, or label names not appearing in this dict will be colored using a default color scheme.

  * **width** (_int_) – Plot width

  * **height** (_int_) – Plot height

  * **collect_all** (_bool_) – Deprecated. Use n_divisions instead.

  * **n_divisions** (_int, optional_) – Factor by which to downsample (default value = 500). A lower input results in fewer output datapoints. Use None to collect all points.

  * **missing_label** (_str_) – Label to use when a point is missing data for a categorical label

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)") if no label or a single label was given, otherwise
[`bokeh.models.layouts.Column`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/layouts.html#bokeh.models.Column
"\(in Bokeh v3.1.0\)")

hail.plot.manhattan(_pvals_ , _locus =None_, _title =None_, _size =4_,
_hover_fields =None_, _collect_all =None_, _n_divisions =500_,
_significance_line
=5e-08_)[[source]](_modules/hail/plot/plots.html#manhattan)

    

Create a Manhattan plot. (<https://en.wikipedia.org/wiki/Manhattan_plot>)

Parameters:

    

  * **pvals** (```Float64Expression```) – P-values to be plotted.

  * **locus** (```LocusExpression```, optional) – Locus values to be plotted.

  * **title** (_str, optional_) – Title of the plot.

  * **size** (_int_) – Size of markers in screen space units.

  * **hover_fields** (Dict``str, [`Expression```], optional) – Dictionary of field names and values to be shown in the HoverTool of the plot.

  * **collect_all** (_bool, optional_) – Deprecated - use n_divisions instead.

  * **n_divisions** (_int, optional._) – Factor by which to downsample (default value = 500). A lower input results in fewer output datapoints. Use None to collect all points.

  * **significance_line** (_float, optional_) – p-value at which to add a horizontal, dotted red line indicating genome-wide significance. If `None`, no line is added.

Returns:

    

[`bokeh.models.Plot`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/plots.html#bokeh.models.Plot
"\(in Bokeh v3.1.0\)")

hail.plot.output_notebook()[[source]](_modules/hail/plot/plots.html#output_notebook)

    

Configure the Bokeh output state to generate output in notebook cells when
[`bokeh.io.show()`](https://docs.bokeh.org/en/3.1.0/docs/reference/io.html#bokeh.io.show
"\(in Bokeh v3.1.0\)") is called. Calls
[`bokeh.io.output_notebook()`](https://docs.bokeh.org/en/3.1.0/docs/reference/io.html#bokeh.io.output_notebook
"\(in Bokeh v3.1.0\)").

hail.plot.visualize_missingness(_entry_field_ , _row_field =None_,
_column_field =None_, _window =6000000_, _plot_width =1800_, _plot_height
=900_)[[source]](_modules/hail/plot/plots.html#visualize_missingness)

    

Visualize missingness in a MatrixTable.

Inspired by
[naniar](https://cran.r-project.org/web/packages/naniar/index.html).

Row field is windowed by default, and missingness is aggregated over this
window to generate a proportion defined. This windowing is set to 6,000,000 by
default, so that the human genome is divided into ~500 rows. With ~2,000
columns, this function returns a sensibly-sized plot with this windowing.

Warning

Generating a plot with more than ~1M points takes a long time for Bokeh to
render. Consider windowing carefully.

Parameters:

    

  * **entry_field** (```Expression```) – Field for which to check missingness.

  * **row_field** (```NumericExpression``` or ```LocusExpression```) – Row field to use for y-axis (can be windowed). If not provided, the row key will be used.

  * **column_field** (```StringExpression```) – Column field to use for x-axis. If not provided, the column key will be used.

  * **window** (_int, optional_) – Size of window to summarize by `row_field`. If set to None, each field will be used individually.

  * **plot_width** (_int_) – Plot width in px.

  * **plot_height** (_int_) – Plot height in px.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

---

## Plotting With hail.ggplot Overview


Warning

Plotting functionality is in early stages and is experimental.

The `hl.ggplot` module is designed based on R’s tidyverse `ggplot2` library.
This module provides a subset of `ggplot2`’s functionality to allow users to
generate plots in much the same way they would in `ggplot2`.

This module is intended to be a new, more flexible way of plotting compared to
the `hl.plot` module. This module currently uses plotly to generate plots, as
opposed to `hl.plot`, which uses bokeh.

Core functions

`ggplot` | Create the initial plot object.  
---|---  
`aes` | Create an aesthetic mapping  
`coord_cartesian` | Set the boundaries of the plot.  
  
hail.ggplot.ggplot(_table_ , _mapping
={}_)[[source]](../_modules/hail/ggplot/ggplot.html#ggplot)

    

Create the initial plot object.

This function is the beginning of all plots using the `hail.ggplot` interface.
Plots are constructed by calling this function, then adding attributes to the
plot to get the desired result.

Examples

Create a y = x^2 scatter plot

    
    
    >>> ht = hl.utils.range_table(10)
    >>> ht = ht.annotate(squared = ht.idx**2)
    >>> my_plot = hl.ggplot.ggplot(ht, hl.ggplot.aes(x=ht.idx, y=ht.squared)) + hl.ggplot.geom_point()
    

Parameters:

    

  * **table** – The table containing the data to plot.

  * **mapping** – Default list of aesthetic mappings from table data to plot attributes.

Returns:

    

`GGPlot`

hail.ggplot.aes(_** kwargs_)[[source]](../_modules/hail/ggplot/aes.html#aes)

    

Create an aesthetic mapping

Parameters:

    

**kwargs** – Map aesthetic names to hail expressions based on table’s plot.

Returns:

    

`Aesthetic` – The aesthetic mapping to be applied.

hail.ggplot.coord_cartesian(_xlim =None_, _ylim
=None_)[[source]](../_modules/hail/ggplot/coord_cartesian.html#coord_cartesian)

    

Set the boundaries of the plot.

Parameters:

    

  * **xlim** ([`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python v3.9\)") with two int) – The minimum and maximum x value to show on the plot.

  * **ylim** ([`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python v3.9\)") with two int) – The minimum and maximum y value to show on the plot.

Returns:

    

`FigureAttribute` – The coordinate attribute to be applied.

Geoms

`geom_point` | Create a scatter plot.  
---|---  
`geom_line` | Create a line plot.  
`geom_text` | Create a scatter plot where each point is text from the `text` aesthetic.  
`geom_bar` | Create a bar chart that counts occurrences of the various values of the `x` aesthetic.  
`geom_col` | Create a bar chart that uses bar heights specified in y aesthetic.  
`geom_histogram` | Creates a histogram.  
`geom_density` | Creates a smoothed density plot.  
`geom_hline` | Plots a horizontal line at `yintercept`.  
`geom_vline` | Plots a vertical line at `xintercept`.  
`geom_area` | Creates a line plot with the area between the line and the x-axis filled in.  
`geom_ribbon` | Creates filled in area between two lines specified by x, ymin, and ymax  
  
hail.ggplot.geom_point(_mapping ={}_, _*_ , _color =None_, _size =None_,
_alpha =None_, _shape
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_point)

    

Create a scatter plot.

Supported aesthetics: `x`, `y`, `color`, `alpha`, `tooltip`, `shape`

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_line(_mapping ={}_, _*_ , _color =None_, _size =None_, _alpha
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_line)

    

Create a line plot.

Supported aesthetics: `x`, `y`, `color`, `tooltip`

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_text(_mapping ={}_, _*_ , _color =None_, _size =None_, _alpha
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_text)

    

Create a scatter plot where each point is text from the `text` aesthetic.

Supported aesthetics: `x`, `y`, `label`, `color`, `tooltip`

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_bar(_mapping ={}_, _*_ , _fill =None_, _color =None_, _alpha
=None_, _position ='stack'_, _size
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_bar)

    

Create a bar chart that counts occurrences of the various values of the `x`
aesthetic.

Supported aesthetics: `x`, `color`, `fill`, `weight`

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_col(_mapping ={}_, _*_ , _fill =None_, _color =None_, _alpha
=None_, _position ='stack'_, _size
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_col)

    

Create a bar chart that uses bar heights specified in y aesthetic.

Supported aesthetics: `x`, `y`, `color`, `fill`

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_histogram(_mapping ={}_, _*_ , _min_val =None_, _max_val
=None_, _bins =None_, _fill =None_, _color =None_, _alpha =None_, _position
='stack'_, _size
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_histogram)

    

Creates a histogram.

Note: this function currently does not support same interface as R’s ggplot.

Supported aesthetics: `x`, `color`, `fill`

Parameters:

    

  * **mapping** (`Aesthetic`) – Any aesthetics specific to this geom.

  * **min_val** (int or float) – Minimum value to include in histogram

  * **max_val** (int or float) – Maximum value to include in histogram

  * **bins** (int) – Number of bins to plot. 30 by default.

  * **fill** – A single fill color for all bars of histogram, overrides `fill` aesthetic.

  * **color** – A single outline color for all bars of histogram, overrides `color` aesthetic.

  * **alpha** (float) – A measure of transparency between 0 and 1.

  * **position** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Tells how to deal with different groups of data at same point. Options are “stack” and “dodge”.

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_density(_mapping ={}_, _*_ , _k =1000_, _smoothing =0.5_,
_fill =None_, _color =None_, _alpha =None_, _smoothed
=False_)[[source]](../_modules/hail/ggplot/geoms.html#geom_density)

    

Creates a smoothed density plot.

This method uses the hl.agg.approx_cdf aggregator to compute a sketch of the
distribution of the values of x. It then uses an ad hoc method to estimate a
smoothed pdf consistent with that cdf.

Note: this function currently does not support same interface as R’s ggplot.

Supported aesthetics: `x`, `color`, `fill`

Parameters:

    

  * **mapping** (`Aesthetic`) – Any aesthetics specific to this geom.

  * **k** (int) – Passed to the approx_cdf aggregator. The size of the aggregator scales linearly with k. The default value of 1000 is likely sufficient for most uses.

  * **smoothing** (float) – Controls the amount of smoothing applied.

  * **fill** – A single fill color for all density plots, overrides `fill` aesthetic.

  * **color** – A single line color for all density plots, overrides `color` aesthetic.

  * **alpha** (float) – A measure of transparency between 0 and 1.

  * **smoothed** (boolean) – If true, attempts to fit a smooth kernel density estimator. If false, uses a custom method do generate a variable width histogram directly from the approx_cdf results.

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_hline(_yintercept_ , _*_ , _linetype ='solid'_, _color
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_hline)

    

Plots a horizontal line at `yintercept`.

Parameters:

    

  * **yintercept** ([`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Location to draw line.

  * **linetype** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Type of line to draw. Choose from “solid”, “dashed”, “dotted”, “longdash”, “dotdash”.

  * **color** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Color of line to draw, black by default.

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_vline(_xintercept_ , _*_ , _linetype ='solid'_, _color
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_vline)

    

Plots a vertical line at `xintercept`.

Parameters:

    

  * **xintercept** ([`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Location to draw line.

  * **linetype** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Type of line to draw. Choose from “solid”, “dashed”, “dotted”, “longdash”, “dotdash”.

  * **color** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Color of line to draw, black by default.

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_area(_mapping ={}_, _fill =None_, _color
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_area)

    

Creates a line plot with the area between the line and the x-axis filled in.

Supported aesthetics: `x`, `y`, `fill`, `color`, `tooltip`

Parameters:

    

  * **mapping** (`Aesthetic`) – Any aesthetics specific to this geom.

  * **fill** – Color of fill to draw, black by default. Overrides `fill` aesthetic.

  * **color** – Color of line to draw outlining non x-axis facing side, none by default. Overrides `color` aesthetic.

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_ribbon(_mapping ={}_, _fill =None_, _color
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_ribbon)

    

Creates filled in area between two lines specified by x, ymin, and ymax

Supported aesthetics: `x`, `ymin`, `ymax`, `color`, `fill`, `tooltip`

Parameters:

    

  * **mapping** (`Aesthetic`) – Any aesthetics specific to this geom.

  * **fill** – Color of fill to draw, black by default. Overrides `fill` aesthetic.

  * **color** – Color of line to draw outlining both side, none by default. Overrides `color` aesthetic.

  * _return:_

  * **``FigureAttribute``** – The geom to be applied.

Scales

`scale_x_continuous` | The default continuous x scale.  
---|---  
`scale_x_discrete` | The default discrete x scale.  
`scale_x_genomic` | The default genomic x scale.  
`scale_x_log10` | Transforms x axis to be log base 10 scaled.  
`scale_x_reverse` | Transforms x-axis to be vertically reversed.  
`scale_y_continuous` | The default continuous y scale.  
`scale_y_discrete` | The default discrete y scale.  
`scale_y_log10` | Transforms y-axis to be log base 10 scaled.  
`scale_y_reverse` | Transforms y-axis to be vertically reversed.  
`scale_color_continuous` | The default continuous color scale.  
`scale_color_discrete` | The default discrete color scale.  
`scale_color_hue` | Map discrete colors to evenly placed positions around the color wheel.  
`scale_color_manual` | A color scale that assigns strings to colors using the pool of colors specified as values.  
`scale_color_identity` | A color scale that assumes the expression specified in the `color` aesthetic can be used as a color.  
`scale_fill_continuous` | The default continuous fill scale.  
`scale_fill_discrete` | The default discrete fill scale.  
`scale_fill_hue` | Map discrete fill colors to evenly placed positions around the color wheel.  
`scale_fill_manual` | A color scale that assigns strings to fill colors using the pool of colors specified as values.  
`scale_fill_identity` | A color scale that assumes the expression specified in the `fill` aesthetic can be used as a fill color.  
  
hail.ggplot.scale_x_continuous(_name =None_, _breaks =None_, _labels =None_,
_trans
='identity'_)[[source]](../_modules/hail/ggplot/scale.html#scale_x_continuous)

    

The default continuous x scale.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The label to show on x-axis

  * **breaks** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – The locations to draw ticks on the x-axis.

  * **labels** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The labels of the ticks on the axis.

  * **trans** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The transformation to apply to the x-axis. Supports “identity”, “reverse”, “log10”.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_x_discrete(_name =None_, _breaks =None_, _labels
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_x_discrete)

    

The default discrete x scale.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The label to show on x-axis

  * **breaks** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The locations to draw ticks on the x-axis.

  * **labels** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The labels of the ticks on the axis.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_x_genomic(_reference_genome_ , _name
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_x_genomic)

    

The default genomic x scale. This is used when the `x` aesthetic corresponds
to a
[`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression").

Parameters:

    

  * **reference_genome** – The reference genome being used.

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The label to show on y-axis

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_x_log10(_name
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_x_log10)

    

Transforms x axis to be log base 10 scaled.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The label to show on x-axis

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_x_reverse(_name
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_x_reverse)

    

Transforms x-axis to be vertically reversed.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The label to show on x-axis

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_y_continuous(_name =None_, _breaks =None_, _labels =None_,
_trans
='identity'_)[[source]](../_modules/hail/ggplot/scale.html#scale_y_continuous)

    

The default continuous y scale.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The label to show on y-axis

  * **breaks** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – The locations to draw ticks on the y-axis.

  * **labels** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The labels of the ticks on the axis.

  * **trans** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The transformation to apply to the y-axis. Supports “identity”, “reverse”, “log10”.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_y_discrete(_name =None_, _breaks =None_, _labels
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_y_discrete)

    

The default discrete y scale.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The label to show on y-axis

  * **breaks** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The locations to draw ticks on the y-axis.

  * **labels** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The labels of the ticks on the axis.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_y_log10(_name
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_y_log10)

    

Transforms y-axis to be log base 10 scaled.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The label to show on y-axis

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_y_reverse(_name
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_y_reverse)

    

Transforms y-axis to be vertically reversed.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The label to show on y-axis

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_color_continuous()[[source]](../_modules/hail/ggplot/scale.html#scale_color_continuous)

    

The default continuous color scale. This linearly interpolates colors between
the min and max observed values.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_color_discrete()[[source]](../_modules/hail/ggplot/scale.html#scale_color_discrete)

    

The default discrete color scale. This maps each discrete value to a color.
Equivalent to scale_color_hue.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_color_hue()[[source]](../_modules/hail/ggplot/scale.html#scale_color_hue)

    

Map discrete colors to evenly placed positions around the color wheel.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_color_manual(_*_ ,
_values_)[[source]](../_modules/hail/ggplot/scale.html#scale_color_manual)

    

A color scale that assigns strings to colors using the pool of colors
specified as values.

Parameters:

    

**values** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list
"\(in Python v3.9\)") of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – The colors to choose when assigning values to colors.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_color_identity()[[source]](../_modules/hail/ggplot/scale.html#scale_color_identity)

    

A color scale that assumes the expression specified in the `color` aesthetic
can be used as a color.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_fill_continuous()[[source]](../_modules/hail/ggplot/scale.html#scale_fill_continuous)

    

The default continuous fill scale. This linearly interpolates colors between
the min and max observed values.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_fill_discrete()[[source]](../_modules/hail/ggplot/scale.html#scale_fill_discrete)

    

The default discrete fill scale. This maps each discrete value to a fill
color.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_fill_hue()[[source]](../_modules/hail/ggplot/scale.html#scale_fill_hue)

    

Map discrete fill colors to evenly placed positions around the color wheel.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_fill_manual(_*_ ,
_values_)[[source]](../_modules/hail/ggplot/scale.html#scale_fill_manual)

    

A color scale that assigns strings to fill colors using the pool of colors
specified as values.

Parameters:

    

**values** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list
"\(in Python v3.9\)") of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – The colors to choose when assigning values to colors.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_fill_identity()[[source]](../_modules/hail/ggplot/scale.html#scale_fill_identity)

    

A color scale that assumes the expression specified in the `fill` aesthetic
can be used as a fill color.

Returns:

    

`FigureAttribute` – The scale to be applied.

Facets

`facet_wrap` | Introduce a one dimensional faceting on specified fields.  
---|---  
`vars` | 

Parameters:

    ***args** (```hail.expr.Expression```) -- Fields to facet by.  
  
hail.ggplot.facet_wrap(_facets_ , _*_ , _nrow =None_, _ncol =None_, _scales
='fixed'_)[[source]](../_modules/hail/ggplot/facets.html#facet_wrap)

    

Introduce a one dimensional faceting on specified fields.

Parameters:

    

  * **facets** (```hail.expr.StructExpression``` created by hl.ggplot.vars function.) – The fields to facet on.

  * **nrow** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – The number of rows into which the facets will be spread. Will be ignored if ncol is set.

  * **ncol** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – The number of columns into which the facets will be spread.

  * **scales** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Whether the scales are the same across facets. For more information and a list of supported options, see [the ggplot documentation](https://ggplot2-book.org/facet.html#controlling-scales).

Returns:

    

`FigureAttribute` – The faceter.

hail.ggplot.vars(_*
args_)[[source]](../_modules/hail/ggplot/facets.html#vars)

    

Parameters:

    

***args**
([`hail.expr.Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Fields to facet by.

Returns:

    

[`hail.expr.StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A struct to pass to a faceter.

Labels

`xlab`(label) | Sets the x-axis label of a plot.  
---|---  
`ylab`(label) | Sets the y-axis label of a plot.  
`ggtitle`(label) | Sets the title of a plot.  
  
hail.ggplot.xlab(_label_)[[source]](../_modules/hail/ggplot/labels.html#xlab)

    

Sets the x-axis label of a plot.

Parameters:

    

**label** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The desired x-axis label of the plot.

Returns:

    

`FigureAttribute` – Label object to change the x-axis label.

hail.ggplot.ylab(_label_)[[source]](../_modules/hail/ggplot/labels.html#ylab)

    

Sets the y-axis label of a plot.

Parameters:

    

**label** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The desired y-axis label of the plot.

Returns:

    

`FigureAttribute` – Label object to change the y-axis label.

hail.ggplot.ggtitle(_label_)[[source]](../_modules/hail/ggplot/labels.html#ggtitle)

    

Sets the title of a plot.

Parameters:

    

**label** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The desired title of the plot.

Returns:

    

`FigureAttribute` – Label object to change the title.

Classes

_class _hail.ggplot.GGPlot(_ht_ , _aes_ , _geoms=[]_ , _labels=
<hail.ggplot.labels.Labels object>_, _coord_cartesian=None_ , _scales=None_ ,
_facet=None_)[[source]](../_modules/hail/ggplot/ggplot.html#GGPlot)

    

The class representing a figure created using the `hail.ggplot` module.

Create one by using `ggplot()`.

to_plotly()[[source]](../_modules/hail/ggplot/ggplot.html#GGPlot.to_plotly)

    

Turn the hail plot into a Plotly plot.

Returns:

    

_A Plotly figure that can be updated with plotly methods._

show()[[source]](../_modules/hail/ggplot/ggplot.html#GGPlot.show)

    

Render and show the plot, either in a browser or notebook.

write_image(_path_)[[source]](../_modules/hail/ggplot/ggplot.html#GGPlot.write_image)

    

Write out this plot as an image.

This requires you to have installed the python package kaleido from pypi.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The path to write the file to.

_class
_hail.ggplot.Aesthetic(_properties_)[[source]](../_modules/hail/ggplot/aes.html#Aesthetic)

    

_class
_hail.ggplot.FigureAttribute[[source]](../_modules/hail/ggplot/geoms.html#FigureAttribute)

---

