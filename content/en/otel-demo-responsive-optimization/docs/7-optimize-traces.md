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

## Step 3: Insert State Enrichment

## Step 4: Route Based on State

## Step 5: Sample Traces in Normal State

## Step 6: Sending Data Downstream Systems

While we simply dumped this data into a Blackhole, note that you can easily send this data on to any OpenTelemetry destination, allowing for optimization of numerous Observability tools.

## Step 7: Deploy
Finally, you must deploy your pipeline in order to start exploring your log data.
![OpenTelemetry Trace Handler](../../images/pipeline_trace_handler.png)

## Step 8: Initiate State and Grab State ID
