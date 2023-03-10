---
title: Create a Pipeline with Financial Events
weight: 2
tags:
  - Mezmo Pipeline
  - Demo Logs Source
  - Financial Transactions
---

{{% alert title="Make sure you have a Mezmo account" color="danger" %}}

In order to complete this workshop, you will need a Mezmo account with Pipeline enabled.  Note that this is a `technical preview`, Pipeline may not be enabled off the bat.  If you do not have an account, you can sign up for a free trial [here](https://mezmo.com/signup) and/or if you don't have Pipeline enabled you can reach out to us at [support@mezmo.com](mailto:support@mezmo.com) to get it set up.

{{% /alert %}}

## Step 1: Create a new Pipeline

Once you have Pipeline enabled, go ahead and [Create a new Pipeline](https://app.mezmo.com/pipelines/pipeline/new).  You will be prompted to name your Pipeline, call it what you will but we will go with `Edge Transaction to S3`.  After creation, you will be taken to the following blank canvas

![Blank Pipeline](../../images/sources_pipeline_blank.png)


## Step 2: Add the Demo Financial Transaction Source

We are going to connect up some Demo Logs that simulate financial transaction's from edge devices.  You can think of this as data from Point-of-Sale systems, payment processing devices, etc. While you can [connect many sources](https://docs.mezmo.com/telemetry-pipelines/about-mezmo-telemetry-pipelines), we made it easy and simulate having multiple edge devices streaming through one Source endpoint.

Doing so is easy, click *Add Source*

![Add Source](../../images/sources_add-demo-logs.png)

And from there
* Select `Demo Logs`
* Give it a *Title* like `Edge Devices`
* Set *Format* to `fincancial`
* Click *Save*

![Added Source](../../images/sources_added-demo-logs.png)
