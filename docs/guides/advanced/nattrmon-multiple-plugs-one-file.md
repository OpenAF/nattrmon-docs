---
layout: default
title: nAttrMon multiple plugs in one file
parent: Advanced
grand_parent: Guides
---

# nAttrMon multiple plugs in one file

When adding any nAttrMon plug (either inputs, validations or outputs), the advisable setup is to have a structured set of files on a folder hierarchy. In this structure each file is usually a single plug definition.

Nevertheless once you get more advanced setups there are cases where you might want to have more than one plug per file taking advantage of the [plug "ignore"/"disabling" mechanisms](https://github.com/OpenAF/nAttrMon/wiki/nAttrMon-nattrmonignore).

Let's take a simple example of a nAttrMon input plug:

````yaml
input:
  name         : Database queries
  cron         : "*/10 * * * *"
  waitForFinish: true
  onlyOnEvent  : true
  execFrom     : nInput_DB
  execArgs     :
     key: MYDB
     sqls:
        Database/Status XYZ: >
           SELECT control "Control", value "Value"
           FROM status
           WHERE control in ('X', 'Y', 'Z')

        Database/Status ABC: >
           SELECT control "Control", value "Value"
           FROM status
           WHERE control in ('A', 'B', 'C')
````

In this example both queries will execute every 10 minutes. But you might want different cron schedules temporarily to find some issue on ABC controls. 

Usually you would create two files, comment the ABC query on the first and just have the ABC on the second. But now if you have to quickly ignore all database queries you will have to remember the extra temporary plug you created before. Could there be a better alternative?

The answer is yes. You can keep both SQLs on the same file and temporarily have different cron settings:


````yaml
input:
  #--------------------------------
  - name         : Database queries
    cron         : "*/10 * * * *"
    waitForFinish: true
    onlyOnEvent  : true
    execFrom     : nInput_DB
    execArgs     :
       key: MYDB
       sqls:
          Database/Status XYZ: >
             SELECT control "Control", value "Value"
             FROM status
             WHERE control in ('X', 'Y', 'Z')

          #Database/Status ABC: >
          #   SELECT control "Control", value "Value"
          #   FROM status
          #   WHERE control in ('A', 'B', 'C')

  #--------------------------------------------
  - name         : Database queries (temporary)
    cron         : "*/1 * * * *"
    waitForFinish: true
    onlyOnEvent  : true
    execFrom     : nInput_DB
    execArgs     :
       key: MYDB
       sqls:
          Database/Status ABC : >
             SELECT control "Control", value "Value"
             FROM status
             WHERE control in ('A', 'B', 'C')             
````

What's the difference? Instead of defining a map, if you define an array nAttrMon it will consider two plugs instead of one. So now you can control the temporary different cron setting by just commenting lines while keeping everything in the same file.

_Note: always have different plug names, otherwise the last will overwrite the previous._