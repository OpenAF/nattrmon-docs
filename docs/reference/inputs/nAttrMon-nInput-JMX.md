---
layout: default
title: JMX
parent: Inputs
grand_parent: Reference
---
# nInput JMX

Enables the gathering of remote JMX values as nAttrMon's input attributes. It's possible to filter the JMX objects gathered and use OpenAF's [$path](https://docs.openaf.io/docs/concepts/OpenAF-path.html) or [$from](https://docs.openaf.io/docs/concepts/OpenAF-nLinq.html) to filter the results. If no list of objects is provided the default java.lang JMX attributes will be retrieved.

Example of use of the execArgs:

```yaml
input:
  name         : Input jmx
  cron         : "*/15 * * * * *"
  waitForFinish: true
  onlyOnEvent  : false
  execFrom     : nInput_JMX
  execArgs     :
    #chKeys  : jmxs
    url     : service:jmx:rmi:///jndi/rmi://127.0.0.1:9999/jmxrmi
    #user    : aUser
    #pass    : aPassword
    #provider: weblogic.management.remote
    # ------
    objects :
    - object  : "java.lang:type=OperatingSystem"
    - object  : "java.lang:type=Runtime"
      path    : "{SpecName:SpecName,SpecVendor:SpecVendor,SpecVersion:SpecVersion,ManagementSpecVersion:ManagementSpecVersion,InputArguments:InputArguments,BootClassPathSupported:BootClassPathSupported,VmName:VmName,VmVendor:VmVendor,VmVersion:VmVersion,Uptime:Uptime,StartTime:StartTime,Name:Name,ClassPath:ClassPath}"
      #selector:
    - object  : "java.lang:type=Memory"
    - object  : "java.lang:type=Compilation"
    - object  : "java.lang:type=Threading"
      path    : "{CurrentThreadAllocatedBytes:CurrentThreadAllocatedBytes,ThreadCount:ThreadCount,TotalStartedThreadCount:TotalStartedThreadCount,CurrentThreadCpuTime:CurrentThreadCpuTime,CurrentThreadUserTime:CurrentThreadUserTime,PeakThreadCount:PeakThreadCount,DaemonThreadCount:DaemonThreadCount}"
      #selector:
    - object: "java.lang:type=MemoryManager,name=Metaspace Manager"
    - object: "java.lang:type=MemoryPool,name=Metaspace"    
```

| execArgs | Type | Mandatory | Description | 
| -------- | ---- | --------- |:----------- |
| **chKeys** | String | No | If defined it will use the channel (defined previously in a nInputInit (see example below)) to retrieve the values for url, login, pass and provider for each specific key. |
| **attrTemplate** | String | No | Allows you to customize the nAttrMon attribute name to hold the resulting values parameterized with any of the execArgs. |
| **url** | String | Yes | A Java JMX url to access the target JMX (e.g service:jmx:rmi:///jndi/rmi://1.2.3.4:1234/jmxrmi). |
| **login** | String | No | If needed, the login to use when accessing the remote JMX target. |
| **pass** | String | No | If needed, the password to use when accessing the remote JMX target. |
| **provider** | String | No | If needed, the custom class to use when accessing the remote JMX target (NOTE: this requires that nAttrMon is started with the extra Java classes on the defined classpath). |
| **objects** | Array | No | An array of JMX object definitions to gather into nAttrMon input attributes. |
| **objects.object** | String | Yes | A JMX object reference (e.g. java.lang:type=MemoryPool,name=Metaspace) |
| **objects.path** | String | No | Use OpenAF's [$path](https://docs.openaf.io/docs/concepts/OpenAF-path.html) to filter the output of the JMX object reference (for example: to select which attributes should be considered) |
| **objects.selector** | String | No | Use OpenAF's [$from](https://docs.openaf.io/docs/concepts/OpenAF-nLinq.html) to filter the output of the JMX object reference (for example: to select which attributes should be considered) |

## Using chKeys

For using a single input definition for several dynamic (e.g. Kubernetes' pods) remote JMX targets it's possible to define a dynamic list ([OpenAF's channel](https://docs.openaf.io/docs/concepts/OpenAF-Channels.html)).

Example of the use chKeys:

**00.init.yaml**
````yaml
input:
  name    : Input init
  cron    : "*/15 * * * * *" # every 15 seconds
  execFrom: nInput_Init
  execArgs:
    CH:
    - name   : jmxs
      entries:
      - key  : jmx1
        value:
          url: service:jmx:rmi:///jndi/rmi://127.0.0.1:9999/jmxrmi
    
    Kube:
      CH:
      - name   : kubeJMXs
        entries:
        - key  : "JMX {{metadata.name}}"
          value:
            url  : "service:jmx:rmi:///jndi/rmi://{{status.podIP}}:9999/jmxrmi"
        _kube:
          selector:
            where:
            - cond: starts
              args:
              - "metadata-name"
              - "test-"
            - cond: equals
              args:
              - "status.phase"
              - "Running"
````

**10.jmx.yaml**
````yaml
input:
- name         : Input simple jmx example
  cron         : "*/15 * * * *"
  waitForFinish: true
  onlyOnEvent  : false
  execFrom     : nInput_JMX
  execArgs     :
    chKeys  : jmxs

- name         : Input kube jmx example
  cron         : "*/15 * * * *"
  waitForFinish: true
  onlyOnEvent  : false
  execFrom     : nInput_JMX
  execArgs     :
    chKeys  : kubeJMXs
````