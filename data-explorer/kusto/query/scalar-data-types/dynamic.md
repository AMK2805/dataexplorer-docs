---
title: The dynamic data type - Azure Kusto | Microsoft Docs
description: This article describes The dynamic data type in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# The dynamic data type

The `dynamic` data type is a scalar type that behaves somewhat like a JSON data
type. A value of this type can be:

* A value of any of the primitive Kusto data types (`bool`, `datetime`, `guid`, `int`, `long`,
  `real`, `string`, and `timespan`)
* An array `dynamic` values (single-dimensional, 0-based, possibly empty)
* A property bag mapping unique `string` to `dynamic` values (possibly empty)
* A null value of the `dynamic` type

> Values of type `dynamic` are limited to 1MB bytes (2^20).

> Although the `dynamic` type appears JSON-like, it can hold values that the JSON
  model does not represent, as the Kusto model is more-detailed than JSON's
  (for example, it differentiates between `long` and `real` numbers, has explicit
  support for `datetime` and `timespan` values, etc.) Therefore, in serializing
  `dynamic` values into a JSON representation, Kusto will use a string to serialize
  values that JSON does not support. Conversely, Kusto will parse some strings
  as strongly-typed values if they can be parsed as such (for example, `datetime`
  values). For more about the JSON object model, see See [json.org](http://json.org/).

## dynamic literals

The external representation of a `dynamic` value is JSON. A literal of type `dynamic`
wraps the actual JSON representation of the value by `dynamic(` and `)`:

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

For convenience, `dynamic` literals that appear in the query text itself may
also include other Kusto literals (such as `datetime` literals, `timespan`
literals, etc.) This extension over JSON is not available when parsing strings
(such as when using the `parsejson` function or when ingesting data), but it
allows one to do this:

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

To parse a `string` value that follows the JSON encoding rules into a `dynamic`
value, use the `parsejson` function. For example:

* `parsejson('[43, 21, 65]')` - an array of numbers
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` - a dictionary
* `parsejson('21')` - a single value of dynamic type containing a number
* `parsejson('"21"')` - a single value of dynamic type containing a string
* `parsejson('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')` - gives the same
   value as `o` in the example above.

> [!WARNING]
> Note that, unlike JavaScript, JSON mandates the use of double-quote
> (`"`) characters around strings and property-bag property names.
> Therefore, it is generally easier to quote a JSON-encoded string literal by using
> a single-quote (`'`) character.
  
The following example shows how one might define a table that holds a `dynamic` column (as well as
a `datetime` column) and then ingest into it a single record. it also demonstrates how one
can encode JSON strings in CSV files:

```kusto
// dynamic is just like any other type:
.create table Logs (Timestamp:datetime, Trace:dynamic)

// Everything between the "[" and "]" is parsed as a CSV line would:
// 1. Since the JSON string includes double-quotes and commas (two characters
//    that have a special meaning in CSV), we must CSV-quote the entire second field.
// 2. CSV-quoting means adding double-quotes (") at the immediate beginning and end
//    of the field (no spaces allows before the first double-quote or after the second
//    double-quote!)
// 3. CSV-quoting also means doubling-up every instance of a double-quotes within
//    the contents
.ingest inline into table Logs
  [2015-01-01,"{""EventType"":""Demo"", ""EventValue"":""Double-quote love!""}"]
```

|Timestamp                   | Trace                                                 |
|----------------------------|-------------------------------------------------------|
|2015-01-01 00:00:00.0000000 | {"EventType":"Demo","EventValue":"Double-quote love!"}|

## Dynamic object accessors

To subscript a dictionary, use either the dot notation (`dict.key`) or the brackets notation (`dict["key"]`).
When the subscript is a string constant- both options are equivalent.

> Note: to use an expression as the subscript, use the brackets notation. When using arithmetic expressions, the expression inside the brackets must be wrapped in parenthesis.

In the examples below `dict` and `arr` are columns of dynamic type:

|Expression                        | Accessor expression type | Meaning                                                                              | Comments                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict[col]                         | Entity name (column)     | Subscripts a dictionary using the values of the column `col` as the key              | Column must be of type string                 | 
|arr[index]                        | Entity index (column)    | Subscripts an array using the values of the column `index` as the index              | Column must be of type integer or boolean     | 
|arr[-index]                       | Entity index (column)    | Retrieves the 'index'-th value from the end of the array                             | Column must be of type integer or boolean     |
|arr[(-1)]                         | Entity index             | Retrieves the last value in the array                                                |                                               |
|arr[toint(indexAsString)]         | Function call            | Casts the values of column `indexAsString` to int and use them to subscript an array |                                               |
|dict[['where']]                   | Keyword used as entity name (column) | Subscripts a dictionary using the values of column `where` as the key    | Entity names that are identical to some query language keywords must be quoted | 
|dict.['where'] or dict['where']   | Constant                 | Subscripts a dictionary using `where` string as the key                              |                                               |

**Performance tip:** Prefer to use constant subscripts when possible

Accessing a sub-object of a `dynamic` value yields another `dynamic` value,
even if the sub-object has a different underlying type. Use the `gettype`
function to discover the actual underlying type of the value, and any
of the cast function listed below to cast it to the actual type.

## Casting dynamic objects

> After subscripting a dynamic object, you must cast the value to a simple type.

|Expression | Value | Type|
|---|---|---|
| X | parsejson('[100,101,102]')| array|
|X[0]|parsejson('100')|dynamic|
|toint(X[1])|101| int|
| Y | parsejson('{"a1":100, "a b c":"2015-01-01"}')| dictionary|
|Y.a1|parsejson('100')|dynamic|
|Y["a b c"]| parsejson("2015-01-01")|dynamic|
|todate(Y["a b c"])|datetime(2015-01-01)| datetime|

Cast functions are:

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## Building dynamic objects

Several functions allow one to create new `dynamic` objects:

* [pack()](https://kusdoc2.azurewebsites.net/docs/packfunction.html) creates a property bag from name/value pairs.
* [pack_array()](https://kusdoc2.azurewebsites.net/docs/packarrayfunction.html) creates an array from name/value pairs.
* [range()](https://kusdoc2.azurewebsites.net/docs/rangefunction.html) creates an array with an arithmetic series of numbers.
* [zip()](https://kusdoc2.azurewebsites.net/docs/zipfunction.html) pairs "parallel" values from two arrays into a single array.
* [repeat()](https://kusdoc2.azurewebsites.net/docs/repeatfunction.html) creates an array with a repeated value.

Additionally, there are several aggregate functions which create `dynamic`
arrays to hold aggregated values:

* [buildschema()](https://kusdoc2.azurewebsites.net/docs/buildschema-aggfunction.html) returns the aggregate schema of multiple `dynamic` values.
* [makelist()](https://kusdoc2.azurewebsites.net/docs/makelist-aggfunction.html) returns an array holding all values, in sequence.
* [makeset()](https://kusdoc2.azurewebsites.net/docs/makeset-aggfunction.html) returns an array holding all unique values.

## Operators and functions over dynamic types

|||
|---|---|
| *value* `in` *array*| True if there is an element of *array* that == *value*<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| True if there is no element of *array* that == *value*
|[`arraylength(`array`)`](https://kusdoc2.azurewebsites.net/docs/arraylengthfunction.html)| Null if it isn't an array
|[`extractjson(`path,object`)`](https://kusdoc2.azurewebsites.net/docs/extractjsonfunction.html)|Uses path to navigate into object.
|[`parse_json(`source`)`](https://kusdoc2.azurewebsites.net/docs/parsejsonfunction.html)| Turns a JSON string into a dynamic object.
|[`range(`from,to,step`)`](https://kusdoc2.azurewebsites.net/docs/rangefunction.html)| An array of values
|[`mvexpand` listColumn](https://kusdoc2.azurewebsites.net/docs/mvexpandoperator.html) | Replicates a row for each value in a list in a specified cell.
|[`summarize buildschema(`column`)`](https://kusdoc2.azurewebsites.net/docs/buildschema-aggfunction.html) |Infers the type schema from column content
|[`summarize makelist(`column`)` ](https://kusdoc2.azurewebsites.net/docs/makelist-aggfunction.html)| Flattens groups of rows and puts the values of the column in an array.
|[`summarize makeset(`column`)`](https://kusdoc2.azurewebsites.net/docs/makeset-aggfunction.html) | Flattens groups of rows and puts the values of the column in an array, without duplication.
|[`summarize make_dictionary(`column`)`](https://kusdoc2.azurewebsites.net/docs/make-dictionary-aggfunction.html) | Merges the property bag (dictionary) values in the column into one property bag, without key duplication.