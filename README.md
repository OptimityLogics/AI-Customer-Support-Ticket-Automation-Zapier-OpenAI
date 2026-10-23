# AI Customer Support Ticket Automation with Zapier and OpenAI

An AI-assisted support-ticket workflow that receives incoming tickets through a webhook, classifies each request with OpenAI, assigns a support team and priority, routes the ticket through priority-specific paths, records it in Zapier Tables, and sends a customer acknowledgement email.

> **Workflow status:** The supplied JSON export is currently off/paused. It contains account-specific Slack channel IDs, a Zapier Tables ID, and connection references. Reconnect and replace these resources in your own Zapier account, then test every path before enabling it.

## What it does

1. **Receive a ticket** through Webhooks by Zapier.
2. **Analyze the ticket with OpenAI** and return structured fields: `category`, `priority`, `assigned_team`, and `reasoning`.
3. **Route by priority** using four conditional paths: Urgent, High, Medium, and Low.
4. **Send Slack alerts** for Urgent and High tickets.
5. **Store ticket details** in Zapier Tables with a status and creation timestamp.
6. **Send an acknowledgement email** using a priority-appropriate template.

## Workflow at a glance

```text
Incoming ticket
      |
Webhooks by Zapier — Catch Hook
      |
OpenAI — Analyze Request
      |
Priority Paths
  |       |       |       |
Urgent   High   Medium    Low
  |       |       |       |
Slack    Slack   —        —
alert    alert
  |       |       |       |
Create/update ticket record in Zapier Tables
  |       |       |       |
Send acknowledgement email to customer
```

The exact actions and ordering are defined by the exported workflow. Review the path connections in Zapier after import/recreation; do not assume a downloaded JSON is ready to run without reconnecting integrations.

## Classification rules

### Categories and assigned teams

| Category | Typical request | Assigned team |
|---|---|---|
| `billing` | Payments, subscriptions, billing questions | Finance Support |
| `technical` | Bugs, errors, feature requests, performance | Technical Support |
| `account` | Login, password, profile, permissions | Account Support |
| `general` | Feedback, general questions, other requests | Customer Support |

### Priority guidelines

- **Urgent:** payment failure, security issue, blocked account, or service unavailable.
- **High:** an important issue from a premium customer or a repeated issue.
- **Medium:** a normal support issue.
- **Low:** a general question or feedback.

OpenAI is instructed to return one of the exact priority values `Urgent`, `High`, `Medium`, or `Low`, along with category, assigned team, and a brief reasoning explanation. Treat the result as an AI recommendation and review consequential security or account decisions.

## Ticket data

The webhook trigger expects these fields, which are mapped into the AI step and ticket records:

| Field | Purpose |
|---|---|
| `ticket_id` | Unique ticket identifier |
| `customer_name` | Customer name used in the acknowledgement |
| `customer_email` | Recipient address for the acknowledgement |
| `subject` | Ticket subject |
| `message` | Customer's issue details |
| `category` | Submitted category, validated by AI |
| `plan` | Customer plan, used as context for priority |

The Zapier Tables record is mapped to include ticket ID, customer name, customer email, subject, message, classified category, customer plan, priority, assigned team, status, and creation timestamp. Status values in the export include `Urgent - Open`, `High - Open`, and `Open`.

## Repository structure

```text
.
├── README.md
├── setUp/
│   └── setupguide.md
└── workflow/
    └── customer-support-ticket-automation.json
```

## Setup

Follow the step-by-step [Setup Guide](setUp/setupguide.md) for webhook configuration, OpenAI field mapping, priority paths, Slack channels, Zapier Tables, acknowledgement emails, testing, and troubleshooting.

- [Workflow JSON](workflow/customer-support-ticket-automation.json)
- [Setup Guide](setUp/setupguide.md)

## Testing checklist

Test at least one ticket for each priority and verify:

- OpenAI returns all four expected fields and valid values.
- The intended priority path runs and non-matching paths do not.
- Urgent and High alerts arrive in the correct Slack channels.
- The ticket is written to the correct Zapier Tables destination with the expected status.
- The acknowledgement email uses the right recipient and template.
- Missing or invalid customer email and malformed AI output are handled safely.

## Security and operational notes

- Never commit API keys, OAuth tokens, passwords, customer secrets, or private webhook URLs.
- Do not send passwords, payment-card data, or other secrets in ticket payloads.
- Limit Slack and table access to people who need customer information.
- Treat ticket text as untrusted input and review AI decisions before consequential actions.
- Add duplicate-ticket handling if the sender may retry the same `ticket_id`; create-record actions alone do not guarantee deduplication.
- Check your email provider's sending permissions and organizational policies before enabling acknowledgement emails.

## Limitations

This repository contains a workflow JSON export and documentation; it is not a hosted customer support application. The export may not be directly importable in every Zapier account, and app connections, resource IDs, field mappings, and path wiring must be validated in the target workspace.
