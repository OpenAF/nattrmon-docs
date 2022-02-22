---
layout: default
title: FMSRuleStatus
parent: Inputs
grand_parent: Reference
---
# nInput FMSRuleStatus

_tbc_

Example:

````yaml
input:
   name         : Fraud rules status
   cron         : "*/30 * * * *"
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nInput_FMSRulesStatus
   execArgs     :
     key: FMS
````