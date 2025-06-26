---
title: Create the OpenTelemetry Shared Sources
weight: 2
tags:
  - Mezmo Pipeline
  - OpenTelemetry
  - Shared Sources
---

{{% alert title="Make sure you have a Mezmo account" color="danger" %}}
In order to complete this workshop, you will need a Mezmo account with Pipeline enabled.  If you do not have an account, you can sign up for a free trial [here](https://www.mezmo.com/sign-up-mezmo-platform).

Please feel free to select either of the demo logs upon signup, we will be connecting new data from a local OpenTelemetry Demo deployment.  You can explore [Mezmo Flow](https://docs.mezmo.com/telemetry-pipelines/about-mezmo-flow) or continue on with the workshop.
{{% /alert %}}

## Step 1: Create an OpenTelemetry Log Shared Source

Once you have an account, go ahead and [Create a new Shared Source](https://app.mezmo.com/pipelines/shared-sources).  Select `OpenTelemetry Logs` and call it what you will, be sure to save your created Access Key as you will need this later.

![OpenTelemetry Log Source](../../images/2-shared-sources_init.png)

Note that a [Shared Source](https://docs.mezmo.com/telemetry-pipelines/shared-sources) is a powerful way to re-use sources within Mezmo without needing to configure new integrations.  Organizations can take advantage of them to provide teams the self-service ability to pull in sanitized and normalized telemetry data from any stream needed.

![OpenTelemetry Log Source](../../images/2-shared-sources_otel-log.png)

## Step 2: Create an OpenTelemetry Metric Shared Source

Repeat the prior step but this time select `OpenTelemetry Metrics`.  Be sure to write that Access Key down for later.

![OpenTelemetry Metric Source](../../images/2-shared-sources_otel-metric.png)

## Step 3: Create an OpenTelemetry Trace Shared Source

Finally create a `OpenTelemetry Metrics` Shared Source and save that Access Key.

![OpenTelemetry Trace Source](../../images/2-shared-sources_otel-trace.png)
