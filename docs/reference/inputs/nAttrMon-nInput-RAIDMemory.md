---
layout: default
title: RAIDMemory
parent: Inputs
grand_parent: Reference
---

_tbc_

Example:

````yaml
input:
   name         : RAID Memory
   cron         : "*/30 * * * * *"
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nInput_RAIDMemory
   execArgs     :
      chKeys      : raidServers
````