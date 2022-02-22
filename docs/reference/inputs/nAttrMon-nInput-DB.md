---
layout: default
title: DB
parent: Inputs
grand_parent: Reference
---
# nInput DB

_tbc_

Example:

````yaml
input:
  name         : Test input db
  cron         : "*/1 * * * *"
  waitForFinish: true
  onlyOnEvent  : true
  execFrom     : nInput_DB
  execArgs     :
     key: MYAPP_DAT
     sqls:
        Database/Test 1  : >
           SELECT user FROM dual

        Database/Test 2  : >
           SELECT level "My level"
           FROM dual connect by level <= 5
````
