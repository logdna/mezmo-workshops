---
title: Create the Pipeline and Add a Source
weight: 3
tags:
  - Mezmo Pipeline
  - HTTP Source
---

{{% alert title="Make sure you have a Mezmo account" color="danger" %}}

In order to complete this workshop, you will need a Mezmo account with Pipeline enabled.  Note that this is a technical preview, Pipeline may not be enabled off the bat.  If you do not have an account, you can sign up for a free trial [here](https://mezmo.com/signup) and/or if you don't have Pipeline enabled you can reach out to us at [workshop-support@mezmo.com](mailto:workshop-support@mezmo.com) to get it set up.

{{% /alert %}}

## Step 1: Create a new Pipeline

Once you have Pipeline enabled, go ahead and [Create a new Pipeline](https://app.mezmo.com/pipelines/pipeline/new).  You will be prompted to name your Pipeline, call it what you will but we will go with `Edge Transaction to S3`.  After creation, you will be taken to the following blank canvas

![Blank Pipeline](../../images/pipeline_blank.png)


## Step 2: Add the Source

This parts easy, click *Add Source*

![Add Source](../../images/add-source_1.png)

From there, just select `HTTP`, give it a *Title* like `Edge Devices`, set *Decoding Method* to `JSON` and click *Save*.

![Added Source](../../images/add-source_2.png)

Now, we need to create an access key corresponding to the new `HTTP` source.  Click on the `HTTP` source to bring up the following panel.

![Added Source](../../images/add-source_4.png)

Go ahead and click the `Create new key` button in the `Access Key Management` section.  Here you can give the new access key a name of `Edge Device Key` and click the `Create` button.

![Added Source](../../images/add-source_5.png)

A new key will be generated for you to use and is displayed on the source details, as well as the HTTP address to send the data to.  Be sure to copy this Access Key somewhere safe for later reference as you will not be able to view it again once the source node is updated.  Click `Update` to save your changes.

![Added Source](../../images/add-source_6.png)

You now have an endpoint defined that can receive any data.  If you run into trouble here, please checkout out our comprehensive [Mezmo Platform workshop](/mezmo-workshops/pet-clinic/) to learn how to utilize the sytem in depth.


## Step 3: Configure the Simulation

Now let's point the simulation at the Pipeline.  To do this, we can use the `API Key` we created in the previous step.

If you [previously started the simulation](/mezmo-workshops/transaction-to-s3/docs/run-simulation/), terminate the docker (*ctrl-c* in the terminal) and then add the `API Key` from above to the envrionemt variable `KEY`.  For example:

```cmd
export KEY=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Then, run the Docker you built or use our DockerHub image with that new `KEY` via one of the two terminal commands below:

### Mezmo's DockerHub image
```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it mezmo/transaction-device-sim:0.1.0
```

### Local Docker image
See [GitHub repo for steps](https://github.com/logdna/financialTransactionDeviceSim#build-the-docker-image) to build the docker image.
```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it transaction-device-sim
```
