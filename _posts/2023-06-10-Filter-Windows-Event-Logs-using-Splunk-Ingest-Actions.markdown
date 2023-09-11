---
title:  "Filter Windows Event Logs using Splunk Ingest Actions"
date:   2023-06-10 17:00:00
tags: [windows,splunk]
---

This post is a follow-up, more advance post to filter [Window Event Logs in Splunk][win-event-logs-filter].  

What are we trying to accomplish? Why?

Windows Event Logs (WEL)can often be noisy, and veracious when it comes to data produced. The best practice is to collect quality data into Splunk, but not all data is created equally. For this reason, we must filter WEL using regex rules before we ingest the log files into our Splunk Env. 

We want to do this for one or mane of several reasons:
- Reduce ingest size (Helpful for SC Ingest Customers).
- Reduce lenght of time processed due to smaller data size (Helpful for SC Workload Customers).
- Increase efficience of searches, increase speed of alerts 
- Meet compliance requirements, while meeting budget numbers. 

With the introduction of [Ingest Actions (IA)][ingest-actions], Splunk users gain access to an additional method for data ingestion. This approach allows users to focus on specific logs they require, enhancing data quality while effectively managing the quantity of ingested data. The availability and functionality of Ingest Actions depend on the user's Splunk version, platform, and permissions. 
<br />
<br />For more information, refer to the Splunk documentation on [Ingest Actions requirements][ia-reqs].

This post will primarily focus on creating [IA rulesets][ia-rulesets] to streamline and filter the excessive verbosity often associated with Windows Event Logs. We assume that you have already installed and configured the [Windows Add-on for Splunk][win-event-add-on].

Before we proceed, it's important to note that the recommended IA rules we are suggesting are directly derived from the Splunk documentation for [Windows event clean-up best practices][win-event-bp-splunk]. In our case, we will be utilizing the recommended transforms and translating them into IA rulesets.

Let's beging by navigating to Ingest Actions:
- Inside of Splunk select Settings > Data > Ingest Actions
- Once here we will select ["Mask with regular expression"][mask-regex]
- Open [IA Win Event Rule Set Gist][ingest-action-win-event-rules], this will list all the translate rules. 
- Select the appropriate Source (e.g., WinEventLog:System) and choose the correct regex. 
- In the replace box, enter a blank space, this will replace the matched string with a blank space.
- Confirm that string is matched, and replaced with a blank space.

You are finished.

#### Disclaimer

The information shared is for general informational purposes only. I do not provide any warranty and recommend readers to test the content thoroughly before implementing it. Use the information at your own discretion and risk. 

[win-event-logs-filter]: https://blog.anmtrn.com/2023/Windows-Event-Logs-Filter-Splunk/
[ingest-actions]: https://community.splunk.com/t5/Splunk-Tech-Talks/Introducing-Ingest-Actions-Filter-Mask-Route-Repeat/ba-p/608111
[ia-reqs]: https://docs.splunk.com/Documentation/Splunk/9.0.5/Data/DataIngest#Requirements
[ia-rulesets]: https://docs.splunk.com/Documentation/Splunk/9.0.5/Data/DataIngest#Introduction_to_rules_and_rulesets
[win-event-add-on]: https://docs.splunk.com/Documentation/WindowsAddOn/8.1.2/User/DeploytheSplunkAdd-onforWindowswithForwarderManagement
[win-event-bp-splunk]: https://docs.splunk.com/Documentation/WindowsAddOn/latest/User/Configuration#Configure_event_cleanup_best_practices_in_props.conf
[mask-regex]: https://docs.splunk.com/Documentation/Splunk/9.0.5/Data/DataIngest#Mask_with_regular_expression
[ingest-action-win-event-rules]: https://gist.github.com/animetauren/afddab3a2aff30526f6032766542ad22