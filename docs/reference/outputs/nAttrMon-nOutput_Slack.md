---
layout: default
title: Slack
parent: Outputs
grand_parent: Reference
---
# nOutput Slack

This output will output warnings to a Slack webhook. Optionally you can specify a regular expression to filter by warning title, a warning level and if the slack message should use simple API or not.

## Example of use of the execArgs

````yaml
output:
  name       : Output warnings slack
  chSubscribe: nattrmon::warnings
  execFrom   : nOutput_Slack
  execArgs   :
    notifications:
    - warnLevel: MEDIUM
      #warnTitle: My warning title 
      webhook  : https://hooks.slack.com/services/ABCD1234A/A01234B2C3D/aB1Cd23cdE4FGHIjkLmnnAA
      #simple   : true
````

## Description of execArgs

| execArgs   | Type   | Mandatory | Description |
|:-----------|:------:|:---------:|:------------|
| warnLevel  | String | No | The warning level (defaults to HIGH) |
| warnTitle  | String | No | A regular expression to match a warning title (defaults to all) |
| webhook    | String | Yes | The incoming webhook URL provided by Slack |
| simple     | Boolean | No | If true the Slack output will just use plain text instead of the Slack block-based API (defaults to false). |