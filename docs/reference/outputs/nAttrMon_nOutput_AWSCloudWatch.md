---
layout: default
title: AWSCloudWatch
parent: Outputs
grand_parent: Reference
---
# nOutput AWSCloudWatch

Outputs a set of attributes metrics to AWS CloudWatch. Only attribute numeric values will be output.

## Example of use of the execArgs

````yaml
output:
  name       : Output AWS CloudWatch
  chSubscribe: nattrmon::cvals
  execFrom   : nOutput_AWSCloudWatch
  execArgs   :
    #include       :
    #exclude       :
    #includeDim    :
    #excludeDim    :
    #- pod
    #unitByMetric  :
    #  Service_Memory_Metrics_Amount: "Bytes"
    #considerSetAll: true
    #region        : us-east-1
    #logGroup      : nattrmon
    #accessKey     : AKI1234567
    #secretKey     : A1B2C3D4E5F6G7
    #sessionToken  :
    #debug         : true
````

## Description of execArgs

| execArgs   | Type   | Mandatory | Description |
|:-----------|:------:|:---------:|:------------|
| include | Array | No | An array of attribute names to restrict which attributes should be output |
| exclude | Array | No | An array of attribute names to exclude from output | 
| includeDim | Array | No | An array of labels to include on output |
| excludeDim | Array | No | An array of labels to exclude from output |
| unitByMetric | Map | No | Optional unit to report per metric (the metric is the attribute name after replacing each " " and "/" with "_") (*) |
| considerSetAll | Boolean | No | If _true_ will process attributes in bulk (defaults to true) |
| region | String | No | The AWS region to use for AWS CloudWatch (defaults to us-east-1) |
| logGroup | String | No | The AWS CloudWatch log group to use (defaults to nattrmon) |
| accessKey | String | No | The AWS API access key with permissions to put metrics in AWS CloudWatch |
| secretKey | String | No | The AWS API secret key with permissions to put metrics in AWS CloudWatch |
| sessionToken | String | No | The AWS API session token with permissions to put metrics in AWS CloudWatch |
| debug | Boolean | No | Shows additional information logs in case of error |

> (*) The list of units by metric, according with AWS documentations, is: econds | Microseconds | Milliseconds | Bytes | Kilobytes | Megabytes | Gigabytes | Terabytes | Bits | Kilobits | Megabits | Gigabits | Terabits | Percent | Count | Bytes/Second | Kilobytes/Second | Megabytes/Second | Gigabytes/Second | Terabytes/Second | Bits/Second | Kilobits/Second | Megabits/Second | Gigabits/Second | Terabits/Second | Count/Second | None

> Note: The _accessKey_, _secretKey_ and _sessionToken_ are used with the oPack AWS. So they might be required or not depending where nAttrMon is running and which AWS roles are associated. If not defined it will default to other role-based AWS authentication mechanisms.

## Necessary AWS permissions

The main AWS permission necessary would be: _"cloudwatch:PutMetricData"_

## Usage

With the right AWS authentication and permissions this output will start sending the necessary metrics to the configured _logGroup_. On the AWS CloudWatch Metrics console you should see the new AWS CloudWatch group appearing under "Custom namespaces".

### Important tips

1. Keep in mind that once a metric is sent to AWS CloudWatch it's not possible (at the moment of writing according with AWS documentation) to delete it. So you should be __very carefull__ with what you sent. To help with this you can use the _include_ execArgs (as described above) to slowly add new nAttrMon attribute values as needed.

2. For multiple entries (table/array) attribute values, the alpha-numeric values will be translated into AWS CloudWatch dimensions. It's not possible to generically cover all cases but some of these values might not be helpful in AWS CloudWatch. 
For example, if you are using nInput_Kube_PodsMetrics it will output the attributes: _namespace, name, pod, cpuAmount and memAmount_. From theses 5 attributes, only _cpuAmount_ and _memAmount_ are numeric. So the other 3 will be converted to dimensions. Since pods get created and destroyed the attribute _pod_ will have multiple values and **won't be helpful** as a dimension poluting AWS CloudWatch metrics. To avoid this use the _excludeDim_ execArg to ensure that the _pod_ is not used.

3. On the initial setup use the _debug_ execArg to ensure no errors are occurring while trying to put metrics in AWS CloudWatch. Debug adds extra verbose to nAttrMon logs so you can control to turn on or off as needed.