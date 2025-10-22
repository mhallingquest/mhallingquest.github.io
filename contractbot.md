### Contract Processing Bot
**Type:** Document Intelligence  
**Stack:** Zapier, OpenAI, Google Drive, Gmail  

This Zap automatically analyzes incoming contracts from Gmail attachments, extracts key data (parties, dates, amounts, renewal terms), checks for missing or risky clauses using OpenAI, and routes them for review or auto-approval.

**Workflow Overview:**
1. Trigger: Gmail — new attachment containing “contract”
2. Send immediate Slack notifications
3. Filter to only process contract files
4. Upload to Google Drive → /Contracts/Incoming
5. OpenAI step extracts JSON summary
6. Code by Zapier parses key fields
7. Log everything to Google Sheets
8. Filter routes risky contracts to Slack for human review
9. Send email confirmations for auto-approved contracts

<a class="btn" href="https://github.com/mhallingquest/contract-processing-bot" target="_blank">View Zap Setup Notes</a>

<details><summary><strong>Advanced Contract Processing Workflow (Zapier + OpenAI)</strong></summary>

```mermaid
flowchart TD
  %% Direction
  %% TD = top-down; change to LR for horizontal layout
  %% Numbers in labels map to the step list below.

  subgraph Intake
    A1["(1) Gmail Trigger: new attachment matching “contract”"]
    A2["(2) Slack: immediate intake notification"]
    A3{"(3) Filter: file is a contract?"}
    A1 --> A2 --> A3
  end

  A3 -- Yes --> A4["(4) Save to Google Drive → /Contracts/Incoming"]
  A3 -- No  --> R1["Stop + Log skip in Sheets"] --> H1[End]

  %% AI Extraction / Validation loop
  subgraph AI_Extraction_and_Validation
    direction TB
    B1["(5) OpenAI: extract JSON summary (parties, dates, amounts, renewal, risks)"]
    B2{"Schema-valid JSON with required fields?"}
    B1 --> B2
    B2 -- No --> B1R["Retry: stricter system prompt + few-shot + (optional) OCR fallback"]
    B1R --> B2
    B2 -- Yes --> C1["(6) Code by Zapier: parse JSON → typed fields"]
  end

  A4 --> B1
  C1 --> D1["(7) Google Sheets: append full run log (inputs, outputs, file link)"]

  %% Routing
  subgraph Routing_and_Actions
    D2{"(8) Risks detected or missing clauses?"}
    D2 -- Yes --> E1["Slack: human review thread with summary, risks, Drive link"]
    D2 -- No  --> F1["Move file → /Contracts/Approved"]
    F1 --> F2["(9) Gmail: send confirmation to requester (auto-approved)"]
  end

  D1 --> D2
  E1 --> H1
  F2 --> H1

  %% Optional observability / guardrails (annotations)
  click B1R "https://mermaid.live" "Edit diagram online (optional)"

</details> ```
