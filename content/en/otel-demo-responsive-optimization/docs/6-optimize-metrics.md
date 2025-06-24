---
title: Implement Metric Handling Pipeline for Downstream Systems
weight: 6
tags:
  - Mezmo Pipeline
  - OpenTelemetry
  - OpenTelemetry Demo
  - Metrics
  - Aggregate
---

## Step 1: Create a new Pipeline to handle and route OpenTelemetry Metrics

Create a new Mezmo Pipeline by clicking [New Pipeline](https://app.mezmo.com/pipelines/pipeline/new) in the platform.  Give this a name like `Metric Handler`.

## Step 2: Add OpenTelemetry Metric Source

Click `Add Source` and select your OpenTelemetry Metric source from the `Shared Sources` list similar to before.  

## Step 3: Insert State Enrichment

## Step 4: Route Based on State

![OpenTelemetry State Router](../../images/pipeline_metric_state_route_config.png)

## Step 5: Aggregate in Normal State

## Step 6: Sending Data Downstream Systems

While we simply dumped this data into a Blackhole, note that you can easily send this data on to any OpenTelemetry destination, allowing for optimization of numerous Observability tools.

## Step 7: Deploy
Finally, you must deploy your pipeline in order to start exploring your log data.
![OpenTelemetry Metric Handler](../../images/pipeline_metric_handler.png)

## Step 8: Initiate State and Grab State ID
