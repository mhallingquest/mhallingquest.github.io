---
layout: single
title: "Projects"
permalink: /projects/
classes: wide
---

# 🧠 AI Automation Projects
A collection of automation builds combining **Zapier**, **OpenAI**, and clean orchestration to remove manual work and improve accuracy.

---

## ⚙️ Contract Processing Bot {#contract-processing-bot}
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

<div id="contract-demo" style="border:1px solid #444; border-radius:10px; padding:1.25rem; margin:1.5rem 0; background:rgba(255,255,255,0.03);">
  <p style="margin-top:0; font-weight:600;">🎬 Try it live</p>
  <p style="font-size:0.9rem; opacity:0.85;">
    Paste contract text (or use the example below) and this calls the
    actual deployed FastAPI backend — not a canned response.
  </p>
    <p style="font-size:0.8rem; opacity:0.75; border-left:3px solid #ff9800; padding:0.4rem 0.7rem; background:rgba(255,152,0,0.08); border-radius:0 4px 4px 0; margin:0.75rem 0;">
    ⚠️ <strong>Not legal advice.</strong> This is a demo of an AI document-scanning
    tool, not a substitute for review by a licensed attorney. Do not rely on
    its output for real contract decisions.
  </p>
  <textarea id="contract-demo-input" rows="8" style="width:100%; box-sizing:border-box; font-family:inherit; padding:0.6rem; border-radius:6px; border:1px solid #555; background:rgba(0,0,0,0.2); color:inherit;">This Software Subscription Agreement ("Agreement") is entered into as of January 1, 2026 by and between Acme Corp ("Client") and Beta LLC ("Provider"). Provider shall provide access to its software platform for a monthly fee of $2,500, payable on the 1st of each month. A late payment fee of $150 applies to payments received more than 5 days after the due date. This Agreement shall commence on the Effective Date and continue until December 31, 2026, and shall automatically renew for successive 12-month terms unless either party provides written notice of non-renewal at least 30 days prior to the end of the then-current term. Provider's total liability under this Agreement shall not exceed the fees paid by Client in the month immediately preceding the claim.</textarea>
  <div style="margin-top:0.75rem;">
    <button id="contract-demo-btn" style="padding:0.5rem 1.1rem; border-radius:6px; border:none; background:#4f7cff; color:white; cursor:pointer; font-weight:600;">
      Run Live Demo
    </button>
    <span id="contract-demo-status" style="margin-left:0.75rem; font-size:0.85rem; opacity:0.8;"></span>
  </div>
  <div id="contract-demo-result" style="margin-top:1rem;"></div>
</div>

<script>
(function () {
  // Update this once your Contract Processing Bot Railway deployment is
  // live — this is a separate service from the Meeting Intelligence Agent.
  const DEMO_API_URL = "https://contract-processing-bot-production.up.railway.app";

  const btn = document.getElementById("contract-demo-btn");
  const input = document.getElementById("contract-demo-input");
  const status = document.getElementById("contract-demo-status");
  const resultBox = document.getElementById("contract-demo-result");

  function escapeHtml(str) {
    const div = document.createElement("div");
    div.textContent = str;
    return div.innerHTML;
  }

  const severityColor = { low: "#4caf50", medium: "#ff9800", high: "#f44336" };

  function renderResult(data) {
    const parties = data.parties.length
      ? data.parties.map(escapeHtml).join(", ")
      : "<span style='opacity:0.7;'>None identified</span>";

    const keyDates = data.key_dates.length
      ? "<ul>" + data.key_dates.map(kd =>
          `<li><strong>${escapeHtml(kd.label)}</strong>: ${kd.date ? escapeHtml(kd.date) : "<span style='opacity:0.7;'>not stated</span>"}</li>`
        ).join("") + "</ul>"
      : "<p style='opacity:0.7;'>None identified</p>";

    const amounts = data.amounts.length
      ? "<ul>" + data.amounts.map(a => `<li>${escapeHtml(a)}</li>`).join("") + "</ul>"
      : "<p style='opacity:0.7;'>None identified</p>";

    const riskFlags = data.risk_flags.length
      ? "<ul>" + data.risk_flags.map(rf =>
          `<li><strong style="color:${severityColor[rf.severity] || '#999'}">[${rf.severity.toUpperCase()}]</strong> ` +
          `<strong>${escapeHtml(rf.clause)}</strong> — ${escapeHtml(rf.risk)}</li>`
        ).join("") + "</ul>"
      : "<p style='opacity:0.7;'>None flagged</p>";

    resultBox.innerHTML = `
      <div style="border-top:1px solid #444; padding-top:0.75rem;">
        <p><strong>Summary</strong><br>${escapeHtml(data.summary)}</p>
        <p><strong>Parties</strong><br>${parties}</p>
        <p><strong>Key Dates</strong></p>${keyDates}
        <p><strong>Amounts</strong></p>${amounts}
        <p><strong>Renewal Terms</strong><br>${data.renewal_terms ? escapeHtml(data.renewal_terms) : "<span style='opacity:0.7;'>Not specified</span>"}</p>
        <p><strong>Risk Flags</strong></p>${riskFlags}
      </div>
    `;
  }

  btn.addEventListener("click", async function () {
    const contractText = input.value.trim();
    if (!contractText) {
      status.textContent = "Paste contract text first.";
      return;
    }

    btn.disabled = true;
    status.textContent = "Calling the live backend…";
    resultBox.innerHTML = "";

    try {
      const resp = await fetch(DEMO_API_URL + "/demo/contract-extract", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ contract_text: contractText }),
      });

      if (resp.status === 429) {
        status.textContent = "Demo rate limit reached — try again in a bit.";
        return;
      }
      if (!resp.ok) {
        const err = await resp.json().catch(() => ({}));
        status.textContent = "Error: " + (err.detail || resp.statusText);
        return;
      }

      const data = await resp.json();
      status.textContent = "Done.";
      renderResult(data);
    } catch (e) {
      status.textContent = "Could not reach the backend. It may be waking up from sleep — try again in a few seconds.";
    } finally {
      btn.disabled = false;
    }
  });
})();
</script>

---

## 📧 Support Email Agent {#support-email-agent}
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

## 🗒️ Meeting Intelligence & Action-Item Agent {#meeting-intelligence-agent}
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
    
<div id="meeting-demo" style="border:1px solid #444; border-radius:10px; padding:1.25rem; margin:1.5rem 0; background:rgba(255,255,255,0.03);">
  <p style="margin-top:0; font-weight:600;">🎬 Try it live</p>
  <p style="font-size:0.9rem; opacity:0.85;">
    Paste a meeting snippet (or use the example below) and this calls the
    actual deployed FastAPI backend — not a canned response.
  </p>
  <textarea id="meeting-demo-input" rows="6" style="width:100%; box-sizing:border-box; font-family:inherit; padding:0.6rem; border-radius:6px; border:1px solid #555; background:rgba(0,0,0,0.2); color:inherit;">Sarah: We agreed to push the launch to October 15th. Sarah will update the marketing timeline by Friday. Mike, can you finalize the pricing page copy? Mike: Sure, I will have it done by next Wednesday. We still need to decide on the referral program structure.</textarea>
  <div style="margin-top:0.75rem;">
    <button id="meeting-demo-btn" style="padding:0.5rem 1.1rem; border-radius:6px; border:none; background:#4f7cff; color:white; cursor:pointer; font-weight:600;">
      Run Live Demo
    </button>
    <span id="meeting-demo-status" style="margin-left:0.75rem; font-size:0.85rem; opacity:0.8;"></span>
  </div>
  <div id="meeting-demo-result" style="margin-top:1rem;"></div>
</div>

<script>
(function () {
  // Update this once your Railway deployment is live, e.g.:
  // "https://meeting-intelligence-agent-production.up.railway.app"
  const DEMO_API_URL = "https://meeting-intelligence-agentdemo-production.up.railway.app";

  const btn = document.getElementById("meeting-demo-btn");
  const input = document.getElementById("meeting-demo-input");
  const status = document.getElementById("meeting-demo-status");
  const resultBox = document.getElementById("meeting-demo-result");

  function escapeHtml(str) {
    const div = document.createElement("div");
    div.textContent = str;
    return div.innerHTML;
  }

  function renderResult(data) {
    const decisions = data.decisions.length
      ? "<ul>" + data.decisions.map(d => `<li>${escapeHtml(d)}</li>`).join("") + "</ul>"
      : "<p style='opacity:0.7;'>None recorded</p>";

    const actionItems = data.action_items.length
      ? "<ul>" + data.action_items.map(ai =>
          `<li><strong>${escapeHtml(ai.task)}</strong> — <em>${escapeHtml(ai.owner)}</em>` +
          (ai.due_date ? ` (due ${ai.due_date})` : "") + "</li>"
        ).join("") + "</ul>"
      : "<p style='opacity:0.7;'>None identified</p>";

    const openQuestions = data.open_questions.length
      ? "<ul>" + data.open_questions.map(q => `<li>${escapeHtml(q)}</li>`).join("") + "</ul>"
      : "<p style='opacity:0.7;'>None</p>";

    resultBox.innerHTML = `
      <div style="border-top:1px solid #444; padding-top:0.75rem;">
        <p><strong>Summary</strong><br>${escapeHtml(data.summary)}</p>
        <p><strong>Decisions</strong></p>${decisions}
        <p><strong>Action Items</strong></p>${actionItems}
        <p><strong>Open Questions</strong></p>${openQuestions}
      </div>
    `;
  }

  btn.addEventListener("click", async function () {
    const transcript = input.value.trim();
    if (!transcript) {
      status.textContent = "Paste a transcript first.";
      return;
    }

    btn.disabled = true;
    status.textContent = "Calling the live backend…";
    resultBox.innerHTML = "";

    try {
      const resp = await fetch(DEMO_API_URL + "/demo/extract", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ transcript: transcript }),
      });

      if (resp.status === 429) {
        status.textContent = "Demo rate limit reached — try again in a bit.";
        return;
      }
      if (!resp.ok) {
        const err = await resp.json().catch(() => ({}));
        status.textContent = "Error: " + (err.detail || resp.statusText);
        return;
      }

      const data = await resp.json();
      status.textContent = "Done.";
      renderResult(data);
    } catch (e) {
      status.textContent = "Could not reach the backend. It may be waking up from sleep — try again in a few seconds.";
    } finally {
      btn.disabled = false;
    }
  });
})();
</script>

---

## 📊 Medicare Data Pipeline: Raw Data to Production Dashboard {#medicare-data-pipeline}
**Type:** Data Engineering / Cloud Data Pipeline
**Stack:** Google Cloud Storage, BigQuery, dbt, Power BI
**Code:** [View on GitHub](https://github.com/mhallingquest/data-analytics/tree/main/medicare-data-pipeline)

A real, working pipeline built on public CMS Medicare provider/payment
data — de-identified, public-domain data, no PII — carried end to end from
raw CSV to a live production dashboard. Built specifically to demonstrate
cloud-native data warehousing and transformation (BigQuery + dbt), not just
a script that reads a CSV.

**Highlights**
- Raw CMS provider/service data (millions of rows) landed in Cloud Storage, loaded to BigQuery, modeled with dbt
- Staging layer normalizes and types raw fields; production marts aggregate to provider- and procedure-level summaries
- dbt-defined tests (not_null, unique) enforce data quality on every run
- Power BI connects directly to the BigQuery production marts — no manual export/import step

### 🧩 Workflow Diagram

### 📊 Medicare Data Pipeline — Diagram

```mermaid
%%{init: {'flowchart': { 'htmlLabels': true, 'wrap': true, 'nodeSpacing': 60, 'rankSpacing': 80 }}}%%
flowchart LR
  subgraph Ingest [📥 Ingest]
    A1["(1) data.cms.gov<br/>raw CSV download"]
    A2["(2) Cloud Storage<br/>raw landing zone"]
    A1 --> A2
  end

  A2 --> B1["(3) BigQuery<br/>bq load raw table"]

  subgraph Transform [🧱 dbt Transform]
    B1 --> C1["(4) Staging model<br/>clean, rename, type-cast"]
    C1 --> D1["(5) Mart: provider summary<br/>aggregated by NPI"]
    C1 --> D2["(6) Mart: procedure summary<br/>aggregated by HCPCS + state"]
    D1 --> E1["(7) dbt test<br/>not_null / unique checks"]
    D2 --> E1
  end

  subgraph Serve [📈 Serve]
    E1 --> F1["(8) Power BI<br/>connects to BigQuery marts"]
    F1 --> F2["(9) Publish to web<br/>live embedded dashboard"]
  end

```

**Workflow Steps**

1. **Source:** Download the CMS "Medicare Physician & Other Practitioners - by Provider and Service" CSV from data.cms.gov
2. **Land:** Upload the raw file to a Cloud Storage bucket — the raw, untouched landing zone
3. **Load:** `bq load` the CSV into a raw BigQuery table, no transformation yet
4. **Stage:** dbt staging model cleans column names, casts types, filters obvious nulls — no business logic
5. **Model (provider):** dbt mart aggregates to one row per provider (NPI) — total services, beneficiaries, Medicare payments
6. **Model (procedure):** dbt mart aggregates to one row per (procedure code, state) — cost and volume by geography
7. **Test:** dbt's built-in test framework enforces not_null/unique constraints on key columns before the marts are trusted downstream
8. **Connect:** Power BI connects directly to the BigQuery production marts — no CSV export step in between
9. **Publish:** Power BI's "Publish to web" generates a live, filterable embed for the portfolio site

<!-- Live Demo: paste your Power BI "Publish to web" iframe embed code here.
     It looks like:
     <iframe title="Medicare Dashboard" width="100%" height="600"
       src="https://app.powerbi.com/view?r=YOUR_EMBED_TOKEN"
       frameborder="0" allowFullScreen="true"></iframe>

     Example wrapper matching the other project sections' styling: -->

<div id="medicare-demo" style="border:1px solid #444; border-radius:10px; padding:1.25rem; margin:1.5rem 0; background:rgba(255,255,255,0.03);">
  <p style="margin-top:0; font-weight:600;">🎬 Try it live</p>
  <p style="font-size:0.9rem; opacity:0.85;">
    This is the actual production Power BI dashboard, built on the pipeline
    above — filter and explore it directly.
  </p>
  <div style="position:relative; padding-bottom:56.25%; height:0; overflow:hidden; border-radius:6px; border:1px solid #555;">
    <iframe
      title="Medicare Provider & Procedure Dashboard"
      style="position:absolute; top:0; left:0; width:100%; height:100%;"
      src="https://app.powerbi.com/view?r=eyJrIjoiZDA1ZTkyMmYtZDNiMS00YmYyLTg4OGQtOTU5MTlkNGVhOTE4IiwidCI6IjQyNmVjMmY0LTM5YTgtNGE2ZS1iZmI5LTRlMDE5OGJkYTg2NyIsImMiOjF9"
      frameborder="0"
      allowFullScreen="true">
    </iframe>
  </div>
</div>
