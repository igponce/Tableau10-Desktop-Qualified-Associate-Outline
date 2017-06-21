# Tableau Desktop 10 Qualified Associate - Exam Outline

Exam outline with links to questions, and solutions that
exemplify what's needed / asked in the Tableau 10 QA Exam.

# Dimensions and Measures

Hint: If it makes sense to add it up, it's likely a measure.



# Data Connections
## Understand how to connect to Tableau Server
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


#
##
##
##
##

####


# LOD (Level of detail)

Syntax

```{R}
{ FIXED : avg ({fixed [Product Sub-Category]  : sum([Profit]) }) 
```

{ FIXED: something_that_will_condition the outcome : calculation_that_depends_on_the_previous_result }