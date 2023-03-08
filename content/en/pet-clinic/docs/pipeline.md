---
title: Pipelining
weight: 5
description: >
    Introduce the concept of an Observability Pipeline and how it can be used to free developer resources and allow SRE teams to filter log information coming from PetClinic to make their ability to manage and operate the application more efficient.
tags:
- PetClinic
- Pipeline
- HTTP Source
- Unroll Processor
- Stringify Processor
- Filter Processor
- Mezmo Log Analysis Destination
---

With the **PetClinic App** View created, one thing becomes immediately apparent- the PetClinic app is very chatty outputting DEBUG statements related to its pool stats every 30 seconds.  While it's simple enough to edit the logging configuration for the app itself, it would require changes to the config file, maybe there's additional **DEBUG** info that we _do_ want to see, just not that of the **HikariPool** output.

For the purposes of this exercise, we will assume the PetClinic app is a 3rd party app that we don't have control over but we still want to remove the DEBUG output that is cluttering up our Log Analysis.  For that, we can utilize a **Pipeline** that will look for those entries and drop them before passing the non-DEBUG entries on to Log Analysis.

## Build a Pipeline

1. To get started, click the **Pipeline** icon in the top-left corner of the dashboard:

   {{< figure src="../../images/pipeline.png" alt="Pipeline" width="200">}}

   then click on **New Pipeline**:

   {{< figure src="../../images/new-pipeline.png" alt="New Pipeline" width="350">}}

2. Name the new pipeline **PetClinic preprocess**:

   {{< figure src="../../images/pipeline-name.png" alt="Pipeline Name" width="600">}}

   Click **Save**.

3. Next we'll add a **Source** that will receive log data from the **OpenTelemetry Collector** we configured earlier.  Click the **Sources - Add** button.
  
   {{< alert title="NOTE" color="warning">}}The **Mezmo Exporter** in the **OpenTelemetry Collector** is responsible for sending this data.  We haven't yet configured it to send the data, but we will in a few short steps. {{< /alert >}}

4. We will receive the **OpenTelemetry** logs via HTTP.  In the list of available sources, type `http` in the filter which should highlight the **HTTP Source**

   {{< figure src="../../images/sources-http.png" alt="Sources - HTTP" width="450">}}

   Click on the **HTTP** source.

5. Configure the following:

   * **Title** as `OTEL Ingest`
   * add a meaningful **Description** such as `Receive logs from OTEL`
   * leave the **Decoding Method** as `json`
   
   <br/>
   {{< figure src="../../images/configured-http.png" alt="Configured - HTTP" width="450">}}

   Click **Save**.

6. With the `OTEL Ingest` endpoint created, we need to edit its configuration to create a new **Access Key**.  Click on `OTEL Ingest` endpoint and then click the `Create new key` button.

   {{< figure src="../../images/http-createnewkey.png" alt="HTTP - Create new key" width="450">}}

7. For the **Title**, enter `Ingest Key` and click **Create**.

8. Be sure to copy and save the value of the new key as well as the URL to this specific endpoint 
    as we will need to use them in a later step:

   {{< figure src="../../images/http-configured-key.png" alt="HTTP - Configured key" width="450">}}

    In this example:

    * the value of **Ingest Key** is `+19opdnwjWmDUD302J2jsT9xCF87Ibu0rk2t95jC/ps=` and 
    * the URL is `https://pipeline.mezmo.com/v1/b745ce28-546e-11ed-a64b-d233826e7531`.

    Click **Update**.

9.
   Our pipeline is starting to take shape as:

   {{< figure src="../../images/petclinic-ingest-pipeline1.png" alt="PetClinic Ingest Pipeline - Step 1" width="600">}}

10. Next, the log data received from OTEL follows the format specified in the [**Send Log Lines** API](https://docs.mezmo.com/log-analysis-api/ref#ingest).  An example of a JSON payload with two log entries is sent as:

   ```json
   {
     "lines": [
       {
         "timestamp": 1666275427712,
         "line": "\u003c135\u003eOct 20 09:17:02 mbp1 HikariPool-1 - Pool stats (total=10, active=0, idle=10, waiting=0)",
         "app": "",
         "level": "info",
         "meta": {}
       },
       {
         "timestamp": 1666275427712,
         "line": "\u003c135\u003eOct 20 09:17:02 mbp1 HikariPool-1 - Fill pool skipped, pool is at sufficient level.",
         "app": "",
         "level": "info",
         "meta": {}
       }
     ]
   }
   ```

   We want to process each log entry individually, so we will use the **Unroll** processor to do this.  This processor will convert a JSON array into individual JSON objects that will appear at the output of the processor.

   Click **Processors** &rarr; **Add**.  In the **Processor** list, filter on `unroll` and select the **Unroll Processor**:

   {{< figure src="../../images/add-unroll.png" alt="Add Unroll Processor" width="600">}}

   Click on the **Unroll** processor.

11. Configure the following:

    * **Title** as `Unroll Logs`
    * add a meaningful **Description** such as `Convert logs array to individual logs for processing`
    * set the **Field** value to `.lines`

    <br/>
    {{< figure src="../../images/configured-unroll.png" alt="Configured - Unroll" width="450">}}
   
    Click **Save**.
   
    Our pipeline now appears as:

    {{< figure src="../../images/unconnected-http-unroll.png" alt="Unconnected HTTP & Unroll" width="600">}}

   {{< alert title="NOTE" color="info">}}You'll notice there is nothing connecting the output of the **HTTP** source and the **Unroll** processor.  Let's correct that in the next step.{{< /alert >}}

12. Hover your mouse over the right edge of the **HTTP** source we configured (`OTEL Ingest`).  An attach anchor will appear:

   {{< figure src="../../images/connect1.png" width="200">}}

   Click and drag from the **HTTP** source to the **Unroll** processor.  The **Unroll** processor will be highlighted:

   {{< figure src="../../images/connect2.png" width="500">}}

   With the **Unroll** processor highlighted, release the mouse click and the **HTTP** source will now send its output to the **Unroll** processor.

   {{< alert title="NOTE" color="info">}}Additional connections from the same **Source** or **Processor** can be made as well.  The output from the one will be sent to **all** the connections there exists a connection for.{{< /alert >}}

   The pipeline should now appear as:

   {{< figure src="../../images/petclinic-ingest-pipeline2.png" alt="PetClinic Ingest Pipeline - Step 2" width="600">}}

13. With the logs converted to individual entries, we can accomplish what we set out to do- remove the `DEBUG` entries.  To accomplish this, we'll add a **Filter** processor.

   Click **Processors** &rarr; **Add**.  In the **Processor** list, filter on `filter` and select the **Filter Processor**:

   Click on the **Filter** processor.

14. Configure the following:

    * **Title** as `Discard DEBUG Msgs`
    * add a meaningful **Description** such as `Allow non-DEBUG messages to pass`
    * set the **Field** value to `.lines.level` 
    * set the **Operator** to `not_equal`
    * set the **Value** to `DEBUG` (the value **is case-sensitive**)

    <br/>
    {{< figure src="../../images/configured-filter.png" alt="Configured - Filter" width="450">}}

    Click **Save**.

15. Connect the output from the **Unroll** processor to the input of the **Filter** processor similar to Step 8.  Our pipeline now appears as:

    {{< figure src="../../images/petclinic-ingest-pipeline3.png" alt="PetClinic Ingest Pipeline - Step 3" width="750">}}

16. To prepare to send our log entries from our pipeline to Log Analysis, we must first convert the JSON format to a string format.  For this, we use the **Stringify** processor.  Click **Processors** &rarr; **Add**.  In the **Processor** list, filter on `stringify` and select the **Stringify Processor**:

    {{< figure src="../../images/add-stringify.png" alt="Add Stringify Processor" width="600">}}

    Click on the **Stringify** processor.

17. Configure the following:
     
    * **Title** as `Stringify`
    * add a meaningful **Description** such as `Convert JSON to text`

    <br/>
    {{< figure src="../../images/configured-stringify.png" alt="Configured - Stringify" width="450">}}

    Click **Save**.

18. Connect the output from the **Filter** processor to the input of the **Stringify** processor similar to Step 8.  Our pipeline now appears as:

    {{< figure src="../../images/petclinic-ingest-pipeline4.png" alt="PetClinic Ingest Pipeline - Step 4" width="750">}}

19. Finally, we are ready to take the output of the **Stringify** processor, and send it out of our pipeline and over to Log Analysis.  To do so, we'll add a **Destination**.  The destination will require an **ingest key** as part of its configuration.  To obtain your ingest key, click **Settings** (<img src="../../images/nav-settings.png" width="30"/>) &rarr; **Organization** &rarr; **API Keys**.

    We can use the existing Ingestion Key by simply clicking on the clipboard to copy it:

    {{< figure src="../../images/ingestion-keys.png" width="500">}}

    We will paste this value into the **Destination** that will be configured next.

20. Click **Destinations** &rarr; **Add**.  In the **Destinations** list, filter on `log analysis` and select the **Mezmo Log Analysis** destination:

    {{< figure src="../../images/add-log-analysis.png" alt="Add Log Analysis" width="500">}}

    Click on the **Mezmo Log Analysis** destination.

21. Configure the following:

    * **Title** as `Send to LA` 
    * add a meaningful **Description** such as `Send logs to Log Analysis` 
    * leave **End-to-end Acknowledgement** as **checked**
    * set the **Mezmo Host** value to `logs.mezmo.com`
    * set the **Hostname** to `petclinic-pipeline` 
    * paste the ingestion key in the **Ingestion Key** field that we looked up in the previous step

    <br/>
    {{< figure src="../../images/configured-loganalysis.png" alt="Configured - Log Analysis" width="450">}}

    Click **Save**.

22. Lastly, connect the output of the **Stringify** processor to the **Send to LA** destination.  The final pipeline should appear as:

    {{< figure src="../../images/completed-pipeline.png" alt="Completed Pipeline" width="950">}}

23. With the pipeline finalized, the last step is to deploy the pipeline so it is active.  Click the **Deploy pipeline** button:

    {{< figure src="../../images/deploy-pipeline.png" alt="Add Log Analysis" width="150">}}

    When the deployment completes, a blue checkmark will appear next to the pipeline name indicating the pipeline is now active:

    {{< figure src="../../images/deploy-complete.png" alt="Add Log Analysis" width="250">}}

## Reconfigure OTEL 

You may recall when we installed and configured the **OpenTelemetry Collector** that we set it up to send the logs to the Log Analysis endpoint.  We now want to reconfigure the collector to, instead, send logs to our pipeline endpoint.  This will start the flow of logs through the pipeline and the processors we've configured above.

1. Stop the OTEL Collector if it's running.
 
2. Edit the `$HOME/otelcol/config.yaml` file.

      * Change the value of `ingest_url` to the URL we saved from **Step 8** in the previous section.
      * Change the value of `ingest_key` to the **Ingest Key** value we saved from **Step 8** in the previous section.

    The `mezmo` section will look similar to this:

    ```yaml
    #######################################
    exporters:
      mezmo:
        ingest_url: "https://pipeline.mezmo.com/v1/b745ce28-546e-11ed-a64b-d233826e7531"
        ingest_key: "+19opdnwjWmDUD302J2jsT9xCF87Ibu0rk2t95jC/ps="
    ```
   
    Save the changes and exit.

4. Restart the collector with:

    ```shell
    ./otelcol-contrib --config config.yaml
    ```
   
## Testing

At this point, any new log messages output by the PetClinic app will get picked up by the collector and sent to the pipeline.  The pipeline should filter out any messages with `level=DEBUG` set and forward on the rest to Log Analysis.

1.  Head over to **PetClinic App** View we created earlier.  No **new** **DEBUG** messages should be showing up now.

    {{< alert title="NOTE" color="warning" >}}Be sure to check the timestamp on any *existing* messages in the View.  They should have a timestamp that is older than when we changed the flow of messages to the pipeline.
    {{< /alert >}}

2. One method for generating **INFO** messages in the PetClinic app is to restart the PetClinic app.  Both shutting down as well as starting up the app generate quite a bit of **INFO** and **DEBUG** entries.  Shut down the PetClinic app and you should see only the **INFO** logs show up in the View:

   {{< figure src="../../images/petclinic-shutdown-logs.png" alt="PetClinic Shutdown Logs" width="1200">}}

    likewise, starting the PetClinic app produces similar **INFO**-only logs:

    ```bash
    java -jar target/spring-petclinic-*.jar --spring.profiles.active=mysql
    ```

   {{< figure src="../../images/petclinic-startup-logs.png" alt="PetClinic Startup Logs" width="1200">}}
