---
layout: default
title: Filesystem
parent: Inputs
grand_parent: Reference
---
# nInput Filesystem

_tbc_

Example:

````yaml
input:
   name         : Filesystem volumes check
   cron         : "*/1 * * * *"
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nInput_Filesystem
   execArgs     :
     attrTemplate: Filesystem/Volumes
     volumeNames :
      - /some/path/1
      - /some/path/2
````