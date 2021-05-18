---
layout: default
title: Single mode
parent: Concepts
grand_parent: nAttrMon docs
---

# Single mode

## nAttrmon daemon vs single mode

The usual nAttrMon running mode is to be launched as a daemon/service. But there are specific situations where it might be more helpful to run it in a "single mode".

When nAttrMon is running as a daemon:

* Several **input plugs** will be running at specific time intervals or following a cron expression or being triggered some other way (e.g. channel subscription in more advanced setups)
* Whenever these input plugs produce an output, **output plugs** will be executed to handle that result. 
* If there is also **validation plugs** they will also be executed to handle the input plug's results eventually producing warnings that will also trigger specific outputs to deal with those warnings (e.g. sending an email with a list of warnings).
* Execution continues waiting for **input plugs** to execute when needed.

In "single mode" the idea is that:

* **Input plugs** will be immediately executed upon nAttrMon "single mode" startup
* The input plugs will trigger any correspondent **output plugs** and **validation plugs**
* The **execution will be terminated** after all input plugs have been executed and all output & validation triggers have been also executed.

In this "single mode" nAttrMon can be executed directly from a **crontab**, or a scheduled process execution, producing immediate results. For this nAttrMon uses an OpenAF functionality called [oJob](https://docs.openaf.io/docs/concepts/oJob.html).

**oJob** brings several functionalities that are helpful for "single mode" nAttrMon such as: 
* handling specific logging requirements
* external version dependency on oPacks 
* checking for "longer-than-expected" executions if necessary
* ability to have all the input, output and validation plugs in **one single file** (althought not mandatory but usefull for small nAttrMon deployments).

## Configuring it

On the nAttrMon installation you will find a _nAttrMon_single.yaml.sample_ file. Copy this file to a new *.yaml* file to change accordingly with your needs.

> NOTE: as with any oJob file definition you can also describe it in JSON althougt you would lose a lot of YAML functionality that makes it easier to write and maintain these files.

The following sub-sections explain the specific parts of this file (an oJob file):

### init

On the __init map__ you have __NAM_* parameters. These are equivalent to the parameters that you would set on the regular *nattrmon.yaml* file only prefixed with "__NAM_":

````yaml
init:
  # These parameters are similar to nattrmon.yaml
  nattrmonInit: &INIT
    __NAM_DEBUG              : false  # change to true for debugging
    __NAM_NEED_CH_PERSISTENCE: false
    __NAM_LOGCONSOLE         : true
````

### nattrmon

This __nattrmon map__ entry is an array of nAttrMon plugs. In here you can define as many input, validation and output plugs as you like. Each will follow the exact same structure as the [regular nAttrMon plug definition](docs/howto/Examples) for YAML files.

````yaml
nattrmon: &NATTRMON
  # ------
  # INPUTS
  - input:
      name: Input test
  # [...]

  # -----------
  # VALIDATIONS
  - validation:
      name         : Validation generic
  # [...]

  # ------
  # OUTPUT
  - output:
      name         : Output email
  # [...]

  - output:
      name         : Output slack
  # [...]
````

Each array entry should be a map with the key _"input"_, _"validation"_ or _"output"_. If you need more than one just add another array entry (as seen on the example for email and slack).

### other entries

There are other entries on the sample file for advance use. If you know oJob you will understand the _todo_, _include_, _ojob_ and _jobs_ section which can be customized for your specific needs. The _ojob_ section can be particularly helpful for using oJob functionality. 

## Using it

To execute you have to ensure that nAttrMon oPack is installed on the OpenAF deployment you are using. Then simply execute:

````bash
[path to OpenAF folder]/ojob myNAttrMonSingleMode.yaml 
````

To set it up on a crontab entry, edit crontab (_crontab -e_) and add something similar to:

````bash
*/10 * * * *    [path to OpenAF folder]/ojob [path to single mode yaml]/myNAttrMonSingleMode.yaml 2>&1 >> [path to single mode yaml]/myNAttrMonSingleMode.out
````