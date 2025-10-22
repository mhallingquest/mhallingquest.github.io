###⚙️ Contract Processing Bot
<p>Automates contract analysis and routing using Zapier and OpenAI.</p>
<details> <summary><strong>Advanced Contract Processing Workflow (Zapier + OpenAI)</strong></summary>
flowchart LR
  %% Intake
  A1["(1) Gmail Trigger: new attachment containing 'contract'"]
  A2["(2) Slack: immediate intake notification"]
  A3{"(3) Filter: file is a contract?"}
  A1 --> A2 --> A3

  %% Storage
  A3 -- Yes --> A4["(4) Save to Google Drive → /Contracts/Incoming"]
  A3 -- No  --> R1["Stop + Log skip in Sheets"] --> H1[End]

  %% AI extraction + validation
  B1["(5) OpenAI: extract JSON (parties, dates, amounts, renewal, risks)"]
  B2{"Schema-valid JSON?"}
  B1 --> B2
  B2 -- No --> B1R["Retry with stricter instructions + examples"]
  B1R --> B2
  B2 -- Yes --> C1["(6) Code by Zapier: parse JSON → typed fields"]

  %% Logging
  A4 --> B1
  C1 --> D1["(7) Google Sheets: append run log (inputs, outputs, file link)"]

  %% Routing
  D1 --> D2{"(8) Risks detected or missing clauses?"}
  D2 -- Yes --> E1["Slack: human review thread with summary + Drive link"]
  D2 -- No  --> F1["Move file → /Contracts/Approved"]
  F1 --> F2["(9) Gmail: confirmation email to requester"]

  %% Terminate
  E1 --> H1
  F2 --> H1
</details>
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
