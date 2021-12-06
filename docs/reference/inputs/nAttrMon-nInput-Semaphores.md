---
layout: default
title: Semaphores
parent: Inputs
grand_parent: Reference
---

_tbc_

Example:

````yaml
input:
   name         : Semaphores
   cron         : "*/5 * * * * *"
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nInput_CBPMSemaphores
   execArgs     :
      chKeys      : raidServers
````