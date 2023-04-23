---
layout: default
title: Objects
parent: Reference
grand_parent: nAttrMon docs
---

# Current objects

The following is a list of the current objects base for Inputs, Outputs and Validations. Do note that some are provided in different GitHub repositories (the main repository for nAttrMon is openaf/nattrmon):
  
## Inputs

|       Type | Name                                                                      | Repository              | Category             |
| ----------:| ------------------------------------------------------------------------- | ----------------------- | -------------------- |
|      Input | [nInput_AFPing](inputs/nAttrMon-nInput-AFPing.md)                         | openaf/nattrmon-configs | RAID                 |
|      Input | [nInput_BPMDebugChecks](inputs/nAttrMon-nInput-BPMDebugChecks)            | openaf/nattrmon-configs | RAID                 | 
|      Input | [nInput_CBPMDebugChecks](inputs/nAttrMon-nInput-CBPMDebugChecks)          | openaf/nattrmon-configs | RAID                 | 
|      Input | [nInput_CBPMSemaphores](inputs/nAttrMon-nInput-CBPMSemaphores.md)         | openaf/nattrmon-configs | RAID                 | 
|      Input | [nInput_DB](inputs/nAttrMon-nInput-DB.md)                                 | openaf/nattrmon         | DB                   | 
|      Input | [nInput_EndPoints](inputs/nAttrMon-nInput-EndPoints)                      | openaf/nattrmon         | OS                   | 
|      Input | [nInput_JMX](inputs/nAttrMon-nInput-JMX.md)                               | openaf/nattrmon         | JMX                  |
|      Input | [nInput_FMSRuleStatus](inputs/nAttrMon-nInput-FMSRuleStatus.md)           | openaf/nattrmon-configs | RAID                 | 
|      Input | [nInput_Filesystem](inputs/nAttrMon-nInput-Filesystem.md)                 | openaf/nattrmon         | OS                   | 
|      Input | [nInput_FilesystemCount](inputs/nAttrMon-nInput-FilesystemCount.md)       | openaf/nattrmon         | OS                   | 
|      Input | [nInput_HTTPJson](inputs/nAttrMon-nInput-HTTPJson)                        | openaf/nattrmon         | OS                   | 
|      Input | [nInput_IMAbnormalLoadings](inputs/nAttrMon-nInput-IMAbnormalLoadings.md) | openaf/nattrmon-configs | RAID                 | 
|      Input | [nInput_IMLoadingsProgress](inputs/nAttrMon-nInput-IMLoadingsProcess.md)  | openaf/nattrmon-configs | RAID                 | 
|      Input | [nInput_IMMemory](inputs/nAttrMon-nInput-IMMemory.md)                     | openaf/nattrmon-configs | RAID                 | 
|      Input | [nInput_Kube_Events](inputs/nAttrMon-nInput-Kube_Events.md)               | openaf/nattrmon         | Kubernetes           | 
|      Input | [nInput_LogErrorAgg](inputs/nAttrMon-nInput-LogErrorAgg.md)               | openaf/nattrmon         | OS                   | 
|      Input | [nInput_OSInfo](inputs/nAttrMon-nInput-OSInfo.md)                         | openaf/nattrmon         | OS                   | 
|      Input | [nInput_RAIDMemory](inputs/nAttrMon-nInput-RAIDMemory.md)                 | openaf/nattrmon-configs | RAID                 | 
|      Input | nInput_RunningFlows                                                       | openaf/nattrmon-configs | RAID                 |
|      Input | nInput_Semaphores                                                         | openaf/nattrmon-configs | RAID                 | 
|      Input | [nInput_Sessions](inputs/nAttrMon-nInput-Sessions.md)                     | openaf/nattrmon-configs | RAID                 | 
|      Input | [nInput_Shell](inputs/nAttrMon-nInput-Shell)                              | openaf/nattrmon         | OS                   | 
|      Input | [nInput_Channel](inputs/nAttrMon-nInput-Channel)                          | openaf/nattrmon         | Input Channel        | 
|      Input | [nInput_RemoteChannel](inputs/nAttrMon-nInput-RemoteChannel)              | openaf/nattrmon         | Input Channel        | 
|      Input | [nInput_Schedulers](inputs/nAttrMon-nInput-Schedulers.md)                 | openaf/nattrmon-configs | RAID                 | 
|      Input | [nInput_CompareTimes](inputs/nAttrMon-nInput-CompareTimes.md)             | openaf/nattrmon         | OS                   | 
|      Input | [nInput_DMPerformance](inputs/nAttrMon-nInput-DMPerformance.md)           | openaf/nattrmon-configs | RAID                 |
|      Input | [nInput_EndPoints](inputs/nAttrMon-nInput-EndPoints.md)                   | openaf/nattrmon         | OS                   | 

## Outputs

|       Type | Name                                                                      | Repository              | Category             |
| ----------:| ------------------------------------------------------------------------- | ----------------------- | -------------------- |
|     Output | [nOutput_AWSCloudWatch](outputs/nAttrMon-nOutput-AWSCloudWatch)           | openaf/nattrmon         | Output AWSCloudWatch | 
|     Output | [nOutput_DSV](outputs/nAttrMon-nOutput-DSV)                               | openaf/nattrmon         | Output DSV           | 
|     Output | [nOutput_EmailWarnings](outputs/nAttrMon-nOutput-EmailWarnings)           | openaf/nattrmon         | Output Warning       | 
|     Output | [nOutput_H2](outputs/nAttrMon-nOutput-H2.md)                              | openaf/nattrmon         | Output History       | 
|     Output | nOutput_HealthZ                                                           | openaf/nattrmon         | Output HTTP          | 
|     Output | [nOutput_HTTP](outputs/nAttrMon-nOutput_HTTP.md)                          | openaf/nattrmon         | Output HTTP          | 
|     Output | [nOutput_HTTP_Metrics](outputs/nAttrMon-nOutput_HTTP_Metrics.md)          | openaf/nattrmon         | Output HTTP          | 
|     Output | [nOutput_HTTP_JSON](outputs/nAttrMon-nOutput_HTTP_JSON.md)                | openaf/nattrmon         | Output HTTP          | 
|     Output | nOutput_HTTP_Status                                                       | openaf/nattrmon         | Output HTTP          |
|     Output | [nOutput_HTMLStatus](outputs/nAttrMon-nOutput_HTMLStatus.md)              | openaf/nattrmon         | Output HTTP          | 
|     Output | [nOutput_Log](outputs/nAttrMon-nOutput_Log.md)                            | openaf/nattrmon         | Output Log           | 
|     Output | [nOutput_Oracle](outputs/nAttrMon-nOutput_Oracle.md)                      | openaf/nattrmon         | Output History       | 
|     Output | [nOutput_Stdout](outputs/nAttrMon-nOutput_Stdout.md)                      | openaf/nattrmon         | Output Log           | 
|     Output | [nOutput_Channels](outputs/nAttrMon-nOutput-Channels)                     | openaf/nattrmon         | Output Channels      | 
|     Output | [nOutput_RemoteChannel](outputs/nAttrMon-nOutput-Channel)                 | openaf/nattrmon         | Output Channel       | 
|     Output | [nOutput_Notifications](outputs/nAttrMon-nOutput-Notifications)           | openaf/nattrmon         | Output Warnings      |
|     Output | [nOutput_WarnsHistory](outputs/nAttrMon-nOutput_WarnsHistory.md)          | openaf/nattrmon         | Output History       | 
|     Output | [nOutput_ES](outputs/nAttrMon-nOutput_ES.md)                              | openaf/nattrmon         | Output ES            | 
|     Output | [nOutput_Slack](outputs/nAttrMon-nOutput_Slack.md)                        | openaf/nattrmon         | Output Slack         | 

## Validations

|       Type | Name                                                                      | Repository              | Category             |
| ----------:| ------------------------------------------------------------------------- | ----------------------- | -------------------- |
| Validation | [nValidation_Generic](validations/nAttrMon-nValidation-Generic)           | openaf/nattrmon         | Validation Generic   | 

## Object templates

### Input object

To build an input object follow the following javascript template:

````javascript
/**
* <odoc>
* <key>nattrmon.nInput_MyInput(aMap)</key>
* aMap is composed of:\
* - keys (the key of the objects to refer to)
* - chKey (the channel name for the keys of objects to refer to)
* - attrTemplate (a string template for the name of the attribute using {{name}})
* </odoc>
*/
var nInput_MyInput = function(aMap) {

  this.params = aMap;

  if (isUnDef(this.params.myMandatoryArg)) this.params.myMandatoryArg = "something";  

  nInput.call(this, this.input);
}

inherit(nInput_MyInput, nInput);

nInput_MyInput.prototype.input = function(scope, args) {
  var ret = {};
  var attrname = templify(this.params.attrTemplate, { name: this.params.name });
  
  ret[attrname] = runSomething();

  return ret;
}
````

### Validation object

To build a validation object follow the following javascript template:

````javascript
/**
* <odoc>
* <key>nattrmon.nValidation_MyValidation(aMap)</key>
* aMap is composed of:\
* - warnTemplate (a string template for the warning title using {{title}})
* - warnDescTemplate (a string template for the warning description {{title}})
* </odoc>
*/
var nValidation_MyValidation = function(aMap) {
  this.params = aMap;
  if (isUnDef(this.params.myMandatoryArg)) this.params.myMandatoryArg = "something";

  nValidation.call(this, this.validation);
}

inherit(nValidation_MyValidation, nValidation);

nValidation_MyValidation.prototype.validate = function(scope, args) {
  var ret = [];
  var title = templify(this.params.warnTemplate);
  var description = templify(this.params.warnDescTemplate);

  if (somethingIsWrong)
    ret.push(new nWarning(nWarning.LEVEL_HIGH, title, descripiton));
  else
    this.closeWarning(title);

  ret.push(new nWarning(nWarning.LEVEL_INFO, "A information", "Just for fyi..."));

  return ret;
}
````

### Output object

To build an output object follow the following javascript template:
  
````javascript
/**
* <odoc>
* <key>nattrmon.nOutput_MyOutput(aMap)</key>
* aMap is composed of:\
* - inclusionList (a list of attributes to include)
* - chInclusionList (a channel name with attributes to include)
* </odoc>
*/
var nOutput_MyOutput = function(aMap) {
  this.params = aMap;
  if (isUnDef(this.params.myMandatoryArg)) this.params.myMandatoryArg = "something";

  nOutput.call(this, this.output);
}

inherit(nOutput_MyOutput, nOutput);

nOutput_MyOutput.prototype.output = function(scope, args) {
  for(key in scope.getCurrentValues()) {
    ...
  }
}
````