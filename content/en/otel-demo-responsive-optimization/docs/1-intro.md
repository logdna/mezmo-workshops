---
title: Getting Started
weight: 1
---

{{% alert title="Support" %}} Outside our [Telemetry Pipeline docs](https://docs.mezmo.com/telemetry-pipelines), if you run into any issues or have feedback on either the workshop or Pipeline, please reach out to us at [support@mezmo.com](mailto:support@mezmo.com). {{% /alert %}}

{{% alert title="Prerequisites" color="danger" %}}
Before beginning, you will need the following
* A Mezmo account, [sign up for a trial here](https://www.mezmo.com/sign-up-mezmo-platform).
* [Docker](https://www.docker.com/get-started/)
{{% /alert %}}

## Overview

In this workshop, we will be exploring telemetry data produced with the [OpenTelemetry Demo](https://github.com/braxtonj/opentelemetry-demo) while optimizing it for both MTTR and cost.

To accomplish this we will:
* Create a OpenTelemetry Log, Metric and Trace [Shared Sources](https://docs.mezmo.com/telemetry-pipelines/shared-sources) in Mezmo
* [Configure OpenTelemetry collector](https://github.com/braxtonj/opentelemetry-demo/mezmo-otel-config-extras.yml) with Mezmo Shared Source credentials
* Explore the OpenTelemetry Logs via [Data Profiling](https://docs.mezmo.com/telemetry-pipelines/data-profiling)
* Send log data to [Mezmo Log Analysis](https://docs.mezmo.com/docs)
* [Aggregate](https://docs.mezmo.com/telemetry-pipelines/reduce-processor) specific log patterns
* [Parse](https://docs.mezmo.com/telemetry-pipelines/parse-sequentially-processor) custom Apache data
* [Aggregate OpenTelemetry Metrics](https://docs.mezmo.com/telemetry-pipelines/aggregate-processor) to lower fidelity
* [Sample OpenTelemetry Traces](https://docs.mezmo.com/telemetry-pipelines/sample-processor)
* Configure Pipelines to be [Responsive](https://docs.mezmo.com/telemetry-pipelines/configure-responsive-pipelines) (ie, capture full fidelity when in an incident or deployment state)

## Final Product

In the end you are going to build four Pipelines that look like

* Log Profiling Pipeline
![Final Pipeline: Log Exploration](../../images/pipelines_final_log_exploration.png)
* Log Handler Pipeline
![Final Pipeline: Log Handler](../../images/pipelines_final_log_handler.png)
* Metric Handler Pipeline
![Final Pipeline: Metric Handler](../../images/pipelines_final_metric_handler.png)
* Trace Handler Pipeline
![Final Pipeline: Trace Handler](../../images/pipelines_final_trace_handler.png)

These pipelines will optimize your OpenTelemetry data by aggregating, better parsing and configuring data flow responsively. By allowing for easy, granular control you can ensure the right data ends up where it belongs.

The end result is a system that provides the insight needed, at the fidelity when it's needed, leading to an order of magnitude in savings.
