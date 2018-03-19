# makelist() (aggregation function)

Returns a `dynamic` (JSON) array of all the values of *Expr* in the group.

* Can be used only in context of aggregation inside [summarize](query_language_summarizeoperator.md)

**Syntax**

`summarize` `makelist(`*Expr*` [`,` *MaxListSize*]`)`

**Arguments**

* *Expr*: Expression that will be used for aggregation calculation.
* *MaxListSize* is an optional integer limit on the maximum number of elements returned (default is *128*).

**Returns**

Returns a `dynamic` (JSON) array of all the values of *Expr* in the group.
If the input to the `summarize` operator is not sorted, the order of elements in the resulting array is undefined.
If the input to the `summarize` operator is sorted, the order of elements in the resulting array tracks that of the input.
