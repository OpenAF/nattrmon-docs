---
layout: default
title: Simple nAttrMon debugging
parent: Medium
grand_parent: Guides
---

# Simple nAttrMon debugging

One of the common questions when using nAttrMon is: "has my plug even executed?"

There are several approaches to find the answer being, one of the most common ones, just adding a _print_ statement on your nAttrMon plug or turning the specific plug debug flag, if it exists. There also functions to just test specific plugs inside and outside nAttrMon.

Nevertheless, has your nAttrMon's configuration grows it might become difficult to understand: _what is running at which moment? what is taking so much CPU? why it was fast and now it's slow?_

Almost always the answer relies on what is being executed at each time.

## Channel 'ps' and others

Wouldn't you like to just execute a _"ps"_ (similar to the unix 'ps' command) on nAttrMon and just see what's running? Well, you can.

Just add the channels output plug (check on outputs.disabled for *channels.yaml).

Now, on an openaf-console, just execute:

````javascript
> $ch("ps").createRemote("http://nattrmon:nattrmon@your.server:8090/chs/ps");
> table $ch("ps").getAll();
    name    | type |                uuid                |         start
------------+------+------------------------------------+------------------------
Input Test 1|inputs|1fc61235-97d6-068f-e705-5693712aba75|2019-10-01T09:42:10.027Z
````

In this example there was only one plug executing but it will return as many as there is currently running. It's useful to understand which plugs take too long to execute. The plugs that you find here for a long period of time probably need to be rethinked: _is the plug taking longer than expected? is it ocasional?_

There is another channel that comes also _to the rescue_ to answer these questions:

````javascript
> $ch("plugs").createRemote("http://nattrmon:nattrmon@your.server:8090/chs/plugs");
> $ch("plugs").getAll();
[...]
+-----+----------+--------------------------+----------------+
| [1] |    meta: |                    name: | Input Test 1   |
|     |          |             description: |                |
|     |          |                    type: | inputs         |
|     |          |                category: | uncategorized  |
|     |          |                    cron: | */10 * * * * * |
|     |          |            timeInterval: | -1             |
|     |          |           waitForFinish: | true           |
|     |          |             onlyOnEvent: | true           |
+-----+----------+--------------------------+----------------+
|     |    args: |            {}            | -              |
+-----+----------+--------------------------+----------------+
|     |    last: | 2019-10-01T09:49:02.521Z |                |
|     | created: | 2019-10-01T09:46:25.237Z |                |
+-----+----------+-------------------------------------------+
|     |   stats: |        lastExecTimeInMs: | 2501           |
|     |          |         avgExecTimeInMs: | 2500.5         |
|     |          |           numberOfExecs: | 16             |
|     |          |    numberOfExecsInError: | 0              |
|     |          |         numberOfRunning: | 1              |
+-----+----------+--------------------------+----------------+
[...]
````

_Note: if you have the http output activated you can also see this info in the "Plugs" tab on your browser_

This information allows you to see how the plug is registered, when was the "last" time it executed, how much time it took (yes, I added a sleep on the dummy test input), the average execution time, how many times it executed without and with errors and how many of them are currently being executed.

## Debug flag

There is also another way: if you edit or create a nattrmon.yaml configuration file (based on the nattrmon.yaml.sample) there are three options that might help you while debugging:

````yaml
LOGCONSOLE: true
LOG_ASYNC: false
DEBUG: true
````

_Note: do uncomment them if they are commented. And don't forget to comment again after debugging._

The first flag, __LOGCONSOLE__, will not create log files and just print the log to the stdout. Of course this isn't mandatory but you probably don't want to fill up your logs with debug information.

The second flag, __LOG_ASYNC__, determines if nAttrMon's logs should be _"written when possible without stopping nAttrMon"_ (option true, the default) OR if nAttrMon's logs should be _"written immediatelly stopping nAttrMon if needed"_.

The third flag, __DEBUG__, will turn on all the logging debug messages. It will show you a lot more information, __including which plug is being executed__.

````
2019-10-01 09:57:18.344 | INFO | DEBUG | nAttrMon monitor plug
2019-10-01 09:57:18.367 | INFO | DEBUG | Added plug system monitor
2019-10-01 09:57:18.377 | INFO | DEBUG | Starting at fixed rate for system monitor - 30000
2019-10-01 09:57:18.393 | INFO | DEBUG | Creating a thread for system monitor with uuid = ff7c708c-ba70-4371-b764-96f2d1c44eed
2019-10-01 09:57:18.408 | INFO | DEBUG | nAttrMon start load plugs
2019-10-01 09:57:18.417 | INFO | DEBUG | Ignore list: []
2019-10-01 09:57:18.857 | INFO | Loading inputs: /openaf/nAttrMon/config/inputs/01.test.yaml
2019-10-01 09:57:18.870 | INFO | DEBUG | Added plug Input Test 1
2019-10-01 09:57:18.875 | INFO | DEBUG | nAttrMon exec input plugs
2019-10-01 09:57:18.904 | INFO | DEBUG | nAttrMon exec output plugs
2019-10-01 09:57:18.907 | INFO | DEBUG | nAttrMon exec validation plugs
2019-10-01 09:57:18.910 | INFO | DEBUG | nAttrMon restoring snapshot
2019-10-01 09:57:18.917 | INFO | nAttrMon started.
2019-10-01 09:57:20.010 | INFO | DEBUG | Executing 'Input Test 1' (0b7787a8-1135-a823-705b-1574a2eed64e)
2019-10-01 09:57:30.005 | INFO | DEBUG | Executing 'Input Test 1' (0b7787a8-1135-a823-705b-1574a2eed64e)
2019-10-01 09:57:40.007 | INFO | DEBUG | Executing 'Input Test 1' (0b7787a8-1135-a823-705b-1574a2eed64e)
2019-10-01 09:57:48.395 | INFO | DEBUG | Executing 'system monitor' (ff7c708c-ba70-4371-b764-96f2d1c44eed)
2019-10-01 09:57:50.009 | INFO | DEBUG | Executing 'Input Test 1' (0b7787a8-1135-a823-705b-1574a2eed64e)
2019-10-01 09:58:54.626 | INFO | nAttrMon stopped.
````

On this example we just added a single input plug "Input Test 1" to be executed every 10 seconds. You can see those executions and also the "system monitor". The "system monitor" is a special plug to ensure nAttrMon is running (if not it will restart itself).