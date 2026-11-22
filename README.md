# AI-Powered IT Incident Triage (n8n)

An n8n workflow that triages IT service-desk incidents with an LLM, with guardrails built in for regulated (HIPAA-style) environments.

## Flow

```
New incident → Redact PII → Retrieve similar past resolutions → AI triage (GPT-4.1-mini)
            → Validate AI output → Needs human approval?
                                     ├─ yes → Human Approval Queue
                                     └─ no  → Auto-route ticket
```

| Step | What it does |
|---|---|
| **Redact PII** | Removes emails, phone numbers and member IDs before anything reaches the LLM |
| **Retrieve similar past resolutions** | Scores a knowledge base of past incidents and passes the top 3 to the model |
| **AI triage** | Returns category, priority, assignment group, summary, suggested resolution, cited incidents, draft reply and confidence as JSON |
| **Validate AI output** | Rejects invalid JSON, unknown categories/priorities/groups, and citations to incidents that were not retrieved |
| **Needs human approval?** | P1/P2 tickets, confidence below 0.75 or any validation error go to a human; the rest are auto-routed |

## Guardrails

- No personal data sent to the model
- Answers grounded in retrieved past incidents, with citations checked
- Strict schema validation of model output
- Human-in-the-loop for high-impact or uncertain decisions

## Run it

1. In n8n, go to **Workflows → Import from File** and select `ai-incident-triage.json`.
2. Open **AI Triage (GPT-4.1-mini)** and create an **OpenAI** credential with your API key.
3. Click **Execute workflow**. The sample (urgent Citrix outage) is routed to the **Human Approval Queue**.

## Production path

- Replace the manual trigger with a Jira Service Management or ServiceNow trigger.
- Replace the in-workflow knowledge base with a vector store or ticket search.
- Replace the queue nodes with Slack or email "send and wait for approval".

All sample data is fictional.
