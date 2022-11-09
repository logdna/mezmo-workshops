---
title: Getting Started
weight: 1
---

{{% alert title="Prerequisites" %}}
Before beginning, you will need the following

* A Mezmo account with Pipeline enabled.
* Docker
* An AWS account with the necessary permission to administer S3 buckets.

{{% /alert %}}

## Overview

In this workshop, we will be managing signals coming in from many simulated edge devices.

To accomplish this we will

* Create a new Pipleline
* Configure an endpoint (ie, Source) to receive the data
* Filter unneccesary events
* Encrypt PII in motion
* Store required data in S3

## Final Product

In the end you are going to build a Pipeline that looks like

![Final Pipeline](../../images/pipeline_final.png)

This pipeline will drop unnecsary information from events, encrypt a filtered subset to pass those on to S3 for data engineers, and pass the rest on to [Mezmo's Log Analysis](https://www.mezmo.com/log-analysis).
