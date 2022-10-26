---
title: Troubleshooting
weight: 6
---

{{< alert title="NOTE" color="warning" >}}Additional troubleshooting and diagnostic tools are in **development** that will be available from within the platform itself.  Stay tuned for future releases when these become available.{{< /alert >}}

This section will highlight some techniques for troubleshooting and collecting insight at various steps of the pipeline as data flows through the system.

## Webhook.site

Webhook.site is a free website that can be used to make HTTP requests to and see the full HTTP payload to verify it's contents.  By creating a **Destination** in pipeline and connecting the output of a **Processor** to it, you can view the data on **Webhook.site** as it appeared at that stage of the pipeline.

1.  Visit [https://webhook.site/]() in a browser.

2.  Copy your unique URL to the clipboard:

    {{< figure src="../../images/webhook.site-url.png" alt="Webhook.site URL" width="800">}}

3. Open the **Petclinic Preprocess** pipeline and click **Destinations** &arr; **Add**.

4. Select the **HTTP** destination.

5. Configure the following:

    * **Title** as `To Webhook.site`,
    * add a meaningful **Description** such as `Send output to Webhook.site`,
    * leave **End-to-end Acknowledgement** as **checked**,
    * set the **URI** to the value you copied from the **Webhook.site** page,
    * set the **Encoding** to `json`, and
    * leave **Compression** and **Authentication &rarr; Strategy** as `none`.

   The configured destination should look like this:

   {{< figure src="../../images/configured-webhook.site.png" alt="Configured - Webhook.site" width="450">}}

   Click **Save**.

6. With the **Webhook.site** destination configured, we can select the output from any **Source** or **Processor** and send it to the destination.  For this example, drag a connection from the output of the **Discard DEBUG Msgs** filter to the **To Webhook.site** destination.  The pipeline will now look like this:

   {{< figure src="../../images/pipeline-with-webhook.site.png" alt="Pipeline with Webhook.site" width="1200">}}

