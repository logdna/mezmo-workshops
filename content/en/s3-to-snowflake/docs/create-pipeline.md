---
title: Create the Pipeline
weight: 3
tags:
  - Mezmo Pipeline
  - HTTP Source
  - S3 Destination
  - Financial Transactions
---

{{% alert title="Make sure you have a Mezmo account" %}}

In order to complete this workshop, you will need a Mezmo account with Pipeline enabled.  Note that this is a technical preview, Pipeline may not be enabled off the bat.  If you do not have an account, you can sign up for a free trial [here](https://app.mezmo.com/signup) and/or if you don't have Pipeline you can reach out to us at [workshop-support@mezmo.com](mailto:workshop-support@mezmo.com) to get it enabled.

{{% /alert %}}

## Step 1: Create a new Pipeline

Once you have Pipeline enabled, go ahead and [Create a new Pipeline](https://app.mezmo.com/pipelines/pipeline/new).  You will be prompted to name your Pipeline, call it what you will but we will go with `Snowflake-Integration`.  After creation, you will be taken to the following blank canvas

![Blank Pipeline](../../images/pipeline_blank.png)


## Step 2: Add the Source

This parts easy.  Go to the pipeline you created previously and click *Add Source*

![Add Source](../../images/add_sources_1.png)

From there, just select `HTTP`, give it a *Title* like `Edge Devices`, set *Decoding Method* to `JSON` and click *Save*.

![Add Source](../../images/add_sources_2.png)

You now have an endpoint defined that can receive any data.  If you run into trouble here, please checkout out our comprehensive [Mezmo Platform workshop](/mezmo-workshops/pet-clinic/) to learn how to utilize the sytem to it's fullest.

Make sure to grab the API key from the HTTP Source after adding it.

![Add Source](../../images/add_sources_3.png)

## Step 3: Add the S3 Destination

Next we will add S3 as a destination. 

You will need some information from you AWS account here.  Specifically, you will need the following you created during [step 2](/mezmo-workshops/s3-to-snowflake/docs/create-s3-bucket-and-user/#step-2-create-aws-mezmo-pipeline-user) of the previous section.

* `AWS Access Key ID`
* `AWS Secret Access Key`
* `AWS Bucket name`
* `AWS Region`

With access information in hand
* Add a new Destination and select `AWS S3`
* Give this Destination the title `Snowflake Bucket`
* Enter your `Access Key ID` and `Secret Access Key`
* Enter `Bucket` name (we will go with `mezmo-use1-snowflake-demo`)
* Enter the prefix of `device-sim/event_date=%F/event_name={{ .message.event }}/`.  This prefix allows for dynamic location routing and will store data including date and event name coming from the event field.  Example `device-sim/event_date=2022-11-09/event_name=transaction/`
* Chose `text` for the `Encoding`, with a compression of `gzip`
* Select the `Region` where you created your bucket (in this example we use `us-east-1`)
* Click the `save` button

Your destination should look similar to the image below.

*Note: Make sure messages going to this S3 destination contain the dynamic field as part of the path.  Any events that do not have the fields will not go to S3.*

![S3 Destination Definition](../../images/add_destination_1.png)

Now let's connect the `Edge Device` Source to the `Snowflake Bucket` Destination.  For this example, we are not going to use any processors.

![S3 Destination Connected](../../images/s3_connected.png)

Now, simply Deploy your Pipeline.