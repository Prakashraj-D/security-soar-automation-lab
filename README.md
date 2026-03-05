# Security SOAR Automation Lab

This project demonstrates a SOAR automation workflow integrating:

- Wazuh SIEM
- MISP Threat Intelligence
- n8n Workflow Automation
- Email Alerting System

The automation analyzes security alerts from firewall logs and enriches them with threat intelligence before sending automated notifications.

---

## Architecture

![SOAR Architecture](architecture/soar-automation-architecture.png)

---

## Workflow

FortiGate Firewall  
↓  
Wazuh SIEM  
↓  
n8n Webhook  
↓  
IOC Extraction  
↓  
MISP Threat Intelligence Lookup  
↓  
Decision Engine  
↓  
Email Alert

---

## Screenshots

### Webhook Node
![Webhook](screenshots/webhook-node.png)

### MISP Lookup
![MISP](screenshots/misp-lookup.png)

### Decision Node
![Decision](screenshots/decision-node.png)

### Email Alert
![Email](screenshots/email-alert.png)

---

## Technologies Used

- Wazuh
- MISP
- n8n
- FortiGate
- Linux
- Threat Intelligence

---

## Skills Demonstrated

- SIEM Integration
- Threat Intelligence Correlation
- Security Automation
- SOAR Workflow Design
