---
layout: default
title: nAttrMon
parent: Inputs
grand_parent: Reference
---
# nInput nAttrMon

This input provides internal nAttrMon metrics:

* Internal Java heap memory (max, total, free, used, nonHeadUsed, nonHeapCommitted, nonHeapInit and gcCount & gcTime per arch gc type)
* Internal threads (active, total, peak, daemon and count per state (waiting, runnable, timed_waiting, etc...))
* Internal list of active plugs (PS)
* Internal list of plugs (including numberOfExecs, avgExecTimeInMs, lastExectTimeInMs, numberRunning and numberOfExecsInError)

Example of use of the execArgs:

```yaml
input: 	
  name         : Get nattrmon metrics
  cron         : "*/5 * * * *"
  waitForFinish: true
  onlyOnEvent  : false
  execFrom     : nInput_nAttrMon
  execArgs     :
    # n/a
````

| execArgs | Type | Mandatory | Description | 
| -------- | ---- | --------- |:----------- |
| n/a | n/a | n/a | n/a |