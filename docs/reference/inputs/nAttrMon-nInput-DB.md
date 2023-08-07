---
layout: default
title: DB
parent: Inputs
grand_parent: Reference
---
# nInput DB

**updated for nAttrMon version**: >= 20230806

Performs a series of SQL queries on the identified databases (through _chKeys_ or _key_). The results of each query will be transformed into the value of the corresponding attribute. 

If the result set is composed of only one record with one column (using _key_ or _chKeys_ with _dontUseKey=true_) only the corresponding single value will be captured.

Example:

````yaml
input:
  name         : Test input db
  cron         : "*/1 * * * *"
  waitForFinish: true
  onlyOnEvent  : true
  execFrom     : nInput_DB
  execArgs     :
     key: MYAPP_DAT
     sqls:
        Database/Test 1  : >
           SELECT user FROM dual

        Database/Test 2  : >
           SELECT level "My level"
           FROM dual connect by level <= 5
````

If _chKeys_ is used (without defining any value for _dontUseKey_) each record will also have a _key_ field to identify the source of the value(s). If the query also has a _key_ field it will be ignored (with a warning message being output). To consider the _key_ field, if present in the query result, set the argument _dontUseKey_ to true. If you customized the _attrTemplate_ you will need to use {% raw %}{{key}}{% endraw %} to distinguish the source of the results.

> Keep in mind that different databases give different default names to fields. Oracle, for example, makes all non double-quoted fields as uppercase.

> Queries are executed in parallel. Don't forget to adjust the DB pool sizing on the nInput_Init configuration accordingly. After exceeding the max DB pool size, for a given key, a query would "wait" (within the configured timeout period) for execution until a previous one finishes.

> Use _sqlsByType_ instead of _sqls_ if you intend to have the same attribute monitored for different database vendors.

| execArgs | Type | Mandatory | Description | 
| -------- | ---- | --------- |:----------- |
| chKeys | String | No | The channel of keys to use. Must be configured with nInput_Init on the DB category. |
| key | String/Map | No | The key for the database access. |
| key.parent | String |  No | In alternative provide the parent key (e.g. RAS) |
| key.child | String | No | In alternative provide the child key synonym (e.g. db.app) |
| sqls | Map | No | A map of query templates (if a '{% raw %}{{lastdate}}{% endraw %}' is included it will be replaced by the last checked date), each will become an attribute |
| sqlsByType | Map | No | In alternative to sqls lets you divide further into SQL statement per database product (e.g. postgresql, oracle, h2, etc...) |
| dontUseKey | Boolean | No | Boolean to indicate if a key field should not be added in all records returned with chKeys. |
| attrTemplate | String | No | The template to determine the attribute name. Defaults to "Performance/{% raw %}{{key}}{% endraw %} datamodel". |
