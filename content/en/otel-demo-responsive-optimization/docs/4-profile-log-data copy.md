---
title: Profile OpenTelemetry Log Data for Understanding
weight: 4
tags:
  - Profiler
  - OpenTelemetry
---

## Why it matters

Mezmo allows teams to understand their telemetry data before they choose to store and pay for it downstream in Observability tools, Data Lakes and more.  To achieve this, the platform analyzes and identifies patterns within telemetry data to inform on what is and is not important.

Now that data is wired up, let's first explore what we have and create a profile similar to the following

![OpenTelemetry Log Profile](../../images/otel_log_profile.png)

## Step 1: Create an "Exploration" Pipeline

Create a new Mezmo Pipeline by clicking [New Pipeline](https://app.mezmo.com/pipelines/pipeline/new) in the platform.  Give this a name like `Log Explorer`.

## Step 2: Add OpenTelemetry Log Source

Click `Add Source` and select your OpenTelemetry Log source from the `Shared Sources` list.  

## Step 3: Insert Otel to Profile mapping Script

In order to fully take advantage of the [Mezmo Profiler](https://docs.mezmo.com/telemetry-pipelines/data-profiling), let's tweak the structure of this data to easily grab insights about the data using Mezmo standards.

To do this, add a Script Processor connected to the Log Source and copy in the following code
```javascript
function processEvent(message, metadata, timestamp, annotations) {
  
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

  let new_msg = {
    "line":line,
    "app":app,
    "host":host,
    "level":level
  }

  // Extract metadata to top level fields
  for( const meta of Object.entries(metadata) ){
    let meta_name = 'metadataotel_' + meta[0].toString()
    let meta_val = meta[1]
    new_msg[meta_name] = meta_val
  }

  return new_msg
}
```

The above script simply maps Otel data to the defaults of Mezmo's profiling nodes.  Note that the Profiler is completely configurable and thus mapping is not always needed.

## Step 4: Connect a Profiler node
Add a Profiler node connected after the Script processor from Step 3.  Give it a name like `Otel Demo Log Exploration` and leave the default configuration.

## Step 5: Deploy
Finally, you must deploy your pipeline in order to start exploring your log data.
![OpenTelemetry Log Exploration](../../images/pipeline_log_exploration.png)

## Step 6: Analyze Log Patterns
After allowing the profiler to run a short amount of time Once your docker has been built and deployed, give it a few and you will start to see the profiler build out.  Right away we can notice two things:

1. There is an inordinate amount of **logs simply stating a homepage is being flooded** coming from the `load-generator` service.  This is standard behavior of the OpenTelemetry Demo using the Feature Flag X but as one can see, it is quite noisy and inevitably costly to keep each.
2. Unparsed events that appear to be custom Apache logs from the `frontend-proxy` service.  While these are [defined in the demo here](https://github.com/open-telemetry/opentelemetry-demo/blob/21fe6e7fb7e6a5e8b2b21f6bf10a0f3c5f4c0f12/src/frontend-proxy/envoy.tmpl.yaml#L80), we can ensure this data is structred and parsed properly to be fully searchable in any downstream Observability system.

![OpenTelemetry Log Profile](../../images/otel_log_profile.png)

In the next section, we will build out a log telemetry pipeline to address both these concerns.