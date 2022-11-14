---
title: Add a Source
weight: 3
---

## Step 1: Add the Source

This parts easy.  Go to the pipeline you created [previously](/mezmo-workshops/transaction-to-s3/docs/create-pipeline) and click *Add Source*

![Add Source](../../images/add-source_1.png)

From there, just select `HTTP`, give it a *Title* like `Edge Devices`, set *Decoding Method* to `JSON` and click *Save*.

![Added Source](../../images/add-source_2.png)

You now have an endpoint defined that can recieve any data.  If you run into trouble here, please checkout out our comprehensive [Mezmo Platform workshop](/mezmo-workshops/pet-clinic/) to learn how to utilize the sytem in depth.

## Step 2: Configure the Simulation

Now let's point the simulation at the Pipeline.  To do this, we just need the proper `API Key`.  You can get this by opening the Source we just created via the epsilon in the top right of the node and copying the `API Key`.  Click Cancel or Update to back out.

![Source API Key](../../images/add-source_3.png)

Note that you will need to prefix `s_` to the `API Key` when you authenticate to your Pipeline.  See below for an example.

If you previously started the simulation, terminate the docker (*ctrl-c* in the terminal) and then add the `API Key` from above to the envrionemt variable `KEY`.  For example:

```cmd
export KEY=s_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Then, end the simulation (via `ctrl-c` if it's already running) and run the Docker you built or use our DockerHub image with that new `KEY` via one of the two terminal commands below:

### Mezmo's DockerHub image
```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it mezmo/transaction-device-sim:0.1.0
```

### Local Docker image
See [GitHub repo for steps](https://github.com/logdna/financialTransactionDeviceSim#build-the-docker-image) to build the docker image.
```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it transaction-device-sim
```
