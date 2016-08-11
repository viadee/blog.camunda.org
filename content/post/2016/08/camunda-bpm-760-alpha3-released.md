+++
author = "Christopher Zell"
categories = ["Execution"]
date = "2016-08-11T12:00:00+01:00"
tags = ["Release Note"]
title = "Camunda BPM 7.6.0-alpha3 Released"

+++

Camunda 7.6.0-alpha3 is here and it is packed with new features. The highlights are:



* Support for Decisions with Literal Expressions
* Extended Queries for Decision Requirements Definitions
* CMMN Engine Improvements
* Support for Rolling Upgrades
* [23 Bug Fixes](https://app.camunda.com/jira/issues/?jql=issuetype%20%3D%20%22Bug%20Report%22%20AND%20fixVersion%20%3D%207.6.0-alpha3)

The [complete release notes](https://app.camunda.com/jira/secure/ReleaseNote.jspa?projectId=10230&version=14609) are available in Jira.

You can [Download Camunda For Free](https://camunda.org/download/)
or [Run it with Docker](https://hub.docker.com/r/camunda/camunda-bpm-platform/).

<!--more-->

# Support for Decisions with Literal Expressions

In Camunda 7.5, the DMN engine only supports decisions which are implemented as decision tables. Now, we added support for decisions which are implemented as literal expressions. This kind of decision allows to specify the decision logic as an expression. The following snippet shows an example decision:

```xml
<definitions xmlns="http://www.omg.org/spec/DMN/20151101/dmn11.xsd" id="dish" name="Desired Dish" namespace="party">
  
  <decision id="season" name="Season">
    <variable name="season" typeRef="string" />
    <literalExpression expressionLanguage="groovy">
      <text>calendar.getSeason(date)</text>
    </literalExpression>
  </decision>

</definitions>
```

The `literalExpression` element contains the expression and allows to set the expression language. The name of the result variable and their type is specified by the `variable` element.

You can use the expression to aggregate the result of required decisions, or to invoke a bean which provides the decision logic. 

To evaluate decisions with literal expressions, the DMN engine provides new methods that work with any kind of decision logic:

```java
DmnDecisionResult result = dmnEngine.evaluateDecision(decision, variables);

DmnDecisionResult result = dmnEngine.evaluateDecision("key", inputStream, variables);
```

You can also evaluate decisions with literal expressions using the Decision Service, a Business Rule Task or a Decision Task. See the [user guide](https://docs.camunda.org/manual/latest/user-guide/process-engine/decisions/decision-service/) and the [reference guide](https://docs.camunda.org/manual/latest/reference/dmn11/decision-literal-expression/) for details.

Note that you can’t model decisions with literal expressions in the Camunda Modeler, yet. We will add support in future releases.

# Extended Queries for Decision Requirements Definitions

In the latest alpha release, we introduced Decision Requirements Graphs which are represented by Decision Requirements Definitions in the process engine. We extended the `DecisionDefinitionQuery` and the `HistoricDecisionInstanceQuery` to query decisions that belong to decision requirements definition. For example:

```java
repositoryService
  .createDecisionDefinitionQuery()
  .decisionRequirementsDefinitionKey("dish")
  .list();
  
historyService
  .createHistoricDecisionInstanceQuery()  
  .decisionRequirementsDefinitionKey("dish")
  .list();
```


# CMMN Engine Improvements

Based on user feedback, a number of improvements have been made to the CMMN engine.

Probably the most interesting new feature is [Variable On-Parts](http://docs.camunda.org/manual/latest/reference/cmmn11/sentry/#variableonpart) for sentries. Variable On-Parts allow a sentry to react to `Create`, `Update` and `Delete` events for Variables. Or in other words, it is now possible to control tasks and other plan items in a case based on data. The following is an example of how to define a Sentry with a Variable On-Part:

```xml
<sentry id="Sentry_1">
  <extensionElements>
    <camunda:variableOnPart variableName="interestRate">
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


# Rolling Upgrades
In the current release, we add support for rolling upgrades.

A rolling upgrade is a process to perform a Camunda upgrade in a cluster. 
The nodes are updated one by one or in groups. During the upgrade process, it is ensured that at least one node is available to handle incoming requests, guaranteeing availability and minimizing downtime.

{{< figure class="teaser no-border" src="architecture.png" alt="Architecture" caption="" >}}

The above picture displays an example system. There are three process engine nodes connected to a shared database. A load balancer distributes requests to the Process Engine nodes. 
“Process Engine node” shall be an instance of an application or application server hosting a process engine.
It must not necessarily be a separate physical host.

A rolling upgrade can be orchestrated in a 2 step process:

 * Update the Database Schema,
 * Update the Camunda libraries on all nodes, one by one or in groups.

To facilitate updates, Camunda ensures backwards compatibility of the database schema. Backwards compatibility makes it possible to operate an older version of the process engine on a newer version of the database schema.
This property is crucial in the first step of the rolling upgrade process: after the database has been updated, the process engine libraries are still at the previous version.

Camunda also gurantees that an older version and a newer version of the process engine can work together on a new database schema.

## Limits for Rolling Upgrades

There are also limits for rolling upgrades.

Rolling upgrades are not possible prior to Version 7.5. Or in other words: the first update that can be done is the update from version 7.5.x to 7.6.y.

Also note that it is only possible to update from one minor version to the next. For example, it is possible to update from 7.5.3 to 7.6.2 in a rolling fashion but it is not possible to update from 7.5.3 to 7.7.2 in one go.

For more information about rolling upgrades, the gurantees from Camunda and limits see the [rolling upgrade](https://docs.camunda.org/manual/latest/update/rolling-upgrade/) documentation.

# Feedback Welcome

Please try out the awesome new features of this release and provide feedback by commenting on this post or reaching out to us in the [forum](https://forum.camunda.org/).



