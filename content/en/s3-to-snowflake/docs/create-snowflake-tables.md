---
title: Create Snowflake Tables
weight: 5
tags:
  - Snowflake
---

## Snowflake Integration

There are multiple ways to integrate with snowflake once data flows into S3.  In this section we are going to demonstrate two different ways to integrate the data.
1. **Snowflake External Table** - This method is useful if you do not want to pull all the data into Snowflake, but still want to access it and perform SQL over it.
2. **Snowpipe Internal Table** - This method is useful if you want to continuously load data from S3 into an internal Snowflake table.

## Create Snowflake External Table

At this point we have data flowing to S3.  Now we would like to create an external table that reads data directly from S3.  This is a great option if you have a lot of data and need to only access a small portion of it.

The below SQL will
1. Create a new schema.
2. Create a new file format.  This file format indicates the data is JSON and compressed with GZIP.
3. Create a new stage.  This is a pointer to an S3 location with access credentials and the file format the data is.  You will need to substitute the S3 bucket name we created previously and the Snowflake User AWS Access Key and Secret Key.
4. Create a new external table.  This external table utilizes partitioning, so it does not have to read all the data in when given a partition in the where clause of the select.

``` sql
USE DATABASE MEZMO_DEMO;

CREATE SCHEMA DEVICE_DEMO;

CREATE FILE FORMAT MEZMO_DEMO.DEVICE_DEMO.JSON_FORMAT TYPE=JSON COMPRESSION=GZIP;

CREATE STAGE MEZMO_DEMO.DEVICE_DEMO.mezmo_use1_snowflake_demo url='s3://<bucket>'
credentials=(aws_key_id='<key>' aws_secret_key='<secret>')
file_format=MEZMO_DEMO.DEVICE_DEMO.JSON_FORMAT;

CREATE OR REPLACE EXTERNAL TABLE MEZMO_DEMO.DEVICE_DEMO.EVENT_EXT
WITH location =  @MEZMO_DEMO.DEVICE_DEMO.mezmo_use1_snowflake_demo/device-sim/
FILE_FORMAT=MEZMO_DEMO.DEVICE_DEMO.JSON_FORMAT;

CREATE OR REPLACE EXTERNAL TABLE MEZMO_DEMO.DEVICE_DEMO.EVENT_EXT (
EVENT_DATE DATE as to_date(split_part(split_part(METADATA$FILENAME, '/', 2), '=', 2)), 
EVENT_NAME VARCHAR as split_part(split_part(METADATA$FILENAME, '/', 3), '=', 2)
)
PARTITION BY ( EVENT_DATE, EVENT_NAME )
WITH location =  @MEZMO_DEMO.DEVICE_DEMO.mezmo_use1_snowflake_demo/device-sim/
FILE_FORMAT=MEZMO_DEMO.DEVICE_DEMO.JSON_FORMAT;
```

Finally, we want to select some data to verify our connection is working.  You will need to substitute the event_date field with a date from your S3 bucket.

``` sql
Select event_date, 
VALUE:transaction.result::VARCHAR as transaction_result, 
sum(VALUE:transaction.total_price)
from MEZMO_DEMO.DEVICE_DEMO.EVENT_EXT where event_date = '<date>' and event_name = 'transaction'
GROUP BY event_date, VALUE:transaction.result;
```

![External Table Select](../../images/snowflake_sql_1.png)


## Create Snowpipe Internal Table

Now we would like to create an snowpipe that reads data from S3 and loads it directly to a internal table.  This option is great if you want to do near real time reporting as data is constantly loaded to the database.

The below SQL will
1. Create a new schema.
2. Create a new file format.  This file format indicates the data is JSON and compressed with GZIP.
3. Create a new stage.  This is a pointer to an S3 location with access credentials and the file format the data is.  You will need to substitute the S3 bucket name we created previously and the Snowflake User AWS Access Key and Secret Key.
4. Create a new pipe.  This pipe will utilize a copy command that issues a select statement for each file that lands in S3.

``` sql
USE DATABASE MEZMO_DEMO;

CREATE SCHEMA DEVICE_DEMO;

CREATE FILE FORMAT MEZMO_DEMO.DEVICE_DEMO.JSON_FORMAT TYPE=JSON COMPRESSION=GZIP;

CREATE STAGE MEZMO_DEMO.DEVICE_DEMO.mezmo_use1_snowflake_demo url='s3://<bucket>'
credentials=(aws_key_id='<key>' aws_secret_key='<secret>')
file_format=MEZMO_DEMO.DEVICE_DEMO.JSON_FORMAT;

CREATE OR REPLACE TABLE MEZMO_DEMO.DEVICE_DEMO.EVENT_PIPE ( 
  EVENT VARIANT,
  EVENT_DATE DATE,
  EVENT_NAME VARCHAR(64)
);

CREATE PIPE MEZMO_DEMO.DEVICE_DEMO.EVENT_PIPE AUTO_INGEST=TRUE AS
    COPY INTO MEZMO_DEMO.DEVICE_DEMO.EVENT_PIPE 
    FROM (Select $1::VARIANT, 
          to_date(split_part(split_part(METADATA$FILENAME, '/', 2), '=', 2)),
          split_part(split_part(METADATA$FILENAME, '/', 3), '=', 2)
          FROM @MEZMO_DEMO.DEVICE_DEMO.mezmo_use1_snowflake_demo/device-sim/)
```

Now that we have the pipe created.  We need to get the SQS queue information associated with the pipe, so that we can enable event notification on the s3 bucket, that will tell snowflake a new file exists when the pipeline creates it.

``` sql
SHOW PIPES;
```

Copy the SQS arn from the notification_channel field.

![Show Pipes](../../images/snowflake_notification_channel.png)

With the SQS arn in hand, navigate back to your S3 bucket and add a new Event Notification.  This can be found on the properties tab of the bucket.

When creating the event give it an event name (we are using `Snowpipe S3 Event`) and a Prefix (we are using `device-sim/`).  Be sure to select the `All object create events` under event type.  Under the Destinations section, select `SQS queue` and `Enter SQS queue ARN`.  Provide the arn from the `SHOW PIPES` command in snowflake.

![S3 Event](../../images/s3_event.png)

Make sure the simulation is running and data is flowing through as only new files will be loaded via Snowpipe.

After a few minutes, issue a select statement against the snowpipe table.

``` sql
Select * from MEZMO_DEMO.DEVICE_DEMO.EVENT_PIPE;
```

You should see data in the table like the following.

![Snowpipe Select](../../images/snowpipe_select.png)