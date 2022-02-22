---
layout: default
title: LogErrorAgg
parent: Inputs
grand_parent: Reference
---
# nInput LogErrorAgg

_tbc_

Example:

````yaml
input:
   name         : Log error check
   cron         : "*/1 * * * *"
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nInput_LogErrorAgg
   execArgs     :
     name   : Main log log.txt
     file   : log.txt
     pattern: ".+ERROR\\s+(\\S+).+"
````