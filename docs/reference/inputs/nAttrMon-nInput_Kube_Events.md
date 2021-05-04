---
layout: default
title: Kube_Events
parent: Inputs
grand_parent: Reference
---
# nInput Kube_Events

This input retrieves the Kubernetes events to an attribute.

Example of use of the execArgs:

```yaml
input: 	
  name         : Get Kube events
  cron         : "*/5 * * * *"
  waitForFinish: true
  onlyOnEvent  : false
  execFrom     : nInput_Kube_Events
  execArgs     :
    full         : true
    #kubeURL     : 
    #kubeUser    :
    #kubePass    :
    #kubeToken   :
    #attrTemplate:
````

| execArgs | Type | Mandatory | Description | 
| -------- | ---- | --------- |:----------- |
| full | Boolean | No | If false will only map startTime, endTime, type, count, sourceComponent, sourceHost, reportingComponent, message and reason otherwise all Kubernetes event fields will be available. |
| kubeURL | String | No | The k8s cluster API URL (if ommited it will try to use any existing KUBECONFIG info or cluster environment variables (if running inside the cluster)) |
| kubeUser | String | No | The k8s service account user to use (if ommited it will try to use any existing KUBECONFIG info) |
| kubePass | String | No | The k8s service account password to use (if ommited it will try to use any existing KUBECONFIG info) |
| kubeToken | String | No | The k8s service account token to use (if ommited it will try to use any existing KUBECONFIG info) |
| attrTemplate | String | No | A Handlebars template to build the local attribute name. Defaults to "Kube/Events". |