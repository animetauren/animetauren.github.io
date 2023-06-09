---
title:  "Kinesis Firehose Re-Ingestion Pipeline for Splunk"
date:   2023-05-10 17:00:00
tags: [aws,splunk]
---

As of writing there are two methods to ingest data from AWS into Splunk, one is the [push method][sqs-s3] which is based on SQS-S3, and the [pull method][firehose] which is based on Amazon Kinesis Data Firehose (KDF). In this guide, we will assume the pull method.

Before we begin it is helpful to have some AWS experience, to be specific the following services:
- Amazon Simple Storage Service (S3)
- Amazon Kinesis Data Firehose (KDF)
- AWS Lambda
- AWS Identity and Access Management (IAM)

Situation:

Delivery of AWS logs to Splunk [HEC][hec] via KDF has failed, failed delivery logs are being stored in an S3 Dead Letter Queue (DLQ)  bucket.   

Task:

Re-Ingest failed delivery logs into Splunk, confirming that logs are delivered in the correct format, and can be queried in Splunk.

Action:

Deploy the [Re-Ingest Pipeline][firehose-reingest], this will create a KDF pipeline, along with two Lambda functions that will ingest the logs from the S3 DLQ bucket, push to a KDF Data Stream, and sent to Splunk HEC to be ingested by Splunk.

Result:
Logs are confirmed to be in Splunk in the correct format, and that there is no break in logs. 


[sqs-s3]: https://docs.splunk.com/Documentation/AddOns/released/AWS/SQS-basedS3
[firehose]: https://docs.splunk.com/Documentation/AddOns/released/AWS/ConfigureFirehoseOverview
[firehose-reingest]: https://github.com/animetauren/aws-splunk-firehose-error-reingest/tree/main/firehose-reingest
[hec]: https://docs.splunk.com/Documentation/Splunk/9.0.5/Data/UsetheHTTPEventCollector