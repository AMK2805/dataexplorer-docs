---
title: Kusto.Explorer keyboard shortcuts (hot-keys) - Azure Data Explorer | Microsoft Docs
description: This article describes Kusto.Explorer keyboard shortcuts (hot-keys) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: reference
ms.date: 09/24/2018
---
# Kusto.Explorer keyboard shortcuts (hot-keys)

## Application-level

The following keyboard shortcuts can be used from any context:

|Hot Key|Description|
|-------|-----------|
|`F1`     | Opens the help|
|`F11`    | Toggle full-view mode|
|`Ctrl`+`F1` | Toggle ribbon appearance |
|`Ctrl`+`+`| Increases query and data results font|
|`Ctrl`+`-`| Decreases query and data results font|
|`Ctrl`+`0`| Resets query and data results font|
|`Ctrl`+`1` .. `7`| Switches to Query document panel with respective number (1..7)|
|`Ctrl`+`N` |Opens a new query editor|
|`Ctrl`+`O` |Open query editor script in a new query editor|
|`Ctrl`+`W` |Closes active query editor|
|`Ctrl`+`S` |Saves query into a file|
|`Shift`+`F3` | Opens Analytical Report Gallery|
|`Ctrl`+`F12`| Opens embedded calculator window|
|`Ctrl`+`Shift`+`O`|Opens Kusto.Explorer options and settings dialog|

## Query and results view

The following keyboard shortcuts can be used when editing a query in the query editor
or when the context is in the results view:

|Hot Key|Description|
|-------|-----------|
|`Ctrl`+`Shift`+`C`|Copies query, deep-link, and data to the clipboard|
|`Alt`+`Shift`+`C` |Copies query and deep-link the clipboard|
|`Ctrl`+`~` |Copies query and data to the clipboard in markdown format |
|`Ctrl`+`Shift`+`D`|Toggles mode of hiding duplicate rows in the data view|
|`Ctrl`+`Shift`+`H`|Toggles mode of hiding empty columns in the data view|
|`Ctrl`+`Shift`+`J`|Toggles mode of collapsing columns with single value in the data view|
|`Ctrl`+`Shift`+`A`|Opens a Query Analyzer tool in a new query panel|
|`Ctrl`+`U`  |Opens a panel showing current column values with client-side filtering|
|`Alt`+`C`  |Renders Column chart over existing data|
|`Alt`+`T`  |Renders Timeline chart over existing data|
|`Alt`+`A`  |Renders Anomaly timeline chart over existing data|
|`Alt`+`P`  |Renders Pie chart over existing data|
|`Alt`+`L`  |Renders Ladder timeline chart over existing data|
|`Alt`+`V`  |Renders Pivot chart over existing data|
|`Ctrl`+`Shift`+`V`|Shows Timeline pivot over existing data|
|`Ctrl`+`F3`  | Toggles `show only matching rows`/`highlight matching rows` modes for  client text search (`Ctrl`+`F`) behaves in data grid. |
|`Ctrl`+`F`  | Shows search box for the panel that is currently in focus. Supported in `Connetions`, `Data Results`, and `Query Editor` panels|
|`Ctrl`+`Tab`| Shows Query Editor document selector dialog. You can hold `Ctrl` and swithch between documents with `Tab` |
|`Ctrl`+`R`|Toggles appearance of the result panel|
|`Ctrl`+`E`|Toggles appearance of the query editor and result panel in cycle of: `Query Editor and Results` -> `Query Editor` -> `Query Editor and Results` -> `Results` |
|`Ctrl`+`Shift`+`E`|Toggles appearance of the query editor and result panel in cycle of: `Query Editor and Results` -> `Results` -> `Query Editor and Results` -> `Query Editor` |
|`Ctrl`+`Shift`+`R` | Focuses on Results panel |
|`Ctrl`+`Shift`+`Y` | Focuses on Query editor |
|`Ctrl`+`Shift`+`T` | Focuses on Connections panel |
|`Alt`+`Ctrl`+`L`|Locks current connection context to the Query Editor, so changing selected row in the Connetion panel has no effect on the Query Editor context. |

## Query editor

The following keyboard shortcuts can be used when editing a query in the query editor:

|Hot Key|Description|
|-------|-----------|
|`F1`|When cursor points to an operator or function - opens a help window with information about the operator or function. If help topic is not present - opens a help URL|
|`F5`|Run currently selected query|
|`Shift`+`Enter`|Run currently selected query|
|`F8`|Fetch query results from the local cache. If results are not present - run currently selected query|
|`F6`|Run currently selected query in `Progressive Results` mode|
|`Ctrl`+`F5` | Preview results of the selected query (shows few results and total count)|
|`Ctrl`+`Shift`+`Space`| Insert data cell selections as filters into the query|
|`Ctrl`+`Space`| Force IntelliSense rules check. Possible options will be shown in any rule matched |
|`Ctrl`+`Enter`| Adds `pipe` symbol and moves to a new line|
|`Ctrl`+`Z`| Undo |
|`Ctrl`+`Y`| Re-do |
|`Ctrl`+`L`| Deletes current line|
|`Ctrl`+`D`| Deletes current line| 
|`Ctrl`+`F`| Opens `Find and Replace` dialog |
|`Ctrl`+`G`| Opens `Go-to line` dialog |
|`Ctrl`+`F8` | Show my queries past 3 days |
|`Ctrl`+ bracket | When cursor is at bracket symbols: `(` , `)` , `[` , `]` , `{` , `}` - moves cursor to the matching openning or closing bracket |
|`Ctrl`+`Shift`+`Q` | Prettify current query |
|`Ctrl`+`Shift`+`L` | Make current query or selection lower-case |
|`Ctrl`+`Shift`+`U` |  Make current query or selection upper-case |
|`Ctrl`+`Mouse wheel up`| Increases font of the query editor| 
|`Ctrl`+`Mouse wheel down`| Decreases font of the query editor|
|`Alt`+`P` | Opens query parameters dialog |
|`F2`|  Open current line / selected text in Phyton editor dialog |
|`Ctrl`+`K`, `Ctrl`+`D` | Inserts current timestamp as detatime literal |
|`Ctrl`+`K`, `Ctrl`+`R` | Inserts `range x from 1 to 1 step 1` snippet |
|`Ctrl`+`K`, `Ctrl`+`C` | Comment current line or selected lines |
|`Ctrl`+`K`, `Ctrl`+`F` | Prettify current query |
|`Ctrl`+`K`, `Ctrl`+`V` | Duplicate current query (append it to the end of current query document) |
|`Ctrl`+`K`, `Ctrl`+`U` | Uncomment current line or selected lines |
|`Ctrl`+`K`, `Ctrl`+`S` | Turn current line or selected lines into multi-line string literal |
|`Ctrl`+`K`, `Ctrl`+`M` | Remove multi-line stirng literal marks (reverse of `Ctrl`+`K`, `Ctrl`+`S`) |

## JSON viewer

The following keyboard shorcuts can be used from within the results JSON viewer
(displayed when one double-clicks on a JSON-like value in the results view cell):

|Hot Key|Description|
|-------|-----------|
|`Ctrl`+`Up Arrow`|Navigate to parent|
|`Ctrl`+`Right Arrow`|Expand current node (one level)|
|`Ctrl`+`Left Arrow`|Collapse current node (one level)|
|`Ctrl`+`.`|Toggle expansion of the current node (all levels expanded/collapsed)
|`Ctrl`+`Shift`+`.`|Toggle expansion of the current node parent (all levels expanded/collapsed)|