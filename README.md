# Lead Triage

**Know who to call first. Never miss an enquiry.**

Lead Triage is an AI-powered lead management system for study-abroad agencies (UK and Canada). Every enquiry is scored, assigned to the right counselor, explained by AI, and followed up automatically.

> Built as my graduation project at Utiva. All data in this repository is fictional demo data.


## The problem

Businesses that live on enquiries lose customers because messages are answered late, never ranked, and forgotten. For a study-abroad agency, a student is lost before the first conversation.

Research on US firms (Oldroyd, McElheran and Elkington, *The Short Life of Online Sales Leads*, HBR, 2011) found an average reply time of 42 hours, 23% of firms never replying, and leads contacted within an hour about 7x more likely to qualify.

## What it does

1. **Capture:** a student fills in a short public enquiry form (`form/index.html`). Consent is required.
2. **Store:** the enquiry is saved in Supabase with row-level security.
3. **Score:** n8n scores the lead out of 100 and labels it Hot (70+), Warm (40-69) or Cold (under 40).
4. **Assign:** the lead goes to the counselor who covers the destination country and has the fewest active leads.
5. **Explain and draft:** an AI model writes the reason for the score, the next action, and a first message.
6. **Alert:** the counselor gets an email, the student gets a confirmation with an appropriate message for each lead type.
7. **Follow up:** every day at 8 AM (Lagos), a second workflow reminds counselors of follow-ups that are due on slack.
8. **Work the pipeline:** staff log in to a dashboard and move leads from New to Won or Lost.

## Scoring

| Factor | Points |
|---|---|
| Budget | 5 / 12 / 19 / 25 |
| Proof of funds | 3 / 12 / 20 |
| English test | 0 to 15 |
| Intake timing | 5 / 12 / 20 |
| Course clarity | 0 / 10 |

The raw maximum is 90, scaled to 100. Lead source is recorded for reporting only and is not scored.

| Label | Score | Next action |
|---|---|---|
| Hot | 70+ | Call within 2 hours and offer a consultation |
| Warm | 40-69 | Message today and follow up within 24 hours |
| Cold | under 40 | Send an information message and call within 24 hours |

## Tech stack

| Tool | Role |
|---|---|
| Lovable | Staff dashboard with login |
| Supabase | Database, authentication, row-level security, triggers |
| n8n | Workflow 1 (score, assign, alert) and Workflow 2 (daily reminders) |
| OpenRouter | AI reasoning and draft messages |
| Slack | Hot-lead alerts and reminders |
| Gmail SMTP | Counselor and student emails |
| Calendly | Booking link |
| Netlify | Hosting for the enquiry form |

## Repository structure

```
lead-triage/
├── README.md
├── form/index.html              Public enquiry form
├── database/
│   ├── schema.sql               Tables: femtorpath_leads, counselors, interactions
│   ├── policies.sql             Row-level security
│   ├── triggers.sql             set_first_contact trigger
│   └── demo_leads.csv           24 fictional leads
├── n8n/
│   ├── workflow-1-score-assign-alert.json
│   └── workflow-2-daily-reminders.json
├── docs/
│   ├── workflow.png
│   ├── dashboard.png
│   └── architecture.md
├── slides/Lead_Triage_Presentation.pptx
├── .env.example
└── .gitignore
```

## Setup

1. **Supabase:** create a project, then run `database/schema.sql`, `database/policies.sql` and `database/triggers.sql` in the SQL editor. Import `database/demo_leads.csv` through Google Sheets or the table editor.
2. **Form:** open `form/index.html`, set your Supabase project URL and **anon** key at the top, and deploy to Netlify. Use `?src=Instagram` (or another source) in the link to pre-select the source.
3. **n8n:** import both workflow files, then create your own credentials (Supabase service role, OpenRouter, Gmail SMTP, Slack). Node names must stay exactly as exported.
4. **Dashboard:** build the staff app in Lovable and connect it to the same Supabase project.
5. Copy `.env.example` to `.env` and fill in your own values. Never commit `.env`.

## Security notes

- The public form uses only the Supabase **anon** key, and row-level security allows inserts only when `consent_given = true`.
- The service role key lives only in n8n credentials. It is never in this repository.
- Phone numbers and emails in the demo data are fictional.

## Status pipeline

New → Contacted → Counselling → Documents → Applied → Offer → Won / Lost

## Roadmap

- Per-counselor permissions
- Automatic recount of counselor workload
- More destination countries
- Multi-agency support
- Duplicate record

## Author

**Name: [Orobe Munachi]**
Email: [orobemunachi22@gmail.com]
LinkedIn: [linkedin.com/in/munachiorobe]

## License

MIT. See `LICENSE`.


