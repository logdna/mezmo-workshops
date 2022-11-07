---
title: Getting Started
weight: 1
---

{{% alert title="Prerequisites" %}}
For this workshop, you will need the following installed on your machine:

1. JDK 11 or greater installed (https://www.java.com/en/)
2. Port 8080 open inbound/outbound
3. Docker
{{% /alert %}}

## Overview

In this workshop, we will start by taking an existing Java Application (Spring PetClinic) and perform the following:

* Show the steps of how to download, compile, package and run the Spring PetClinic application
* Enable JSON-logging via configuration
* Stand up an OpenTelemetry Collector to collect the logs output by the application and send them to Mezmo Observability Cloud
* Configure an Observability Pipeline on Mezmo to filter out data we don't want to collect
* Define Alerts for conditions that we want notifications for.

## Reference Architecture

The reference architecture that will be established looks like this:

![PetClinic Exercise](../../images/petclinic.png)

[JDK]: https://jdk.java.net


