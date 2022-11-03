---
title: Encrypt and Filter
weight: 4
---

## Overview

There is a lot we want to do with the data.  You can see the general structure from the terminal output if you ran the Docker in the [first part](/transaction-to-s3/docs/create-pipeline).  At high level, we are going to encrypt PII of all data coming in, drop useless data on failed transactions and then route transaction data to S3 while leaving the rest for something else (here we used the Mezmo Log Analysis but we won't get into that here (to learn more, check out our [Pet Clinic](/pet-clinic/) workshop).

But, let's take this one step at a time.

## Step 0: Understand your data

We have a couple types of logs flowing through via the devices, the ones we care about contain transaction information and are of the form

```json
{
   "datetime": "2022-11-03 23:15:03",
   "device": {
      "id": "57edad55-83d5-4c03-9108-55ff33034a8d",
      "name": "/dev/sdi",
      "vrs": "1.3.8",
      "location": [
         "41.72059",
         "-87.70172",
         "Evergreen Park",
         "US",
         "America/Chicago"
      ],
      "status": "active"
   },
   "transaction": {
      "product_id": "375e7bbc-f01c-4e5a-b624-46d134009d73",
      "customer_id": "78dc11b5-c5a5-4f4e-a55b-9acd3fbee9a3",
      "quantity": 18,
      "unit_price": 226.67,
      "net_price": 4080.06,
      "tax": 0.0,
      "total_price": 4080.06,
      "cc": {
         "cc_number": "4718016953220523",
         "cc_exp": "04/26",
         "cc_cvv": "319",
         "cc_name": "Stephanie Barron",
         "cc_zip": "39065"
      },
      "transaction_status": "fail"
   }
}
```

## Step 1: Encrypt the Device Location

## Step 2: Encrypt the Credit Card Information

## Step 3: Add a Router to filter transaction data

## Step 4: Drop CC info on failed transactions


