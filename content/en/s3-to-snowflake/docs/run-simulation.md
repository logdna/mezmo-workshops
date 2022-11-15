---
title: Run Simulation
weight: 4
tags:
  - Financial Transactions
---

## Run the Simulation

### Get the Docker
First you will need the Docker to simulate.  You can either use our [DockerHub image](https://hub.docker.com/repository/docker/mezmo/transaction-device-sim) or clone the [GitHub repo](https://github.com/logdna/financialTransactionDeviceSim).  If using the repo, follow the instructions in [README.md](https://github.com/logdna/financialTransactionDeviceSim/blob/main/README.md)to build with one command.  To learn more about using docker, check out [their brief overview](https://docs.docker.com/get-started/overview/).

### Configure the Simulation
Next you need to configure the devices via environment variables.  All you need is your Mezmo Pipeline Source Key (`KEY`) and the number of devices to run (`NUMBER_DEVICES`).  Utilize the http authorization key created in [Step 2: Add the Source](/mezmo-workshops/s3-to-snowflake/docs/create-pipeline.md#step-2-add-the-source).  To do this on MacOS, simply run:

```cmd
export KEY=<http_secret_key>
export NUMBER_DEVICES=25
```

### Run it
In a terminal, run one of the following commands:

#### Mezmo's Docker Hub
```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it mezmo/transaction-device-sim:0.1.0
```

#### Local after Building
See [GitHub repo for steps](https://github.com/logdna/financialTransactionDeviceSim#build-the-docker-image) to build the docker image.
```cmd
docker run -e KEY=${KEY} -e NUMBER_DEVICES=${NUMBER_DEVICES} -it transaction-device-sim
```

#### Output
When running by default, you should see data like the following streaming through in that terminal.
![Device Simulation Ouput](../../images/device_simulation_output.png)

## Verify data is flowing into S3

Now let's verify data is flowing to S3.  It may take a few minutes for new data to show up as data is `buffered` in the pipeline for a few seconds before writing to S3.

![S3 Data](../../images/s3_data.png)