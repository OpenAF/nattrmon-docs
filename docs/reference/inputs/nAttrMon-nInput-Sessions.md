---
layout: default
title: Sessions
parent: Inputs
grand_parent: Reference
---

_tbc_

Example:

````yaml
input:
   name         : Sessions
   cron         : "*/5 * * * * *"
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nInput_Sessions
   execArgs     :
      chKeys      : raidServers
````