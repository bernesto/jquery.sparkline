Documentation
-------------

### Quick Start

To add some sparklines to your web page you need four things:

1.  The [jQuery javascript library](http://www.jquery.com/) loaded into the page - At least version 1.4.3 or higher
2.  A copy of jquery.sparkline.js loaded into the page which you can download from this site
3.  An inline tag on the page within which to display the sparkline (eg. <span>)
4.  A call to the sparkline() function to actually display the sparkline.

Additionally rendering the page in standards mode (see the DOCTYPE declaration in the example below) maximizes compatibilty with Internet Explorer.

Here's a simple web page that will display some sparklines:
```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" 
    "http://www.w3.org/TR/html4/strict.dtd">
<head>

    **<script type\="text/javascript" src\="jquery-1.7.2.js"\></script>
    <script type\="text/javascript" src\="jquery.sparkline.js"\></script>**

    <script type\="text/javascript"\>
    $(function() {
        /\*\* This code runs when everything has been loaded on the page \*/
        /\* Inline sparklines take their values from the contents of the tag \*/
        **$('.inlinesparkline').sparkline();** /\* Sparklines can also take their values from the first argument 
        passed to the sparkline() function \*/
        **var myvalues \= \[10,8,5,7,4,4,1\];
        $('.dynamicsparkline').sparkline(myvalues);**

        /\* The second argument gives options such as chart type \*/
        **$('.dynamicbar').sparkline(myvalues, {type: 'bar', barColor: 'green'} );**

        /\* Use 'html' instead of an array of values to pass options 
        to a sparkline with data in the tag \*/
        **$('.inlinebar').sparkline('html', {type: 'bar', barColor: 'red'} );**
    });
    </script>
</head>
<body>

<p>
Inline Sparkline: **<span class\="inlinesparkline"\>1,4,4,7,5,9,10</span>**.
</p>
<p>
Sparkline with dynamic data: **<span class\="dynamicsparkline"\>Loading..</span>**
</p>
<p>
Bar chart with dynamic data: **<span class\="dynamicbar"\>Loading..</span>**
</p>
<p>
Bar chart with inline data: **<span class\="inlinebar"\>1,3,4,5,3,5</span>**
</p>

</body>
</html>
```
[Click here to open this example in a new window](examples/simple.html).

As the example shows, the values to be used in a sparkline can either be supplied inline, inside the tag to be used as the target or can be passed as the first parameter to the sparkline() function.

To draw different types of charts, or overlay one on top of another, or display charts into hidden layers, read on.

### Syntax

$(selector).sparkline(values, options);

Values can either be an array of numbers or "html" which causes the values to be read from from the selected tag:

<span class\="sparklines"\>1,2,3,4,5,4,3,2,1</span>
<span id\="ticker"">Loading..</span>

<script type\="text/javascript"\>
$('.sparklines').sparkline('html');
$('#ticker').sparkline(\[1,2,3,4,5,4,3,2,1\]);
</script>

Values supplied in the tag can also appear inside a comment, or as an attribute of the tag itself:
```
<span class\="sparklines"\><!-- 1,2,3,4,5,4,3,2,1 --></span>
<span class\="sparklines" values\="1,2,3,4,5,4,3,2,1"\></span>
```
By default the plugin will look for an attribute called "values" on the tag to find values to render, but you can change this by setting the [tagValuesAttribute option](#common). This can be useful if you want to create a composite graph as you can use different names for the values attribute for each graph.

Options is an object that specifies the type of sparkline to draw, colours to use, etc.
```
$('#barchart').sparkline(myvalues, { type:'bar', barColor:'green' });
```
If necessary, options can be passed as attributes on each tag. This requires setting the [enableTagOptions option](#common) when calling the sparklines() function and reduces performance somewhat (more critical on IE6)
```
<span class\="sparklines" sparkType\="bar" sparkBarColor\="green"\><!-- 1,2,3,4,3,2,1 --></span>
<span class\="sparklines" sparkType\="bar" sparkBarColor\="red"\><!-- 1,2,3,4,3,2,1 --></span>

<script type\="text/javascript"\>
$('.sparklines').sparkline('html', { enableTagOptions: true });
</script>
```
Each option must be prefixed with "spark", though this can be changed by passing a [tagOptionPrefix option](#common) to the sparkline() function

You can also override the default options for all subsequent sparklines by assigning values to $.fn.sparkline.defaults

Eg. to change the default line color as listed in the common options below, you can do:

$.fn.sparkline.defaults.common.lineColor \= 'red';

Replace 'common' with 'line', 'bar', 'tristate', 'discrete', 'bullet', 'pie' or 'box' to set options specific to those chart types.

### Common Options

These options can be set for most of the supported chart types.

**type**

One of 'line' (default), 'bar', 'tristate', 'discrete', 'bullet', 'pie' or 'box'

**width**

Width of the chart - Defaults to 'auto' - May be any valid css width - 1.5em, 20px, etc (using a number without a unit specifier won't do what you want) - This option does nothing for bar and tristate chars (see barWidth)

**height**

Height of the chart - Defaults to 'auto' (line height of the containing tag)

**lineColor**

Used by line and discrete charts to specify the colour of the line drawn as a CSS values string

**fillColor**

Specify the colour used to fill the area under the graph as a CSS value. Set to false to disable fill

**chartRangeMin**

Specify the minimum value to use for the range of Y values of the chart - Defaults to the minimum value supplied

**chartRangeMax**

Specify the maximum value to use for the range of Y values of the chart - Defaults to the maximum value supplied

**composite**

If true then don't erase any existing chart attached to the tag, but draw another chart over the top - Note that width and height are ignored if an existing chart is detected.  
**Note:** You'll usually want to lock the axis on both charts using chartRangeMin and chartRangeMax if you want the same value on each chart to occupy the same point.

**enableTagOptions**

If true then options can be specified as attributes on each tag to be transformed into a sparkline, as well as passed to the sparkline() function. See also tagOptionPrefix

**tagOptionPrefix**

String that each option passed as an attribute on a tag must begin with. Defaults to 'spark'

**tagValuesAttribute**

The name of the tag attribute to fetch values from, if present - Defaults to 'values'

**disableHiddenCheck**

Set to true to disable checking for hidden sparklines. This can be beneficial if you know you'll never attempt to draw a sparkline into a hidden parent element as it avoids a browser reflow for the test, increasing rendering performance.  
Defaults to false - _New in 2.0_

#### Range Maps

Several parameters, such as colorMap and tooltipLookupValues accept a range map as a paramter. As the name suggest, it maps ranges of numbers to values. For example:
```
    var range\_map \= $.range\_map({
        1: 'red',
        '2:9': 'yellow',
        '10:': 'red'
    })
```
This would map 1 to red, values of 2 through 9 (inclusive) to yellow and values of 10 and higher to 'red'

### Hidden Sparklines

If you try to display a sparkline in a tag that's currently not visible (ie. the tag or one of its parents are set to display:none) then you'll find that the sparkline hasn't been rendered when you finally do make the tag visible. This is because a tag with display:none has no size, and thus a canvas can't be created and sized for the sparkline to be rendered to.

The solution is to call the **$.sparkline\_display\_visible()** function anytime a sparkline may have become visible so that it can be correctly rendered. This is the technique this site uses to handle the sparklines that are hidden in the different tabbed sections; the site calls the routine in the tab-changed callback.

Note that as of version 2.0, support for hidden sparklines can be disabled by supplying the _disabledHiddenCheck_ option. This can result in a performance increase in some cases.

### Line Charts

Line charts are the default chart type, but to specify the type explicitly set an option called "type" to "line".

**defaultPixelsPerValue**

Defaults to 3 pixels of width for each value in the chart

**spotColor**

The CSS colour of the final value marker. Set to false or an empty string to hide it

**minSpotColor**

The CSS colour of the marker displayed for the mimum value. Set to false or an empty string to hide it

**maxSpotColor**

The CSS colour of the marker displayed for the maximum value. Set to false or an empty string to hide it

**spotRadius**

Radius of all spot markers, In pixels (default: 1.5) - Integer

**valueSpots**

Specifies which points to draw spots on, and with which colour. Accepts a range. For example, to render green spots on all values less than 50 and red on values higher use `{':49': 'green, '50:': 'red'}`  
\- _New in 2.0_

**highlightSpotColor**

Specifies a colour for the spot that appears on a value when moused over. Set to null to disable.  
Defaults to #f5f - _New in 2.0_

**highlightLineColor**

Specifies a colour for the vertical line that appears through a value when moused over. Set to null to disable.  
Defaults to #f22 - _New in 2.0_

**lineWidth**

In pixels (default: 1) - Integer

**normalRangeMin, normalRangeMax**

Specify threshold values between which to draw a bar to denote the "normal" or expected range of values. For example the green bar heremight denote a normal operating temperature range

**drawNormalOnTop**

By default the normal range is drawn behind the fill area of the chart. Setting this option to true causes it to be drawn over the top of the fill area

**xvalues**

See below

**chartRangeClip**

If true then the y values supplied to plot will be clipped to fall between chartRangeMin and chartRangeMax - By default chartRangeMin/Max just ensure that the chart spans at least that range of values, but does not constrain it

**chartRangeMinX**

Specifies the minimum value to use for the X value of the chart

**chartRangeMaxX**

Specifies the maximum value to use for the X value of the chart

See also all of the [common options](#common) above, that can also be used with line charts

By default the values supplied to line charts are presumed to be y values mapping on to sequential (integer) x values. If you need to specify both the x and y values for your chart, you have a few options:

1.  **Inline:** x and y values are separated by a colon: x:y,x:y,x:y - eg. <span class="linechart">1:3,2.7:4,4.8:3</span>
2.  **Array of arrays:** An array of \[x,y\] arrays: $('#linechart').sparkline(\[ \[1,3\], \[2.7,4\], \[4.8,3\] \]);
3.  **Separate arrays:** Pass x values separately: $('#linechart').sparkline(\[3,4,3\], { xvalues: \[1,2.7,4.8\]});

You can also specify a value of "null" to omit values from the chart completely. eg: 
```
<span class="linechart">1,2,3,null,3,4,2</span> becomes:  
```
### Bar Charts

Set the "type" option to "bar" to generate bar charts. Values can be omitted by using the "null" value instead of a number.

**barColor**

CSS colour used for postive values

**negBarColor**

CSS colour used for negative values

**zeroColor**

CSS colour used for values equal to zero

**nullColor**

CSS colour used for values equal to null - By default null values are omitted entirely, but setting this adds a thin marker for the entry - This can be useful if your chart is pretty sparse; perhaps try setting it to a light grey or something equally unobtrusive

**barWidth**

Width of each bar, in pixels (integer)

**barSpacing**

Space between each bar, in pixels (integer)

**zeroAxis**

Centers the y-axis at zero if true (default)

**colorMap**

A [range map](#common) to map specific values to selected colours. For example if you want all values of -2 to appear yellow, use colorMap: { '-2': '#ff0' }.  
As of version 1.5 you may also pass an array of values here instead of a mapping to specifiy a color for each individual bar. For example if your chart has three values 1,3,1 you can set colorMap=\["red", "green", "blue"\]  
As of version 2.0, objects will be automatically be converted to [range maps](#common) so you can specify colours for a range of values

**stackedBarColor**

An array of colours to use for stacked bar charts. The first series will use the first value in the array, the second series will use the second, etc. _New in 2.0_

See also all of the [common options](#common) above, that can also be used with bar charts

Version 2.0 adds support for stacked bar charts. Values for each data series in the chart can be separated by colons if passed by html, or as an array of arrays.

For example, to draw series one with values of 1,2,3,4 and series 2 with values of 4,3,2,1:
```
    <span class\="bar"\>1:4,2:3,3:2,4:1</span>

    // same result as the html data above
    $('.bar').sparkline(\[ \[1,4\], \[2, 3\], \[3, 2\], \[4, 1\] \], { type: 'bar' });
```
### Tristate Charts

Tri-state charts are useful to show win-lose-draw information, such as the SF Giants recent game results at the top of the page. You can also use the colorMap option to use different colours for different values, or for arbitrary positions in the chart.

Set the "type" option to "tristate" to generate tristate charts.

**posBarColor**

CSS colour for positive (win) values

**negBarColor**

CSS colour for negative (lose) values

**zeroBarColor**

CSS colour for zero (draw) values

**barWidth**

Width of each bar, in pixels (integer)

**barSpacing**

Space between each bar, in pixels (integer)

**colorMap**

A [range map](#common) to map specific values to selected colours. For example if you want all values of -2 to appear yellow, use colorMap: { '-2': '#ff0' }.  
As of version 1.5 you may also pass an array of values here instead of a mapping to specifiy a color for each individual bar. For example if your chart has three values 1,3,1 you can set colorMap=\["red", "green", "blue"\]  
As of version 2.0, objects will be automatically be converted to [range maps](#common) so you can specify colours for a range of values.

See also all of the [common options](#common) above, that can also be used with tristate charts

### Discrete Charts

Discrete charts provide a separated thin vertical line for each value.

Set the "type" option to "discrete" to generate discrete charts.

**lineHeight**

Height of each line in pixels - Defaults to 30% of the graph height

**thresholdValue**

Values less than this value will be drawn using thresholdColor instead of lineColor

**thresholdColor**

Colour to use in combination with thresholdValue

See also all of the [common options](#common) above, that can also be used with discrete charts

### Bullet Graphs

_See the [wikipedia page](http://en.wikipedia.org/wiki/Bullet_graph) for more information on Bullet graphs.  
Supplied values must be in this order: target, performance, range1, range2, range3, ..._

Set the "type" option to "bullet" to generate bullet graphs.

**targetColor**

The CSS colour of the vertical target marker

**targetWidth**

The width of the target marker in pixels (integer)

**performanceColor**

The CSS color of the performance measure horizontal bar

**rangeColors**

Colors to use for each qualitative range background color - This must be a javascript array. eg \['red','green', '#22f'\]

See also all of the [common options](#common) above, that can also be used with bullet charts

### Pie Charts

  
  

Set the "type" option to "pie" to generate pie charts.

_These little pie charts tend only to be useful with 2 or 3 values at most_

**sliceColors**

An array of CSS colors to use for pie slices

**offset**

Angle in degrees to offset the first slice - Try -90 or +90

**borderWidth**

Width of the border to draw around the whole pie chart, in pixels.  
Defaults to 0 (no border) - _New in 2.0_

**borderColor**

CSS color to use to draw the pie border.  
Defaults to #000 - _New in 2.0_

See also all of the [common options](#common) above, that can also be used with pie charts

### Box Plots

  
  

_See the [wikipedia page](http://en.wikipedia.org/wiki/Box_plot) for more information on Box plots_

Set the "type" option to "box" to generate box plots.

**raw**

If set to false (default) then the values supplied are used to caculate the box data points for you. If true then you must pre-calculate the points (see below)

**showOutliers**

If true (default) then outliers (values > 1.5x the IQR) are marked with circles and the whiskers are placed at Q1 and Q3 instead of the least and greatest value

**outlierIQR**

Set the inter-quartile range multipler used to calculate values that qualify as an outlier - Defaults to 1.5

**boxLineColor**

CSS line colour used to outline the box

**boxFillColor**

CSS fill colour used for the box

**whiskerColor**

CSS colour used to draw the whiskers

**outlierLineColor**

CSS colour used to draw the outlier circles

**outlierFillColor**

CSS colour used to fill the outlier circles

**spotRadius**

Radius in pixels to draw the outlier circles

**medianColor**

CSS colour used to draw the median line

**target**

If set to a value, then a small crosshair is drawn at that point to represent a target value

**targetColor**

CSS colour used to draw the target crosshair, if set

**minValue**

If minvalue and maxvalue are set then the scale of the plot is fixed. By default minValue and maxValue are deduced from the values supplied

**maxValue**

See minValue

See also all of the [common options](#common) above, that can also be used with box plot charts

As noted in the options above, by default "raw" is set to false. This means that you can just pass an arbitrarily long list of values to the sparkline function and the corresponding box plot will be calculated from those values. This is probably the behaviour you want most of the time.

If, on the other hand, you have thousands of values to deal with you may want to pre-compute the points needed for the box plot. In that case, set raw=true and pass in the computed values. If showing outliers, supplied values of:  
_low\_outlier, low\_whisker, q1, median, q3, high\_whisker, high\_outlier_  
Omit the outliers and set showOutliers to false to omit outlier display.

### Interactive Sparklines

Version 2.0 of the plugin introduces support for interactive sparklines in the form of tooltips, mouseover highlighting and click interaction for all supported types of sparkline.

If you're happy with the default look and feel of the tooltips, you can skip this entire section.

Options to control sparkline interactions:

**disableInteraction**

Set to true to disable all sparkline interactivity, making the plugin behave in much the same way as it did in 1.x  
Defaults to false

**disableTooltips**

Set to true to disable mouseover tooltips.  
Defaults to false

**disableHighlight**

Set to true to disable the highlighting of individual values when mousing over a sparkline.  
Defaults to false

**highlightLighten**

Controls the amount to lighten or darken a value when moused over. A value of 1.5 will lighten by 50%, 0.5 will darken by 50%.  
Defaults to 1.4

**highlightColor**

If specified, then values that are moused over will be changed to this colour instead of being lightend

**tooltipContainer**

Specifies the DOM element that tooltips should be rendered into.  
Defaults to document.body

**tooltipClassname**

Specifies a CSS class name to apply to tooltips to override the default built-in style.

**tooltipOffsetX**

Specifies how many pixels away from the mouse pointer to render the tooltip on the X axis

**tooltipOffsetY**

Specifies how many pixels away from the mouse pointer to render the tooltip on the Y axis

**tooltipFormatter**

Pass a javascript function to use as a callback to override the HTML used to generate tooltips. The callback will be passed arguments of (sparkline, options, fields).  
sparkline is the sparkline object being rendered, "options" is the key:value mapping of options set for this sparkline - use options.get(key, default) to fetch an individual option. "fields" is an array of fields to render for the sparkline. This will be a single element array unless its a box plot.

**tooltipChartTitle**

If specified then the tooltip uses the string specified by this setting as a title

**tooltipFormat**

A format string or spformat object (or an array thereof for multiple entries) to control the format of the tooltip

**tooltipPrefix**

A string to prepend to each field displayed in a tooltip

**tooltipSuffix**

A string to append to each field displayed in a tooltip

**tooltipSkipNull**

If true then null values will not have a tooltip displayed  
Defaults to true

**tooltipValueLookups**

An object or range map to map field values to tooltip strings. For example you may want to map -1, 0 and 1 to the strings "Lost", "Draw", "Won"

**tooltipFormatFieldlist**

An array of values specifying which fields to display in a tooltip and in what order. Currently only useful for box plots. See below for more details

**tooltipFormatFieldlistKey**

Specifies which key holds the field name to reference above. For box plots this should be "field"

**numberFormatter**

Pass a javascript function to control how numbers are formatted in tooltips. The callback will be passwd a number to format and must return a string.  
Default behaviour is to format numbers to western conventions.

**numberDigitGroupSep**

Character to use for group separator in numbers "1,234" for l10n purposes.  
Defaults to the comma - ","

**numberDecimalMark**

Character to use for the decimal point in numbers for l10n purposes.  
Defaults to the period - "."

**numberDigitGroupCount**

Number of digits between the group seperator in numbers for l10n purposes.  
Defaults to 3.

### Formatting Tooltips

There are a couple of approaches you can take to format tooltips. Either you can manually generate tooltips by using the tooltipFormatter callback function (perhaps to hook in an external tooltip library), or you can use the other tooltip options above to fine-tune the default formatter.

The tooltipFormat and toolipValueLookups options provide the main methods of formatting the text displayed for each value in a toolip.

The tooltipFormat is applied whenever the mouse is moved over a value. Various fields enclosed between double curly braces in the format string are substituted depending on the type of sparkline in use. For example, the default format string for line charts is:
```
  <span style\="color: {{color}}"\>&#9679;</span> {{prefix}}{{y}}{{suffix}}</span>
```
"color" is derived from the colour of the line being drawn.  
"prefix" and "suffix" and set by setting the tooltipPrefix and tooltipSuffix items.  
"y" represents the y value of the point under the mouse. "x" can also be used to if useful.

The supported fields for the different types of sparklines include:

*   **All types:** "prefix", "suffix" as set by tooltipPrefix and tooltipSuffix options
*   **Line:** "color", "fillColor", "x", "y", "isNull" (true if a null value)
*   **Bar:** "value" (the value under the pointer), "color", "isNull"
*   **Tristate:** "value" (the value under the pointer), "color", "isNull"
*   **Discrete:** "value", "isNull"
*   **Pie:** "value", "percent" (number between 0 and 100), "color" (of the moused-over slice)"
*   **Bullet:** "value", "fieldkey", "isNull"
*   **Box:** "field" and "value". Field may be one of "lq" (lower quartile), "med" (median), "uq" (upper quartile), "lo" (left outlier), "ro" (right outlier), "lw" (left whisker) "rw" (right whisker)

#### Box plot field order

For box plots, you can control which fields are displayed and in what order using the tooltipFormatFieldlist and tooltipFormatFieldlistKey options. For example, to display only the median, lower quartile and upper quartile values, in that order:  
```
$('.boxplot').sparkline(values, {
    type: 'box', 
    tooltipFormatFieldlist: \['med', 'lq', 'uq'\], 
    tooltipFormatFieldlistKey: 'field'
});
```
#### Formatting field names and values

You can translate field names and values to other arbitrary strings using the tooltipValueLookups option. Box plots do this, for example, to remap "lq" to the string "Lower Quartile", etc but you can also use it to map values (numbers) or ranges of values to strings too using a [range map](#common)

For example, we can create a bar chart with tooltips that say "low", "medium" or "high" along with the value:
```
var values \= \[1, 3, 5, 3, 8\];
$('.bar').sparkline(values, {
    type: 'bar',
    tooltipFormat: '{{value:levels}} - {{value}}',
    tooltipValueLookups: {
        levels: $.range\_map({ ':2': 'Low', '3:6': 'Medium', '7:': 'High' })
    }
});
```
You can also specify a CSS class name to associate with a format by passing the format string and class to $.spformat:
```
$('.bar').sparkline(values, {
    type: 'bar',
    tooltipFormat: $.spformat('{{value}}', 'tooltip-class')
});
```
### Click Events

When a user clicks on a sparkline, a sparklineClick event is generated. The event object contains a property called "sparklines" that holds an array of the sparkline objects under the mouse at the time of the click. For non-composite sparklines, this array will have just one entry.

This example will display an alert with the moused-over value whenever a value is clicked:
```
$('.clickdemo').sparkline();
$('.clickdemo').bind('sparklineClick', function(ev) {
    var sparkline \= ev.sparklines\[0\],
        region \= sparkline.getCurrentRegionFields();
    alert("Clicked on x="+region.x+" y="+region.y);
});
```
The fields available from getCurrentRegionFields() match those listed in the formatting tooltips section, above.

### Mouse-over Events

When the mouse moves over a different value in a sparkline a sparklineRegionChange event is generated. This can be useful to hook in an alternate tooltip library.

As with the sparklineClick event, the generated event has a property called sparklines that holds an array of the sparklines under the pointer.

Example that updates a span with the current region details:Last region:
```
$('.mouseoverdemo').sparkline();
$('.mouseoverdemo').bind('sparklineRegionChange', function(ev) {
    var sparkline \= ev.sparklines\[0\],
        region \= sparkline.getCurrentRegionFields(),
        value \= region.y;
    $('.mouseoverregion').text("x="+region.x+" y="+region.y);
}).bind('mouseleave', function() {
    $('.mouseoverregion').text('');
});
```
### License

This plugin is copyright [Splunk Inc](http://www.splunk.com/) and licensed using the [New BSD License](http://www.opensource.org/licenses/BSD-3-Clause)
