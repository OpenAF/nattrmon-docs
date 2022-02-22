---
layout: default
title: FilesystemCount
parent: Inputs
grand_parent: Reference
---
# nInput FilesystemCount

_tbc_

Example:

````yaml
input:
   name         : Filesystem check
   cron         : "*/1 * * * *"
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nInput_FilesystemCount
   execArgs     :
     attrTemplate: Filesystem/Test
     folders     :
      - name   : Input
        folder : /data/loading/test/in
        pattern: .*\.csv
      - name   : Error
        folder : /data/loading/test/err
        pattern: .*
      - name   : Done
        folder : /data/loading/test/done
        pattern: .*\.csv
````