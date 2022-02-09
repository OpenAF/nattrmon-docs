---
layout: default
title: Adding a new validation to nAttrmon
parent: How To
grand_parent: nAttrMon docs
---

# Adding a new validation to nAttrMon

One of easiest ways to add a new validation to nAttrMon is to add a "_validation plug_" using the [nValidation_Generic](/docs/reference/validations/nAttrMon-nValidation-Generic):

````yaml
validation:
   name         : My generic validation
   chSubscribe  : nattrmon::cvals
   waitForFinish: true
   execFrom     : nValidation_Generic
   execArgs     :
      checks:
      - attribute        : testAttribute
        expr             : {% raw %}{{value.c}} == 3{% endraw %}
        warnLevel        : MEDIUM
        warnTitleTemplate: A test warning
        warnDescTemplate : {% raw %}This is just a warning given {{name}} because the values was {{value.c}}{% endraw %}
````

In this simple example you will be checking a value of the output of attribute "_testAttribute_" gather by some other nAttrMon input plug. But how can you check what are the values provided by attribute "_testAttribute_"?

## Checking the current attribute values

There are several ways but one of the most pratical and easiest it's to simply add a [nOutput_HTTP_JSON](/docs/reference/outputs/nAttrMon-nOutput_HTTP_JSON) if you haven't already. In some cases, to make it easier, you just need to copy [outputs.disabled/yaml/00.httpAll.yaml](https://github.com/OpenAF/nAttrMon/blob/master/config/outputs.disabled/yaml/00.httpAll.yaml) to the _outputs_ folder or ensure you have the proper YAML configuration added to your [single mode](/docs/concepts/nAttrMon-single-mode) definition file.

With the _/json_ endpoint you can quickly visualize what is the current value of attribute _testAttribute_:

````json
$ curl http://a.address:8090/json
[...]
  "values": {
    "testAttribute": {
      "name": "testAttribute",
      "val": {
        "a": "some description",
        "c": 3
      },
      "date": "2020-01-02T03:04:05.508Z"
    }
  },
[...]
````

So _{% raw %}{{value}}{% endraw %}_ will correspond to the map (or single value) that you see on the _"testAttribute.val"_ entry on the _values_ map.

> If the value is an array the generic validation rule will be evaluated for each array entry where _{% raw %}{{value}}{% endraw %}_ will represent each array entry value.

## Guidelines when adding a new validation

* Avoid ["alarm fatigue"](https://en.wikipedia.org/wiki/Alarm_fatigue) Choose the right warning level (_warnLevel_). See below the table for advisable warning level interpretation.
* Separate validations by domain groups. You can add rules for more than one attribute(s) in a single _nValidation_Generic_ entry but it will be easier later on to disable/enable by simply moving a validation plug file or definition.
* If you are using YAML to define it make use of the YAML anchors and aliases for repeating entries. This will help you minimize possible typos.

## Advisable warning levels

An usual temptation is to consider almost everything as warning level HIGH. The problem with that is that you usually have humans having to check why a warning level HIGH was created in the first place. Too many will just end-up causing ["alarm fatigue"](https://en.wikipedia.org/wiki/Alarm_fatigue) and the common human bahaviour of starting to ignore or lower the priority for news ones. If you really have a serious alarm on the middle of several it might get ignored.

As an example imagine that you have an application with three services: _web_, _processing_ and a _database_. Let's consider the following situations:

| Service    | Current status      | Warning    |
|------------|---------------------|------------|
| web        | up without issues   | _none_     | 
| processing | up with some errors | HIGH or MEDIUM? |
| database   | restarting          | HIGH or MEDIUM? |

Given this situation should we generate one HIGH warning for processing and a HIGH warning for the database? Well, if the database isn't offline for more than a couple of seconds it might be a _temporary problem_ thus it should actually be a MEDIUM warning. And _processing_ is also working partially probably because it's unable to temporially access the database so it should also actually generate a MEDIUM warning.

More important above all: 
* "is the solution completly down to the end user?" __no__
* "is the solution facing some issues in some functionalities?" __yes__
* "should a human be awake up during his sleep because of this?" __no__

Now consider that after a couple of minutes the restart fails and the current situation now becomes like this:

| Service    | Current status      | Warning    |
|------------|---------------------|------------|
| web        | up with issues      | MEDIUM     | 
| processing | up with persistent errors | MEDIUM     |
| database   | offline for more than double the usual restart time | HIGH |

Now let's check again:
* "is the solution completly down to the end user?" __no__
* "is the solution facing some issues in some functionalities?" __yes__
* "should a human be awake up during his sleep because of this?" __yes__

If we had rated two HIGH warnings on the previous status every minute and the human actually understood that the database was just taking a little longer to restart his attention my get diverted to other warnings (or getting back to sleep).

Let's make a final scenario where the database errors actually lead to more serious issues:

| Service    | Current status      | Warning    |
|------------|---------------------|------------|
| web        | up with issues      | MEDIUM     | 
| processing | down after several retries to the database | HIGH     |
| database   | offline for more than double the usual restart time | HIGH |

Of course, needless to say that we now have a serious issue. If the human was "bombed" with the initialy HIGH warning issues during the restart and then he got more because the database was done but _processing_ was ok what are the chances that the real problem now that _processing_ and _database_ are down that a human would ignore these last warnings? Unfortunatelly pretty high.

The solution should have been to only classify as HIGH when real human attention was needed AND to create a global single warning that would determine if the other warnings were serious enough to get human attention. 

Of course, each set of services and solutions are very different and you should adapt to what makes more sense but resisting the temptation of classifing everything with HIGH. As a "rule of thumb" we suggest:

| Warning level | When should be issued? | What it means |
|---------------|------------------------|---------------|
| __INFO__ | Whenever some observation might be relevant for the analysis of the situation as an all. Think of it as extra info you would receive on a warning email to answer your boss something like "although the database is down there are currently 10 customers browsing with active shopping carts right now" | Purely extra information for human analysis or to correlate with other inputs. |
| __LOW__ | When an input observation is off the "normal" boundaries. | Might be nothing serious or escalate to a serious issue. |
| __MEDIUM__ | When there are errors and/or functionality not working correctly | There is impaired functionality on the solution. Might be temporary or not (self-healing might be able to solve it). Still worth noting for humana analysis. | 
| __HIGH__ | Whenever there is a complete failure of functionality for end-users or stackholders and the solution is considered to be down. | Human attention is required has the solution monitored is failing and not recovering. |

> [nValidation_Generic](/docs/reference/validations/nAttrMon-nValidation-Generic) provides some capability to trigger other services to provide auto-healing. This should be used as much as possible to avoid the ["alarm fatigue"](https://en.wikipedia.org/wiki/Alarm_fatigue) within your specific services/solutions constraints.