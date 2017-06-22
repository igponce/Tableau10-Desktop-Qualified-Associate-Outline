# Tableau Desktop 10 Qualified Associate - Exam Outline

Exam notes with links to questions, and solutions that
exemplify what's needed / asked in the Tableau 10 QA Exam.

Not 100% done, but enough to pass.

Exam prep guide:
http://mkt.tableau.com/files/Desktop-10-QA-Exam-Prep-Guide.pdf

# Dimensions and Measures

Hint: If it makes sense to add it up, it's likely a measure.

Dimension: The stuff you use to ask questions. The "who" you restrict the answer to.
Measures: The numbers / answers you get.

Measures usually are aggregated. It makes sense to see the total/max/min under the point of view of a Dimension.
By default, Tableau will interpret numerical fields as Measures.

Source: http://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_typesandroles_dataroles_dimensionmeasure.html


# Data Connections
## Understand how to connect to Tableau Server

This is covered in the !(product documentation (for desktop))[https://onlinehelp.tableau.com/current/pro/desktop/en-us/help.html#examples_tableauserver.html%3FTocPath%3DReference%7CConnector%2520Examples%7CTableau%2520Server%2520or%2520Tableau%2520Online%7C_____0]

## Understand Performance Optimization
### Parallel query
### Data engine vectorization

Scope: Calculations

From https://alanattableau.files.wordpress.com/2015/07/designing-efficient-workbooks-v92.pdf

Now tableau uses SIMD instructions (SSE4.1) to make calculations, thus better filling the
data and execution pipeline of the processor.

No data about SSE4.2, AVG, or using GPUs to do this... by now.


### Parallel aggregation

Scope: Calculations, joins, etc.

From https://alanattableau.files.wordpress.com/2015/07/designing-efficient-workbooks-v92.pdf

Tableau will use multiple cores where possible. The data engine can now run aggregations in parallel, splitting the work across multiple cores. By default, the maximum degree of parallelism is (number of available logical processors) / 2. This means that query operations on data extracts can run up to N times faster in Tableau 9 (where N is the number of cores in the machine).

### External query caching

Tableau will cache data in memory / disk to avoid to ask ("go to the network") or parse data that won't have changed.
A good example of this is a file-based data.
With extenal query caching Tableau doesn't need to parse a *whole* excel file to show some data.
This data can be cached efficiently.
Warning: when the cache is invalidated, it will need to re-parse the file again, so it's a good idea to maka an extract unless the data is really tiny or can change unexpectedly.

### Query fusion

Scope: Dashboard

Looks at all of dashboard queries and tries to eliminate any redundant queries.
How? Combining queries together and using the results.

If the same query is needeed in the same dashboard in several sheets, Tableau
tries to combine this multiple queryes into (just) one.

This works with queries with the same LOD !!


## Understand how to use Automatic & Custom Split

Scope: Data Integration

https://onlinehelp.tableau.com/current/pro/desktop/en-us/split.html

When? Sometimes your data has fields encoded inside string fields.
Why? Denormalization, you cannot touch the schema, etc.

Automatic split happens when tableau can guess what kind of data is inside,
separators, etc...

Data Split cannot happen automagicaly. Tableau will only split fields it's told to do so.

You can split data from the Data pane, or right-click: Transform -> Split.

Important: Data from splits cannot be used for Joing tables; but can be used for blending.

## Join tables from single and multiple databases

https://onlinehelp.tableau.com/current/pro/desktop/en-us/joining_tables.html

No need to use Apache Drill.
Tableau can Join tables from different databases like if it were from the same one.
Trick is you must make two different connections, one for each data source.

If you must connecto to extract files, it must be done on the *first* connection.
Why? because this way it retains all calculations, and properties.

Advantage: Tableau runs queries to different sources in parallel (like blending).

Warning !!!

Colation problems (like having fields on UTF-8 and CP850 on different connections).
Also, problems with different date formats (be careful to use the same Level of Detail
for all data sources).

## Data Preparation
### Blending

### Metadata Grid

Sometimes a list of fields is more useful than a preview of the data so we’ve added a “Metadata” view of your connection.

It displays, the field name, table, and remote field name.
In the case of splits, there is no remote field name.
For calculations, there is no table field.

https://www.tableau.com/about/blog/2015/1/tableau-90-auto-data-prep-stay-flow-35980#ucsrKSAg7CtOuoBL.99

### Pivot

- The Pivot option is available from the grid and metadata grid.
- All fields in the pivot must be from the same connection.
- Only one pivot is allowed per data source.
- Pivot fields can be used as the join key.
- The Pivot option cannot be used in calculated fields.

### Union

This is similar to database partitioning:

You have part of your data in a datasource, like for example, the fist half of 2016,
and in another datasource you have the second half of 2016.

Potential problem with unions: changed names in fields from file-to-file.
In this case you'll need to Merge the columns into one (with right click - the option is over the Pivol command).

https://www.tableau.com/about/blog/2016/1/combine-your-data-files-union-tableau-93-48891


### Data Interpreter


## Understand connection options
## Understand how to connect to different file types
## Understand data extract capabilities


## Understand Shadow extracts

When used: when processing non-legacy excel files, statistical files, or text files.
Why? Processing some file formats is CPU/IO intensive.

Location:
    - For Windows: Users\AppData\Local\Tableau\Caching\TemporaryExtracts
    - For Mac: ~Library/Caches/com.tableau.Caching/TemporaryExtracts
    - Sometimes also in the "My Tableau Repository\Shadow Extras" folder.

Shadow extracts is data that Tableau stores when using file (like non-legacy excel) to make loading data faster.
They have .ttde extension.
Tableau will store up to 5 files with .ttde extension.
Altough they're named extracts, its file format is not the same as .tde (extract) files.

http://kb.tableau.com/articles/issue/low-disk-space-because-of-ttde-files
http://www.icancrack.com/index.php/52/what-is-shadow-extract-in-tableau

# Organizing & Simplifying Data
Understand how to:
## Filter data

## Sort data

## Build groups

## Build hierarchies

## Build sets


# Field & Chart Types
## Understand discrete v. continuous

How to tell from data:
Blue pill = Discrete
Green pill = continuous

Discrete values -> Take a limited number of values (size: XS,S,M,L,XL - City: Madrid, Barcelona, Chigago).
Could be numeric (customer ID).
Discrete values are "individually separate and distinct".

Continuous values "forming an unbroken whole, without interruption"

Do not mistake with Dimensions and Measures, altough Dimensions are usually Discrete and Measures continuous.

Filtering and color are special for Dimensions and Measures.

Filtering discrete values: choose members (by hand, or with regex).
Filtering continuous: by range.

Coloring discrete values: Color palette.
Coloring continuous values: Using a color gradient.

See http://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_typesandroles_dataroles_dimensionmeasure.html

## Understand measure names and measure values

This is important in charts that show different measures, like parallel coordinates charts.

In this case you can drag to a row/column "measure names" and "measure values".

To select different measures, you have to filter "measure names" in the filter card.

http://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_understanddatawindow_meavalues.html

## Understand generated fields


## Understand how and when to build:
### Histograms



### Heat maps

Heat maps are like "histograms seen from above", or 2D histograms.
They're usefult to locate "hot spots" on data. That's it places where

Variables in both axes must be discrete (or made discrete by using calculations, like DATEPART('weekday',[Purchase Date]) ).
Color comes from aggregating continuous [#num records], SUM([Sales]).

### Tree maps

This kind of graphics does not show a tree.
Instead it fills a rectangle with data, using area size to display each category importance.

Tree maps can use two measures for coloring hierarchies. In this case, hierarchies will get its Chroma component from the fist dimension and the HUE/brigthness from the second.

### Bullet graphs

This graphs don't resemble a bullet.
They are like barcharts with a distribution showing progress towards a goal behing the bar.

https://www.interworks.com/blog/ccapitula/2014/12/29/tableau-essentials-chart-types-bullet-graph

### Combined Axis Charts


### Dual Axis Charts


### Scatter Plots
### Data Highlighter
### Cross tabs
### Motion charts
### Bar in bar charts
### Box plots

### Gantt Bar Charts

Show duration in a calendar.


### Paretos

Similar to having an ordered barchart with the data, plus a cumulative distribution function overlaid on the chart.
This is done with a dual axis, and a table calculation:

- First make the barchart. Order the bars by value (descending).
- Make a line chart with the same data as the barchart.
  - Make a table calculation to compute the CDF: Running sum percentage.
  - Change the CDF axis to go from 0 to 100%.

https://www.tableau.com/learn/tutorials/on-demand/pareto-charts
http://onlinehelp.tableau.com/current/pro/desktop/en-us/help.html#pareto.html

### Sparklines

## Understand how to effectively use titles, captions  and tooltips
### Understand how to edit axes
### Understand mark labels and annotations


# Calculations
## Understand how to:
## Manipulate string and date calculations
## Create quick table calculations
## Use LOD calculations; types of LOD calculations

Syntax

```{R}
{ FIXED : avg ({fixed [Product Sub-Category]  : sum([Profit]) }) 
```

{ FIXED: something_that_will_condition the outcome : calculation_that_depends_on_the_previous_result }

## Use Ad-hoc calculations
## Work with aggregation options
## Build logic statements
## Build arithmetic calculations
## Build grand totals and sub-totals

# Mapping
## Understand how to use:
## Pan & Zoom
## Filtering
## Map layering
## Custom territories
## Lasso & Radial selection
## Understand how to modify locations within Tableau
## Understand how to import and manage custom geocoding
## Understand how to use a background image map
## Understand how to use Geographic search

# Analytics
## Understand how to use:
## Reference Lines
## Reference Bands
## Trend Lines
## Trend Model
## Forecasting
## Drag & Drop Analytics
## Box Plot
## Reference distributions
## Statistical summary card
## Instant Analytics

# Dashboards
## Understand publishing & sharing options
## Understand how to build dashboards
## Understand dashboard actions
## Understand Device Designer
## Understand how to create a drill down report
## Understand how to utilize visual best practices for dashboard design 



