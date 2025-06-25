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

Using the State ID's for your Log, Metric and Trace pipelines modify [`switch_state.sh`](https://github.com/braxtonj/opentelemetry-demo/blob/main/switch_state.sh) with the proper credentials.

## Step 2: Run Responsive Test Script

Now let's run the script with your desired state to initiate Mezmo Pipeline flow changes.  For instance, to flip to Incident mode you would run:
```bash
sh switch_state.sh incident
```

## Step 3: Evaluate Impact

Notice that when in `Normal` mode, data in your pipelines are sampled and rolled up ensuring the needed signals are captured while remaining cost conscious.  However, when in `Incident` or `Deployment` modes, data is grabbed at full fidelity.  You will also see this represented in the UX

### Normal Mode
![Normal Mode](../../images/9-responsive_normal_mode.png)

### Incident Mode
![Incident Mode](../../images/9-responsive_incident_mode.png)

Due to the flexibility of [Mezmo's API](https://docs.mezmo.com/pipeline-api), any pipeline can be integrated with just about any Incident Management or Deployment method, from PagerDuty to Github to Shell scripts.  To learn more, reach out to Mezmo at [support@mezmo.com](mailto:support@mezmo.com).
