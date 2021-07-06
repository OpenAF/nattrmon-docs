---
layout: default
title: HTMLStatus
parent: Outputs
grand_parent: Reference
---
# nOutput HTMLStatus

This output will create a status file, that can be html, yaml, table, json, prettyjson or slon, with two columns: control and status. The control is the title list of all or specific warnings and the status is a summary text given the corresponding warning current level.

Example of a table file:

````bash
$ cat status.txt
 control |status
---------+------
Control 1|NOT OK
Control 2|Issues
Control 3|OK

Update: 2021-01-02T10:00:01.123Z
````

Example of a HTML file:



## Example of use of the execArgs

````yaml
output:
  name       : Output warnings history
  chSubscribe: nattrmon::warnings
  execFrom   : nOutput_WarnsHistory
  execArgs   :
    # chName    : nattrmon::warningsHistory
    chType  : file
    chParams:
      file: "/some/folder/warningHistory.json"
    # historyMax: 100
    # maxEntries: 100
    # include   :
    # - Problem with A
    # - Problem with B
    # exclude   :
    # - Problem with C    
````

## Description of execArgs

| execArgs   | Type   | Mandatory | Description |
|:-----------|:------:|:---------:|:------------|
| chName     | String | No | The OpenAF channel name to use (defaults to nattrmon::warningsHistory) |
| chType     | String | No | The OpenAF channel type to use |
| chParams   | Map    | No | The OpenAF channel params to use |
| historyMax | Number | No | The maximum number of history entries to keep per each warning (defaults to 50, disabled if negative) |
| maxEntries | Number | No | The maximum number of warning entries to keep (defaults to 100, disabled if negative) |
| include    | Array  | No | An array of warning titles regex to include. |
| exclude    | Array  | No | An array of warning titles regex to exclude. |
