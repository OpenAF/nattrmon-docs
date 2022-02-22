---
layout: default
title: AFPing
parent: Inputs
grand_parent: Reference
---
# nInput_AFPing

This input will send an AF Ping operation to the designated RAID servers effectively determining if the RAID server is able to process the request and reply despite having the endpoint up.

Example of use of the execArgs:

````yaml
input: 	
  name         : RAID AF Ping
  cron         : "*/5 * * * * *"
  waitForFinish: true
  onlyOnEvent  : false
  execFrom     : nInput_AFPing
  execArgs     :
    chKeys: raidServers
    #attrTemplate: Server status/Ping
    #keys:
    #single: false
    #timeout: 5000
````

| execArgs | Type | Mandatory | Description | 
| -------- | ---- | --------- |:----------- |
| keys | Array | No | A list of string keys of RAID AF object pools to be queried. The output will be an array with the list of flows, corresponding numeric dbLevel & memLevel, corresponding description "DB Level" & "Mem Level" and number of trace executions found. One attribute will be created for each key. |
| chKeys | Channel | No | Similar to keys but uses an OpenAF channel to dynamically determine the keys. |
| attrTemplate | String | No | The attribute template to use. |
| single | Boolean | No | When single=true the RAID server key won't be included (defaults to false)  |
| timeout | Number | No | Max timeout in ms when trying to perform the AF Ping operation (defaults to 5000ms)| 