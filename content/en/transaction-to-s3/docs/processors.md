---
title: Encrypt and Filter
weight: 5
tags:
  - Encrypt Field Processor
  - Drop Processor
  - Route Processor
---

## Overview

There is a lot we may want to do with the data as you may have saw [while looking at live data](/mezmo-workshops/transaction-to-s3/docs/understand-your-data/).

For this workshop we are going to encrypt PII, drop useless information from events and then route the financial transaction data to S3. Leaving the rest for something else (here we used the [Mezmo Log Analysis](https://www.mezmo.com/log-analysis) but we won't get into that, to learn more check out our [Mezmo Platform](/mezmo-workshops/pet-clinic/) workshop).

But, let's take this one step at a time.

## Step 1: Drop the Unnecessary Buffer

We don't need the buffer, so let's drop it.  We can do this with a simple `drop` processor.  Add a new processor to the pipeline and select `Drop Fields from JSON` from the list.  Give it a title like `Drop buffer` and select the field `.buffer` to drop.  Click `Save`.

![Drop Processor](../../images/add-processor_drop.png)

Then connect this to the Source processor by hovering over the Source till you see a gray half circle.  Click and drag to the right edge of the Drop processor.  Release the mouse and things are linked up.  Data will now flow from the Source to the Processor.  Also note that things will rearrange themselves as you go.

![Drop Processor Connection](../../images/add-processor_connect.gif)

## Step 3: Route Transaction Data

We want to send only the transaction events to S3, to do this we can use a `Route` processor.  Go ahead and add one with the Title `Route Transaction`.

We could group successful and failed transactions (`transaction.result`) but let's seperate the routes.  To do this, we will create two `Outputs`.

For the first, give it a name `Transaction Success`, select an IF and enter `.transaction.result` **equals** `success`.  To weed out any anomalies for later analysis, lets also ensure `.transaction.total_price` is **greater_or_equal** to `0` via an *Added Expresion*.

![Route: Success](../../images/add-processor_route-success.png)

Similarly, for the second output, select *Add route* with the name `Transaction Failed` and enter `.transaction.result` **equals** `fail` as well as the same `.transaction.total_price` treatment.  Click `Save`.

![Route: Failed](../../images/add-processor_route-fail.png)

Connect the `Drop Buffer` processor from Step 1 to the Route processor you just created.

![Route: Connected](../../images/add-processor_route-connected.png)

Note that we will leave the `Unmatched` route untouched for this workshop.  But there are many things that could be done with this data: send to Log Analytics, send to a SIEM, etc.

## Step 4: Encrypt the Credit Card Information

Now, let's encrypt each of the Credit Card fields individually to ensure security and compliance.  The fields we want to encrypt are

* `transaction.cc.cc_number`
* `transaction.cc.cc_exp`
* `transaction.cc.cc_cvv`
* `transaction.cc.cc_name`
* `transaction.cc.cc_zip`

Since each are unique, order doesn't matter so much here.  For each, add an `Encrypt Field` processor using the `AES-256-CFB` algorithm with a 32 character `Encryption Key` (checkout [AllKeysGenerator.com](https://www.allkeysgenerator.com/Random/Security-Encryption-Key-Generator.aspx)to generate each key).  Note that every encryption processor also needs an `Initialization Vector` which will be added to the event itself to allow for decryption down the road.

Click `Save`.

![Encrypt CC Number Dialog](../../images/add-processor_encrypt-cc-number.png)

Once you do this for each of the above fields, you should have 5 floating processors like so

![Encrypt CC: Unconnected](../../images/add-processor_encrypt-cc-unconnected.png)

Now, connect each one sequentially and then link the fail and success routes to the first processor in this group in parallel.  The Pipeline should now look similar to

![Encrypt CC: Connected](../../images/add-processor_encrypt-cc-connected.png)

Now that the transformations have been defined, it's time to sink this up to the S3 and start gathering data.
