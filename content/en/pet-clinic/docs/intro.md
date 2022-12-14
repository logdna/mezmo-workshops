---
title: Getting Started
weight: 1
description: >
    Start the workshop here.
---

{{% alert title="Questions?" color="danger" %}}For issues or questions related to this Workshop, **please reach out to us** at [workshop-support@mezmo.com](mailto:workshop-support@mezmo.com).  Feel free to share any feedback as well!{{% /alert %}}

## Overview

In this workshop, we will start by taking an existing Java Application (Spring PetClinic) and perform the following:

* Show the steps of how to download, compile, package and run the Spring PetClinic application
* Enable JSON-logging via configuration
* Stand up an **OpenTelemetry Collector** to collect the logs output by the application and send them to the Mezmo platform
* Configure an Observability Pipeline on Mezmo to filter out data we don't want to collect
* Define Alerts for conditions that we want notifications for.

## Prerequisites

To get started, please install the required 3rd party software.

{{% alert title="Prerequisites" %}}
For this workshop, you will need the following installed on your machine:

1. Java Development Kit (JDK) 11 or greater installed
2. Docker Desktop
{{% /alert %}}

1. This workshop utilizes the [**PetClinic**](https://spring-petclinic.github.io/) java app from the Spring Project.  To run it, it requires **JDK 11** or greater to be installed.  The **JDK** can be downloaded from https://www.oracle.com/java/technologies/downloads/.  Download and install it locally.  Verify your Java installation by running the following:

    {{% alert title="Note" color="warning" %}}Be sure to install the **Java SE Development Kit** (JDK), not the Java Runtime Engine (JRE){{% /alert %}}

    ```bash
    java -version
    java version "14.0.2" 2020-07-14
    Java(TM) SE Runtime Environment (build 14.0.2+12-46)
    Java HotSpot(TM) 64-Bit Server VM (build 14.0.2+12-46, mixed mode, sharing)
    ```

    In this example, we are running JDK 14.0.2.

2. **Docker Desktop** is also needed.  It can be downloaded from https://www.docker.com/.  Verify your Docker installation by running the following:

    ```bash
    docker --version
    Docker version 20.10.17, build 100c701
    ```

## Reference Architecture

The reference architecture that will be established looks like this:

![PetClinic Exercise](../../images/petclinic.png)

[JDK]: https://jdk.java.net


