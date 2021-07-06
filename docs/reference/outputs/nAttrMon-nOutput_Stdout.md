---
layout: default
title: Stdout
parent: Outputs
grand_parent: Reference
---

# nOutput StdOut

Writes attribute values to StdOut following an OpenAF template.

## Example of use of the execArgs

````yaml
output:
  name       : Output attribute stdout
  chSubscribe: nattrmon::cvals
  execFrom   : nOutput_Stdout
  execArgs   :
    outputTemplate: "{{name}}: {{{value}}} ({{date}})"

````

## Description of execArgs

| execArgs   | Type   | Mandatory | Description |
|:-----------|:------:|:---------:|:------------|
| outputTemplate  | String | No | The OpenAF template-based line output (using variables name, value and date) |