---
title:  "Kinesis Firehose Re-Ingestion Pipeline for Splunk"
date:   2023-05-10 17:00:00
tags: [aws,splunk]
---

At present, there are two methods available for ingesting data from AWS into Splunk. The first method is the ['push method'][sqs-s3] based on SQS-S3, and the second method is the ['pull method'][firehose] based on Amazon Kinesis Data Firehose (KDF). For the purpose of this guide, we will assume that you are using the pull method.

#### Prerequisities:
Before we begin it is helpful to have some AWS experience, to be specific the following services:
- Amazon Simple Storage Service (S3)
- Amazon Kinesis Data Firehose (KDF)
- AWS Lambda
- AWS Identity and Access Management (IAM)

#### Splunk Ingestion Failure Context: 
When Kinesis Firehose encounters a failure while writing to Splunk via HEC (e.g., Connection timeout,  wrong HEC token, etc) it automatically writes its logs into an S3 DLQ bucket. However, re-ingesting the log contents from the DLQ bucket into Splunk becomes challenging because the log contents are wrapped in additional information about the failure and the original message is base64 encoded. 

To re-ingest the failed logs we will need to do the following:
<br />1/ Deploy Re-Ingestion Pipeline
<br />2/ Prepare the data to be Re-Ingested
<br />3/ Re-Ingest Data into Splunk
<br />4/ Confirm data is in Splunk

#### Deploy Re-Ingestion Pipeline
The Re-Ingestion Pipeline architecture diagram:

Arch Image Here 

To deploy this pipeline, you can use the [cfn template][firehose-cfn]. The deployment process includes setting up two Lambda Functions, an S3 Temp Bucket, and a KDF Stream. The S3 Temp Bucket serves the purpose of temporarily storing the failed logs for re-ingestion. The first Lambda Function, known as 'S3 Re-ingest,' gets triggered by the S3 Temp Bucket. It retrieves the log files, decompresses, decodes, removes extra meta-data, and then sends the logs to the newly created KDF Stream. Once the log files are placed in the KDF Stream, it triggers the second Lambda Function, called 'Kinesis Transformation.' This function processes the log data to match Splunk's expected log format. Once the transformation is complete, the KDF Stream pushes the data to your Splunk HEC.

#### Prepare the data to be Re-Ingested
To enable this solution, we need to perform the initial data preparation for re-ingestion. The process involves copying the logs that require re-ingestion from the S3 DQL bucket to our S3 tmp bucket. This step is manual to ensure that the re-ingestion pipeline is triggered only after resolving the Splunk Ingestion issue and ensuring proper data ingestion into Splunk.

#### Re-Ingest Data into Splunk

To kick off the re-ingest pipeline we need to manually trigger copying log files as stated above. A sample, and example command is below:

Commands: ``aws s3 sync <source> <dest> --include "*splunk-failed/YYYY/MM/DD/HH/*"``

Example: ``aws s3 sync s3://example-splashback s3://example-reingest --include "*splunk-failed/2022/01/01/12/*"``

#### Confirm data is in Splunk

Log into Splunk, and query for recent data, and confirm data is ingested.

#### tl;dr:
<br />
Situation:
<br />Delivery of AWS logs to Splunk [HEC][hec] via KDF has failed, failed delivery logs are being stored in an S3 Dead Letter Queue (DLQ)  bucket.   

Task:
<br />Re-Ingest failed delivery logs into Splunk, confirming that logs are delivered in the correct format, and can be queried in Splunk.

Action:
<br />Deploy the [Re-Ingest Pipeline][firehose-reingest], this will create a KDF pipeline, along with two Lambda functions that will ingest the logs from the S3 DLQ bucket, push to a KDF Data Stream, and sent to Splunk HEC to be ingested by Splunk.

Result:
<br />Logs are confirmed to be in Splunk in the correct format, and that there is no break in logs. 

#### Disclaimer

The information shared is for general informational purposes only. I do not provide any warranty and recommend readers to test the content thoroughly before implementing it. Use the information at your own discretion and risk. 

[sqs-s3]: https://docs.splunk.com/Documentation/AddOns/released/AWS/SQS-basedS3
[firehose]: https://docs.splunk.com/Documentation/AddOns/released/AWS/ConfigureFirehoseOverview
[firehose-reingest]: https://github.com/animetauren/aws-splunk-firehose-error-reingest/tree/main/firehose-reingest
[firehose-cfn]: https://github.com/animetauren/aws-splunk-firehose-error-reingest/blob/main/firehose-reingest/firehose_reingest.yaml
[hec]: https://docs.splunk.com/Documentation/Splunk/9.0.5/Data/UsetheHTTPEventCollector