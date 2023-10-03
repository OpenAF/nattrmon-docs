---
layout: default
title: NDJson
parent: Outputs
grand_parent: Reference
---

# nOutput NDJson

Outputs current values, warnings and/or warnings to a local file in the NDJSON (New Line delimited JSON) format for processing by other tools. This output will also automatically gzip older files and delete old files in order to maintain a defined retention period. Optionally these local NDJSON files can be pushed to a S3 compatible object storage (retention in the object storage bucket won't be enforced by nAttrMon).

## Example of use of the execArgs

````yaml
output:
  name       : Output ndjson
  chSubscribe: nattrmon::cvals::buffer
  execFrom   : nOutput_NDJson
  execArgs   :
    folder                : /tmp/logs
    #dontCompress          : true
    #filenameTemplate      : {{timedate}}.ndjson
    #fileDateFormat        : "yyyy-MM-dd"
    fileDateFormat        : "yyyyMMdd-HHmm"
    #backupFilenameTemplate: "\\d{4}-\\d{2}-\\d{2}\\.ndjson"
    backupFilenameTemplate: "\\d{4}\\d{2}\\d{2}-\\d{2}\\d{2}\\.ndjson.gz"
    #howLongAgoInMinutes   : 7200
    #includeValues         : true
    #includeWarns          : false
    #includeLValues        : false
    #includeAttrs          : false
    #include               :
    #- Some/Attribute 1
    #exclude               :
    #- Some/Attribute 2
    #considerSetAll        : true
    s3sync                :
      secFile  : /secrets/secrets.yaml
      secBucket: nattrmon
      secKey   : s3
      #url      : http://127.0.0.1:9000
      #accessKey: minio
      #secret   : minio123
      bucket   : logs
      prefix   : "monitoring/logs/{{owFormat_fromDate now 'yyyyMMdd'}}/"
````

## Description of execArgs

| execArgs   | Type   | Mandatory | Description |
|:-----------|:------:|:---------:|:------------|
| folder | String | No | The folder path where the ndjson files will be dumped. Defaults to output_ndjson |
| dontCompress | Boolean | No | Controls if the generated ndjson files should be compressed when not actively being updated. Defaults to false |
| filenameTemplate | String | No | Template of the filename to generate (uses _"{% raw %}{{timedate}}{% endraw %}"_ to place the time & date string determined by _"fileDateFormat"_). Defaults to _"{% raw %}{{timedate}}{% endraw %}.ndjson"_ |
| fileDateFormat | String | No | Format to use for time & date like 'yyyyMMdd-HHmm" (see more ow.format.toDate in OpenAF). Defaults to "yyyy-MM-dd". |
| backupFilenameTemplate | String | No | Regular expression used to determine the file candidates to be deleted after the period _howLongAgoInMinutes_. Keep in mind that the selection of files to delete are based on the each file modified date **and not on any date & time on the filename**. Defaults to "\\d{4}-\\d{2}-\\d{2}\\.ndjson" |
| howLongAgoInMinutes | Number | No | This is the value, in minutes, for which files to be deleted, using the _backupFilenameTemplate_, will be identified using the file's modified date. Defaults to 7200 minutes. |
| includeValues | Boolean | No | Boolean flag to indicate with current values/metrics should be included. Defaults to true. |
| includeWarns | Boolean | No | Boolean flag to indicate if warnings should be included. Defaults to false. |
| includeLValues | Boolean | No | Boolean flag to indicate if previous values/metrics should be included. Defaults to false. |
| includeAttrs | Boolean | No | Boolean flag to indicate if attribute/metric definitions should be included. Defaults to false. |
| include    | Array  | No | An array of attribute names or warning titles regex to include. |
| exclude    | Array  | No | An array of attribute names or warning titles regex to exclude. |
| considerSetAll | Boolean | No | The nOutput_NDJson can be based on channel subscription (chSubscribe) so it can potential receive a setAll operation. In case you use nAttrMon channel buffers you need to set this argument to true. Otherwise you are probably fine with false. |
| s3sync | Map | No | Map of extra arguments to configure the push of the ndjson files to a S3 compatible bucket (see below for a more complete description) |

> **Don't forget to change _backupFilenameTemplate_ whenever the _fileDateFormat_ is updated.**

### Description of  the s3sync argument map

| execArgs   | Type   | Mandatory | Description |
|:-----------|:------:|:---------:|:------------|
| s3sync.url | String | Yes | The S3 compatible service URL |
| s3sync.accessKey | String | No | The S3 compatible service access key (for S3 IAM role authentication or equivalent leave it unset). |
| s3sync.secret | String | No | The S3 compatible service secret (for S3 IAM role authentication or equivalent leave it unset). |
| s3sync.region | String | No | The S3 compatible service region to use (if any). |
| s3sync.bucket | String | No | The S3 compatible service bucket to use. |
| s3sync.prefix | String | No | The S3 compatible service prefix to use to push the NDJSON files on the configured bucket. Since this is also a template you can use _now_, _folder_, _region_ and _bucket_ as data (e.g. "logs/{% raw %}{{owFormat_fromDate now 'yyyyMMdd'}}{% endraw %}/") |

> You can also use s3sync.secFile, s3sync.secBucket and s3sync.secKey to use a secrets file, for example. This file can contain the ulr, accessKey, secret and region, for example. This is a good security practice to keep all access related credentials on a specific secrets file with controlled/restricted access.

## Tips

### When nAttrMon is running on a container

Containers are limited on the amount of ephemeral storage available. You should configure:

* the _howLongAgoInMinutes_ parameter more aggressively to save on space
* change the _fileDateFormat_ to store per minute files (e.g. "yyyyMMdd-HHmm")
* change the _backupFilenameTemplate_ according with then changes to _fileDateFormat_ (e.g "\\d{4}\\d{2}\\d{2}-\\d{2}\\d{2}\\.ndjson")
* configure the _s3sync_ parameter to store the NDJSON files in a S3 compatible storage bucket

> Storing a file per minute will generate several files (1440 files per day) but, on the other hand, it will ensure that if the container, where nAttrMon is running, fails only a minute of metrics will be lost. Do balance this according with your capturing needs. Setting _fileDateFormat_ to "yyyyMMdd-HHm", for example, will just keep a file every 10 minutes which results in 144 files per day while losing 10 minutes of metrics in the event of a failure. 

### When nAttrMon is running outside a container

Be mindfull of the _folder_ you set to ensure enough space is available. The default _howLongAgoInMinutes_ of 7200 minutes will keep a 5 days retention on the NDJSON files which should be enough for most cases. You can, of course, increase it.

### Generic notes

* Choose the _fileDateFormat_ wisely to avoid large amounts of files retained. If necessary using the _s3sync_ is a better alternative than leaving the files on a regular filesystem.
* Keep the default option for compression. NDJSON files traditionally have a high degree of compression.
* When pushing files using _s3sync_ it's a good practice to set a retention period on the bucket + path used. 
* You can use the templating functionality for the prefix to change it daily (e.g. "logs/{% raw %}{{owFormat_fromDate now 'yyyyMMdd'}}{% endraw %}/") or even use environment variables to set it (e.g. "{% raw %}{{$env 'LOGFOLDER'}}{% endraw %}/")
