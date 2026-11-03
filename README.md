# ai-customer-support-triage
AI-powered customer support ticket triage automation built with n8n

## Project Overview
Customer support teams often spend time manually reviewing emails and determining which issues require immediate attention. This workflow monitors emails automatically, uses an LLM to classify them by department (e.g. technical support, billing), and assigns an urgency level. It then sends the emails to the right departments and posts on Slack if an issue is urgent. 

## Current Workflow Architecture

<img width="858" height="436" alt="Screenshot 2026-11-04 at 12 22 54 AM" src="https://github.com/user-attachments/assets/fd77f67e-9743-4720-ab70-986896993e75" />

How it works:

### 1. Detect Emails via Gmail

The workflow periodically checks a dedicated support Gmail inbox for new customer emails.

### 2. Data Extraction 

Relevant email information is extracted and standardized, including:
* Message ID
* Sender
* Subject
* Email preview (Gmail snippet)
* Received timestamp

### 3. Issue Classification by AI

Each email is analyzed using a Groq-hosted language model. The model assigns exactly one category and one urgency level.

Categories:
* Billing
* Technical Support
* Account Management
* General Inquiry

Urgency Levels:
* High: Critical outages, security incidents, or time-sensitive operational blockers.
* Medium: Issues disrupting normal operations without immediate critical consequences.
* Low: Routine requests and general inquiries.

A Structured Output Parser enforces a consistent JSON output structure.

### 4. Ticket Logging on Google Sheets

The workflow records email details and classification results in a Google Sheets ticket log.

Each record includes the message ID, sender, subject, email preview, received timestamp, category, and urgency.

### 5. Department Routing — Edit Fields + Gmail

The workflow maps each classified ticket to its corresponding department (e.g. billing team). It then sends an email with the ticket. 

For testing, Gmail plus-addressing simulates multiple departments using one inbox.

### 6. Urgent Ticket Escalation on Slack

An IF node checks whether the ticket has been classified as High urgency. High-urgency tickets trigger a Slack notification in a dedicated support channel.

Each alert includes the assigned department, urgency level, customer information, and reported issue to help the team prioritize its response.

Medium- and Low-urgency tickets do not trigger Slack escalation.

### 7. Basic Error Handling

The workflow includes retry logic for temporary AI classification, Gmail, and Slack failures. Any error gets recorded in Google Sheets logs. 

Persistent logging of notification failures remains a planned improvement.

## Example Outputs

AI Classification and Ticket Log (Google Sheets)

<img width="923" height="226" alt="Screenshot 2026-11-04 at 12 31 30 AM" src="https://github.com/user-attachments/assets/e92345e6-ca93-432e-8422-63e3fb8ebca1" />

Department Email Notification

<img width="1455" height="1081" alt="Redacted Customer Support Invoice Email" src="https://github.com/user-attachments/assets/e144483c-3dfd-4e58-b910-53c1b014c305" />

Urgent Ticket Escalation on Slack

<img width="2160" height="728" alt="Urgent Customer Support Ticket Notification" src="https://github.com/user-attachments/assets/1cf88e5a-7c7c-43bc-81dd-556784999d08" />

## Tech Stack

* n8n: Workflow orchestration, conditional routing, and error handling
* Gmail: Email ingestion and department notifications
* Groq API: LLM-powered ticket classification
* Google Sheets: Ticket logging and classification failure tracking
* Slack: High-urgency ticket escalation

## Current Limitations

* AI classifications may occasionally be inaccurate and require human review.
* It assumes one category per email, rather than handling different types of issues in one email. 
* Error handling does not yet cover every possible integration failure.

## Development Roadmap

### Version 1 — Email Classification and Logging (Completed)

* Gmail email ingestion
* AI-powered ticket classification
* Urgency assignment
* Google Sheets logging
* Basic error handling

### Version 2 — Department Routing and Urgent Escalation (Completed)

* Dynamic department assignment
* Automated Gmail notifications
* Slack escalation for High-urgency tickets
* Notification retry logic
* Basic notification error-processing branches

### Version 3 — Reliability Improvements (Planned)

Introduce additional validation, duplicate detection, monitoring, and error recovery.
