
# n8n SOAR Workflow Description

## Overview

This workflow implements a **Security Orchestration, Automation and Response (SOAR)** pipeline using **n8n**, **Wazuh SIEM**, and **MISP Threat Intelligence**.

The automation processes security alerts generated from **FortiGate firewall logs**, enriches them with threat intelligence, and automatically sends alert notifications to security analysts.

This system reduces manual SOC investigation time and improves incident response efficiency.

---

# System Components

| Component | Role |
|--------|------|
| FortiGate Firewall | Generates security logs and authentication events |
| Wazuh SIEM | Collects, analyzes, and forwards security alerts |
| n8n Automation Engine | Executes SOAR workflow |
| MISP Threat Intelligence | Provides IOC threat intelligence lookup |
| Email Notification System | Sends automated alerts to SOC analysts |

---

# Workflow Architecture

```

FortiGate Firewall
│
▼
Wazuh SIEM
│
▼
n8n Webhook Trigger
│
▼
IOC Extraction
│
▼
Rule Filtering
│
▼
MISP Threat Intelligence Lookup
│
▼
Merge Alert Data + Threat Intel
│
▼
Decision Engine
│
┌──────┴────────────┐
▼                   ▼
Malicious IP       Unknown IP
Email Alert        Suspicious Alert

````

---

# Workflow Stages

## 1. Webhook Trigger Node

The workflow begins when **Wazuh SIEM sends a security alert** to the n8n webhook.

The webhook receives the alert in **JSON format**.

Example alert payload:

```json
{
  "timestamp": "2026-03-04T09:58:00",
  "rule": {
    "level": 8,
    "description": "FortiGate Authentication Failure"
  },
  "data": {
    "srcip": "192.168.0.62"
  }
}
````

This node acts as the **entry point for the automation pipeline**.

---

## 2. IOC Extraction Node

This step extracts important information from the alert payload.

Extracted fields:

* Source IP address
* Rule description
* Alert severity
* Timestamp

Example output:

```
srcip: 192.168.0.62
rule_desc: FortiGate Authentication Failure
severity: 8
timestamp: 2026-03-04
```

These fields are used for threat intelligence enrichment.

---

## 3. FortiGate Rule Filtering

This stage filters incoming alerts to process only **relevant firewall events**.

Filtered events include:

* Authentication failures
* Login attempts
* Suspicious access attempts

Filtering prevents unnecessary workflow execution and reduces false positives.

---

## 4. Threat Intelligence Lookup (MISP)

The extracted **source IP address** is queried against the **MISP Threat Intelligence database**.

Example API Request:

```json
{
  "type": "ip-src",
  "value": "192.168.0.62"
}
```

Possible results:

### IP Found in Threat Intelligence

```
response.length > 0
```

Indicates the IP is a **known malicious indicator**.

### IP Not Found

```
response = []
```

Indicates the IP is not currently known as malicious.

---

## 5. Data Merge Node

This node merges:

* Wazuh alert data
* MISP threat intelligence results

Example merged output:

```json
{
"srcip": "192.168.0.62",
"rule_desc": "FortiGate Authentication Failure",
"severity": 8,
"timestamp": "2026-03-04",
"response": []
}
```

This consolidated data is passed to the decision engine.

---

## 6. Decision Engine (IF Node)

The IF node determines the alert type based on the threat intelligence response.

Condition:

```
response.length > 0
```

Decision outcomes:

| Condition | Result                |
| --------- | --------------------- |
| TRUE      | Malicious IP detected |
| FALSE     | Suspicious activity   |

---

# Automated Response

## Malicious IP Alert

If the IP exists in MISP:

```
Malicious IP Detected

Source IP: 192.168.0.62
Threat Intel: Found in MISP
Severity: High

Recommended Action:
Investigate immediately and block the IP.
```

---

## Suspicious Login Alert

If the IP is not found in threat intelligence:

```
Suspicious Login Observed

Source IP: 192.168.0.62
Rule: FortiGate Authentication Failure
Severity: Medium
Threat Intel: Not found in MISP

Recommendation:
Monitor repeated authentication attempts.
```

---

# Security Automation Benefits

This automation provides several operational advantages:

* Automated IOC enrichment
* Faster threat detection
* Reduced SOC analyst workload
* Real-time threat intelligence correlation
* Improved incident response speed

---

# Limitations

The current implementation relies on **MISP threat intelligence only**.

If the IP address does not exist in the database, it will be classified as **suspicious rather than malicious**.

---

# Future Improvements

The workflow can be enhanced with additional security integrations:

### Additional Threat Intelligence Sources

* AbuseIPDB
* VirusTotal
* AlienVault OTX

### Automated Response

* Automatic firewall blocking
* Incident ticket creation
* SOC dashboard integration

### Threat Enrichment

* GeoIP location lookup
* ASN information
* Reputation scoring

---

# Conclusion

This project demonstrates how **SIEM alerts can be automated using SOAR techniques**.

By integrating **Wazuh, n8n, and MISP**, the system can automatically:

* analyze firewall alerts
* enrich them with threat intelligence
* generate actionable alerts for security analysts

This approach improves SOC operational efficiency and reduces manual investigation effort.
