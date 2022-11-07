---
title: Log Analysis
weight: 4
---

## Connect OpenTelemetry to the PetClinic logs

In this section, we will update the OpenTelemetry Collector's configuration to read the logs output by the PetClinic app and forward them on to Mezmo.

To get started, we'll need to modify the `receivers` section of the OpenTelemetry configuration to add a **filelog** receiver.  This receiver will be responsible for reading the `/tmp/petclinic.json` file into the running collector.

1. Edit `config.yaml` and add this configuration under the `receivers` section:

    {{< tabpane >}}
    {{< tab header="$HOME/otelcol/config.yaml" lang="yaml" >}}
receivers:
  filelog:
    include: [ /tmp/petclinic.json ]
    include_file_name: false
    start_at: end
    operators:
    - type: json_parser
      timestamp:
        parse_from: attributes.timestamp
        layout_type: gotime
        layout: "2006-01-02 15:04:05"
      severity:
        parse_from: attributes.level
    {{< /tab >}}
    {{< /tabpane >}}

2. With the new receiver defined, we need to add it to the `logs` pipeline.  Under the `service` &rarr; `pipelines` &rarr; `logs` section, add `filelog` to the list of `receivers`:

    {{< tabpane >}}                                               
    {{< tab header="$HOME/otelcol/config.yaml" lang="yaml" >}}
service:
   pipelines:
       logs:
           receivers: [ otlp, filelog ]
           exporters: [ logging, mezmo ]
    {{< /tab >}}     
    {{< /tabpane >}}

3. Save the configuration.
4. Restart the collector:

    ```shell
    ./otelcol-contrib --config config.yaml
    ```

## Browse PetClinic logs in **Mezmo Observability Cloud**

1. Restart the PetClinic app:

    ```bash
    java -jar target/spring-petclinic-*.jar --spring.profiles.active=mysql
    ```
   
    the console logs will appear locally as:

    ```
    ...
    Oct 24 15:06:35 [main] DEBUG _.s.w.s.H.Mappings:
               o.s.b.a.w.s.e.BasicErrorController:
               { [/error]}: error(HttpServletRequest)
               { [/error], produces [text/html]}: errorHtml(HttpServletRequest,HttpServletResponse)
    Oct 24 15:06:35 [main] DEBUG _.s.w.s.H.Mappings: 'beanNameHandlerMapping' {}
    Oct 24 15:06:35 [main] DEBUG _.s.w.s.H.Mappings: 'resourceHandlerMapping' {/webjars/**=ResourceHttpRequestHandler [classpath [META-INF/resources/webjars/]], /**=ResourceHttpRequestHandler [classpath [META-INF/resources/], classpath [resources/], classpath [static/], classpath [public/], ServletContext [/]]}
    Oct 24 15:06:36 [main] INFO  o.s.b.a.e.w.EndpointLinksResolver: Exposing 13 endpoint(s) beneath base path '/actuator'
    Oct 24 15:06:36 [main] INFO  o.a.c.h.Http11NioProtocol: Starting ProtocolHandler ["http-nio-8080"]
    Oct 24 15:06:36 [main] INFO  o.s.b.w.e.t.TomcatWebServer: Tomcat started on port(s): 8080 (http) with context path ''
    Oct 24 15:06:36 [main] INFO  o.s.s.p.PetClinicApplication: Started PetClinicApplication in 15.448 seconds (JVM running for 17.292)
    Oct 24 15:06:59 [HikariPool-1 housekeeper] DEBUG c.z.h.p.HikariPool: HikariPool-1 - Pool stats (total=10, active=0, idle=10, waiting=0)
    Oct 24 15:06:59 [HikariPool-1 housekeeper] DEBUG c.z.h.p.HikariPool: HikariPool-1 - Fill pool skipped, pool is at sufficient level.
    ```

2. Navigate to the **Mezmo Observability Cloud** at https://app.mezmo.com.  In the dashboard, click the <img src="../../images/views.png" width="40px"/> **Views** button &rarr; <img src="../../images/everything.png" width="175px"/> **Everything**.  You should see the same log entries appear at the bottom of the dashboard:

    ![PetClinic Logs](../../images/petclinic-logs.png)

3. Each log entry can be expanded to reveal additional details and metadata about the log entry by clicking the disclosure icon <img src="../../images/disclosure-closed.png" width="15px"/>:
   
    ![PetClinic Logs](../../images/log-details-1.png)

    You'll notice that the additional metadata information being output by the PetClinic app like `context`, `level`, `logger`, and `thread` appears in the `_meta` portion of the log entry. We can query on this information by entring it as a search string field.  For example, if we wanted to find all entries where the `logger` is `HikariPool`, we could enter this search string:

    ```
    _meta.logger:com.zaxxer.hikari.pool.HikariPool
    ```

    which would give us a filtered view that only includes the entries we wish to see:

   ![PetClinic Logs](../../images/hikaripool-searchresults.png)

4. Having a filtered view that isolates only the logs coming from our PetClinic app is very convenient.  Let's save this View so it can easily be recalled in the future.  At the top of the log view, click the **Unsaved View** drop down and select **Save as new view**:

   {{< figure src="../../images/save-as-new-view.png" alt="Save As New View" width="250">}}

5. Name the new view **PetClinic App**, leave the **Category** and **Alert** fields blank for now.

   {{< figure src="../../images/create-new-view.png" alt="Create New View" width="350">}}

   Click **Save View**.

6. The new is saved under the **Uncategorized** group of views on the list:

   {{< figure src="../../images/petclinic-app-view.png" alt="PetClinic App View" width="200">}}

    Now, anytime we want to view only the logs from the PetClinic app, we can click on this view to see the filtered set of log entries.
