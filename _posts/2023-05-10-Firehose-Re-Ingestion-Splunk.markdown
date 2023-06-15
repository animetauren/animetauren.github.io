---
title:  "Kinesis Firehose Re-Ingestion Pipeline for Splunk"
date:   2023-05-10 17:00:00
tags: [aws,splunk]
---

As of writing there are two methods to ingest data from AWS into Splunk, one is the [push method][sqs-s3] which is based on SQS-S3, and the [pull method][firehose] which is based on Amazon Kinesis Data Firehose (KDF). In this guide, we will assume the you are using the pull method.

#### Prerequisities:
Before we begin it is helpful to have some AWS experience, to be specific the following services:
- Amazon Simple Storage Service (S3)
- Amazon Kinesis Data Firehose (KDF)
- AWS Lambda
- AWS Identity and Access Management (IAM)

#### Splunk Ingestion Failure Context: 
When Kinesis Firehose fails to write to Splunk via HEC (due to connection timeout, HEC token issues or other), it will write its logs into an S3 DLQ bucket. However, the contents of the logs in the bucket is not easily re-ingested into Splunk, as it is log contents is wrapped in additional information about the failure, and the original message base64 encoded. So for example, if using the AWS Splunk Add-On, it is not possible to decode the contents of the message.

To re-ingest the failed logs we will need to do the following:
<br />1/ Deploy Re-Ingestion Pipeline
<br />2/ Prepare the data to be Re-Ingested
<br />3/ Re-Ingest Data into Splunk
<br />4/ Confirm data is in Splunk

#### Deploy Re-Ingestion Pipeline
The Re-Ingestion Pipeline architecture diagram:

Arch Image Here 

You can deploy this pipeline via [cfn template][firehose-cfn], this will deploy two Lambda Functions, an S3 Bucket, and a KDF Stream. The S3 Bucket will server to temporarily store the failed logs to be re-ingested. The first Lambda Function (S3 Re-ingest) will be triggered by the S3 Bucket, will grab the log file, will decompress, decode, and remove extra meta-data, finally sending the logs to the newly created KDF Stream. Once the log file is placed on KDF Stream, the Lambda Function (Kinesis Transformation) will be triggered that will massage the log data in the format that Splunk is expecting the logs. Once that function is complete, KDF Stream will push the data to Splunk HEC. 

#### Prepare the data to be Re-Ingested
To enable this solution, we will need to first prepare the data to be re-ingested. The way this happens is via copying the logs we want to re-ingest from the S3 DQL bucket to our S3 tmp bucket. This is a manual step, the reason for this is we want to make sure that the re-ingestion pipeline is only triggered once the Splunk Ingestion issue is properly fixed, and data can be properly ingested back into Splunk.

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