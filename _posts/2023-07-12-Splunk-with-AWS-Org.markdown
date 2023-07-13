---
title:  "Splunk with AWS Orgs"
date:   2023-07-12 17:00:00
tags: [aws,splunk]
---

This blog post will go over how to setup Splunk integration with AWS Orgs managed AWS environments.

This makes the assumption that you leverage AWS Orgs, and follow the centralized account principle (e.g. [AWS Centralized Logging Account][centr-log-acct])

Before we being we must first cover how to Get Data Into (GDI) Splunk from AWS:

The first approach is to use Splunk Data Manager [(DM)][dm] to configure what AWS sources we want to ingest, ingest mechanism to use, and what AWS Account to pull data from. This does require some comfort with AWS CloudFormation (Cfn). Splunk Data Manager is the future for GDI from AWS, so if you can leverage DM for your use case, you use it. At the time of writing it currently does not support all sources, nor all use cases, in this case [AWS Orgs][onboard-dm] support.

The second approach is to use either the SNS/SQS [Pull Mechanism][pull-based] or the Kinesis Firehose/CloudWatch [Push Mechanism][push-based] to ingest AWS data into Splunk. This approach requires comfortability with AWS CloudFormation Templates and IaC concepts. This is the underlying ingestion mechanisms that Splunk DM uses, the difference is that here we must create the cfn template, and configure the infrastructure to send logs into Splunk.

Let's define the use case, and what our requirements are: 
- All logs will be stored in a centralized logging account (CLA) in S3 or CloudWatch.
- Solution needs to support ingesting logs from either S3 or CloudWatch into Splunk.
- Solution needs to support fine control of which logs are sent to Splunk.
- Solution needs to be able to scale up, and down as new logs sources are added or removed.
- Solution needs to be decoupled, and resilient to assure all logs are delivered to Splunk.
- Solution needs to be secure, and support encryption-at-rest (EAR), encryption-in-transit (EIT). 


FAQ:  

Who is this for?

This is for customers who are using Splunk, want to ingest logs from AWS, and __AWS Orgs__.

Who is this NOT for?

This is __NOT__ for customers who can use Splunk Data Manager today to ingest logs from AWS into Splunk. 
> [AWS Onboarding from a Single Account][onboard-single-dm]
> [AWS Onboarding from Multiple Accounts (No AWS Orgs)][onboard-multi-dm]

What should I consider?
This solution is a general solution, this means that it aims to solve the problem for most, and may break for few.

Why should do this?
You need a solution, that is centralized, will scale, and will leverage the investments your team has made in terms of money, knowledge, and time.

Why should I not do this?
Your solution is working, there is no rush, and you can wait for Splunk Data Manager to get AWS Orgs support. Wait for that.

When should I do this?
That will depend on the priority, and urgency of your team. In short, the sooner you get this configured, the less technical debt you will have later on as your onboard more AWS workloads.

#### Disclaimer

The information shared is for general informational purposes only. I do not provide any warranty and recommend readers to test the content thoroughly before implementing it. Use the information at your own discretion and risk. 

[dm]: https://www.splunk.com/en_us/blog/platform/meet-the-data-manager-for-splunk-cloud.html
[onboard-dm]: https://docs.splunk.com/Documentation/DM/1.8.1/User/AWSAbout#Onboard_AWS_in_Data_Manager
[centr-log-acct]: https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/centralized-logging-and-multiple-account-security-guardrails.html
[aws-addon]:https://splunkbase.splunk.com/app/1876
[pull-based]:https://docs.splunk.com/Documentation/AddOns/released/AWS/DataTypes#Pull-based_API_data_collection_sourcetypes
[push-based]:https://docs.splunk.com/Documentation/AddOns/released/AWS/DataTypes#Push-based_Amazon_Kinesis_Firehose_data_collection_sourcetypes
[onboard-single-dm]: https://docs.splunk.com/Documentation/DM/1.8.1/User/AWSSingleAccount
[onboard-multi-dm]: https://docs.splunk.com/Documentation/DM/1.8.1/User/AWSMultipleAccount