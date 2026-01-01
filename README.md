# DNS Telemetry Investigation in Splunk Cloud

## Overview
This repository documents a focused Security Operations Center (SOC) investigation using DNS telemetry analyzed in Splunk Cloud. The purpose of this work was to determine whether DNS logs alone can be used to identify abnormal host behavior and support escalation decisions based on evidence rather than alerts.

The analysis emphasizes investigative reasoning: defining a hypothesis, validating data, examining behavior at scale, isolating anomalies, and assessing security significance.

---

## Environment
- Platform: Splunk Cloud
- Log Type: DNS telemetry
- Dataset: `botsv1.stream_dns.csv.gz`
- Index: `main`
- Analysis Window: 24 hours

---

## What Was Looked For
The investigation focused on identifying internal hosts whose DNS activity deviated significantly from normal patterns. Specifically, the analysis sought to determine:

- Which hosts generated unusually high volumes of DNS queries
- Whether elevated DNS activity was short-lived or sustained
- Whether observed patterns were consistent with automated behavior rather than normal user activity

This approach aligns with common SOC use cases such as detecting malware beaconing, misconfigured services, or unauthorized automated processes.

---

## How the Analysis Was Performed
DNS events were first validated to ensure the dataset was successfully ingested and searchable. Because the dataset did not reliably expose key investigative fields by default, the source IP address was extracted directly from raw event data to establish a stable entity for analysis.

With source IPs extracted, DNS query volume was examined across internal hosts to establish a behavioral baseline. This comparative analysis made it possible to identify hosts whose activity stood out relative to peers rather than relying on static thresholds.

Once an outlier host was identified, its DNS activity was isolated and analyzed over shorter time intervals. This time-based analysis was used to determine whether the behavior represented a transient spike or a persistent pattern. The resulting visualization showed consistent, periodic DNS activity rather than irregular bursts.

---

## What Was Found
The analysis identified a single internal host (`192.168.225.111`) that generated more than 1,000 DNS queries during the analysis window. This volume was significantly higher than that of other internal hosts and remained elevated over time.

The DNS activity exhibited a steady and repetitive pattern, indicating automated behavior. Unlike user-driven DNS requests, which tend to be irregular and correlated with browsing activity, this pattern suggested a background process repeatedly resolving domains at consistent intervals.

---

## Why the Findings Are Significant
Sustained, high-volume, and periodic DNS activity is a common indicator of security-relevant behavior. In operational SOC environments, such patterns are frequently associated with:

- Malware beaconing to command-and-control infrastructure
- Misconfigured applications generating excessive DNS lookups
- Unauthorized scripts or scheduled tasks operating without visibility

Although this investigation did not include endpoint telemetry or threat intelligence enrichment, the DNS behavior alone was sufficient to justify escalation for deeper analysis. The ability to reach this conclusion using only DNS logs demonstrates how low-level telemetry can surface meaningful security signals when analyzed behaviorally.

---

## Next Steps
To advance this investigation in a production setting, the following actions would be taken:

- Enrich DNS queries with domain reputation and threat intelligence
- Analyze query entropy to detect algorithmically generated domains
- Correlate DNS activity with proxy, firewall, or EDR telemetry
- Inspect endpoint processes associated with the identified host
- Convert the observed behavior into a scheduled detection or alert

---

## Conclusion
This project demonstrates that DNS telemetry, when properly analyzed, can provide actionable insight into abnormal host behavior. By validating data, establishing baselines, and focusing on behavioral patterns rather than alerts, it is possible to identify activity that warrants security escalation even in constrained environments such as Splunk Cloud trials.

## Notes on Ethics & Safety
- No malware execution is required for this lab
- Datasets will be public, simulated, or sanitized
- Findings are evidence-based and written conservatively

---
