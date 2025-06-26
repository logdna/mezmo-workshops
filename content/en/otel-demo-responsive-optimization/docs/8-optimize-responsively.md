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

Telemetry data is both fundamental and costly for any business.  While this data is invaluable for troubleshooting, monitoring and various other concerns, it is not always valuable at the same time.

Mezmo introduced [Responsive Pipelines](https://docs.mezmo.com/telemetry-pipelines/configure-responsive-pipelines) specifically to address the dynamic nature of telemetry data.  By allowing for extreme configurability, telemetry flows can be tuned for changing circumstances from incidents to deployments. 

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
