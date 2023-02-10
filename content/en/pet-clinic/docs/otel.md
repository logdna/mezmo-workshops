---
title: Install the OpenTelemetry Collector
weight: 2
description: >
    Install an OpenTelemetry Collector that will be used for collecting logs from the PetClinic app and forwarding to Mezmo.
tags:
- OpenTelemetry
---

## Getting Started

The **OpenTelemetry Collector** is the core component of instrumenting infrastructure and applications.  Its role is to collect and send:

* Infrastructure metrics (disk, cpu, memory, etc)
* Application Performance Monitoring (APM) traces
* Host and application logs

In this workshop, we will be forwarding **logs** from the PetClinic application to Mezmo.

1. To get started, download the latest release (**required minimum of 0.71.0 or later**) of the **OpenTelemetry Contrib Collector**.  This can be found at this site:

    https://github.com/open-telemetry/opentelemetry-collector-releases/releases/latest

    {{% alert title="Choose the correct download" color="danger" %}} 
There are two types of collectors available for download:

* `otelcol-contrib_*`
* `otecol_*`

Be sure to download the **`otelcol-contrib_*`** binary that matches your platform.
    {{% /alert %}}

2. If your binary has an installer, go ahead and run the installer.  For this example, the **darwin** tarball (`otelcol-contrib_0.71.0_darwin_arm64.tar.gz`) will be installed in a new directory located off the user home directory:

    ```bash
    mkdir $HOME/otelcol
    cd $HOME/otelcol
    tar zxvf <location of downloaded file>/otelcol-contrib_0.71.0_darwin_arm64.tar.gz
    ```

   {{% alert title="Use the correct download name" color="warning" %}}
   NOTE: Be sure to replace **`otelcol-contrib_0.71.0_darwin_arm64.tar.gz`** above with the filename you downloaded.
   {{% /alert %}}

3. With the **OpenTelemetry Collector** installed, verify the contents of the directory:

    ```bash
    ls -l
    ```
    ```bash
    total 389112
    -rw-r--r--@   1 bmeyer  staff      11357 Feb  9 00:46 LICENSE
    -rw-r--r--@   1 bmeyer  staff        770 Feb  9 00:46 README.md
    -rwxr-xr-x@   1 bmeyer  staff  211532914 Feb  9 01:04 otelcol-contrib*
    ```

4. Create a file named `config.yaml` in the same directory as the `otelcol-contrib` binary (e.g., `$HOME/otelcol/config.yaml`).  Add the following to the file:

    ```yaml
    #######################################
    receivers:
      otlp:
        protocols:
          grpc:
            endpoint: "0.0.0.0:4317"
          http:
            endpoint: "0.0.0.0:4318"
    
    #######################################
    exporters:
      mezmo:
        ingest_url: "https://logs.mezmo.com/otel/ingest/rest"
        ingest_key: "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
        timeout: 2s
    
      logging:
        verbosity: normal
    
      file:
        path: /tmp/otelcol.json
    
    #######################################
    service:
      pipelines:
        logs:
          receivers: [ otlp ]
          exporters: [ mezmo,logging ]
    ```

    {{% alert title="Set your Ingest Key" %}}
Be sure to change the value of the `exporters` &rarr; `mezmo` &rarr; `ingest_key` key to your own ingest key.  To find your ingest key, sign into https://app.mezmo.com and navigate to **Settings** &rarr; **Organization** &rarr; **API Keys**.  
    {{% /alert %}}

5. **For MacOS users**: when running `otelcol-contrib` for the first time, you may get a security warning from Apple:

    ![Malicious Software Warning](../../images/macos-otel-security1.png)

    To resolve this, open **System Preferences**, select **Security & Privacy**.  On the **General** tab, you should see a warning at the bottom related to **"otelcol-contrib" was blocked from use because it is not from an identified developer.**:

    ![Identified Developer Warning](../../images/macos-otel-security2.png)

    Click the **Allow Anyway** button.

6. Run the **OpenTelemetry Collector**:

    ```bash
    ./otelcol-contrib --config config.yaml
    ```
   
    Again, for MacOS users, if this is the first time running `otelcol-contrib`, you may get a popup message as before:

    ![Malicious Software Warning](../../images/macos-otel-security3.png)

    but this time you will be able to open it by clicking **Open**.  At this point, MacOS will no longer prompt you with these warnings.

7. Confirm the collector starts appropriately.  You should see output similar to:

    ```
    2023-02-09T10:37:53.095-0600	info	service/telemetry.go:90	Setting up own telemetry...
    2023-02-09T10:37:53.095-0600	info	service/telemetry.go:116	Serving Prometheus metrics	{"address": ":8888", "level": "Basic"}
    2023-02-09T10:37:53.095-0600	info	exporter/exporter.go:286	Development component. May change in the future.	{"kind": "exporter", "data_type": "logs", "name": "logging"}
    2023-02-09T10:37:53.096-0600	info	service/service.go:140	Starting otelcol-contrib...	{"Version": "0.71.0", "NumCPU": 10}
    2023-02-09T10:37:53.096-0600	info	extensions/extensions.go:41	Starting extensions...
    2023-02-09T10:37:53.096-0600	warn	internal/warning.go:51	Using the 0.0.0.0 address exposes this server to every network interface, which may facilitate Denial of Service attacks	{"kind": "receiver", "name": "otlp", "data_type": "logs", "documentation": "https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/security-best-practices.md#safeguards-against-denial-of-service-attacks"}
    2023-02-09T10:37:53.096-0600	info	otlpreceiver@v0.71.0/otlp.go:94	Starting GRPC server	{"kind": "receiver", "name": "otlp", "data_type": "logs", "endpoint": "0.0.0.0:4317"}
    2023-02-09T10:37:53.096-0600	warn	internal/warning.go:51	Using the 0.0.0.0 address exposes this server to every network interface, which may facilitate Denial of Service attacks	{"kind": "receiver", "name": "otlp", "data_type": "logs", "documentation": "https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/security-best-practices.md#safeguards-against-denial-of-service-attacks"}
    2023-02-09T10:37:53.096-0600	info	otlpreceiver@v0.71.0/otlp.go:112	Starting HTTP server	{"kind": "receiver", "name": "otlp", "data_type": "logs", "endpoint": "0.0.0.0:4318"}
    2023-02-09T10:37:53.096-0600	info	service/service.go:157	Everything is ready. Begin running and processing data.
    ```

## Test the Collector

In this section, we'll test out the OTEL Collector to confirm it's working as expected.  To get started, we'll create a sample log entry in JSON format.

1. Create a file named `samplelog.json` and add this to the file:

    ```json
    {
      "resourceLogs": [
        {
          "resource": {},
          "scopeLogs": [
            {
              "scope": {},
              "logRecords": [
                {
                  "observedTimeUnixNano": "1664830800000000000",
                  "body": {
                    "stringValue": "This is the sample log message."
                  },
                  "attributes": [
                    {
                      "key": "log.file.name",
                      "value": {
                          "stringValue": "access_log"
                      }
                    }
                  ],
                  "traceId": "",
                  "spanId": ""
                }
              ]
            }
          ]
        }
      ]
    }
    ```

2. With the OTEL Collector running in a separate terminal, run this `curl` command from a new terminal:

    ```bash
    curl -vi http://localhost:4318/v1/logs -H "Content-Type: application/json" -d @samplelog.json
    ```
    ```shell
    *   Trying 127.0.0.1:4318...
    * Connected to localhost (127.0.0.1) port 4318 (#0)
    > POST /v1/logs HTTP/1.1
    > Host: localhost:4318
    > User-Agent: curl/7.85.0
    > Accept: */*
    > Content-Type: application/json
    > Content-Length: 615
    >
    * Mark bundle as not supporting multiuse
      < HTTP/1.1 200 OK
      HTTP/1.1 200 OK
      < Content-Type: application/json
      Content-Type: application/json
      < Date: Thu, 09 Feb 2023 16:35:39 GMT
      Date: Thu, 09 Feb 2023 16:35:39 GMT
      < Content-Length: 21
      Content-Length: 21
    
    <
    * Connection #0 to host localhost left intact
      {"partialSuccess":{}}
    ```

3. Now take a look at the output of the OTEL Collector.  There should be a new entry that looks similar to this:

    ```shell
    2023-02-09T10:37:55.411-0600	info	LogsExporter	{"kind": "exporter", "data_type": "logs", "name": "logging", "#logs": 1}
    2023-02-09T10:37:55.411-0600	info	ResourceLog #0
    Resource SchemaURL:
    ScopeLogs #0
    ScopeLogs SchemaURL:
    InstrumentationScope
    LogRecord #0
    ObservedTimestamp: 2022-10-03 21:00:00 +0000 UTC
    Timestamp: 1970-01-01 00:00:00 +0000 UTC
    SeverityText:
    SeverityNumber: Unspecified(0)
    Body: Str(This is the sample log message.)
    Attributes:
         -> log.file.name: Str(access_log)
    Trace ID:
    Span ID:
    Flags: 0
            {"kind": "exporter", "data_type": "logs", "name": "logging"}
    ```

## Verify Connection to Mezmo

1. Sign into your Mezmo account at https://app.mezmo.com.
2. When you first sign into Mezmo, you will land on the **View** &rarr; **Everything** page.  At the bottom of the page, in the **Search** field, enter `app:OpenTelemetryExporter` as the search string:

   ![App Search](../../images/search-otel.png)

    and hit the **enter** key.  The results of the `curl` command (step 2) should show up in the search results on Mezmo:

   ![App Search](../../images/search-otel-results.png)
