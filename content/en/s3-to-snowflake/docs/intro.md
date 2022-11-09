---
title: Getting Started
weight: 1
---

{{% alert title="Prerequisites" %}}
For this workshop, you will need access to the following:

1. AWS Account
   1. Create / Modify S3 Bucket
   2. Create / Modify IAM Privileges
2. Snowflake account
   1. Create /  Modify Schemas, Tables, Stages and Pipes

{{% /alert %}}

## Overview

In this workshop, we will be taking signals coming from multiple edge nodes and loading them into Snowflake.

To accomplish this, we will
* Create a new Pipeline
* Configure an endpoint to receive the data (ie, a Source (LINK TO DOCS))
* Send data to S3 Destination, utilizing dynamic pathing
* Create external table in Snowflake accessing S3 directly
* Create Snowpipe in Snowflake that automatically loads data into an internal table.


