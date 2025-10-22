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

<details> ### 🧩 Workflow Diagram

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

</details> ```

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
