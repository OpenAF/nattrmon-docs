---
layout: default
title: Schedulers
parent: Inputs
grand_parent: Reference
---

_tbc_

Example:

````yaml
input:
   name         : RAID Schedulers
   cron         : "*/1 * * * *"
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nInput_Schedulers
   execArgs     :
      keys        :
       - Fraud
       - CM
````