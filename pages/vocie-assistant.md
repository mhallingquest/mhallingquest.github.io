---
layout: single
title: "Voice Assistant for Ops — AI-Powered Operations Assistant"
permalink: /projects/voice-assistant/
author_profile: true
---

## 🎙️ Voice Assistant for Ops

An AI-powered voice assistant built for operations teams — enabling hands-free ticket creation, email drafting, and document querying through natural spoken commands.

---

## The Problem

Operations teams spend significant time on repetitive administrative tasks: logging tickets, drafting status emails, and hunting through documentation for answers. These interruptions break focus and slow down real work. This assistant eliminates that friction entirely.

---

## What It Does

- **Create tickets** — speak a description, the assistant logs it directly to your ticketing system
- **Draft emails** — dictate context, receive a polished draft ready to review and send
- **Query documents** — ask questions against internal docs and get precise answers instantly
- **All hands-free** — designed for warehouse, logistics, and operations environments where keyboards aren't practical

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Speech-to-Text | OpenAI Whisper |
| Language Model | OpenAI GPT (action + response generation) |
| Orchestration | Python |
| Integrations | Ticketing API, Gmail, Document Store |

---

## Workflow

```
Voice Input
    ↓
Whisper (transcription)
    ↓
Intent Classification (ticket / email / query)
    ↓
┌─────────────────────────────────────────────┐
│  Ticket       │  Email Draft  │  Doc Query  │
│  → API call   │  → GPT draft  │  → RAG      │
└─────────────────────────────────────────────┘
    ↓
Human-in-the-loop review → Execute
```

---

## Key Engineering Decisions

- **Human-in-the-loop by default** — all actions surface for confirmation before execution, preventing accidental ticket spam or misfired emails
- **Intent routing** — a lightweight classifier determines the action type before passing to the appropriate handler, keeping each module focused and testable
- **Whisper chosen over browser speech APIs** — better accuracy in noisy environments and no dependency on a specific browser or OS

---

## Results

- Eliminated manual ticket entry for a 6-person ops team
- Reduced email drafting time by ~70% for recurring status updates
- Enabled instant document lookup without breaking workflow context

---

[← Back to Projects](/projects/){: .btn .btn--inverse}
[View All Projects](/projects/){: .btn .btn--primary}
