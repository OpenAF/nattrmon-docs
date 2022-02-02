---
layout: default
title: Init plugs
parent: Concepts
grand_parent: nAttrMon docs
---
# Init plugs

Older nAttrMon configurations usually had the need for a special [plug](/docs/concepts/nAttrMon-Plugs) whose solely function was to initialize variables, objects, object pools, etc... that might be needed and common to other input, validation or output plugs.

To ensure this plug would be the first to execute in nAttrMon the most common naming used was [_00.init.js_](https://github.com/OpenAF/nAttrMon/blob/master/config/inputs.disabled/js/00.raid.init.js) since loading order in nAttrMon is alphabetic. Basically it was pure OpenAF Javascript code initializing whatever was needed and lead to the hard coding of credentials, for example. Additionally it was usually hard to learn for any new nAttrMon user although extremely reusable. 

## Why Init plugs are needed?

For the different types of plugs the need to initialize common functionality is obvious. If you need to add multiple HTTP/HTTPs based outputs (e.g. /metrics, /healthz, etc...) you need to start/initialize a common web server object. 

If you need to perform multiple database queries, with different schedules, you are going to need multiple [database query input plugs](/docs/reference/inputs/nAttrMon-nInput-DB) probably sharing the same database credentials and access information.

Let's imagine that you have 3 nInput_DB plugs each with 4 queries. Even if they all have different cron schedules there might be certain points in a day that 2 or 3 of theses plugs are running at the same time. So in order to avoid establishing 4 * 3 = 12 database connections and adding load and/or impacting the database nAttrMon allows you to create database ***object pools*** with a maximum number of connections (among other parameters). 

In our example if you configure to have a maximum of 3 connections whenever a new connection is requested to nAttrMon to perform a new database query that request will _wait_ until one of the other 3 connections is free and can be eventually reuse it also avoiding the time to reestablish a new connection. This applies to a broad categories of connections to different types of services or servers and it's a built-in functionality of nAttrMon.

> In case you are wondering where to find examples of output init plugs the integrated nOutput_HTTP* output plugs automatically create the necessary web server object under the hoods for convenience. So for those particular cases you don't need an output init plug.

## Avoiding credentials on Init plugs

In order to avoid the old and bad practice of having hard-coded credentials on init plugs nAttrMon supports the use of secrets files:

````yaml
# secrets.yaml

nattrmon:
  ras_server:
    url: http://user:password@1.2.3.4:7100/xdt

  ras_db:
    url    : jdbc:oracle:thin:@1.2.3.5:1521:aSID 
    login  : rasadmuser
    pass   : rasadmpass
````

Depending on the nAttrMon deployment this file can be deployed as a Kubernetes secret, for example. The idea is to keep this file outside of source repositories and with secure access ideally only to nAttrMon.

On any nAttrMon plug (including Init plugs) you case refer to a "secret" contained on this secrets file:

````yaml
  raidServers:
  - key      : RAS Server
    # Using the ras_server secret under the nattrmon entry on secrets.yaml
    secFile  : /some/path/secrets.yaml
    secBucket: nattrmon
    secKey   : ras_server
    timeout  : 5000
    pool     : 
      max: 3

  dbServices:
  - key      : RAS Database
    # Using the ras_db secret under the nattrmon entry on secrets.yaml
    secFile  : /some/path/secrets.yaml
    secBucket: nattrmon
    secKey   : ras_db
    timeout  : 30000
````

As you can see on the example this can be achieved through the use of 3 specific map entries:

| Map entry | Type | Description |
|-----------|------|-------------|
| secFile   | String | The filepath location to the YAML secret file. |
| secBucket | String | The main map entry under which the secret that you want to refer is located. |
| secKey | String | The specific map entry under secBucket with the entries you need to add to a plug |

nAttrMon will process these 3 map entries and add the secret entries internally. 

## nInput Init plugs

After understanding why init plugs are needed and how secrets are mapped to nAttrMon plugs lets understand what goes into a nInput Init plug.

First of all, we still need it to the among the first, so it's usually named "00.init.yaml". But it's not mandatory to have just one. You can actually have many depending on your plugs organization as long as you make sure that alphabetically they will always come first in relation to your other plugs.

First lets declare a simple input plug based on the built-in input plug object "nAttrMon_Init":

````yaml
input:
  name    : My first init plug
  execFrom: nAttrMon_Init
  execArgs:
````

We left the input plug object arguments (_execArgs_) empty but let's start adding one or more servers.

Most nAttrMon input plugs require you to provide a server/service key or a set/array of server/service keys. If a set/array of keys is provided the input plugs will automatically request connections to those to nAttrMon and will produce metrics with a _key_ field so you know from which server/service the metrics were collected from.

> These set/array of server/services keys uses [OpenAF's channels functionality](https://docs.openaf.io/docs/concepts/OpenAF-Channels.html) so they are usually refered, on the execArgs of non-init input plugs, as _"chKeys"_.

Using _nInput_Init_ you just need to add the type of servers/services and then describe the array:

````yaml
input:
  name    : My first init plug
  execFrom: nAttrMon_Init
  execArgs:
    # AF is the type for RAID servers
    AF:
      rasServers:
      - key: RAS Server 1
      - key: RAS Server 2
      # ...
      fmsServers:
      - key: FMS Server
      # ...

    # DB is the type for database services
    DB:
      dbServices:
      - key: RAS DB 
    # ...
````

Now, as you saw previously, we can relate each key entry for AF and DB to the corresponding secret. To avoid having to repeat _secFile_ and _secBucket_ for every entry you can use the standard YAML anchors functionality:

````yaml
secrets: &SECRETS
  secFile  : /some/path/secrets.yaml
  secBucket: nattrmon

input:
  name    : My first init plug
  execFrom: nAttrMon_Init
  execArgs:
    # AF is the type for RAID servers
    AF:
      rasServers:
      - key   : RAS Server 1
        <<    : *SECRETS
        secKey: ras_server_1
      - key   : RAS Server 2
        <<    : *SECRETS
        secKey: ras_server_2
      
      fmsServers:
      - key   : FMS Server 
        <<    : *SECRETS
        secKey: fms_server
    # ...

    # DB is the type for database services
    DB:
      dbServices:
      - key   : RAS DB 
        <<    : *SECRETS
        secKey: ras_db
    # ...
````

After defining these sets of servers/services on other nAttrMon input plugs you just need to reference the corresponding set/array name (_chKeys_) or the specific key inside a set/array. For this reason even if a specific _key_ belongs to a different set/array the name should always be unique to that particular nAttrMon instance.

*nInput_AFPing examples:*
````yaml
# 01.ping.ras.yaml
input: 	
   name         : Ping RAS servers
   cron         : "*/1 * * * *"
   waitForFinish: true
   execFrom     : nInput_AFPing
   execArgs     : 
      chKeys: rasServers
````

````yaml
# 01.ping.fms.yaml
input: 	
   name         : Ping FMS servers
   cron         : "*/1 * * * *"
   waitForFinish: true
   execFrom     : nInput_AFPing
   execArgs     : 
      chKeys: fmsServers
````

**Note:** Now to ping more or less servers you just a simple change on the corresponding entries on the init plug

*nInput_DB example:*

````yaml
input: 	
   name         : My database queries
   cron         : "*/30 * * * * *"
   waitForFinish: true
   execFrom     : nInput_DB
   execArgs     : 
      key : RAS DB
      sqls:
         Database/My query: |
           SELECT count(1) AS numberOfSomethings
           FROM a_table
           WHERE a_field = 'something'
````

### Pool parameters

_tbc_

### AF type parameters

_tbc_

### DB type parameters

_tbc_

### CH type parameters

_tbc_

### ES type for nInput_ESInit

[nInput_ESInit sample](https://github.com/OpenAF/nattrmon-configs/blob/main/ES/inputs.disabled/00.init.es.yaml)

_tbc_

### Presto/Trino type for nInput_PrestoInit

_tbc_

### S3 type for nInput_S3Init

[nInput_S3Init sample](https://github.com/OpenAF/nattrmon-configs/blob/main/S3/inputs.disabled/00.init.s3.yaml)

_tbc_