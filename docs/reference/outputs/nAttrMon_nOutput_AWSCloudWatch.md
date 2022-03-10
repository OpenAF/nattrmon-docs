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
    #considerSetAll: true
    #region        : us-east-1
    #logGroup      : nattrmon
    #accessKey     : AKI1234567
    #secretKey     : A1B2C3D4E5F6G7
    #sessionToken  :
````

## Description of execArgs

| execArgs   | Type   | Mandatory | Description |
|:-----------|:------:|:---------:|:------------|
| include | Array | No | An array of attribute names to restrict which attributes should be output |
| exclude | Array | No | An array of attribute names to exclude from output | 
| considerSetAll | Boolean | No | If _true_ will process attributes in bulk (defaults to true) |
| region | String | No | The AWS region to use for AWS CloudWatch (defaults to us-east-1) |
| logGroup | String | No | The AWS CloudWatch log group to use (defaults to nattrmon) |
| accessKey | String | No | The AWS API access key with permissions to put metrics in AWS CloudWatch |
| secretKey | String | No | The AWS API secret key with permissions to put metrics in AWS CloudWatch |
| sessionToken | String | No | The AWS API session token with permissions to put metrics in AWS CloudWatch |

> Note: The _accessKey_, _secretKey_ and _sessionToken_ are used with the oPack AWS. So they might be required or not depending where nAttrMon is running and which AWS roles are associated. If not defined it will default to other role-based AWS authentication mechanisms.