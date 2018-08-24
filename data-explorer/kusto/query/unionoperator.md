# union operator

Takes two or more tables and returns the rows of all of them. 

```kusto
Table1 | union Table2, Table3
```

**Syntax**

*T* `| union` [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

Alternative form with no piped input:

`union` [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

**Arguments**

* `Table`:
 *  The name of a table, such as `Events`; or
 *  A query expression that must be enclosed with parenthesis, such as `(Events | where id==42)` or `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))`
 *  A set of tables specified with a wildcard. For example, `E*` would form the union of all the tables in the database whose names begin `E`.
* `kind`: 
 * `inner` - The result has the subset of columns that are common to all of the input tables.
 * `outer` - The result has all the columns that occur in any of the inputs. Cells that were not defined by an input row are set to `null`.
* `withsource`=*ColumnName*: If specified, the output will include a column
called *ColumnName* whose value indicates which source table has contributed each row.
If the query effectively (after wildcard matching) references tables from more than one database (default database always counts) the value of this column will have a table name qualified with the database.
Similarly __cluster and database-_ qualifications will be present in the value if more than one cluster is referenced. 
* `isfuzzy=` `true` | `false`: If `isfuzzy` is set to `true` - allows fuzzy resolution of union legs. `Fuzzy` applies to the set of `union` sources. It means that while analyzing the query and preparing for execution, the set of union sources is reduced to the set of table references that exist and are accessible at the time. If at least one such table was found, any resolution failure will yield a warning in the query status results (one for each missing reference), but will not prevent the query execution; if no resolutions were successful - the query will return an error.
The default is `isfuzzy=` `false`.

**Returns**

A table with as many rows as there are in all the input tables.

**Notes**
1. `union` scope can include [let statements](./letstatement.md) if those are 
attributed with [view keyword](./letstatement.md)
2. `union` scope will not include [functions](https://kusdoc2.azurewebsites.net/docs/controlCommands/controlcommands_functions.html). To include 
function in the union scope - define a [let statement](./letstatement.md) 
with [view keyword](./letstatement.md)
3. If the `union` input is [tables](https://kusdoc2.azurewebsites.net/docs/controlCommands/controlcommands_tables.html) (as oppose to [tabular expressions](./findoperator.md)), and the `union` is followed by a [where operator](./whereoperator.md), consider replacing both with [find](./syntax.md) for better performance. Please note the different [output schema](./findoperator.md#output-schema) produced by the `find` operator. 
4. `isfuzzy=` `true` applies only to the phase of the `union` sources resolution. Once the set of source tables was determined, possible additional query failures will not be suppressed.

**Example**

```kusto
union K* | where * has "Kusto"
```

Rows from all tables in the database whose name starts with `K`, and in which any column includes the word `Kusto`.

**Example**

```kusto
union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```

The number of distinct users that have produced
either a `Query` event or a `Command` event over the past day. In the result, the 'SourceTable' column will indicate either "Query" or "Command".

```kusto
Query
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

This more efficient version produces the same result. It filters each table before creating the union.

**Example: Using `isfuzzy=true`**
 
```kusto     
// Using union isfuzzy=true to access non-existing view:                   
let View-1 = view () { range x from 1 to 1 step 1 };
let View-2 = view () { range x from 1 to 1 step 1 };
let OtherView-1 = view () { range x from 1 to 1 step 1 };
union isfuzzy=true
(View-1 | where x > 0), 
(View-2 | where x > 0),
(View-3 | where x > 0)
| count 
```

|Count|
|---|
|2|

Observing Query Status - the following warning returned:
`Failed to resolve entity 'View-3'`

```kusto
// Using union isfuzzy=true and wildcard access:
let View-1 = view () { range x from 1 to 1 step 1 };
let View-2 = view () { range x from 1 to 1 step 1 };
let OtherView-1 = view () { range x from 1 to 1 step 1 };
union isfuzzy=true View*, SomeView*, OtherView*
| count 
```

|Count|
|---|
|3|

Observing Query Status - the following warning returned:
`Failed to resolve entity 'SomeView*'`



