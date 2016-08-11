+++
author = "Philipp Ossler"
categories = ["Execution"]
date = "2016-07-06T12:00:00+01:00"
tags = ["Release Note"]
title = "Camunda BPM 7.6.0-alpha2 Released"

+++

Camunda 7.6.0-alpha2 is here and it is packed with new features. The highlights are:

* Support for Decision Requirements Graphs
* New Task Dashboard in Cockpit
* Mapping Input/Output Parameters of a Call Activity using Java Code
* [38 Bug Fixes](https://app.camunda.com/jira/issues/?jql=issuetype%20%3D%20%22Bug%20Report%22%20AND%20fixVersion%20%3D%207.6.0-alpha2)

The [complete release notes](https://app.camunda.com/jira/secure/ReleaseNote.jspa?projectId=10230&version=14605) are available in Jira.

You can [Download Camunda For Free](https://camunda.org/download/)
or [Run it with Docker](https://hub.docker.com/r/camunda/camunda-bpm-platform/).

<!--more-->

# CMMN Engine Improvements

Based on user feedback, a number of improvements have been made to the CMMN engine.

Probably the most interesting new feature is [Variable On-Parts](http://docs.camunda.org/manual/latest/reference/cmmn11/sentry/#variableonpart) for sentries. Variable On-Parts allow a sentry to react to `Create`, `Update` and `Delete` events for Variables. Or in other words, it is now possible to control tasks and other plan items in a case based on data. The following is an example of how to define a Sentry with a Variable On-Part:

```xml
<sentry id="Sentry_1">
  <extensionElements>
    <camunda:variableOnPart variableName = "interestRate">
      <camunda:variableEvent>update</camunda:variableEvent>
    </camunda:variableOnPart>
  </extensionElements>	
</sentry> 
```

The above sentry has an on part which is satisfied as the variable `interestRate` gets updated.

Case Workers can now also terminate a Case:

```java
caseService.terminateCaseExecution(...);
```
Also, please note the following bugfix concerning the interpretation of the manual activation rule attribute of CMMN. This bugfix (link to https://app.camunda.com/jira/browse/CAM-6362) is included in this release and originates from an official bugfix (link to https://app.camunda.com/jira/browse/OMG-12) in the OMG CMMN standard.

# CMMN Cockpit

We have also started work providing monitoring and operation features for CMMN inside Camnunda Cockpit. The next alpha release will allow users to preview these features.

# Feedback Welcome

Please try out the awesome new features of this release and provide feedback by commenting on this post or reaching out to us in the [forum](https://forum.camunda.org/).

