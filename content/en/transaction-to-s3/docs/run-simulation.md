---
title: Run the Simulation
weight: 2
tags:
  - Financial Transactions
---

## Step 1: Get the Docker
First you will need the Docker to simulate.  You can either use our [DockerHub image](https://hub.docker.com/repository/docker/mezmo/transaction-device-sim) or clone the [GitHub repo](https://github.com/logdna/financialTransactionDeviceSim).  If using the repo, follow the instructions in [README.md](https://github.com/logdna/financialTransactionDeviceSim/blob/main/README.md)to build with one command.  To learn more about using docker, check out [their brief overview](https://docs.docker.com/get-started/overview/).

## Step 2: Configure the Simulation
Next you need to configure the devices via `environment variables`.  All you need is your Mezmo Pipeline Source Key (`KEY`) and the number of devices to run (`NUMBER_DEVICES`).  We will snag the key in [the next step](/mezmo-workshops/transaction-to-s3/docs/sources/) but for now you can just use something made up like `NADA`.  To do this on MacOS Terminal, simply run:

```cmd
export KEY=NADA
export NUMBER_DEVICES=25
```

## Step 3: Run it
You could technically skip this part till you have a source to hit, but what the hay.  We will update with an actual key later.  In a terminal, run one of the following commands:

### Mezmo's Docker Hub
```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it mezmo/transaction-device-sim:0.1.0
```

### Local after Building
See [GitHub repo for steps](https://github.com/logdna/financialTransactionDeviceSim#build-the-docker-image) to build the docker image.
```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it transaction-device-sim
```

### Output
When running by default, you should see data like the following streaming through in that terminal.
![Device Simulation Ouput](../../images/device_simulation_output.png)

