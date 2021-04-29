---
layout: default
title: WarnsHistory
parent: Outputs
grand_parent: Reference
---
# nOutput WarnsHistory

This output will create an extra OpenAF channel to keep an history of warnings changes across time.

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
