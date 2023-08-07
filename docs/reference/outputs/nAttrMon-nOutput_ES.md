---
layout: default
title: ES
parent: Outputs
grand_parent: Reference
---
# nOutput_ES

**updated for nAttrMon version**: >= 20230806

The ElasticSearch output can send monitored attribute values and/or warnings to be stored on an ElasticSearch/OpenSearch cluster. It supports sending to one specific index or to execute a function to determine the index to use based on the current date (see _funcIndex_ below). 

Each attribute value will be converted to a structure similar to:

```json
{
  name: "The name of the attribute",
  date: 1234-12-24T23:12:34.123Z,
  id  : "unique-id-hash-based",
  _id : "the-same-as-id",
  The_name_of_the_attribute: { ... }
}
```

> All attribute names will have the corresponding "/" and " " characters replaced by a "_"

If an attribute value is not a map but rather an array (a table of values with multiple rows and columns), since each array element would actually represent a different metric, the array will be converted into mutiple ElasticSearch/OpenSearch documents.

It's possible to "stamp" each document with fixed fields using the _stampMap_ argument. This allows for multiple nAttrMons to output to the same cluster and still be able to distinguish the outputs from a nAttrMon and a nAttrMon B.

## Examples:

### Example using attribute values

````yaml
output:
  name         : Output ES values
  chSubscribe  : nattrmon::cvals
  waitForFinish: true
  onlyOnEvent  : true
  execFrom     : nOutput_ES
  execArgs     :
    url      : http://127.0.0.1:9200
    #user     : nouser
    #pass     : nopass
    #index: 
    #considerSetAll: false 
    #funcIndex: "ow.ch.utils.getElasticIndex('nattrmon-attrs',\"yyyy.ww\")" note: Check getElasticIndex function, If a specific format is needed you can provide it as aFormat (see ow.format.fromDate)"
    funcIndex: "ow.ch.utils.getElasticIndex('nattrmon-attrs')"
    #index: nattrs
    stampMap :
      region: US
    #include  :
    #  - test/test 1
    # exclude  :
````

### Example using warnings

````yaml
output:
  name         : Output ES warnings
  chSubscribe  : nattrmon::warnings
  waitForFinish: true
  onlyOnEvent  : true
  execFrom     : nOutput_ES
  execArgs     :
    url      : http://127.0.0.1:9200
    #user     : nouser
    #pass     : nopass
    #index: 
    #considerSetAll: false 
    #funcIndex: "ow.ch.utils.getElasticIndex('nattrmon-attrs',\"yyyy.ww\")" note: Check getElasticIndex function, If a specific format is needed you can provide it as aFormat (see ow.format.fromDate)"
    funcIndex: "ow.ch.utils.getElasticIndex('nattrmon-warns')"
    #index: nattrs
    stampMap :
      region: US
    #include  :
    #  - test/test 1
    # exclude  :
````

## Arguments:  

| execArgs | Type | Mandatory | Description | 
| -------- | ---- | --------- |:----------- |
| url | String | Yes | A http/https URL to the ElasticSearch/OpenSearch cluster indicating the port (usually 9200) |
| user | String | No | The user login to use if necessary to authenticate with the corresponding ElasticSearch/OpenSearch cluster. |
| pass | String | No | The pass login to use if necessary to authenticate with the corresponding ElasticSearch/OpenSearch cluster. |
| index | String | No | The ElasticSearch/OpenSearch static index name to use to store current values or warnings. |
| funcIndex | String | No | An OpenAF piece of code that should return the index name to use. For example: ```ow.ch.utils.getElasticIndex('nattrmon-attrs', 'yyyy.ww')``` would create an index 'nattrmon-attrs-2023.32'. |
| format | String | No | Alternative to using _funcIndex_ where _index_ becomes mandatory and this _format_ field defines, if any, the date format to use as suffix (see more in ow.format.fromDate) |
| stampMap | Map | No | A map of keys and values to super-impose to all records sent to ElasticSearch/OpenSearch. If _dontUseStampMapTemplating_ is not defined each value can use OpenAF's templating brackets. You can access the current attribute (using the fields: 'name', date', and 'val') or a warning (using the fields: 'title', 'createdate', 'date', 'level' and 'val') |
| unique | Boolean | No | Flag to indicate that the _id_ field for attributes should not include any date and the _id_ field for warnings should use the _createdate_ instead of the warning date (only if _noDateDocId = false_). Defaults to 'false'. |
| noDateDocId | Boolean | No | Flag to indicate that the _id_ field for warnings should not include any date (default is false).  |
| dontUseStampMapTemplating | Boolean | No | Flag to disable the use of OpenAF templating on _stampMap_ (default is false) |
| considerSetAll | Boolean | No | When _chSubscribe_ uses a buffered channel (see using [buffers](../../guides/advanced/using-buffers-in-nattrmon.html)) it will handle batch updates (might improve performance in some cases) (default is true) |
| keyMap | Map | No | If provided will retrieve the key (for example 'cpu.load') and replace by the corresponding value (for example 'load'. |
| include | Array | No | A list of attributes to include. |
| exclude | Array | No | A list of attributes to exclude. | 