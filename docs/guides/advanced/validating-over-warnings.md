---
layout: default
title: Validating over existing warnings
parent: Advanced
grand_parent: Guides
---

# Validating over existing warnings

The validation [nValidation_Generic](../../reference/validations/nAttrMon-nValidation-Generic) is commonly used for validation of attribute values but it can also be used for validating over other warnings.

For example, you may want to "escalate" that two other warnings exist. Here is an example:

```yaml
validation:
    name         : Validation over warnings
    chSubscribe  : nattrmon::warnings
    waitForFinish: true
    execFrom     : nValidation_Generic
    execArgs     :
    checks:
    - titlePattern     : "Problem with .+"
      expr             : |
        '{{{warn 'Problem with Test 1'}}}'.length > 0 && 
        '{{{warn 'Problem with Test 2'}}}'.length > 0
      warnLevel        : HIGH
      warnTitleTemplate: "Escalation!"
      warnDescTemplate : "Escalating because there is more than one problem."
```

Running everything together:

```yaml
# nAttrMon single run ojob
# Usage:
#   ojob nAttrmon_single.yaml.sample
#
init:
  # These parameters are similar to nattrmon.yaml
  nattrmonInit: &INIT
    __NAM_DEBUG              : false  # change to true for debugging
    __NAM_NEED_CH_PERSISTENCE: false
    __NAM_LOGCONSOLE         : true
    __NAM_NOPLUGFILES        : true
    #__NAM_BUFFERCHANNELS     : true
    #__NAM_BUFFERBYNUMBER     : 100
    #__NAM_BUFFERBYTIME       : 1000

nattrmon: &NATTRMON
  # ------
  # INPUTS
  # dummy attribute 1
  - input:
      name: Input test 1
      cron: "*/2 * * * * *"
      exec: |
        return { "Test 1": now() }

  # dummy attribute 2
  - input:
      name: Input test 2
      cron: "*/2 * * * * *"
      exec: |
        return { "Test 2": now() }
  
  # -----------
  # VALIDATIONS
  # simple validation over an attribute
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

  # validation over other two warnings
  - validation:
      name         : Validation over warnings
      chSubscribe  : nattrmon::warnings
      waitForFinish: true
      execFrom     : nValidation_Generic
      execArgs     :
        checks:
        - titlePattern     : "Problem with .+"
          debug            : false
          expr             : |
            '{{{warn 'Problem with Test 1'}}}'.length > 0 && 
            '{{{warn 'Problem with Test 2'}}}'.length > 0
          warnLevel        : HIGH
          warnTitleTemplate: "Escalation!"
          warnDescTemplate : "Escalating because there is more than one problem."

  # ------
  # OUTPUT
  # debug output of inputs
  - output:
      name       : Output Debug Inputs
      chSubscribe: nattrmon::cvals
      exec       : |
        _$(args.v, "args.v").$_();
        print("VAL  | " + ow.format.toSLON(args.v));

  # debug output of warnings
  - output:
      name       : Output Debug Warns
      chSubscribe: nattrmon::warnings
      exec       : |
        _$(args.v, "args.v").$_();
        print("WARN | " + ow.format.toSLON(args.v));

# -------------------------
# -------------------------

todo:
- name: nAttrMon Prepare shutdown
- name: nAttrMon Init
  args: *INIT

- name: Add plugs
- nAttrMon Start

include:
- oJob_nAttrMon.yaml

ojob:
  # Uncomment "daemon" for daemon mode
  daemon      : true
  opacks      :
  - openaf: 20241117
  catch       : "logErr(job.name + ' | ' + exception + ' | ' + ow.format.toSLON(args));"
  logToConsole: false   # change to true for debugging

jobs:
# ---------------
- name: Add plugs
  deps: nAttrMon Init
  to  : nAttrMon Add Plugs
  args: *NATTRMON
```