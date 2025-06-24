---
title: Implement Trace Handling Pipeline for Downstream Systems
weight: 7
tags:
  - Mezmo Pipeline
  - OpenTelemetry
  - OpenTelemetry Demo
  - Metrics
  - Aggregate
---

## Step 1: Create a new Pipeline to handle and route OpenTelemetry Traces

Create a new Mezmo Pipeline by clicking [New Pipeline](https://app.mezmo.com/pipelines/pipeline/new) in the platform.  Give this a name like `Trace Handler`.

## Step 2: Add OpenTelemetry Trace Source

Click `Add Source` and select your OpenTelemetry Trace source from the `Shared Sources` list similar to before.  

![OpenTelemetry Trace Add Source](../../images/pipeline_trace_add_source.png)

## Step 3: Insert State Enrichment

## Step 4: Route Based on State

![OpenTelemetry State Router](../../images/pipeline_trace_state_route_config.png)

## Step 5: Sample Traces in Normal State

Add a Trace Sample processor connected to the Normal and Unmatched routes configured to 1/10 head based sampling.  Note that Tail based sampling is also available in Beta.

![OpenTelemetry Trace Config](../../images/pipeline_trace_sample_config.png)

## Step 6: Sending Data Downstream Systems

Now, connect all outputs to a Blackhole destination.  This is simply a placeholder for any Observability system you'd like.  Explore our destinations in-app or in our [docs]() to easily send telemetry data downstream into tools, data lakes and more.

![OpenTelemetry Blackhole Connected](../../images/pipeline_trace_blackhole.png)

## Step 7: Deploy
Finally, you must deploy your pipeline in order to start exploring your log data.
![OpenTelemetry Trace Handler](../../images/pipeline_trace_handler.png)

## Step 8: Initiate State and Grab State ID
