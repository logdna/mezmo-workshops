---
title: Implement Log Handling Pipeline for Mezmo Log Analysis
weight: 5
tags:
  - Mezmo Pipeline
  - OpenTelemetry
  - OpenTelemetry Demo
  - Logs
  - Reduce Processor
  - Parse Sequentially Processor
  - Responsive Pipeline
---

## Why it matters
Log data can be incredibly valuable, but this is often dependent on context and time.  By utilizing [Responsive Pipelines](https://docs.mezmo.com/telemetry-pipelines/configure-responsive-pipelines) and proper parsing, teams can ensure the right data shows up at the right time in the right place with the right format. 

## Step 1: Create a new Pipeline to handle and route OpenTelemetry Logs

We will begin by routing OpenTelemetry Logs to Mezmo Log Analysis.  To do this while optimizing the data, create a new Mezmo Pipeline by clicking [New Pipeline](https://app.mezmo.com/pipelines/pipeline/new) in the platform.  Give this a name like `Log Handler`.

## Step 2: Add OpenTelemetry Log Source

Click `Add Source` and select your OpenTelemetry Log source from the `Shared Sources` list just like you did for the `Exploration` pipeline in the prior section.  

## Step 3: Insert State Enrichment

In order to take advantage of [Responsive Pipelines](https://docs.mezmo.com/telemetry-pipelines/configure-responsive-pipelines), we need to enrich each event with the current state.  The easiest way to do this is by adding an enrichment script right off the bat.  Click the `three dots` on your Otel Log Source and select `Add Processor->Script`.  Paste in the following Javascript and click `Update`.  Note that the script does a bit more than add the `operational_state` state variable, we are also normalizing some of this Otel data to be easier to use in Mezmo Log Analysis.

```javascript
function processEvent(message, metadata, timestamp, annotations) {
  metadata.resource.attributes["pipeline.path"] = "with_mezmo"
  const state = getPipelineStateVariable("operational_state")

  let line = message
  let app = metadata.resource.attributes["container.name"]
  let host = metadata.resource.attributes["container.hostname"]
  let level = metadata.level
  
  if( app == null || app == '' ){
    app = metadata.resource["service.name"]
  }
  if( app == null || app == '' ){
    app = metadata.resource["service_name"]
  }
  if( app == null || app == '' ){
    app = metadata.scope.name
  }
  if( app == null || app == '' ){
    app = 'na'
  }

  if( host == null || host == '' ){
    host = metadata.headers["x-kafka-partition-key"]
  }
  if( host == null || host == '' ){
    host = metadata.attributes["log.file.path"]
  }
  if( host == null || host == '' ){
    host = 'na'
  }

  if( level == null || level == '' ){
    level = annotations.level
  }

  metadata.headers = null
  
  let new_msg = {
    "line":line,
    "app":app,
    "host":host,
    "level": level,
    "op_state":state,
    "meta":metadata,
    '_cnt': 1
  }

  if( message == null ){ return null }

  return new_msg

}
```

## Step 4: Parse Custom Apache Logs

## Step 5: Route Based on State

After the State Enrichment processor, let's now route the data flow based on this `operational_state`.  Add a connected processor to the enrichment script with the following routes

* Normal
* Incident
* Deploy


## Step 6: Aggregate Normal State Logs

## Step 7: Sample Normal State Logs

## Step 8: Wire to Mezmo Log Analysis

## Step 9: Deploy
Finally, you must deploy your pipeline in order to start exploring your log data.
![OpenTelemetry Log Exploration](../../images/pipeline_log_exploration.png)

## Step 10: Initiate State and Grab State ID

## Step 11: View In Mezmo Log Analysis
Navigate to Log Analysis and view the incoming data.  In particular, if you used the `tag` above you can simply search for `tag:otel-demo`.

Let's look at the outcome of our prior Log Aggregation and Parsing.

First, look for the aggregated data by searching for `tag:otel-demo "flooded homepage"`.  Notice that instead of raw lines like we saw in the Profile, we now have an aggregated message to watch saving tens of thousands of log lines.

![Log Analysis Aggregated Log](../../images/la_agg_log.png)

Second, check out the newly parsed data by searching for `tag:otel-demo app:frontend-proxy`.  While logs are displayed nicely in the Log Viewer, one can expand a line and see all the nested structure that is easily searchable.  For instance, to see all 2xx responses enter the query `tag:otel-demo app:frontend-proxy rsp_code:(>=200 AND <300)`

![Log Analysis Parsed Custom Apache Log](../../images/la_parsed_apache.png)

{{% alert title="Learn About Log Analysis" %}} If you want to learn more about Log Analysis and creating things like saved Views, Alerts and more check out our [docs here](https://docs.mezmo.com/docs) or reaching out to [support@mezmo.com](mailto:support@mezmo.com) {{% /alert %}}

Now let's move on and see explore some simple but powerful optimizations for OpenTelemetry Metrics and Traces.
