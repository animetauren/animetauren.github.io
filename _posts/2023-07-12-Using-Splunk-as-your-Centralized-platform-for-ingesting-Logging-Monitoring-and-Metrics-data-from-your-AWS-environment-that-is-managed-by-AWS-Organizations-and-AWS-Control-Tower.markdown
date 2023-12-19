---
title:  "Using Splunk as your Centralized platform for ingesting Logging, Monitoring, and Metrics data from your AWS environment that is managed by AWS Organizations w(o) AWS Control Tower"
date:   2023-07-12 17:00:00
tags: [aws,splunk]
---

This post will go over the considerations, archicture designs, and things to be aware of as you centralize Splunk as your platform for your AWS Environments, with the focus on AWS accounts managed by (AWS Organizations)[what-is-aws-org] and deployed by AWS Control Tower.

####Considerations: 

Let's start by first defining the problem statement: Inefficient and scattered logging and monitoring practices across multiple AWS environments results in limited visibility, increased complexity, and higher operational risks. Lack of centralized control and analytics hampers troubleshooting, compliance management, and overall operational efficiency. This can lead to wasteful cycles, and decrease in getting value of the platform. Companies are seeing the value of leveraging a centralized platform for Security, Obseravility, and Resiliency such as Splunk. 

####Use Case:
Centralized logging and monitoring for all AWS environments using Splunk as the centralized platform for data stored in S3 (Logging, Metrics, and Monitoring). 

In our case we will leverage Splunk as our centralized platform for. 

Pre-Requisites/Audience:

This makes the assumption that you leverage AWS Orgs, and follow the centralized account principle (e.g. [AWS Centralized Logging Account][centr-log-acct])

Solution Requirements:
- All logs will be stored in a centralized logging account (CLA) in S3 or CloudWatch.
- Solution needs to support ingesting logs from either S3 or CloudWatch into Splunk.
- Solution needs to support fine control of which logs are sent to Splunk.
- Solution needs to be able to scale up, and down as new logs sources are added or removed.
- Solution needs to be decoupled, and resilient to assure all logs are delivered to Splunk.
- Solution needs to be secure, and support encryption-at-rest (EAR), encryption-in-transit (EIT). 

####Architecture

Before we begin we must first cover how are Getting Data Into (GDI) Splunk from our AWS sources:

As of today there are two main approaches to ingesting data into Splunk. The first approach leverages Splunk Data Manager [(DM)][dm]. The second approach is the [AWS Get Data In][aws-gdi].

The first approach is to use Splunk Data Manager [(DM)][dm] to configure what AWS sources we want to ingest, ingest mechanism to use, and what AWS Account to pull data from. This does require some comfort with AWS CloudFormation (Cfn). Splunk Data Manager is the future for GDI from AWS, so if you can leverage DM for your use case, you use it. At the time of writing it currently does not support all sources, nor all use cases. Currently Splunk DM does not support [AWS Orgs][onboard-dm].

The second approach is AWS GDI leveraging either the SNS/SQS [Pull Mechanism][pull-based] or the Kinesis Firehose/CloudWatch [Push Mechanism][push-based] to ingest AWS data into Splunk. This approach requires comfortability with AWS CloudFormation Templates and IaC concepts. This is the underlying ingestion mechanisms that Splunk DM uses, the difference is that here we must create a custom cfn template, and configure the infrastructure to send logs into Splunk.

In our solution we will leverage the AWS GDI Method, either mechanism will work just the same with similar end results, but in this example we will use the S3-Kinesis Firehose (Push Method). 

(1) Let's Prepare: Logging, Monitoring, and Metrics data is brought into a central Logging Account (S3 Bucket(s)). S3 buckets created for storing this data will need to partioned by a naming convention that should at least cover the following: app name, account type, the account type of data ingested, and timestamped (e.g. s3://ENV_NAME/ENV_TYPE/AWS_SERVICE/MM/DD/YY). The critical piece is to make sure that the logging,metrics, and monitoring data stored in S3 is properly partioned so that can apply granular control as to what data we ingest into Splunk. 

(2) Let's Build: AWS Control Tower deployment will consist of creating a new partition on the S3 Bucket in the central Logging Account, it will also deploy the AWS GDI Push Mechanism (S3-Kinesis) creating a new Kinesis Stream per new account being added. This new infrastructure can be created in the same Logging Account, but would also fit in the Shared Services Account this would require IAM Permissions that would need to cross accounts. In our assumption, we will deploy the Push Mechanism in the same Logging Account. Here is the accompanying GitHub Repo [S3-Kinesis Deployment CloudFormation Templates][S3-Kinesis-Cfn] to this blog. This contains CloudFormation (cfn) templates that deploy the infrastructure, and sample IAM permission sets. The aim of these cfn templates is to serve as a starting point to deploy this solution. 

(3) Let's Verify:

Still in progress...


####FAQ:  

Who is this for?

This is for customers who are using Splunk, want to ingest logs from AWS, and __AWS Orgs__.

Who is this __NOT__ for?

This is __NOT__ for customers who can use Splunk Data Manager today to ingest logs from AWS into Splunk. 
[AWS Onboarding from a Single Account][onboard-single-dm]
[AWS Onboarding from Multiple Accounts (No AWS Orgs)][onboard-multi-dm]

What should I consider?
This solution is a general solution, this means that it aims to solve the problem for most, and may break for few.

Why should do this?
You need a solution, that is centralized, scalable, predicatable, and cannot wait for Splunk Data Manager to fully support AWS Orgs support.

Why should I not do this?
Your solution is working, there is no rush, and you can wait for Splunk Data Manager to get AWS Orgs support. Wait for that.

When should I do this?
That will depend on the priority, and urgency of your team. In short, the sooner you get this configured, the less technical debt you will have later on as your onboard more AWS workloads.

#### Disclaimer

The information shared is for general informational purposes only. I do not provide any warranty and recommend readers to test the content thoroughly before implementing it. Use the information at your own discretion and risk. 

[what-is-aws-org]: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html
[dm]: https://www.splunk.com/en_us/blog/platform/meet-the-data-manager-for-splunk-cloud.html
[aws-gdi]:https://docs.splunk.com/Documentation/SplunkCloud/latest/Admin/AWSGDI
[onboard-dm]: https://docs.splunk.com/Documentation/DM/1.8.1/User/AWSAbout#Onboard_AWS_in_Data_Manager
[centr-log-acct]: https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/centralized-logging-and-multiple-account-security-guardrails.html
[aws-addon]:https://splunkbase.splunk.com/app/1876
[pull-based]:https://docs.splunk.com/Documentation/AddOns/released/AWS/DataTypes#Pull-based_API_data_collection_sourcetypes
[push-based]:https://docs.splunk.com/Documentation/AddOns/released/AWS/DataTypes#Push-based_Amazon_Kinesis_Firehose_data_collection_sourcetypes
[S3-Kinesis-Cfn]: https://github.com/animetauren/aws-splunk-firehose-error-reingest/tree/main/firehose-reingest
[onboard-single-dm]: https://docs.splunk.com/Documentation/DM/1.8.1/User/AWSSingleAccount
[onboard-multi-dm]: https://docs.splunk.com/Documentation/DM/1.8.1/User/AWSMultipleAccount