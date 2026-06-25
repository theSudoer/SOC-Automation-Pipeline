![Tech: Splunk](https://img.shields.io/badge/Tech-Splunk-black?logo=splunk)
![Tech: n8n](https://img.shields.io/badge/Tech-n8n-EA4B71?logo=n8n)
![Tech: OpenAI](https://img.shields.io/badge/Tech-OpenAI-white?logo=openai)
![Tech: Slack](https://img.shields.io/badge/Tech-Slack-blue?logo=slack)
![Focus: System Design](https://img.shields.io/badge/Focus-System_Design-purple)

## Executive Summary

This repository contains the architectural documentation and system design for a Security Operations Center (SOC) automation pipeline. The architecture utilises **n8n**, a powerful node-based workflow automation platform, to seamlessly integrate Splunk SIEM alerts with Generative AI (OpenAI/ChatGPT).

In this design, n8n serves as the central orchestration engine. It automatically captures triggered alerts, routes them to the AI for contextual triage, formats the response, and delivers real-time, enriched incident summaries to Slack. The goal of this design is to demonstrate how workflow orchestration and AI can solve common SOC challenges: alert fatigue, inconsistent triage, and slow Mean Time to Respond (MTTR).

---

## Business Impact & Use Cases

- **Combating Alert Fatigue:** By filtering and contextually analysing alerts before human eyes see them, analysts only interact with highly actionable data.
- **Standardised Triage:** Removes subjective human bias from initial severity scoring by relying on structured LLM prompt engineering.
- **Operational Efficiency:** Drastically reduces the time spent manually correlating logs, allowing Tier 1/2 analysts to focus on active threat hunting and remediation.

---

## System Architecture & Data Flow

The pipeline is designed as an event-driven workflow entirely managed and orchestrated by n8n.

### 1. Detection (Splunk)
- Splunk monitors network traffic and endpoint logs.
- Upon detecting an anomaly, an alert is triggered.
- **Output:** Splunk fires a webhook containing a JSON payload with the raw event logs and metadata.

### 2. Ingestion & Orchestration (n8n)
- An **n8n Webhook Node** continuously listens for incoming Splunk alerts.
- Upon receiving a payload, n8n utilises data transformation nodes to parse out relevant fields (Source IP, User, Event Type, Timestamp, Failure Counts) and prepare them for AI analysis.

### 3. AI Analysis & Enrichment (OpenAI API via n8n)
- The n8n workflow passes the orchestrated data to the OpenAI API using an HTTP Request node (or native OpenAI node) with a strictly structured prompt.
- **The AI is instructed to:**
    1. Analyse the log context.
    2. Assign a Severity Level (Low, Medium, High, Critical).
    3. Summarise the threat in a concise statement.
    4. Provide three immediate mitigation steps for the analyst.

### 4. Alerting & Delivery (Slack API via n8n)
- n8n receives the AI's response, formats the data into a clean, readable JSON block payload, and routes it to a native Slack node.
- **Output:** The security team receives an instant, actionable notification in their dedicated incident response channel.

<img width="1920" height="914" alt="image" src="https://github.com/user-attachments/assets/6fe5f69f-c999-4cce-b9d5-ccecaf8a6578" />

---

## Core Component: Prompt Engineering Strategy

A critical part of this architecture is how the AI is prompted to ensure consistent, reliable results. The system utilises a system-message constraints approach:

**System Prompt Example:**
> "You are a Tier 3 Senior SOC Analyst. Analyse the following parsed log data. Do not hallucinate. Respond strictly in JSON format containing 'severity_score', 'threat_summary', and 'mitigation_steps'. Base your severity on the MITRE ATT&CK framework."

---

## Output: Simulated Brute Force Attack

<img width="640" height="696" alt="image" src="https://github.com/user-attachments/assets/40fecce4-fee5-4fd9-b693-fd936d25c551" />

---

## Skills Demonstrated

- **Security Automation & Orchestration:** Proficiency in using tools like n8n to build reliable, multi-step automated workflows.
- **Threat Analysis:** Understanding of what data matters most to a SOC analyst during a high-stress incident like a brute force compromise.
- **API Integration:** Knowledge of how disparate systems (SIEM, LLMs, ChatOps) securely exchange data via webhooks and REST APIs.
- **Prompt Engineering:** Designing deterministic and reliable AI interactions for enterprise security use cases.
