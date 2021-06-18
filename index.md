---
layout: default
title: nAttrMon docs
has_children: true
---


<a href="/"><img align="right" src="images/logo.png"></a>
Welcome to the nAttrMon

# Introduction

nAttrMon (*n-Attribute-Monitor*) is basically a small open source generic monitoring tool, using [OpenAF](https://openaf.io). It's extensible through plugs (small editable files) divided into three categories: inputs, outputs and validations:
   * The *inputs* are responsible to gather unique attribute values.
   * The *validations* are responsible to analyze the attribute values and generate warnings and/or perform actions (e.g. self-healing).
   * The *outputs* are responsible to make attribute values and warnings available (e.g. HTTP, Email, Notification, etc...).

(the source code is available in [GitHub](https://github.com/openaf/nattrmon))

# Install

To install nAttrMon you will need to first install [OpenAF](https://openaf.github.io/openaf-docs/#installing) and then simply execute:

````bash
opack install nattrmon
````
This will install a nAttrMon opack folder on your OpenAF installation. Check the following topics for more details regarding the folder structure and base configuration:

* [nAttrMon folder structure](docs/concepts/nAttrMon-folder-structure)
* [nAttrMon base configuration](docs/concepts/nAttrMon-base-configuration)

Afterwards you will need to add some input, output and validation plugs covered below under "Adding plugs". Then you can start by executing:

````bash
oaf -f nattrmon.js
````

Alternatevily you can create a start script be executing:

````bash
opack script nattrmon
````

# Source

You can also checkout all the sources on [GitHub](https://github.com/OpenAF/nAttrMon).

# Architecture

<div style="text-align:center"><img src="images/nattrmon_arch.png" alt="nAttrMon architecture"/></div>

## Inputs, Outputs and Validations

The inputs, outputs plugs are loaded (by alphanumeric order from their corresponding folder) upon nAttrMon start and run in parallel on specific time intervals, internal cron schedule or triggered by changes (e.g. changes on attribute values, creation/update/close of warnings, etc...). These plugs can inherit most of their functionality from available existing objects (to promote reusability) or be totally customized.

## Attribute values and Warnings 

At all times plugs have access to the current warnings, current atribute values and previous attribute values that are stored in memory either by time or by the last different value. These values can be simple types (e.g. strings, numbers, booleans, ...) or maps/arrays. nAttrMon keeps track of when an attribute was last checked and when it's value last changed. 

## Operating and accessing other systems

nAttrMon is designed to be "killed" and restarted whenever needed so all relevant memory information is persisted automatically in snapshot files.

Available to plugs are also object pools to globally manage access to databases, application servers, ssh connections, etc. These object pools are accessed by keys (either static or dynamic) that can have associations between them (e.g. application server A (key "APP1") is associated with database server connection B (key "DAT1")). Since the list of keys and corresponding object pools can be dynamic, plugs can automatically adapt to changes in sources (e.g., adding/removing application servers, adding/removing database connections, adding/removing docker containers).

# Using it

Start by understanding:

1. [The folder structure](docs/concepts/nAttrMon-folder-structure.md)
2. [Examples of adding Inputs, Outputs or Validations](docs/howto/Examples)
3. [The generic plugs parameters available](docs/concepts/nAttrMon-Plugs)
4. [Existing base Inputs, Outputs or Validations (also known as objects)](docs/reference/nAttrMon-Objects.md)
5. [Running as single mode](docs/concepts/nAttrMon-single-mode.md)

# Operational topics

* [Managing warnings](docs/howto/nAttrMon-Warnings)
* [Interconnecting several nAttrMon's instances](docs/howto/nAttrMon-Interconnect)
* [Auditing](docs/howto/nAttrMon-Auditing)
