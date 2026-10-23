# AI Customer Support Ticket Automation | Zapier + OpenAI

An AI-assisted customer support workflow built with Zapier and OpenAI. It receives support tickets, classifies them, assigns priority and a responsible team, routes urgent/high-priority tickets to Slack, records tickets in Zapier Tables, and emails customers an acknowledgement.

## Workflow overview

1. **Receive ticket** — a Zapier webhook receives ticket details: ticket ID, customer name/email, subject, message, category, and plan.
2. **Analyze request with OpenAI** — validates the category, assigns a priority, selects the appropriate team, and returns structured JSON.
3. **Branch by priority** — Zapier Paths evaluate the result as Urgent, High, Medium, or Low.
4. **Notify and record**
   - **Urgent:** post an alert to the urgent Slack channel, create an “Urgent - Open” ticket record, and send an urgent acknowledgement email.
   - **High:** post an alert to the high-priority Slack channel, create a “High - Open” ticket record, and send a high-priority acknowledgement email.
   - **Medium:** create an “Open” ticket record and send a standard acknowledgement email.
   - **Low:** create an “Open” ticket record and send a standard acknowledgement email.

## Classification rules

### Categories and teams

| Category | Typical issues | Assigned team |
|---|---|---|
| `billing` | Payments, subscriptions, billing questions | Finance Support |
| `technical` | Bugs, errors, feature requests, performance | Technical Support |
| `account` | Login, passwords, profiles, permissions | Account Support |
| `general` | Feedback, general questions, other requests | Customer Support |

### Priority rules

- **Urgent:** payment failure, security issue, blocked account, or service unavailable.
- **High:** important issue from a premium customer or repeated attempts to resolve an issue.
- **Medium:** normal support issue.
- **Low:** general question or feedback.

OpenAI is instructed to return `category`, `priority`, `assigned_team`, and a brief `reasoning` explanation.

## Ticket record fields

The workflow writes ticket details to a Zapier Tables table:

- Ticket ID
- Customer name and email
- Subject and message
- AI-classified category
- Customer plan
- Priority
- Assigned team
- Status
- Created timestamp

## Repository structure

```text
.
├── README.md
└── workflow/
    └── customer-support-ticket-automation.json
```

## Setup notes

1. Import or recreate the workflow in Zapier using the JSON export as a reference.
2. Connect your own OpenAI, Slack, email, webhook, and Zapier Tables accounts.
3. Map the webhook fields to the OpenAI analysis step.
4. Update Slack destinations and the target table to match your workspace.
5. Test all four priority paths with representative sample tickets before enabling the automation.

## Important

The supplied workflow export shows the Zap and its steps as **paused/off**. Credentials, table IDs, Slack channel IDs, and account-specific identifiers are environment-specific; verify or replace them in your own Zapier workspace. Treat customer data and exported workflow configuration as sensitive. The JSON file is a workflow configuration/reference, not a guarantee that it can be imported and run without reconnecting apps and validating mappings.
