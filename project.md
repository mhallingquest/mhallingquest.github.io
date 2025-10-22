<details>
<summary><strong>Advanced Contract Processing Workflow (Zapier + OpenAI)</strong></summary>
```mermaid
flowchart LR
  %% === Intake ===
  subgraph Intake [📥 Intake]
    A1["(1) Gmail Trigger: new attachment containing 'contract'"]
    A2["(2) Slack: immediate intake notification"]
    A3{"(3) Filter: file is a contract?"}
    A1 --> A2 --> A3
  end

  %% === Storage ===
  A3 -- Yes --> A4["(4) Save to Google Drive → /Contracts/Incoming"]
  A3 -- No  --> R1["Stop + Log skip in Sheets"] --> H1[End]

  %% === AI Extraction + Validation ===
  subgraph AI_Validation [🧠 AI Extraction + Validation]
    B1["(5) OpenAI: extract JSON summary"]
    B2{"Schema-valid JSON?"}
    B1 --> B2
    B2 -- No --> B1R["Retry with stricter prompt"]
    B2 -- Yes --> C1["(6) Code by Zapier: parse JSON → typed fields"]
  end

  A4 --> B1
  C1 --> D1["(7) Google Sheets: log outputs"]

  %% === Routing ===
  subgraph Routing [📊 Routing & Actions]
    D1 --> D2{"(8) Risks detected?"}
    D2 -- Yes --> E1["Slack: review thread + file link"]
    D2 -- No  --> F1["Move to /Contracts/Approved"]
    F1 --> F2["(9) Gmail: confirmation email"]
  end

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
