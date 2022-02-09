---
layout: default
title: Validation Generic
parent: Validations
grand_parent: Reference
---
# nValidation Generic

This validation provides a simple generic object to perform simple validations on attribute values with a single validation plug. It's ready to be triggered by timeInterval/cron or chSubscribe and the execArgs is composed of a _checks_ array where warnings will be created, updated or closed based on an expression (expr) evaluation (e.g. a true evaluation will create/update a warning, a false will close any previously created warnings) for an attribute or attributes following an attribute pattern (attrPattern). Check the examples and the execArgs description.

## Example of use of the execArgs

````yaml
validation:
   name         : My generic validation
   chSubscribe  : nattrmon::cvals
   waitForFinish: true
   execFrom     : nValidation_Generic
   execArgs     :
      checks:
         - attrPattern      : test$
           expr             : {% raw %}{{value}} < 200 && {{value}} >= 100{% endraw %}
           warnLevel        : HIGH
           warnTitleTemplate: A test warning
           warnDescTemplate : This is just a test warning. 
           healing          :
              execArgs         :
                 key: "{% raw %}{{value}}{% endraw %}"
              execOJob         : /some/path/config/ojobs/healProblem.yaml
              #exec             : "sh('/some/path/config/shs/healProblem.sh ' + args.key)"
              warnLevel        : HIGH
              warnTitleTemplate: "Heal failed"
              warnDescTemplate : "{% raw %}Heal for {{value}} failed with exception {{exception}}{% endraw %}"
   
         - attribute        : test3
           expr             : {% raw %}{{value.c}} == 3{% endraw %}
           warnLevel        : INFO
           warnTitleTemplate: A test info warning
           warnDescTemplate : {% raw %}This is just a info given {{name}} because the values was {{value.c}} for '{{value}}'.{% endraw %} 
````

## Description of execArgs

| execArgs | Type | Mandatory | Description |
|:---------|:----:|:---------:|:------------|
| checks | Array | Yes | An array of warnings to create or close base on the evaluation of an attribute or pattern of attribute names | 
| checks.attribute | String | Yes (if not defined checks.attrPattern) | The complete attribute name |
| checks.attrPattern | RegExp | Yes (if not defined checks.attribute) | A regular expression to match attribute names |
| checks.expr | String | Yes | A javascript conditional expression (using handlebars) to be evaluated to create/update a warning if true or close a warning if false. **If attrPattern is used it will be evaluated for each attribute that matches**. If the attribute value is an array this expression will be executed for each line and the "{{value}}" entry will correspond to each row. See available handlebars entries below that can be used. | 
| checks.not | Boolean | No | If set to true the checks.expr evaluation will be reversed and will only create a warning if it's false (defaults to false). |
| checks.warnLevel | String | No | Upon warning creation/update the level to use between INFO, LOW, MEDIUM and HIGH (defaults to INFO). |
| checks.warnTitleTemplate | String | No | Upon warning creation/update the title template (handlebars) to use. See available handlebars entries below that can be used. |
| checks.warnDescTemplate | String | No | Upon warning creation/update the description template (handlebars) to use. See available handlebars entries below that can be used. | 
| checks.debug | Boolean | No | Will output, if true, to the stdout the expression evaluation and alarm creation/update or close. | 
| checks.healing | Map | No | Provides entries for automated healing ojob or code execution. |
| checks.healing.execArgs | Map | Yes | Provides a map of arguments to be provided to checks.healing.exec or checks.healing.execOJob. It processes handlebars entries described below. |
| checks.healing.exec | String | No | A function body, that receives args (execArgs) as an argument, that should launch a self-healing process. |
| checks.healing.execOJob | String | No | A oJob file, receiving execArgs, that should implement a self-healing process. |
| checks.healing.warnLevel | String | Yes | A warning level template to generate in case exec or execOJob fail. See available handlebars entries below that can be used (including exception). |
| checks.healing.warnTitleTemplate | String | Yes | A warning title template to generate in case exec or execOJob fail. See available handlebars entries below that can be used (including exception). |
| checks.healing.warnDescTemplate | String | Yes | A warning description template to generate in case exec or execOJob fail. See available handlebars entries below that can be used (including exception). |

## Available template entries

| Template entry | Description |
|:---------------|:------------|
| {% raw %}{{value}}{% endraw %} | The current attribute value (you can use {{value.c}} to access a specific map entry). If the attribute is an array it will have each array entry in several evaluations. | 
| {% raw %}{{name}}{% endraw %} | The name of the attribute |
| {% raw %}{{dateAgo.seconds}}{% endraw %} | If current attribute is a date how many seconds ago it was. |
| {% raw %}{{dateAgo.minutes}}{% endraw %} | If current attribute is a date how many minutes ago it was. |
| {% raw %}{{dateAgo.hours}}{% endraw %} | If current attribute is a date how many hours ago it was. |
| {% raw %}{{dateAgo.days}}{% endraw %} | If current attribute is a date how many days ago it was. |
| {% raw %}{{modifiedAgo.seconds}}{% endraw %} | How many seconds ago the attribute value changed. |
| {% raw %}{{modifiedAgo.minutes}}{% endraw %} | How many minutes ago the attribute value changed. |
| {% raw %}{{modifiedAgo.hours}}{% endraw %} | How many hours ago the attribute value changed. |
| {% raw %}{{modifiedAgo.days}}{% endraw %} | How many days ago the attribute value changed. |
| {% raw %}{{dateModified}}{% endraw %} | The date when the attribute was modified. |
| {% raw %}{{originalValue}}{% endraw %} | The current original attribute value. Even if the attribute value is an array it will return the original array. |

You can also use [nAttrMon generic template helpers](nAttrMon-template-helpers) to access other attribute values, previous attribute values, etc...

## More examples

Using output from AFPing

````yaml
validation:
   name         : Ping validation
   chSubscribe  : nattrmon::cvals
   waitForFinish: true
   execFrom     : nValidation_Generic
   execArgs     :
      checks:
         - attribute        : Server status/Ping
           expr             : "{% raw %}{{value.Alive}} == false{% endraw %}"
           warnLevel        : HIGH
           warnTitleTemplate: "{% raw %}Server {{value.Name}} down{% endraw %}"
           warnDescTemplate : "{% raw %}A ping to the {{value.Name}} server failed. The server could be down or not responsive. Check the server status and restart if needed.{% endraw %}"
   
         - attribute        : Server status/Ping
           expr             : "{% raw %}{{value.Alive}} == true{% endraw %}"
           warnLevel        : INFO
           warnTitleTemplate: "{% raw %}RAID {{value.Name}} up{% endraw %}"
           warnDescTemplate : "{% raw %}The {{value.Name}} server is responding as expected appearing to be up.{% endraw %}"
````

Using a compare timestamps input where the attribute value map has spaces:

````yaml
validation:
   name         : Compare times validation
   chSubscribe  : nattrmon::cvals
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nValidation_Generic
   execArgs     : 
      checks: 
         - attribute        : Server status/Compare timestamps
           expr             : "{% raw %}{{value.[db.abc diff (min)]}} >= 5 || {{value.[db.xyz diff (min)]}} >= 5{% endraw %}"
           warnLevel        : MEDIUM
           warnTitleTemplate: "Database/App time difference"
           warnDescTemplate : "{% raw %}The current difference of time between the application instance {{value.key}} and the database is of {{value.[db.abc diff (min)]}} min (abc) and {{value.[db.xyz diff (min)]}} min (xyz){% endraw %}"
````

Using other dates on attributes with map values (this map has a column with warningDate and warningText) and alarming if warningDate longer than 1 minute but less than one day:

````yaml
validation:
   name         : ABC validation
   chSubscribe  : nattrmon::cvals
   waitForFinish: true
   onlyOnEvent  : true
   execFrom     : nValidation_Generic
   execArgs     : 
      checks: 
         - attribute        : Database/ABC
           expr             : "{% raw %}{{owFormat_dateDiff_inMinutes (owFormat_toDate value.warningDate 'yyyy-MM-dd HH:mm:ss.S')}} > 1 && {{owFormat_dateDiff_inDays (owFormat_toDate value.warningDate 'yyyy-MM-dd HH:mm:ss.S')}} < 1{% endraw %}"
           warnLevel        : HIGH
           warnTitleTemplate: "{% raw %}Alarm {{value.warningText}} error{% endraw %}"
           warnDescTemplate : "{% raw %}The alarm {{value.warningText}} has reached and surpassed the defined warning date: {{owFormat_toDate value.warningDate 'yyyy-MM-dd HH:mm:ss.S'}}.{% endraw %}"
````

Comparing current value with previous values with default values if doesn't exist (e.g. generate a warning if the attribute value is not older than 1 day and the current value of the "ABC count" (defaults to 0 if no value is found) - the previous value is bigger than 0 (defaults to the current value and 0 if a previous value doesn't exist):

````yaml
validation:
   name         : Errors validation
   chSubscribe  : nattrmon::cvals
   waitForFinish: true
   onlyForEvent : true
   execFrom     : nValidation_Generic
   execArgs     :
      checks:
         - attribute        : Database/ABC count
           expr             : >
               {% raw %}{{modifiedAgo.days}} < 1 && 
               ({{cval 'Database/ABC count' 'val' '0'}} - {{lval 'Database/ABC count' 'val' (cval 'Database/ABC count' 'val' '0')}}) > 0{% endraw %}
           warnLevel        : HIGH
           warnTitleTemplate: Entries on the error table
           warnDescTemplate : There are errors tracked yesterday in ABC table
````
