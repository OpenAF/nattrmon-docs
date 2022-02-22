---
layout: default
title: OSInfo
parent: Inputs
grand_parent: Reference
---
# nInput OSInfo

_tbc_

Example:

````yaml
input:
   name         : OS Info
   cron         : "*/1 * * * *"
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nInput_OSInfo
   execArgs     :
     attrTemplate: Server status/OS
````