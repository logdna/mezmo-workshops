---
title: Test out Responsive Optimizations
weight: 8
tags:
  - Mezmo Pipeline
  - OpenTelemetry
  - OpenTelemetry Demo
  - Metrics
  - Aggregate
---

## Why It Matters

## Step 1: Modify Responsive Test script

Create a new Mezmo Pipeline by clicking [New Pipeline](https://app.mezmo.com/pipelines/pipeline/new) in the platform.  Give this a name like `Trace Handler`.

## Step 2: Run Responsive Test Script

Click `Add Source` and select your OpenTelemetry Trace source from the `Shared Sources` list similar to before.  

## Step 3: Evaluate Impact

Notice that when in `Normal` mode, data is sampled and rolled up ensuring the needed signals are captured while remaining cost conscious.  However, when in Incident or Deployment modes, data is grabbed at full fidelity.

### Normal Mode
![Normal Mode](../../images/pipeline_normal_mode.png)

### Incident Mode
![Incident and Deployment Mode](../../images/pipeline_incident_mode.png)

Due to the flexibility of [Mezmo's API](https://docs.mezmo.com/pipeline-api), any pipeline can be integrated with just about any Incident Management or Deployment method, from PagerDuty to Github to Shell scripts.  To learn more, reach out to Mezmo at [support@mezmo.com](mailto:support@mezmo.com).
