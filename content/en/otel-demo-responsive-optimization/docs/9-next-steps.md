---
title: Next Steps
weight: 9
---

## Recap

We have succesfully integrated OpenTelemetry Telemetry data (logs, metrics and traces) and optimized them for cost and MTTR using Responsive Techniques.  By now, you should have four pipelines that look like

connected a fleet of simulated devices to a Mezmo Pipeline to clean, encrypt and route an important subset to S3 for later analysis.  You should have Pipelines that looks like

* Log Profiling Pipeline
![Final Pipeline: Log Exploration](../../images/pipelines_final_log_exploration.png)
* Log Handler Pipeline
![Final Pipeline: Log Handler](../../images/pipelines_final_log_handler.png)
* Metric Handler Pipeline
![Final Pipeline: Metric Handler](../../images/pipelines_final_metric_handler.png)
* Trace Handler Pipeline
![Final Pipeline: Trace Handler](../../images/pipelines_final_trace_handler.png)

## Learn More

So we have OpenTelemetry logs flowing into Mezmo and understand a few of the base concepts.  But now what?

Always recommend peaking at [the docs](https://docs.mezmo.com/telemetry-pipelines) but if you feel like exploring more or just want to nerd out on telemetry data, would love to chat!  Reach out to me directly at [braxton.johnston@mezmo.com](mailto:braxton.johnston@mezmo.com).
