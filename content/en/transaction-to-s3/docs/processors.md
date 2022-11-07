---
title: Encrypt and Filter
weight: 4
---

## Overview

There is a lot we want to do with the data.  You can see the general structure from the terminal output if you ran the Docker in the [first part](/transaction-to-s3/docs/create-pipeline).  At high level, we are going to encrypt PII of all data coming in, drop useless data on failed transactions and then route transaction data to S3 while leaving the rest for something else (here we used the Mezmo Log Analysis but we won't get into that here (to learn more, check out our [Pet Clinic](/pet-clinic/) workshop).

But, let's take this one step at a time.

## Step 0: Understand your data

We have a couple types of logs flowing through via the devices, the ones we care about contain transaction information and are of the form

```json
{
     "datetime": "2022-11-03 23:15:03"
   , "device": {
        "id": "57edad55-83d5-4c03-9108-55ff33034a8d"
      , "name": "/dev/sdi"
      , "vrs": "1.3.8"
      , "location": [
         "41.72059"
         , "-87.70172"
         , "Evergreen Park"
         , "US"
         , "America/Chicago"
      ]
      , "status": "active"
   }
   , "buffer": "03ac2040-2c87-49fe-8f92-60201a968dc7"
   , "transaction": {
         "product_id": "375e7bbc-f01c-4e5a-b624-46d134009d73"
        , "customer_id": "78dc11b5-c5a5-4f4e-a55b-9acd3fbee9a3"
        , "quantity": 18
        , "unit_price": 226.67
        , "net_price": 4080.06
        , "tax": 0.0
        , "total_price": 4080.06
        , "cc": {
              "cc_number": "4718016953220523"
            , "cc_exp": "04/26"
            , "cc_cvv": "319"
            , "cc_name": "Stephanie Barron"
            , "cc_zip": "39065"
      }
      , "result": "fail"
      , "result_reason": "internet connection error"
   }
}
```

The other events also contain datetime, device and buffer but transaction is replaced with the event details.

## Step 1: Drop the unnecessary buffer

We don't need the buffer, so let's drop it.  We can do this with a simple `drop` processor.  Add a new processor to the pipeline and select `Drop Fields from JSON` from the list.  Give it a title like `Drop buffer` and select the field `.buffer` to drop.  Click `Save`.

![Drop Processor](../../images/add-processor_drop.png)

Then connect this to the Source processor by hovering over the Source till you see a gray half circle.  Click and drag to the right edge of the Drop processor.  Release the mouse and things are linked up.  Data will now flow from the Source to the Processor.  Also note that things will rearrange themselves as you go.

![Drop Processor Connection Start](../../images/add-processor_connect-start.png)

![Drop Processor Connection Finish](../../images/add-processor_connect-finish.png)


## Step 2: Encrypt the Device Location

Now let's encrypt some sensitive data.  First will be the Location.

Add an `Encrypt Field` processor witht the title `Encrypt Location` and select `.device.location`.  For Encryption Method, select `AES-256-CFB` and for Encryption Key, enter a 32 character key.  You can use something like [AllKeysGenerator.com](https://www.allkeysgenerator.com/Random/Security-Encryption-Key-Generator.aspx) to generate a key.

Every encryption processor needs an Initialization vector.  This will be added to the event itself to allow for decryption down the road.  Let's add ours as `.iv_device_location`.

Click `Save`.

![Local Encryption](../../images/add-processor_encrypt-location.png)

Link this processor to the `Drop Buffer` processor like you did in the previous step.

## Step 3: Route to filter transaction data

We want to send only the transaction events to S3.  To do this we can use a `Route` processor.  Go ahead and add one with the Title `Route Transaction`.

We could group successful and failed transactions (`transaction.result`) but let's seperate the routes.  To do this, we will create two `Outputs`.

For the first, give it a name `Transaction Success`, select an IF and enter `.transaction.result` **equals** `success`.  To weed out any anomalies for later analysis, lets also ensure `.transaction.total_price` is **greater_or_equal** to `0` via an *Added Expresion*.

![Route: Success](../../images/add-processor_route-success.png)

Similarly, for the second output, select *Add Additional Output* with the name `Transaction Failed` and enter `.transaction.result` **equals** `fail` as well as the same `.transaction.total_price` treatment.

![Route: Failed](../../images/add-processor_route-fail.png)

Connect the `Encrypt Location` processor from Step 2 to the Route processor you just created.


![Route: Connected](../../images/add-processor_route-connected.png)

Note that we will leave the Unmatched route untouched for this workshop but there are many things that could be done with this data (send to Log Analytics, store in a separete S3 for long term storage, etc).

## Step 4: Encrypt the Credit Card Information

Now, let's encrypt each of the Credit Card fields individually.  The fields we want to encrypt are

* `transaction.cc.cc_number`
* `transaction.cc.cc_exp`
* `transaction.cc.cc_cvv`
* `transaction.cc.cc_name`
* `transaction.cc.cc_zip`

Since each are unique fields, order doesn't matter so much here.  However, for each of these, add an Encrypt processor with `AES-256-CFB` which should leave you with 5 floating processors like so

![Encrypt CC: Unconnected](../../images/add-processor_encrypt-cc-unconnected.png)

Now, connect each one sequentially and then link the fail and success routes to the first processor in this group in parallel.  The Pipeline should now look like

![Encrypt CC: Connected](../../images/add-processor_encrypt-cc-connected.png)

It's time to sink this up to the S3 and start gathering data.
