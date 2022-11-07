---
title: Route to S3
weight: 5
---

## Add the S3 Destination

Now that all our data is cleaned, let's get the transactional data into an S3 bucket for later use. 

You will need some information from you AWS account here.  Specifically, you will need

* `AWS Access Key ID`
* `AWS Secret Access Key`
* `AWS Bucket name`
* `AWS Region`

With those in hand, add a new Destination and select `AWS S3`.

We can get fancy with how we route information here (see the [workshop on S3 to Snowflake](#) to learn more), but we will keep it simple for now.  Let's dump all this data into a single bucket for safe keeping.

Give this Destination the title `S3 Fin Transactions`. Then, enter your `Access Key ID` and `Secret Access Key` along with the `Bucket` (we will go with `mezmo-pipeline-financial-transactions` and no `Prefix`).  Next, chose `JSON` for the `Encoding`, no compression and select the `Region` (we will go with `us-east-1`).  Click `Save` when yours looks similar to the image below.

![S3 Destination Definition](../../images/s3_definition.png)

Then, connect up the last Encryption Processor for the CC data to this destination like so

![S3 Destination Connected](../../images/s3_connected.png)

Now, simply Deploy your Pipeline and watch as data comes into S3.  Looking at your bucket, you should begin seeing files like so


![S3 Data in AWS](../../images/s3_final.png)
