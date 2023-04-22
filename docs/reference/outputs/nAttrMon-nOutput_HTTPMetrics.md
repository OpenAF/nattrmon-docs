---
layout: default
title: HTTPMetrics
parent: Outputs
grand_parent: Reference
---

# nOutput HTTPMetrics

Publishes internal nAttrMon metrics (self) and, optionally, the input monitored metric values (current and previous) and validation warnings in [OpenMetrics](https://openmetrics.io/) (keeping compatibility with the Prometheus format).

The metrics will be accessible throught the configured HTTP(s) endpoint for nAttrMon with the URI: **/metrics**

It's also possible to customize the output by adding extra query parameters to the configured endpoint:

| URI + query | Description | 
|-------------|-------------|
| /metrics?fmt=json | The output format will be JSON instead of OpenMetrics |
| /metrics?type=self | Will output just metrics related with nAttrMon |
| /metrics?type=cvals | Will output just metrics related with nAttrMon's current attribute values |
| /metrics?type=lvals | Will output just metrics related with nAttrMon's last/previous attribute values |
| /metrics?type=warn | Will output just metrics related with nAttrMon's warnings metrics |

Example of configuration:

````yaml
output:
   name         : Output Metrics
   execFrom     : nOutput_HTTP_Metrics
   execArgs     :
   #   includeSelf: false
   #   includeCVals: true
   #   includeLVals: false
   #   includeWarns: true
   #   nameSelf    : "nattrmon"
   #   nameCVals   : "nattrmon_cval"
   #   nameLVals   : "nattrmon_lval"
   #   nameWarns   : "nattrmon_warn"
````

"execArgs" table:

| execArgs | Type | Mandatory | Description |
|----------|------|-----------|-------------|
| includeSelf | Boolean | No | If true will include nAttrMon's own internal metrics (default is false) |
| includeCVals | Boolean | No | If true will include nAttrMon's collected current attribute values/metrics (default is true) |
| includeLVals | Boolean | No | If true will include nAttrMon's last/previous attribute values/metrics (default is false) |
| includeWarns | Boolean | No | If true will include nAttrMon's warnings metrics (default is true) |
| nameSelf | String | No | If defined changes the metrics prefix to use for nAttrMon's own internal metrics (default is 'nattrmon') |
| nameCVals | String | No | If defined changes the metrics prefix to use for nAttrMon's current attribute values (default is 'nattrmon_cvals' |
| nameLVals | String | No | If defined changes the metrics prefix to use for nAttrMon's last/previous attribute values (default is 'nattrmon_lvals') |
| nameWarns | String | No | If defined changes the metrics prefix to use for nAttrMon's warnings (default is 'nattrmon_warns') |
| format | String | No | If defined as 'json' changes the default OpenMetrics output to 'json'. |
| chName | String | No | If defined will actively collect metrics into the corresponding OpenAF's channel. |
| chType | String | No | If _chName_ is defined determines the type of the OpenAF's channel to create. |
| chParams | Map | No | If _chName_ is defined determines the map of options to use on the creation of the OpenAF's channel to create. |
| chPeriod | Number | No | If _chName_ is defined determines the period of time, in ms, use internally to collect metrics into the defined OpenAF's channel (default is 5000 ms). |
| host | Number | No | _tbc_ |
| port | Number | No | _tbc_ |
| keyStore | String | No | _tbc_ |
| keyPassword | String | No | _tbc_ |
| audit | Boolean | No | _tbc_ |
| auditTemplate | String | No | _tbc_ |
| auth | _tbc_ | _tbc_ | _tbc_ |
| authLocal | _tbc_ | _tbc_ | _tbc_ | 
| authCustom | _tbc_ | _tbc_ | _tbc_ | 
