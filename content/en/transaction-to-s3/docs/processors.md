---
title: Filter and encrypt
weight: 4
---

## Overview

There is a lot we want to do with the data.  You can see the general structure from the terminal output if you ran the Docker in the [first part](/transaction-to-s3/docs/create-pipeline).  At high level, we are going to encrypt PII of all data coming in, drop useless data on failed transactions and then route transaction data to S3 while leaving the rest for something else (here we used the Mezmo Log Analysis but we won't get into that here (to learn more, check out our [Pet Clinic](/pet-clinic/) workshop).


