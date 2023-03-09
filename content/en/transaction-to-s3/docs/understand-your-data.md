---
title: "Tapping: Understand Your Data"
weight: 3
tags:
  - Tap
  - Deploy
  - Monitor View
---

## Why It's Important

Fundamental to any task on flowing data is knowing the structure.  While you can see the general format from the edge (ie terminal output, etc from the device) or by digging into the code/databases, your team can now explore the strucutre of disparate events at scale in [Mezmo](https://mezmo.com).

This is enabled in a `Deployed` pipeline via `Tapping`.  To take advantage of this, we simply deploy and then tap in the Montiored Pipeline view.

Let's do that now.

## Step 1: Deploy the Pipeline

To make this Pipeline live for tapping, we need to `Deploy` it.  In the top right corner of the Pipeline view, select `Deploy pipeline` and accept the popup by selecting `Deploy`.

UPDATE
![Deploy Pipeline](../../images/understand-data_deploy-pipeline.png)

This will take you to the Pipeline Monitoring view where you can see high level statistics and information on the data passing through.  Note that it will take a couple minutes to update the information so at first it will look empty.  But eventually it should look something like this:

UPDATE
![Monitoring View](../../images/understand-data_monitoring-view.png)

## Step 2: Tap the Pipeline

To tap any node, we simply hover over the righthand side of the node and click the blue column that overlays.  This can only be done on a `Deployed` pipeline in the `Monitoring` view.

UPDATE
![Insert Data Tap](../../images/understand-data_insert-data-tap.png)

A sidebar will slide out where you can select the number of events to be grabbed.  Leave it at `10` and select the blue `Start Tap` button to the right.  You should begin to events piling up like below.

UPDATE
![Tap Play Button](../../images/understand-data_tap-play-button.png)

You can expand and explore any event's structure by clicking on the triangle to the left of the event.  As you can see, we have a couple types of logs flowing through via different devices.  But, for this workshop, the ones we care about contain financial transaction information and are of the form

![Tap Structure Exploration](../../images/understand-data_tap-structure-exploration.png)

The other events also contain `datetime`, `device`, `event` and `buffer` but `transaction` is replaced by other unique details.  We won't bother with those for this workshop.
