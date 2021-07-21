---
layout: default
title: HK
parent: Outputs
grand_parent: Reference
---
# nOutput HK

 Perform housekeeping on current values and/or last values and/or warnings (no matter the current level).

 _since version: 20210621_

## Example of use of the execArgs

````yaml
output:
  name       : Output HK
  cron       : @daily
  execFrom   : nOutput_HK
  execArgs   :
    # 1440 minutes = 1 day
    warningUpdateDeleteAfter  : 1440 
    # 10080 minutes = 7 days
    attributeUpdateDeleteAfter: 10080
    # warningDeleteAfter        : 1440
    # attributeUpdateDeleteAfter: 10080
    # include   :
    # - Problem with A
    # - Problem with B
    # exclude   :
    # - Problem with C    
````

## Description of execArgs

| execArgs   | Type   | Mandatory | Description |
|:-----------|:------:|:---------:|:------------|
| warningUpdateDeleteAfter | Number | no | Number of minutes after the last update of a warning that determines that it should be deleted. |
| attributeUpdateDeleteAfter | Number | no | Number of minutes after the last update of a the current value of an attribute that determines that it should be deleted. |
| warningDeleteAfter | Number | no | Number of minutes after the creation of a warning that determines that it should be deleted. |
| attributeDeleteAfter | Number | no | Number of minutes after the last check of an attribute that determines that it should be deleted. |
| include    | Array  | No | Array of regex attributes/warnings to include on output. |
| exclude    | Array  | No | Array of regex attributes/warnings to exclude from output. |
