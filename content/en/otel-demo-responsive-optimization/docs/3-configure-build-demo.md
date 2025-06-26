---
title: Configure the OpenTelemetry Demo and Build
weight: 3
tags:
  - Mezmo Pipeline
  - OpenTelemetry Demo
  - Docker
---

## Step 1: Clone the OpenTelemetry Demo

As we are using a modified version of the OpenTelemetry Demo with expanded logs we will need to: clone the repo, modify the config, and then build from scratch.

Run `git clone https://github.com/braxtonj/opentelemetry-demo` into a folder of your choice.

## Step 2: Configure `mezmo-otel-config-extras.yml` with Shared Source Credentials

Grab the URL and saved Access Keys from your Log, Metric and Trace Shared Sources (don't worry if you didn't save the key——you can always create a new one).  Modify [opentelemetry-demo/mezmo-otel-config-extras.yml](https://github.com/braxtonj/opentelemetry-demo/blob/main/mezmo-otel-config-extras.yml) with the proper credentials and save.  Note that Log, Metrics and Traces will each have their own unique URL and Access Keys.

## Step 3: Build and Run the OpenTelmetry Demo

To easily build from scratch, simply run the convenience script `sh run.sh` from the demo's root folder.  This will build all necessary resources and deploy the demo to [localhost:8080](localhost:8080).

{{% alert title="Build Time" color="danger" %}}
Note that it may take up to **15 minutes** to build the demo from scratch the first time.  Recommend starting this and then continuing on with the workship while things build and deploy.
{{% /alert %}}
