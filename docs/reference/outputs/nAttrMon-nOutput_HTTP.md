---
layout: default
title: HTTP
parent: Outputs
grand_parent: Reference
---

_tbc_

Example:

````yaml
output:
  name         : Output HTTP
  description  : >
    Provides a very basic web site for read-only access to the inputs and warnings generated. It uses the nOutput_HTTPJSON to
    retrieve the necessary data when needed.
  execFrom     : nOutput_HTTP
  execArgs     :
     title: Some title
````