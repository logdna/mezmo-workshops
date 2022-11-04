---
title: Alerting
weight: 7
description: >
    No need to stare at live-tail logs all day, use Mezmo's Alerting capabilities to watch for conditions and notify you when something needs your attention.
---

The PetClinic app has a unique menu named **ERROR** that, when clicked, will throw a Java Exception that shows up in the log output with `level=ERROR`.  Because this log level is not `DEBUG`, it will flow through the entire output and end up in Log Analysis.  From there, we can add an Alert that will watch for these log entries, and handle appropriately.  In this example, we'll send an email message to a recipient.

1. To get started, click the `ERROR` menu item in the **PetClinic** app to generate an error message:

    {{< figure src="../../images/petclinic-error.png" alt="PetClinic - Error" width="350">}}

    In the console of the Java app, you should see the error message it generates:

    {{< highlight text >}}
Nov 04 10:14:02 [http-nio-8080-exec-3] ERROR o.a.c.c.C.[.[.[.[dispatcherServlet]: Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is java.lang.RuntimeException: Expected: controller used to showcase what happens when an exception is thrown] with root cause
java.lang.RuntimeException: Expected: controller used to showcase what happens when an exception is thrown
at org.springframework.samples.petclinic.system.CrashController.triggerException(CrashController.java:33)
at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
at java.base/java.lang.reflect.Method.invoke(Method.java:564)
at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:205)
at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:150)
...
    {{< /highlight >}}

    And the corresponding entry in Mezmo appears as: 

   {{< figure src="../../images/mezmo-error-line.png" alt="PetClinic - Error">}}

2. Sign into Mezmo at https://app.mezmo.com.
3. We are interested in any log that comes from the **PetClinic** app and has a `level=ERROR`.  As such, search for these log entries using this search string in the search field:

    ```bash
    _meta.service_name:PetClinic _meta.level:ERROR
    ```

   {{< figure src="../../images/petclinic-error-search.png" alt="PetClinic Error Search">}}

4. Let's save this search as a new **View**.  Click on the **Unsaved View** drop down and select **Save as new view**.
5. Set the name to **PetClinic Errors** and select the **PETCLINIC** **Category** we created in the **Log Analysis** section.

   {{< figure src="../../images/createnewview-petclinicerrors.png" alt="Create new view - PetClinic Errors" width="350">}}

    Click **Save View**.

6. With the new **View** created, we can attach an alert to it by clicking the **PetClinic Errors** dropdown and selecting **Attach an alert**:

   {{< figure src="../../images/attach-alert.png" alt="Create new view - PetClinic Errors" width="350">}}

7. In the **Alert** dialog, select **View-specific alert** then select **Email**.
8. In the expanded **Alert** dialog:
    * Select a Recipient for the email
    * Uncheck the **At the end of 30 seconds** option
    * Check the **Immediately after 1 Line** option
    * Leave the rest of the defaults as-is
   
    Click **Save Alert**.

9. Head back over to the **PetClinic** app and click the **ERROR** menu item again.  A new error message will output a new ERROR entry which will trigger the email alert.  Check your inbox for the email notification:

   {{< figure src="../../images/alert-email.png" alt="Alert Email" width="550">}}

    You'll notice you have the option mute this notification for various durations directly from the email.

    There are many additional notification types that can be attached to this alert including Slack, PagerDuty as well as invoking a Webhook to take a corrective action if desired.