---
layout: default
title: RAIDMemory
parent: Inputs
grand_parent: Reference
---
# nInput RAIDMemory

**From**: https://github.com/OpenAF/nattrmon-configs\
**oPack**: _OpenCli_

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