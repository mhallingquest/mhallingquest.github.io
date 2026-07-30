---
layout: single
title: "Projects"
permalink: /projects/
classes: wide
---

# 🧠 AI Automation Projects
A collection of automation builds combining **Zapier**, **OpenAI**, and clean orchestration to remove manual work and improve accuracy.

---

## ⚙️ Contract Processing Bot
**Type:** Document Intelligence  
**Stack:** Zapier, OpenAI, Google Drive, Gmail, Slack, Google Sheets  

This bot automates contract intake and review—extracting key clauses, identifying risks, logging results, and routing approvals.

**Highlights**
- 80–90% reduction in contract triage time  
- Real-time Slack alerts for risky clauses  
- Seamless Google Drive + Sheets tracking

### 🧩 Workflow Diagram

### ⚙️ Contract Processing Bot — Diagram

```mermaid
%%{init: {'flowchart': { 'htmlLabels': true, 'wrap': true, 'nodeSpacing': 60, 'rankSpacing': 80 }}}%%
flowchart LR
  subgraph Intake [📥 Intake]
    A1["(1) Gmail Trigger<br/>new attachment 'contract'"]
    A2["(2) Slack<br/>intake notification"]
    A3{"(3) Filter<br/>contract file?"}
    A1 --> A2 --> A3
  end

  A3 -- Yes --> A4["(4) Save to Drive<br/>/Contracts/Incoming"]
  A3 -- No  --> R1["Skip + log<br/>in Sheets"] --> H1([End])

  subgraph AI_Validation [🧠 AI Extraction + Validation]
    B1["(5) OpenAI<br/>extract JSON summary"]
    B2{"Schema-valid<br/>JSON?"}
    B1 --> B2
    B2 -- No --> B1R["Retry<br/>stricter prompt"]
    B2 -- Yes --> C1["(6) Code step<br/>parse JSON → fields"]
  end

  A4 --> B1
  C1 --> D1["(7) Sheets<br/>append run log"]

  subgraph Routing [📊 Routing & Actions]
    D1 --> D2{"(8) Risks<br/>detected?"}
    D2 -- Yes --> E1["Slack<br/>review thread"]
    D2 -- No  --> F1["Move to<br/>/Contracts/Approved"]
    F1 --> F2["(9) Gmail<br/>confirmation email"]
  end

  E1 --> H1
  F2 --> H1

```

**Workflow Steps**

1. **Trigger:** Gmail — new attachment containing “contract”
2. **Notify:** Send immediate Slack notification (intake)
3. **Filter:** Only process contract files (skip & log others)
4. **Store:** Upload to Google Drive → `/Contracts/Incoming`
5. **Extract:** OpenAI step creates JSON summary (parties, dates, amounts, renewal, risks)
6. **Parse:** Code by Zapier converts JSON to typed fields
7. **Log:** Append all run details to Google Sheets
8. **Route:** If risks found → Slack human review thread; else continue
9. **Confirm:** Send email confirmations for auto-approved contracts

---

## 📧 Support Email Agent
**Type:** Agentic Workflow  
**Stack:** Zapier, OpenAI, Gmail, Zendesk, Slack, Google Sheets  

This agent triages every inbound support email, drafts a reply grounded in your knowledge base, and routes anything customer-facing through a one-click Slack approval before it ever leaves the outbox.

**Highlights**
- 24/7 first-response coverage — no ticket sits untouched overnight  
- Human-in-the-loop approval keeps a person accountable for every reply  
- Confidence-based routing sends only low-risk replies (e.g., password resets) automatically  
- Daily Slack digest tracks response time and escalation rate

### 🧩 Workflow Diagram

### 📧 Support Email Agent — Diagram

```mermaid
%%{init: {'flowchart': { 'htmlLabels': true, 'wrap': true, 'nodeSpacing': 60, 'rankSpacing': 80 }}}%%
flowchart LR
  subgraph Intake [📥 Intake]
    A1["(1) Gmail/Zendesk Trigger<br/>new support email"]
    A2["(2) OpenAI<br/>classify intent + urgency"]
    A1 --> A2
  end

  A2 --> B1{"(3) Escalation<br/>keywords? refund/cancel/legal"}
  B1 -- Yes --> H1["Route to<br/>human queue"] --> H2([End])

  B1 -- No --> C1["(4) KB Lookup<br/>Sheets/Airtable match"]

  subgraph AI_Draft [🧠 Draft + Review]
    C1 --> D1["(5) OpenAI<br/>draft grounded reply"]
    D1 --> D2{"(6) Confidence<br/>+ risk check"}
    D2 -- Low-risk/high-confidence --> E1["Auto-send"]
    D2 -- Needs review --> F1["(7) Slack thread<br/>Approve / Edit / Reject"]
  end

  F1 -- Approved --> E1
  F1 -- Edited/Rejected --> D1

  E1 --> G1["(8) Send via<br/>Gmail/Zendesk API"]
  G1 --> G2["(9) Log to Sheets<br/>+ daily digest"]
  G2 --> H2

```

**Workflow Steps**

1. **Trigger:** Gmail/Zendesk — new inbound support email
2. **Classify:** OpenAI scores intent (billing, technical, general) and urgency
3. **Escalation check:** Refund/cancel/legal/angry-sentiment keywords route straight to a human, bypassing AI drafting
4. **Retrieve:** Pull the matching FAQ/knowledge-base article via lookup table
5. **Draft:** OpenAI generates a reply grounded in the matched KB content, in brand voice
6. **Confidence check:** Low-risk, high-confidence replies (e.g., password reset) go straight to send
7. **Human-in-the-loop:** Everything else posts to a Slack thread with Approve / Edit / Reject actions
8. **Send:** Approved replies go out via the Gmail/Zendesk API
9. **Log & report:** Every reply logged to Sheets; daily Slack digest shows volume, response time, escalation rate

---

## 🗒️ Meeting Intelligence & Action-Item Agent
**Type:** Agentic Workflow + Document Intelligence  
**Stack:** FastAPI, OpenAI (GPT-4o-mini), Whisper, MongoDB, Slack API, Notion/Asana API  

Meetings generate decisions and action items that usually die in someone's notes app. This service listens for a finished meeting, turns the recording or transcript into structured minutes, and pushes every action item — with an owner and a due date — straight into the team's task tool.

**Highlights**
- Cuts meeting write-up time from ~20 minutes to under 2  
- Action items land in Notion/Asana automatically, no manual re-typing  
- Weekly rollup flags overdue items by owner, closing the accountability gap  
- Searchable meeting history in MongoDB — "what did we decide about X?" answered in seconds

### 🧩 Workflow Diagram

### 🗒️ Meeting Intelligence Agent — Diagram

```mermaid
%%{init: {'flowchart': { 'htmlLabels': true, 'wrap': true, 'nodeSpacing': 60, 'rankSpacing': 80 }}}%%
flowchart LR
  subgraph Intake [📥 Intake]
    A1["(1) Webhook<br/>Zoom/Teams recording ready"]
    A2["(2) Whisper<br/>transcribe audio"]
    A1 --> A2
  end

  A2 --> B1["(3) FastAPI<br/>send transcript to LLM"]

  subgraph AI_Extract [🧠 Extraction]
    B1 --> C1["(4) OpenAI<br/>extract summary + decisions + action items"]
    C1 --> C2{"(5) Schema-valid<br/>JSON?"}
    C2 -- No --> C1R["Retry<br/>stricter prompt"] --> C1
    C2 -- Yes --> D1["(6) Store in MongoDB<br/>meeting record"]
  end

  subgraph Action [📊 Action + Notify]
    D1 --> E1["(7) Create tasks<br/>Notion/Asana API"]
    D1 --> E2["(8) Post summary<br/>to Slack channel"]
  end

  E1 --> F1["(9) Weekly cron<br/>check overdue items"]
  F1 --> F2["Slack digest<br/>to owners + organizer"]
  E2 --> H1([End])
  F2 --> H1

```

**Workflow Steps**

1. **Trigger:** Zoom/Teams webhook fires when a recording is ready (or a transcript is uploaded manually)
2. **Transcribe:** Whisper API converts audio to text if a transcript wasn't already provided
3. **Send to LLM:** FastAPI service forwards the transcript for structured extraction
4. **Extract:** GPT-4o-mini pulls out a summary, key decisions, and action items (task, owner, due date)
5. **Validate:** Schema check on the returned JSON; retry with a stricter prompt on malformed output
6. **Store:** Structured meeting record saved to MongoDB for searchable history
7. **Create tasks:** FastAPI calls the Notion/Asana API to create one task per action item, tagged to the right owner
8. **Notify:** Clean summary + action item list posted to the team's Slack channel
9. **Track & digest:** Weekly cron checks the DB for items past due date and Slack-DMs each owner, with a rollup to the meeting organizer
    
