---
layout: default
title: nAttrMon kill after an amount of minutes
parent: Beginner
grand_parent: Guides
---

# nAttrMon kill after an amount of minutes

On a nAttrMon configuration you might have several inputs, validations and outputs. You might use the _waitForFinish_ parameter to ensure another instance of the same plug doesn't start while the previous is still running.

This is specially true usually on input plugs that depend on the reply of external systems (e.g. database, sftp, a REST service, etc&#46;&#46;&#46;).

But what if there isn't any timeout in place or you simply don't want that plug to run forever keeping _nAttrMon_ waiting? You can use the setting _killAfterMinutes_.

## Example

````yaml
input:
  name            : Big queries
  # running every hour
  cron            : "0 */1 * * *"
  onlyOnEvent     : true
  # make sure that just one is executing
  waitForFinish   : true
  # make sure if doesn't execute for more than 30 minutes
  killAfterMinutes: 30
  execFrom        : nInput_DB
  execArgs        :
    key : myBigDatabase
    sqls:
      
      Database/Big query 1: |
        SELECT stuff 
        FROM something 
        -- [...]
````

In this case, every hour, _nAttrMon_ will try to execute the "Big queries" but since _killAfterMinutes_ is defined it will interrupt/terminate this input execution if it's longer than 30 minutes.