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
