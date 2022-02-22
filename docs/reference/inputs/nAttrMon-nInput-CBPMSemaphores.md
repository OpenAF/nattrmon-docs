---
layout: default
title: CBPMSemaphores
parent: Inputs
grand_parent: Reference
---
# nInput CBPMSemaphores

**From**: https://github.com/OpenAF/nattrmon-configs\
**oPack**: _OpenCli_

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