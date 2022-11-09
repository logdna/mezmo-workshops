---
title: Create Snowflake Tables
weight: 5
---

## Snowflake Integration

There are multiple ways to integrate with snowflake once data flows into S3.  In this section we are going to demonstrate two different ways to integrate the data.
1. **Snowflake External Table** - This method is useful if you do not want to pull all the data into Snowflake, but still want to access it and perform SQL over it.
2. **Snowpipe Internal Table** - This method is useful if you want to continuously load data from S3 into an internal Snowflake table.

## Create Snowflake External Table

At this point we have data flowing to S3.  Now we would like to create an external table that reads data directly from S3.  This is a great option if you have a lot of data and need to only access a small portion of it.



## Next Steps

So we have the data encrypted and only the relevant events are in our S3.  But now what?

Check out our [S3 to Snowflake workshop](#) to learn how to organize dynamically and get the transactions into a data warehouse for further analysis.  Or take a peak at our [Pet Clinic](/pet-clinic/) workshop to utilize [Open Telemetry](https://opentelemetry.io/) and find other ways you can take advantage of events holistically on the Mezmo Platform.
