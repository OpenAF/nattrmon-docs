---
layout: default
title: Sessions
parent: Inputs
grand_parent: Reference
---
# nInput Sessions

**From**: https://github.com/OpenAF/nattrmon-configs\
**oPack**: _OpenCli_

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