---
title: Understand Your Data
weight: 4
tags:
  - Pipeline Tap
  - Monitor View
---

## Why it's important

Fundamental to any task on flowing data is knowing the structure.  While you can see the general format from the edge (in this case, the terminal output running the [Docker simulation](/mezmo-workshops/transaction-to-s3/docs/run-simulation/#step-3-run-it)) or by digging into the [the code](https://github.com/logdna/financialTransactionDeviceSim),  you can now explore the strucutre of disparate events at scale in [Mezmo](https://mezmo.com).

This is enabled in a `Deployed` pipeline via `Tapping`.  To take advantage of this, we just need to connect up a dummy destination, deploy and then tap in the Montiored Pipeline view.

Let's do that now.

## Step 1: Add a Dummy Destination

Click `Add Destination` and select `HTTP`.  Give it the title "Stub HTTP", add `http://localhost:nada` as the `URL` and click `Save`.

![HTTP Dummy Destination Configuration](../../images/add-processor_http-dest-config.gif)

## Step 2: Deploy the Pipeline

To make this Pipeline live for tapping, we need to `Deploy` it.  In the top right corner of the Pipeline view, select `Deploy pipeline` and accept the popup by selecting `Deploy`.

![Deploy Pipeline](../../images/add-processor_deploy-pipeline.gif)

This will take you to the Pipeline Monitoring view where you can see high level statistics and information on the data passing through.

![Monitoring View](../../images/add-processor_monitoring-view.gif)

## Step 3: Tap the Pipeline

Select the edge between the Source and the Destination and click `Insert data tap`.

![Insert Data Tap](../../images/add-processor_insert-data-tap.gif)

A sidebar will slide out where you can select the number of events to be grabbed.  Leave it at `20` and select the `blue play` button.

![Tap Play Button](../../images/add-processor_tap-play-button.gif)

You should begin to events piling up in the sidebar.  Clicking on any line lets you expand and explore the event's structure.

As you can see, we have a couple types of logs flowing through via the devices.  But, for this workshop, the ones we care about contain financial transaction information and are of the form

![Tap Structure Exploration](../../images/add-processor_tap-structure-exploration.gif)


**WILL BE THIS**

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

The other events also contain `datetime`, `device`, `event` and `buffer` but `transaction` is replaced by other unique details.  We won't bother with that for now.