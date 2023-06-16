---
title:  "Ingest Actions Windows Event Logs"
date:   2023-06-10 17:00:00
tags: [windows,splunk]
---

With the announcement of [Ingest Actions (IA)][ingest-actions], Splunk users can leverage another way to ingest data, focusing on just the logs they need, increasing data quality while managing data quantity ingested. Ingest Actions is dependent on Splunk version, platform, and user's permissions. For more info see [Splunk Doc][ia-reqs].

In this blog we will focus on creating [IA rulesets][ia-rulesets] to clean up, and filter the extra verboseness associated with Windows Event Logs. We will be assuming that you already have the [Windows Add on for Splunk][win-event-add-on] installed, and configured. 

Before we begin, the IA rules that are being reccomended are coming directly from the Splunk documentation for Windows event clean up [best practics][win-event-bp-splunk]. In our case we will the reccomended transforms, and translate them to IA rulesets.

Navigate to Ingest Actions from your Splunk SH (select Settings > Data > Ingest Actions)
Once here we will select "Filter with regular expression"



[ingest-actions]: https://community.splunk.com/t5/Splunk-Tech-Talks/Introducing-Ingest-Actions-Filter-Mask-Route-Repeat/ba-p/608111
[ia-reqs]: https://docs.splunk.com/Documentation/Splunk/9.0.5/Data/DataIngest#Requirements
[ia-rulesets]: https://docs.splunk.com/Documentation/Splunk/9.0.5/Data/DataIngest#Introduction_to_rules_and_rulesets
[win-event-bp-splunk]: https://docs.splunk.com/Documentation/WindowsAddOn/latest/User/Configuration#Configure_event_cleanup_best_practices_in_props.conf
[win-event-add-on]: https://docs.splunk.com/Documentation/WindowsAddOn/8.1.2/User/DeploytheSplunkAdd-onforWindowswithForwarderManagement
[ingest-action-win-event-rules]: https://gist.github.com/animetauren/afddab3a2aff30526f6032766542ad22