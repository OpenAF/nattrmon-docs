---
layout: default
title: Schedulers
parent: Inputs
grand_parent: Reference
---
# nInput Schedulers

**From**: https://github.com/OpenAF/nattrmon-configs\
**oPack**: _OpenCli_

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