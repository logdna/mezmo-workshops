---
title: Next Steps
weight: 6
---

{{% alert title="Survey, $25 for your thoughts?" color="warning" %}}
We are offering $25 Amazon giftcards for completing one of the workshops and filling out a short survey on your experience with the Mezmo Pipeline Tech Preview.  If you have 7 min, head on over **[here](https://go.mezmo.com/fy22q4-survey-workshop-platform)**.
{{% /alert %}}

## Recap

We have succesfully connected a fleet of simulated devices to a Mezmo Pipeline to clean, encrypt and route an important subset to S3 for later analysis.  You should have a Pipeline that looks like

![Final Pipeline](../../images/pipeline_final.png)

## Learn More

So we have the data encrypted and the relevant events are separated in our S3.  But now what?

Always recommend peaking at [the docs](https://docs.mezmo.com/telemetry-pipelines), but if you feel like exploring more through workshops check out our [Dynamic S3 to Snowflake Ingestion](/mezmo-workshops/s3-to-snowflake/) workshop to learn how to organize dynamically and get the transactions into a data warehouse for further analysis.  Or take a peak at our [Mezmo Platform](/mezmo-workshops/pet-clinic/) workshop to utilize [Open Telemetry](https://opentelemetry.io/) and find other ways you can take advantage of events holistically on the Mezmo Platform through our Log Analysis add-on.
