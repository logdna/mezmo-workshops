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

1. To get started, download the latest release of the **OpenTelemetry Contrib Collector**.  This can be found at this site:

    https://github.com/open-telemetry/opentelemetry-collector-releases/releases/latest

    {{% alert title="Choose the correct download" color="danger" %}} 
There are two types of collectors available for download:

* `otelcol-contrib_*`
* `otecol_*`

Be sure to download the `otelcol-contrib_*` binary that matches your platform.
    {{% /alert %}}

2. If your binary has an installer, go ahead and run the installer.  For this example, the **darwin** tarball will be installed in a new directory located off the user home directory: 

    ```bash
    mkdir $HOME/otelcol
    cd $HOME/otelcol
    tar zxvf <location of downloaded file>/otelcol-contrib_0.61.0_darwin_arm64.tar.gz
    ```

3. With the **OpenTelemetry Collector** installed, verify the contents of the directory:

    ```bash
    ls -l
    ```
    ```bash
    total 389112
    -rw-r--r--@ 1 bmeyer  staff      11357 Sep 28 21:47 LICENSE
    -rw-r--r--@ 1 bmeyer  staff        770 Sep 28 21:47 README.md
    -rwxr-xr-x@ 1 bmeyer  staff  199205762 Sep 28 22:15 otelcol-contrib*
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
        logLevel: debug
    
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
    2022/10/03 15:20:24 proto: duplicate proto type registered: jaeger.api_v2.PostSpansRequest
    2022/10/03 15:20:24 proto: duplicate proto type registered: jaeger.api_v2.PostSpansResponse
    2022-10-03T15:20:24.222-0500	info	service/telemetry.go:118	Setting up own telemetry...
    2022-10-03T15:20:24.222-0500	info	service/telemetry.go:159	Serving Prometheus metrics	{"address": ":8888", "level": "basic"}
    2022-10-03T15:20:24.222-0500	info	components/components.go:30	In development component. May change in the future.	{"kind": "exporter", "data_type": "logs", "name": "logging", "stability": "in development"}
    2022-10-03T15:20:24.222-0500	debug	components/components.go:28	Alpha component. May change in the future.	{"kind": "exporter", "data_type": "logs", "name": "file", "stability": "alpha"}
    2022-10-03T15:20:24.222-0500	debug	components/components.go:28	Beta component. May change in the future.	{"kind": "receiver", "name": "otlp", "pipeline": "logs", "stability": "beta"}
    2022-10-03T15:20:24.222-0500	info	service/service.go:112	Starting otelcol-contrib...	{"Version": "0.61.0", "NumCPU": 10}
    2022-10-03T15:20:24.222-0500	info	extensions/extensions.go:42	Starting extensions...
    2022-10-03T15:20:24.222-0500	info	pipelines/pipelines.go:74	Starting exporters...
    2022-10-03T15:20:24.222-0500	info	pipelines/pipelines.go:78	Exporter is starting...	{"kind": "exporter", "data_type": "logs", "name": "file"}
    2022-10-03T15:20:24.222-0500	info	pipelines/pipelines.go:82	Exporter started.	{"kind": "exporter", "data_type": "logs", "name": "file"}
    2022-10-03T15:20:24.222-0500	info	pipelines/pipelines.go:78	Exporter is starting...	{"kind": "exporter", "data_type": "logs", "name": "logging"}
    2022-10-03T15:20:24.222-0500	info	pipelines/pipelines.go:82	Exporter started.	{"kind": "exporter", "data_type": "logs", "name": "logging"}
    2022-10-03T15:20:24.222-0500	info	pipelines/pipelines.go:86	Starting processors...
    2022-10-03T15:20:24.222-0500	info	pipelines/pipelines.go:98	Starting receivers...
    2022-10-03T15:20:24.222-0500	info	pipelines/pipelines.go:102	Receiver is starting...	{"kind": "receiver", "name": "otlp", "pipeline": "logs"}
    2022-10-03T15:20:24.222-0500	info	zapgrpc/zapgrpc.go:174	[core] [Server #1] Server created	{"grpc_log": true}
    2022-10-03T15:20:24.222-0500	info	otlpreceiver/otlp.go:70	Starting GRPC server on endpoint 0.0.0.0:4317	{"kind": "receiver", "name": "otlp", "pipeline": "logs"}
    2022-10-03T15:20:24.222-0500	info	otlpreceiver/otlp.go:88	Starting HTTP server on endpoint 0.0.0.0:4318	{"kind": "receiver", "name": "otlp", "pipeline": "logs"}
    2022-10-03T15:20:24.222-0500	info	pipelines/pipelines.go:106	Receiver started.	{"kind": "receiver", "name": "otlp", "pipeline": "logs"}
    2022-10-03T15:20:24.222-0500	info	service/service.go:129	Everything is ready. Begin running and processing data.
    2022-10-03T15:20:24.222-0500	info	zapgrpc/zapgrpc.go:174	[core] [Server #1 ListenSocket #2] ListenSocket created	{"grpc_log": true}
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
    curl -vi http://localhost:4318/v1/logs -X POST -H "Content-Type: application/json" -d @samplelog.json
    ```
    ```shell
    HTTP/1.1 200 OK
    Content-Type: application/json
    Date: Fri, 07 Oct 2022 13:55:38 GMT
    Content-Length: 2
    ```

3. Now take a look at the output of the OTEL Collector.  There should be a new entry that looks similar to this:

    ```shell
    Resource SchemaURL:
    ScopeLogs #0
    ScopeLogs SchemaURL:
    InstrumentationScope
    LogRecord #0
    ObservedTimestamp: 2022-10-03 19:49:39.558653 +0000 UTC
    Timestamp: 1970-01-01 00:00:00 +0000 UTC
    SeverityText:
    SeverityNumber: SEVERITY_NUMBER_UNSPECIFIED(0)
    Body: This is the sample log message.
    Attributes:
         -> log.file.name: STRING(access_log)
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
