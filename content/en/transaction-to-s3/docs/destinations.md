---
title: Connect to S3 and Deploy
weight: 5
tags:
  - S3 Destination
  - Mezmo Log Analysis Destination
---

## Step 1: Add S3 Destination

With our data cleaned, let's get the financial transactions into an S3 bucket for later use. 

You will need the following information from you AWS account:

* `AWS Access Key ID`
* `AWS Secret Access Key`
* `AWS Bucket Name`
* `AWS Region`

{{% alert title="Note" color="danger" %}}
* The `IAM User` associated with that `Access Key ID` needs to have at least the `PutObject` privlege in AWS.
* You must create the `Bucket` with that `Name` in AWS yourself.

For more details you can check out the [IAM and S3 section](/mezmo-workshops/s3-to-snowflake/docs/create-s3-bucket-and-user/#step-2-create-aws-mezmo-pipeline-user) of the S3 to Snowflake workshop.
{{% /alert %}}

With those in hand, add a new `Destination` and select `AWS S3`.

We can get fancy with how we route information here (see the [workshop on S3 to Snowflake](/mezmo-workshops/s3-to-snowflake/) to learn more), but we will keep it simple for now.  Let's dump all this data into a single bucket for safe keeping.

Give this `Destination` the title `S3 Fin Transactions`. Then, enter your `Access Key ID` and `Secret Access Key` along with the `Bucket` (we will go with `mezmo-pipeline-financial-transactions` and no `Prefix`).  Next, chose `JSON` for the `Encoding`, no compression and select the `Region` (we will go with `us-east-1`).  Click `Save` when yours looks similar to the image below.

![S3 Destination Definition](../../images/s3_definition.png)

Then, connect up the last Encryption Processor for the CC data to this destination like so

![S3 Destination Connected](../../images/s3_connected.png)

## Step 2: Add Destination for Unmatched Route

The last step before deploying is to connect that unmatched route.  Let's throw the rest of the data into Mezmo's Log Analysis.

First you need to grab the LA API Key.  To do this,
open `Settings -> Organization -> API Keys` (or follow [this link](https://app.mezmo.com/manage/api-keys)).  Then Generate an Ingestion Key and copy it to your clipboard.  We will need this shortly.

Now, go back to your Pipeline and add a new Destination.  This time select `Mezmo Log Analysis`.  Give it a title like `LA`, specify a `Hostname` of your choice and paste that API Key into `Ingestion Key`.  Click `Save`.

![Mezmo Log Analysis Dialog](../../images/la_dialog.png)

It's time to connect up that `Destination` to the `Unmatched Route` from earlier.  You should end up with something like this

![Final Connected Pipeline](../../images/la_connected.png)

## Step 3: Deploy

Now, simply `Deploy pipeline` in the top right.  After the Pipeline should no longer be a draft and look like this

![S3 Data in AWS](../../images/pipeline_deployed.png)

Watch as data comes into both S3 and Log Analysis.  Looking at your bucket, you should begin seeing files like so

![S3 Data in AWS](../../images/aws_s3_data_final.png)

**Note that it will take up to 5 min to first see data flowing in to S3.** This is due to batching, no data will be dropped.
