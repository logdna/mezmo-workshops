---
title: Encrypt and Filter
weight: 4
---

## Overview

There is a lot we may want to do with the data.  At a high level, we are going to encrypt PII, drop useless information from events and then route the financial transaction data to S3 while leaving the rest for something else (here we used the [Mezmo Log Analysis](https://www.mezmo.com/log-analysis) but we won't get into that, to learn more check out our [Pet Clinic](/mezmo-workshops/pet-clinic/) workshop).

But, let's take this one step at a time.

## Step 0: Understand your data

You can see the general structure from the terminal output running the [Docker simulation](/mezmo-workshops/transaction-to-s3/docs/create-pipeline/#run-it).  We have a couple types of logs flowing through via the devices but the ones we care about contain financial transaction information and are of the form

```json
{
   "device": {
      "id": "bed2209a-7acd-41c8-9e7d-1a10064b0d51",
      "name": "/dev/vdz",
      "vrs": "1.4.0",
      "location": [
         "41.54566",
         "-71.29144",
         "Middletown",
         "US",
         "America/New_York"
      ],
      "status": "active"
   },
   "buffer": "b812e703-ca7b-452f-8170-d1e9baa9fa40",
   "datetime": "2022-11-09T21:00:39.852667",
   "transaction": {
      "product_id": "3b1fd6e5-d9c6-494c-b8d4-6f78b6d4555a",
      "customer_id": "e36fa5e3-8dde-4818-95b9-912f87c3bd74",
      "quantity": 20,
      "unit_price": 158.75,
      "net_price": 3175.0,
      "tax": 0.06,
      "total_price": 3365.5,
      "cc": {
         "cc_number": "30147640131796",
         "cc_exp": "01/25",
         "cc_cvv": "1765",
         "cc_name": "Michelle Watts",
         "cc_zip": "03398"
      },
      "result": "success",
      "result_reason": "card_accepted"
   },
   "event": "transaction"
}
```

The other events also contain `datetime`, `device` and `buffer` but `transaction` is replaced by other event details.

## Step 1: Drop the unnecessary buffer

We don't need the buffer, so let's drop it.  We can do this with a simple `drop` processor.  Add a new processor to the pipeline and select `Drop Fields from JSON` from the list.  Give it a title like `Drop buffer` and select the field `.buffer` to drop.  Click `Save`.

![Drop Processor](../../images/add-processor_drop.png)

Then connect this to the Source processor by hovering over the Source till you see a gray half circle.  Click and drag to the right edge of the Drop processor.  Release the mouse and things are linked up.  Data will now flow from the Source to the Processor.  Also note that things will rearrange themselves as you go.

![Drop Processor Connection](../../images/add-processor_connect.gif)

## Step 2: Route to filter transaction data

We want to send only the transaction events to S3, to do this we can use a `Route` processor.  Go ahead and add one with the Title `Route Transaction`.

We could group successful and failed transactions (`transaction.result`) but let's seperate the routes.  To do this, we will create two `Outputs`.

For the first, give it a name `Transaction Success`, select an IF and enter `.transaction.result` **equals** `success`.  To weed out any anomalies for later analysis, lets also ensure `.transaction.total_price` is **greater_or_equal** to `0` via an *Added Expresion*.

![Route: Success](../../images/add-processor_route-success.png)

Similarly, for the second output, select *Add Additional Output* with the name `Transaction Failed` and enter `.transaction.result` **equals** `fail` as well as the same `.transaction.total_price` treatment.

![Route: Failed](../../images/add-processor_route-fail.png)

Connect the `Drop Buffer` processor from Step 1 to the Route processor you just created.

![Route: Connected](../../images/add-processor_route-connected.png)

Note that we will leave the `Unmatched` route untouched for this workshop.  But there are many things that could be done with this data: send to Log Analytics, send to a SIEM, etc.

## Step 3: Encrypt the Credit Card Information

Now, let's encrypt each of the Credit Card fields individually to ensure security.  The fields we want to encrypt are

* `transaction.cc.cc_number`
* `transaction.cc.cc_exp`
* `transaction.cc.cc_cvv`
* `transaction.cc.cc_name`
* `transaction.cc.cc_zip`

Since each are unique, order doesn't matter so much here.  For each of these, add an Encrypt processor with `AES-256-CFB` which should leave you with 5 floating processors like so

![Encrypt CC: Unconnected](../../images/add-processor_encrypt-cc-unconnected.png)

Now, connect each one sequentially and then link the fail and success routes to the first processor in this group in parallel.  The Pipeline should now look similar to

![Encrypt CC: Connected](../../images/add-processor_encrypt-cc-connected.png)

Now that the transformations have been defined, it's time to sink this up to the S3 and start gathering data.
