# ai-customer-support-triage
AI-powered customer support ticket triage automation built with n8n

## Project Overview

Customer support teams often spend time manually reviewing emails, identifying the nature of each request, and determining which issues require immediate attention.

This workflow automates the initial triage process by:

1. Monitoring a customer support Gmail inbox.
2. Extracting relevant information from incoming emails.
3. Using an LLM to classify each request.
4. Assigning an urgency level based on the reported issue.
5. Recording the results in Google Sheets.
6. Handling classification failures through retry logic and fallback logging.

## Workflow Architecture

How It Works

1. Email Reception — Gmail Trigger

The workflow checks a dedicated support inbox for new customer emails.

2. Data Extraction — Edit Fields

Relevant email information is extracted and standardized, including:

* Message ID
* Sender
* Subject
* Email body
* Received timestamp

3. AI Classification — Basic LLM Chain

The email is analyzed using a Groq-hosted language model.

The model assigns exactly one category and one urgency level.

Categories:

* Billing
* Technical Support
* Account Management
* General Inquiry

Urgency Levels:

* High: Critical outages, security incidents, or time-sensitive operational blockers.
* Medium: Issues disrupting normal operations without immediate critical consequences.
* Low: Routine requests and general inquiries.

A Structured Output Parser ensures that classifications are returned in a consistent JSON format.

4. Ticket Logging — Google Sheets

The workflow records incoming email details and classification results in a Google Sheets ticket log.

5. Basic Error Handling

The workflow includes retry logic for temporary classification failures.

If classification ultimately fails, the error branch records the email with fallback values:

* Category: Unclassified
* Urgency: Needs Review

This allows failed classifications to be identified for manual review.

## Current Limitations

* AI classifications may occasionally be inaccurate.
* It assumes one category per email, rather than handling different types of issues in one email. 
* Error handling does not cover every possible integration failure.

## Development Roadmap

### Version 1 — Email Classification and Logging

* Gmail email ingestion
* AI-powered ticket classification
* Urgency assignment
* Google Sheets logging
* Basic error handling

### Version 2 — Department Routing

Route classified tickets to the appropriate (test) emails.

### Version 3 — Suggested Responses

Generate AI-powered response drafts for support agents to review.

### Version 4 — Urgent Ticket Escalation

Automatically escalate high-urgency requests.

### Version 5 — Reliability Improvements

Introduce additional validation, duplicate detection, monitoring, and error recovery.
