---
title:  "Windows Event Logs Filter Splunk"
date:   2023-06-01 17:00:00
tags: [windows,splunk]
---

This post will cover Windows Event Log Filtering, Splunk, best practice filters for Windows Event Logs, and implementation steps.

What are we trying to accomplish? Why?

Windows Event Logs (WEL) can often be noisy, and veracious when it comes to data produced. The best practice is to collect quality data into Splunk, but not all data is created equally. For this reason, we must filter WEL using regex rules before we ingest the log files into our Splunk Env. 

We want to do this for one or mane of several reasons:
- Reduce ingest size (Helpful for SC Ingest Customers).
- Reduce lenght of time processed due to smaller data size (Helpful for SC Workload Customers).
- Increase efficience of searches, increase speed of alerts 
- Meet compliance requirements, while meeting budget numbers. 

To do this we can leverage a Splunk Forwarder and/or Universal Forwarder, along with a [configured inputs.conf file][configured-inputs-conf-file].  

A precompiled gist of the regex to implement can be found [here][gist]. Always validate your regex patterns, I recommend [regex101][regex101]

#### Disclaimer

The information shared is for general informational purposes only. I do not provide any warranty and recommend readers to test the content thoroughly before implementing it. Use the information at your own discretion and risk. 

[configured-inputs-conf-file]: https://docs.splunk.com/Documentation/SplunkCloud/latest/Data/MonitorWindowseventlogdata#Use_the_inputs.conf_configuration_file_to_configure_event_log_monitoring
[gist]: https://gist.github.com/animetauren/0b2c74c600c7ddd5a969ae025e0a0321
[regex101]: https://regex101.com