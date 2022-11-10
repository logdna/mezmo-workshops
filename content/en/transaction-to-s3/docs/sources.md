---
title: Add a Source
weight: 3
---

## Step 1: Add the Source

This parts easy.  Go to the pipeline you created [previously](/transaction-to-s3/docs/create-pipeline) and click *Add Source*

![Add Source](../../images/add-source_1.png)

From there, just select `HTTP`, give it a *Title* like `Edge Devices`, set *Decoding Method* to `JSON` and click *Save*.

![Added Source](../../images/add-source_2.png)

You now have an endpoint defined that can recieve any data.  If you run into trouble here, please checkout out our comprehensive [Mezmo Platform workshop](/pet-clinic/) to learn how to utilize the sytem to it's fullest.

## Step 2: Configure the Simulation

Now let's point the simulation at the Pipeline.  To do this, we just need the proper `API Key`.  You can get this by opening the Source we just created via the epsilon in the top right of the node and copying the `API Key`.  Click Cancel or Update to back out.

![Source API Key](../../images/add-source_3.png)

Note that you will need to prefix `s_` to the `API Key` when you authenticate to your Pipeline.  See below for an example.

If you previously started the simulation, terminate the docker (*ctrl-c* in the terminal) and then add the `API Key` from above to the envrionemt variable `KEY`.  For example:

```cmd
export KEY=s_c66132d6-5ad5-11ed-bc9d-1a5310034e7d
```

Then, restart the simulation by either running the docker you built (see [README.md](https://github.com/answerbook/financialTransactionDeviceSim/blob/main/README.md) on the repo to learn how to build) or use our DockerHub image.

***NOTE YOU MUST BUILD FROM THE GITHUB FOR NOW.  SORRY :)***

### From local Docker image

```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it transaction-device-sim
```

### From Mezmo's DockerHub image

```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it mezmo/transaction-device-sim
```
