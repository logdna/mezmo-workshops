---
title: "Tapping: Understand Your Data"
weight: 4
tags:
  - Tap
  - Monitor View
---

## Why it's important

Fundamental to any task on flowing data is knowing the structure.  While you can see the general format from the edge (in this case, the terminal output running the [Docker simulation](/mezmo-workshops/transaction-to-s3/docs/run-simulation/#step-3-run-it)) or by digging into the [the code](https://github.com/logdna/financialTransactionDeviceSim),  you can now explore the strucutre of disparate events at scale in [Mezmo](https://mezmo.com).

This is enabled in a `Deployed` pipeline via `Tapping`.  To take advantage of this, we just need to connect up a dummy destination, deploy and then tap in the Montiored Pipeline view.

Let's do that now.

## Step 1: Add a Dummy Destination

Click `Add Destination` and select `HTTP`.  Give it the title `Stub HTTP`, add `localhost.site:4242` as the `URL`, select `JSON` for `Encoding` and click `Save`.  Note that for this the `URL` is not important, we just need a dummy destination to tap.

![HTTP Dummy Destination Configuration](../../images/understand-data_http-dest-config.png)

## Step 2: Connect the Source to the Destination

Now we need to wire the `Source` you created before to the new Dummy `Destination`.  Hover over the right side of the `Source` till you see a gray half circle.  Click that and drap it over top of the `Destination` until it is highlighted blue and simply release.  These two nodes are now connected and data will flow from left to right.

![Source Destination Connection](../../images/understand-data_source-destination-connect.gif)

## Step 3: Deploy the Pipeline

To make this Pipeline live for tapping, we need to `Deploy` it.  In the top right corner of the Pipeline view, select `Deploy pipeline` and accept the popup by selecting `Deploy`.

![Deploy Pipeline](../../images/understand-data_deploy-pipeline.png)

This will take you to the Pipeline Monitoring view where you can see high level statistics and information on the data passing through.  Note that it will take a couple minutes to update the information so at first it will look empty.  But eventually it should look something like this:

![Monitoring View](../../images/understand-data_monitoring-view.gif)

## Step 4: Tap the Pipeline

Select the edge between the Source and the Destination and click `Insert data tap`.

![Insert Data Tap](../../images/understand-data_insert-data-tap.gif)

A sidebar will slide out where you can select the number of events to be grabbed.  Leave it at `20` and select the `blue play` button.

![Tap Play Button](../../images/understand-data_tap-play-button.gif)

You should begin to events piling up in the sidebar.  Clicking on any line lets you expand and explore the event's structure.

As you can see, we have a couple types of logs flowing through via the devices.  But, for this workshop, the ones we care about contain financial transaction information and are of the form

![Tap Structure Exploration](../../images/understand-data_tap-structure-exploration.png)

The other events also contain `datetime`, `device`, `event` and `buffer` but `transaction` is replaced by other unique details.  We won't bother with that for now.
