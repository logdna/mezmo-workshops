---
title: Encrypt and Filter
weight: 4
tags:
  - Encrypt Field Processor
  - Remove Fields Processor
  - Route Processor
---

## Overview

There is a lot we may want to do with the data as you may have saw [while looking at live data](/mezmo-workshops/transaction-to-s3/docs/understand-your-data/).

For this workshop we are going to encrypt PII, drop useless information from events and then route the financial transaction data to a specific S3 bucket while sending that and everything else to the teams general S3 bucket.

While we are going with S3 for this workshop, we have many other `Destinations` available today and others, like [Mezmo Log Analysis](https://docs.mezmo.com/telemetry-pipelines/mezmo-log-analysis), that are `experimental`.  If interested in access to `experimental` features, reach out to your account representative or [support@mezmo.com](mailto:support@mezmo.com).

But, let's take this one step at a time.

{{% alert title="Note on Editing a Deployed Pipeline" color="warning" %}}
If you previously `Deployed` your pipeline in [Tapping: Understand Your Data](/mezmo-workshops/transaction-to-s3/docs/understand-your-data/), then you need to go into Edit mode on your Pipeline.  You can do this by selecting `Edit pipeline` in the top right corner of the Pipeline view.
{{% /alert %}}

## Step 1: Drop the Unnecessary Buffer 

We don't need the buffer, so let's drop it.  First select *Add Processor* which will pull up a dialog like so

![Add Processor List](../../images/processors_add-processor-list.png)

* Select `Remove Fields` from the list
* Give it a title like `Drop buffer`
* Enter the field `.buffer` to drop it
* Click `Save`.

![Remove Fields Processor](../../images/processors_remove-fields.png)

Then connect this to the Source processor by hovering over the Source till you see a gray half circle.  Click and drag to the right edge of the `Drop buffer` node.  Release the mouse and things are linked up.  Data will now flow left to right, from the Source to the Processor.  Also note that things will rearrange themselves as you go.

![Drop Processor Connection](../../images/processors_connect.gif)

## Step 3: Route Transaction Data

We want to send only the transaction events to S3, to do this we can use a `Route` processor.  Go ahead and add one with the Title `Transactions`.

We could group successful and failed transactions (`.transaction.result`) but let's seperate the routes.  To do this, we will create two `Outputs`.

For the first route:
* Give it the name `Transaction Success`
* Select an IF and enter `.transaction.result` **equals** `true`
* To weed out any anomalies for later analysis, lets also ensure `.transaction.total_price` is **greater_or_equal** to `0` via *Add Expresion*.

![Route: Success](../../images/processors_route-success.png)

Similarly, for the second output:
* Select *Add route* and enter the name `Transaction Fail`
* Configure the IF with `.transaction.result` **equals** `false`
* Eliminate anomalies with the `.transaction.total_price` expression from above.

Click `Save`.

![Route: Failed](../../images/processors_route-fail.png)

Connect the `Drop Buffer` processor from Step 1 to the Route processor you just created.

![Route: Connected](../../images/processors_route-connected.png)

Note that we will leave the `Unmatched` route untouched for this workshop.  But there are many things that could be done with this data: send to Log Analytics, send to a SIEM, etc.

## Step 4: Encrypt the Credit Card Information

Now, let's encrypt each of the credit card fields individually to ensure security and compliance.  The fields we want to encrypt are

* `.transaction.cc.cc_number`
* `.transaction.cc.cc_exp`
* `.transaction.cc.cc_cvv`
* `.transaction.cc.cc_zip`
* `.transaction.cc.cc_name`

Since each are unique, order doesn't matter so much here.  For each:
* Add an `Encrypt Field` processor
* Choose the `AES-256-CFB` algorithm with a 32 character `Encryption Key` (checkout [AllKeysGenerator.com](https://www.allkeysgenerator.com/Random/Security-Encryption-Key-Generator.aspx)to generate each key)
* Add an `Initialization Vector` and name it whatever you like.  Note that every encryption processor needs to add a key like this to the event itself for `decryption` down the road.
* Click `Save`.

![Encrypt CC Number Dialog](../../images/add-processor_encrypt-cc-number.png)

Once you do this for each of the above fields (or don't, it's just a demo pipeline afterall), you should have 5 floating processors like so

![Encrypt CC: Unconnected](../../images/processors_encrypt-cc-unconnected.png)

Now, connect each one sequentially and then link the fail and success routes to the first processor in this group in parallel.  The Pipeline should now look similar to

![Encrypt CC: Connected](../../images/processors_encrypt-cc-connected.png)

Now that the transformations have been defined, it's time to sink this all up to the S3 and start gathering data.
