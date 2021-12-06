---
layout: default
title: H2
parent: Outputs
grand_parent: Reference
---

_tbc_

Example:

````yaml
output:
   name         : H2 history
   description  : Provides to nOutput_HTTP a history service to access the previous values for each attribute.
   chSubscribe  : nattrmon::cvals
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nOutput_H2
   execArgs     :
      db: "{{NATTRMON_HOME}}/config/nattrmon_db"
````