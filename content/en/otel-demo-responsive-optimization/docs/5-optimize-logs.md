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
Log data can be incredibly valuable, but this is often dependent on context and time.  By utilizing the Mezmo Data Profiles along with [Responsive Pipelines](https://docs.mezmo.com/telemetry-pipelines/configure-responsive-pipelines) and proper parsing, teams can ensure the right data shows up at the right time in the right place with the right format. 

## Step 1: Create a new Pipeline to handle and route OpenTelemetry Logs

We will begin by routing OpenTelemetry Logs to Mezmo Log Analysis.  To do this while optimizing the data, create a new Mezmo Pipeline by clicking [New Pipeline](https://app.mezmo.com/pipelines/pipeline/new) in the platform.  Give this a name like `Log Handler`.

## Step 2: Add OpenTelemetry Log Source

Click `Add Source` and select your OpenTelemetry Log source from the `Shared Sources` list just like you did for the `Exploration` pipeline in the prior section.  

## Step 3: Insert State Enrichment and Normalize

In order to take advantage of [Responsive Pipelines](https://docs.mezmo.com/telemetry-pipelines/configure-responsive-pipelines), we need to enrich each event with the current pipelines operational state.  The easiest way to do this is by adding an enrichment script right off the bat.  Click the `three dots` on your Otel Log Source and select `Add Node->Add Processor->Script Execution`.

![OpenTelemetry Log Source](../../images/5-log-handler_add-enrich-inline.png)

Paste in the following Javascript and click `Save`.  Note that the script does a bit more than add the `operational_state` state variable, we are also normalizing some of this Otel data to be easier to use in Mezmo Log Analysis.

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

{{% alert title="Pro Tip" %}} Note that one can assign many state variables to a single pipeline.  This allows for different complex workflows to be automated like QA, curated deployment telemetry and more. {{% /alert %}}

## Step 4: Parse Custom Apache Logs

As noted in the prior section, the raw Apache data coming in from `frontend-proxy` is an unparsed, custom [format defined by the OpenTelemetry demo](https://github.com/braxtonj/opentelemetry-demo/blob/main/src/frontend-proxy/envoy.tmpl.yaml#L80).  To ensure teams can take full advantage, we will parse this data with a Grok parser so that it is structured and easily searchable.

![Custom Apache Profile](../../images/4-profile_apache-profile.png)

To do this, we will add another Route Processor on App along with a Parse Sequentially processor on that slice of data.

### Step 4.a: App Router
Connect a Route processor through the `three dots` or `Add Processor` at the bottom with the following configuration on application

* Title: `App Router`
* Route: 
  * Title: `Frontend Proxy`
  * Criteria: `message.app` `equal` `frontend-proxy`

Hit save.

![Log App Router](../../images/5-log-handler_app-router.png)

### Step 4.b: Parse Sequentially Processor

Now let's wire a `Parse Sequentially` processor to the `Frontend Proxy` output.  We will use the following configuration
* Field: `message.line`
* Target field: `message.line_parsed`
* Custom Parser:
  * Title: `Custom Apache`
  * Parser: `Grok Pattern`
  * Pattern: `%{SQUARE_BRACKET}%{TIMESTAMP_ISO8601:dt}%{SQUARE_BRACKET} %{DOUBLE_QUOTE}%{DATA:method} %{DATA:path} %{DATA:http_protocol}%{DOUBLE_QUOTE} %{DATA:rsp_code} %{DATA:rsp_flags} %{DATA:rsp_code_details} %{DATA:conn_term_details} %{DOUBLE_QUOTE}%{DATA:upstream_transport_failure_reason}%{DOUBLE_QUOTE} %{DATA:bytes_received} %{DATA:bytes_sent} %{DATA:duration} %{DATA:rsp_upstream_service_time} %{DOUBLE_QUOTE}%{DATA:req_forward_for}%{DOUBLE_QUOTE} %{DOUBLE_QUOTE}%{DATA:req_user_agent}%{DOUBLE_QUOTE} %{DOUBLE_QUOTE}%{DATA:req_id}%{DOUBLE_QUOTE} %{DOUBLE_QUOTE}%{DATA:req_authority}%{DOUBLE_QUOTE} %{DOUBLE_QUOTE}%{DATA:upstream_host}%{DOUBLE_QUOTE} %{DATA:upstream_cluster} %{DATA:upstream_local_addr} %{DATA:downstream_local_addr} %{DATA:downstream_remote_addr} %{DATA:requested_server_name} %{GREEDYDATA:route_name}`

![Apache Parser](../../images/5-log-handler_parse-seq-config.png)

Finally, let's keep the original line with a quick Scirpt, add a `Script Execution` processor connected to the `Parse Sequentially: Apache` output containing the following script:
```javascript
function processEvent(message, metadata, timestamp, annotations) {
  let old_line = message.line
  message.line = message.line_parsed
  message.line.message = old_line
  message.line_parsed = null
  return message
}
```

Your pipeline should look like this at this point.

![Cleanup script](../../images/5-log-handler_cleanup-script.png)


## Step 5: Route Based on State

After the initial Enrichment processor and Apache Parser, let's now route the data flow based on Pipelines state, ie that `operational_state`.  Connect a Route processor to all prior outputs with the following configuration

* Title: `State Router`
* Route 1:
  * Title: `Normal`
  * Criteria: `message.op_state` `contains` `normal`
* Route 2:
  * Title: `Incident`
  * Criteria: `message.op_state` `contains` `incident`
* Route 3:
  * Title: `Deploy`
  * Criteria: `message.op_state` `contains` `deploy`

You will end up with a pipeline that looks like the following

![Log State Router](../../images/5-log-handler_interim-pipeline.png)

## Step 6: Aggregate Normal State Logs

Now, lets optimize those noisy homepage flooding logs using a `Reduce` processor.  Effectively this will let us count and aggregate mainy logs into one easy to watch signal.  A common pattern is to turn this into a metric but today we will leave this as a log for the downstream tool.

To accomplish this, create a new Route processor connected to `State Router`'s `Normal` and `Unmatched` outputs with the following configuration:

* Title: `Template Router`
* Route 1:
  * Title: `Flooding homepage`
  * Criteria: `(message.app equal load-generator) AND (message.line contains Flooding homepage, iteration)`

![Template Router Config](../../images/5-log-handler_template-router-config.png)

We will then

## Step 7: Sample Normal State Logs

From `Template Router: Unamtached` let's add sample 1 out of every 10 while in `Normal` state  Simply connect a `Sample` processor with the following configuration:
* Rate: `1/10`

## Step 8: Wire to Mezmo Log Analysis

Finally, we will send all of this data into Mezmo Log Analysis.  Because of our earlier work normalizing data in Step 3, we can simply add a final Destination to all nodes (including the `Incident` and `Deploy` paths).  Create a `Mezmo Log Analysis` destination with the following configuration

* Ingestion key: generate or select
* Query Parameters:
  * Hostname: `{{message.host}}`
  * Tags: `otel-demo`
* Log Construction Scheme:
  * Scheme: `Message pass-through`

![Mezmo Log Analysis Destination Configuration](../../images/5-log-handler_la-config.png)

## Step 9: Deploy
Next, we must deploy your pipeline in order to start optimizing that log data.

To do this, select `Deploy` in the top right of the UX

![Deploy Pipeline](../../images/5-log-handler_deploy.png)

## Step 10: Initiate State and Grab State ID
Our final step is to initiate and grab the state ID for the pipeline for automation and control later on.  To do this, first we will enact a state by toggling the Normal mode to Incident mode and back.

![Log Handler State Flip](../../images/5-logs_state-flip.png)

Now that that has been initiated, you will need to grab the `Log Handler` pipeline's ID (found in the URL at `app.mezmo.com/ACCOUNT_ID/pipelines/PIPELINE_ID`) along with a Pipeline API Key here.  Then, modify the following script with both that `PIPELINE_ID` and Pipeline API Key

```bash
curl --request GET \
 --url 'https://api.mezmo.com/v3/pipeline/state-variable?pipeline_id=PIPELINE_ID' \
 --header 'Authorization: Token PIPELINE_API_KEY'
```

Take the response and save the `STATE_ID` for later.  You will find it in the `operational_state`'s data packet, which should look something akin to

```bash
{
    "meta": {
        "pk": "id",
        "type": "pipeline-state-variable",
        "links": {
            "self": {
                "create": {
                    "uri": "/v3/pipeline/{pipeline_id}/state-variable",
                    "method": "post"
                },
                "list": {
                    "uri": "/v3/pipeline/{pipeline_id}/state-variable",
                    "method": "get"
                },
                "replace": {
                    "uri": "/v3/pipeline/{pipeline_id}/state-variable/{id}",
                    "method": "put"
                },
                "update": {
                    "uri": "/v3/pipeline/{pipeline_id}/state-variable",
                    "method": "patch"
                },
                "detail": null
            },
            "related": {
                "pipeline": {
                    "list": "/v3/pipeline",
                    "detail": "/v3/pipeline/{pipeline_id}"
                }
            }
        },
        "page": {
            "next": null,
            "previous": null
        }
    },
    "data": [
        {
            "id": "STATE_ID",
            "account_id": "ACCOUNT_ID",
            "pipeline_id": "PIPELINE_ID",
            "state": {
                "operational_state": "normal"
            },
            "created_at": "UTC Timestamp",
            "updated_at": "UTC Timestamp"
        }
    ]
}
```

## Step 11: View In Mezmo Log Analysis
Navigate to Log Analysis and view the incoming data.  In particular, if you used the `tag` above you can simply search for `tag:otel-demo`.

Let's look at the outcome of our prior Log Aggregation and Parsing.

First, look for the aggregated data by searching for `tag:otel-demo "flooded homepage"`.  Notice that instead of raw lines like we saw in the Profile, we now have an aggregated message to watch saving tens of thousands of log lines.

![Log Analysis Flooded Log View](../../images/5-logs_la-flooded-aggregation.png)

Second, check out the newly parsed data by searching for `tag:otel-demo app:frontend-proxy`.  While logs are displayed nicely in the Log Viewer, one can expand a line and see all the nested structure that is easily searchable.  For instance, to see all 2xx responses enter the query `tag:otel-demo app:frontend-proxy rsp_code:(>=200 AND <300)`

![Log Analysis Custom Apache Log View](../../images/5-logs_la-apache.png)

{{% alert title="Learn About Log Analysis" %}} If you want to learn more about Log Analysis and creating things like saved Views, Alerts and more check out our [docs here](https://docs.mezmo.com/docs) or reaching out to [support@mezmo.com](mailto:support@mezmo.com) {{% /alert %}}

Now let's move on and see explore some simple but powerful optimizations for OpenTelemetry Metrics and Traces.
