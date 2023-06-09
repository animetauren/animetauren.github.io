---
title:  "Kinesis Firehose Re-Ingestion Pipeline for Splunk"
date:   2023-05-10 17:00:00
tags: [aws,splunk]
---

As of writing there are two methods to ingest data from AWS into Splunk, one is the [push method][sqs-s3] which is based on SQS-S3, and the [pull method][firehose] which is based on Amazon Kinesis Data Firehose (KDF). In this guide, we will assume the pull method.

Before we begin it is helpful to have some AWS experience, to be specific with the following services:
- Amazon Simple Storage Service (S3)
- Amazon Kinesis Data Firehose (KDF)
- AWS Lambda
- AWS Identity and Access Management (IAM)



[sqs-s3]: https://docs.splunk.com/Documentation/AddOns/released/AWS/SQS-basedS3
[firehose]: https://docs.splunk.com/Documentation/AddOns/released/AWS/ConfigureFirehoseOverview
[firehose-reingest]: https://github.com/animetauren/aws-splunk-firehose-error-reingest/tree/main/firehose-reingest