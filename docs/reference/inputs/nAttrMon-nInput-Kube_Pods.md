---
layout: default
title: Kube_Pods
parent: Inputs
grand_parent: Reference
---
# nInput Kube_Pods

This input provides a summary list of the current kubernetes pods and their current status. 

Example of use of the execArgs:

```yaml
input: 	
  name         : Get Kube pods
  cron         : "*/5 * * * *"
  waitForFinish: true
  onlyOnEvent  : false
  execFrom     : nInput_Kube_Pods
  execArgs     :
    #kubeNamespace: my-namespace
    #podName      : "^mypod-"
    #kubeURL      : 
    #kubeUser     :
    #kubePass     :
    #kubeToken    :
    #attrTemplate : 
````

| execArgs | Type | Mandatory | Description | 
| -------- | ---- | --------- |:----------- |
| kubeNamespace | String | No |  If set will restrict to list pods from a specific namespace. |
| podName | String | No | Regular expression to filter the pod name. |
| kubeURL | String | No | The k8s cluster API URL (if ommited it will try to use any existing KUBECONFIG info or cluster environment variables (if running inside the cluster)) |
| kubeUser | String | No | The k8s service account user to use (if ommited it will try to use any existing KUBECONFIG info) |
| kubePass | String | No | The k8s service account password to use (if ommited it will try to use any existing KUBECONFIG info) |
| kubeToken | String | No | The k8s service account token to use (if ommited it will try to use any existing KUBECONFIG info) |
| attrTemplate | String | No | A Handlebars template to build the local attribute name. Defaults to "Kube/Pods". |