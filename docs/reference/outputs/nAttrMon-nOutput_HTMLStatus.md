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

![HTML Status](../../../images/nOutput_HTMLStatus_1.png "HTML Status")

## Example of use of the execArgs

````yaml
output:
  name       : Output HTML Status
  chSubscribe: nattrmon::warnings
  execFrom   : nOutput_HTMLStatus
  execArgs   :
    file          : myStatus.txt 
    format        : table
    # path          : /myConfig   # /myConfig/object.assets/noutputstatus
    # levelsIncluded:
    # - HIGH
    # - MEDIUM
    # - LOW
    # - INFO
    # redLevels     :
    # - HIGH
    # yellowLevels  :
    # - MEDIUM
    # greenLevels   :
    # - LOW
    # - INFO
    controls      :
    - App 1/.+
    - App 2/Status
    # redText       : NOT OK
    # yellowText    : Issues
    # greenText     : OK
    

````

## Description of execArgs

| execArgs   | Type   | Mandatory | Description |
|:-----------|:------:|:---------:|:------------|
| file       | String | Yes | The output filename to generate. |
| format     | String | No | The output filename format to generate. Possible values are: html, table, yaml, json, prettyjson, slon. Defaults to "html". |
| path       | String | No | A path to custom object.assets for noutputstatus (e.g. css, images, etc...) |
| levelsIncluded | Array | No | The warning levels to include on the status (defaults to ["HIGH", "MEDIUM", "LOW", "INFO]) |
| redLevels | Array | No | The warning levels that will be interpreted as a "red" level (defaults to [ "HIGH" ]) |
| yellowLevels | Array | No | The warning levels that will be interpreted as a "yellow" level (defaults to [ "MEDIUM" ]) |
| greenLevels | Array | No | The warning levels that will be interpreted as a "green" level (defaults to [ "LOW", "INFO" ]) |
| controls | Array | No | An array of regular expressions to select the warning titles that will be included in the status output (defaults to all) |
| redText | String | No | Customizes the text to use to described a "red" level (defaults to "NOT OK") |
| yellowText | String | No | Customizes the text to use to described a "yellow" level (defaults to "Issues" ) |
| greenText | String | No | Customizes the text to use to described a "green" level (defaults to "OK") |