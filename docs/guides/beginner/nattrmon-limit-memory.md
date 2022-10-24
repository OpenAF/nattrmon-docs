---
layout: default
title: nAttrMon Limit memory
parent: Beginner
grand_parent: Guides
---

# nAttrMon Limit memory

As with any other OpenAF script it's possible to limit also the memory used by nAttrMon.

## Setting the limits

Following the [generic instructions for limiting memory in OpenAF scripts](https://docs.openaf.io/docs/guides/beginner/limit-memory.html) you should be a startup script to launch nAttrMon with the corresponding memory limits:

__Unix/Mac__:

_nattrmon.sh:_
````bash
export OAF_JARGS="-Xms256m -Xmx512m"
cd /path/to/nAttrMon/folder
/the/openAF/folder/oaf -f nattrmon.js
````

__Windows__:

_nattrmon.bat:_
````powershell
set OAF_JARGS="-Xms256m -Xmx512m"
cd path\to\nAttrMon\folder
the\OpenAF\folder\oaf -f nattrmon.js
````

> Note: For OpenAF versions <= 20220822 you need to add the OAF_JARGS variable to the OpenAF startups scripts ([see how](https://docs.openaf.io/docs/guides/beginner/limit-memory.html))

## Which values to use

To limit memory you need to decide the base (Xms) and the maximum (Xmx) memory settings to use. These values will different depending on the inputs, validations and outputs configured in nAttrMon. So the best way is to actually measure it first and then trying to limit (Xmx) with different values giving some "slack".

