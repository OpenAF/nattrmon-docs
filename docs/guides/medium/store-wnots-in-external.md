---
layout: default
title: Store warning notifications externally
parent: Medium
grand_parent: Guides
---

# Store warning notifications externally

When deploying nAttrMon on ephemeral filesystems (such as a container filesystem) keeping track of the nAttrMon Warning (e.g. email, notifications, etc...) output notifications can become a challenge. For example, if channel persistance (_NEED_CH_PERSISTENCE_ parameter) is set to true outputing warnings through email, after the notification email has been sent a record of that is kept on the container filesystem; if the container for some reason restarts thatn record is lost and a new email is sent. To avoid this the nAttrMon warning notifications (WNOTS) need to be persisted externally.

This can typically be achieved by using an OpenAF channel with external persistance (e.g. S3, ElasticSearch, MongoDB, etc.). Here is an example of nAttrMon configuration for S3 and ElasticSearch. The choice is ultimately driven by the each implementation details.

To change where nAttrMon keeps the warning notifications data you need to use the CHANNEL_WNOTS string parameter. It can be set as an environment variable, a nattrmon.yaml settings parameter or __NAM_CHANNEL_WNOTS in nattrmonInit for [single mode](../../concepts/nAttrMon-single-mode.html#nattrmon-daemon-vs-single-mode))

The CHANNEL_WNOTS string parameter is a JSON based string mainly composed of two entries:

* **type** - the OpenAF channel type
* **options** - a map with the corresponding OpenAF channel type options

For the available options for each type do refer to the OpenAF channel type you plan to use's documentation. 

> When a CHANNEL_WNOTS is used the notifications tracking data is no longer kept in the CHANNEL_WARNS. This can be helpful since CHANNEL_WNOTS gets less frequent updates (just when the notification happens or when it's deleted for housekeeping) than CHANNEL_WARNS were the warning data is kept updated.

## Example using ElasticSearch

If you are using a nAttrMon container with [OpenAF's sBuckets](https://docs.openaf.io/docs/concepts/sBuckets) all you have to do is to set the ElasticSearch details on a sBucket (in this exmaple a file) and then set the container enviroment variable CHANNEL_WNOTS.

Example of setting a sBucket secrets file:

```yaml
nattrmon:
  elastic:
    url : http://elasticdb.internal:9200
    user: ...
    pass: ...

```

Example of setting the CHANNEL_WNOTS enviroment variable in Kubernetes:

```yaml
   # ...
    env:
    - name : CHANNEL_WNOTS
      value: "{ type: 'elasticsearch', options: { index: 'wnots', secKey: 'elastic', secBucket: 'nattrmon', secFile: 'elastic.yaml' } }"
```

## Example using S3

On the latest S3 oPack (> 20240804) it's possible to use a "s3" OpenAF channel type. This can be used, of course, for CHANNEL_WNOTS. 

Here is a simple example using [single mode](../../concepts/nAttrMon-single-mode.html#nattrmon-daemon-vs-single-mode):

```yaml
init:
  nattrmonInit: &INIT
    __NAM_LOGCONSOLE         : true
    __NAM_NOPLUGFILES        : true
    __NAM_LIBS               : s3.js
    __NAM_CHANNEL_WNOTS      : "{ type: 's3', options: { s3url: 'https://play.min.io:9000', s3bucket: 'nam-test', s3accessKey: 'Q3AM3UQ867SPQQA43P2F', s3secretKey: 'zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG', s3prefix: "nattrmon/wnots", multifile: true, gzip: true } }"
    # ...

nattrmon: &NATTRMON
  # ------
  # INPUTS
  - input:
      name: Input test
      cron: "*/5 * * * * *"
      exec: |
        return { "Test 1": now() }
  
  # -----------
  # VALIDATIONS
  - validation:
      name         : Validation generic
      chSubscribe  : nattrmon::cvals
      waitForFinish: true
      execFrom     : nValidation_Generic
      execArgs     :
        checks:
        - attrPattern      : "Test \\d+"
          expr             : |
            {{value}} > 0
          warnLevel        : MEDIUM
          warnTitleTemplate: "Problem with {{name}}"
          warnDescTemplate : "{{name}} had value {{value}}"

  # ------
  # OUTPUT
  - output:
      name       : Output Debug Inputs
      chSubscribe: nattrmon::cvals
      exec       : |
        _$(args.v, "args.v").$_()
        print("VAL  | " + ow.format.toCSLON(args.v, true))

  - output:
      name       : Output Debug Warns
      chSubscribe: nattrmon::warnings
      exec       : |
        _$(args.v, "args.v").$_()
        var warns = isArray(args.v) ? args.v : [args.v]
        var id    = sha1("Output Debug Warns")
        warns.forEach(w => {
          try {
            if (!nattrmon.isNotified(w.title, w.level + id)) {
              print("WARN | " + ow.format.toCSLON(args.v, true))
              warns.forEach(w => nattrmon.setNotified(w.title, w.level + id) )
            }
          } catch(e) {
            if (String(e).indexOf("Warning 'undefined' not found") >= 0) {
              logWarn(`Output Debug Warns | Warning '${w.title}' not found`)
            } else {
              logErr("Output Debug Warns | " + e)
            }
          }
        })

  - output:
      name       : Output Warns HK
      chSubscribe: nattrmon::warnings
      execFrom   : nOutput_HK
      execArgs   :
        warningDeleteAfter: 1

# -------------------------
# -------------------------

todo:
- name: nAttrMon Prepare shutdown
- name: nAttrMon Init
  args: *INIT

- name: Add plugs
#- name: nAttrMon Run Single
  #args:
  #  onStart:
  #  - job 1
  #  - job 2
  #  onStop:
  #  - job 3
  #  - job 4
  
# Comment "Run Single" and uncomment "Start" for daemon mode
- nAttrMon Start

include:
- oJob_nAttrMon.yaml

ojob:
  # Uncomment "daemon" for daemon mode
  daemon      : true
  opacks      :
  - openaf: 20210412
  - S3
  catch       : "logErr(job.name + ' | ' + exception + ' | ' + ow.format.toSLON(args));"
  logToConsole: false   # change to true for debugging

jobs:
# ---------------
- name: Add plugs
  deps: nAttrMon Init
  to  : nAttrMon Add Plugs
  args: *NATTRMON
```

Notice the use of the new LIBS parameter (since nAttrMon >= 20240804). This allows nAttrMon to preload the s3.js library from OpenAF's S3 oPack before the definition of CHANNEL_WNOTS is evaluated.

> For an environment variable implementation you would need to set the LIBS and the CHANNEL_WNOTS environment variables.

### Note on the S3 channel options

The choice between the values of the option _multifile_ of the S3 OpenAF channel real depends on the specific implementation. 

By default, in the absence of the _multifile_ option, or setting it to false, only one JSON or YAML file will be kept at the specific S3 bucket. But everytime there is an update, or a need to check (nattrmon.isNotified function), that will mean the entire file will be retrieved and/or rewritten. 

By using ```multifile=true``` each notification entry becomes an individual object in the S3 bucket and it's checked using the much smaller index file. The corresponding data object is only changed when a change exist for that specific warning. This might be an advantage if your S3 vendor only offers an eventual consistent S3 implementation and there might be multiple concorrent notification changes on the same nAttrmon or accross several containers.

On the hand, it might generate more small files and more operations per second which might also not be desirable tradeoff.