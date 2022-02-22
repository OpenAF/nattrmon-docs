---
layout: default
title: CBPMRunningFlows
parent: Inputs
grand_parent: Reference
---
# nInput CBPMRunningFlows

**From**: https://github.com/OpenAF/nattrmon-configs\
**oPack**: _OpenCli_

_tbc_

Example:

````yaml
input:
   - name            : Flows in error
     cron            : "*/2 * * * * *"
     onlyOnEvent     : true
     waitForFinish   : true
     killAfterMinutes: 5
     execFrom        : nInput_CBPMRunningFlows
     execArgs        :
       chKeys      : raidServers
       status      : ERROR
       hoursToCheck: 120
       attrTemplate: Flows/Flows errors

   - name            : Flows running
     cron            : "*/2 * * * * *"
     onlyOnEvent     : true
     waitForFinish   : true
     killAfterMinutes: 5
     execFrom        : nInput_CBPMRunningFlows
     execArgs        :
       chKeys      : raidServers
       status      : STARTED
       hoursToCheck: 120
       attrTemplate: Flows/Flows running
````