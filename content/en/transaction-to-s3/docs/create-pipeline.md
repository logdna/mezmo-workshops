---
title: Create the Pipeline and Run Simulation
weight: 2
---

{{% alert title="Make sure you have a Mezmo account" %}}

In order to complete this workshop, you will need a Mezmo account with Pipeline enabled.  Note that this as a technical preview, Pipeline may not be enabled off the bat.  If you do not have an account, you can sign up for a free trial [here](https://mezmo.com/signup) and/or if you don't have Pipeline enabled you can reach out to us at [pipeline@mezmo.com](mailto:pipeline@mezmo.com) to get it set up.

If you run into any issues or have feedback on either the workshop or Pipeline, please reach out to us at [pipeline@mezmo.com](mailto:pipeline@mezmo.com).

{{% /alert %}}

## Step 1: Create a new Pipeline

Once you have Pipeline enabled, go ahead and [Create a new Pipeline](https://app.mezmo.com/pipelines/pipeline/new).  You will be prompted to name your Pipeline, call it what you will but we will go with `Edge Transaction to S3`.  After creation, you will be taken to the following blank canvas

![Blank Pipeline](../../images/pipeline_blank.png)

## Step 2: Run the Simulation

### Get the Docker
First you will need the Docker to simulate.  You can either use our provided docker [NOT_UPLOADED_YET](#) or clone the [GitHub repo](https://github.com/logdna/financialTransactionDeviceSim).  If using the repo, follow the instructions in [README.md](https://github.com/logdna/financialTransactionDeviceSim/blob/main/README.md)to build with one command.

### Configure the Simulation
Next you need to configure the devices via environment variables.  All you need is your Mezmo Pipeline Source Key (`KEY`) and the number of devices to run (`NUMBER_DEVICES`).  We will snag the key in [the next step](/content/en/transaction-to-s3/docs/sources.md) but for now you can just use something made up like `NADA`.  To do this on MacOS, simply run:

```cmd
export KEY=NADA
export NUMBER_DEVICES=25
```

### Run it
You could technically skip this part till you have a source to hit, but what the hay.  We will update with an actual key later.  You should see the following output

![Device Simulation Ouput](../../images/device_simulation_output.png)

#### From Docker Hub (NOT UPLOADED TO DOCKER HUB YET)
```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it mezmo/transaction-device-sim
```

#### Local after Building (see GitHub repo for steps)
```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it transaction-device-sim
```

