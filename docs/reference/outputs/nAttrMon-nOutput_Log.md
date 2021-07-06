---
layout: default
title: Log
parent: Outputs
grand_parent: Reference
---
# nOutput Log

Writes/appends attribute values to a log file following a provided template.

## Example of use of the execArgs

````yaml
output:
  name       : Output attribute log
  chSubscribe: nattrmon::cvals
  execFrom   : nOutput_Slack
  execArgs   :
    filename: attributes_values.log
    #outputTemplate: "{{name}}(date={{date}}) {{value}}"
````

## Description of execArgs

| execArgs   | Type   | Mandatory | Description |
|:-----------|:------:|:---------:|:------------|
| filename  | String | No | The log filename where attribute values should be appended to. |
| outputTemplate  | String | No | The OpenAF template-based line output (using variables name, value and date) |