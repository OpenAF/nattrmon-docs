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

_tbc_

## Example of use of the execArgs

_tbc_