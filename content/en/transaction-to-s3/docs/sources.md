---
title: Add a Source
weight: 3
---

## Step 1: Add the Source

This parts easy.  Go to the pipeline you created [previously](/transaction-to-s3/docs/create-pipeline) and click *Add Source*

**ADD SOURCE IMAGE**

From there, just select `HTTP`, give it a *Title* like `Edge Devices`, set *Decoding Method* to `JSON` and click *Save.

**IMAGE OF HTTP FILLED OUT**

You now have an endpoint defined that can recieve any data.

## Step 2: Configure the Simulation

Now let's point the simulation at the Pipeline.  To do this, we just need the proper `API Key`.  You can get this by opening the Source we just created via the epsilon in the top right of the node and copying the `API Key`.  Click Cancel or Update to move on.

**IMAGE OF API KEY**
