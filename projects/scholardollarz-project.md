---
layout: single
title: "ScholarDollarz — AI Scholarship Intelligence Platform"
permalink: /projects/scholardollarz/
author_profile: true
---

## 🎓 ScholarDollarz

**Live:** [scholardollarz.com](https://scholardollarz.com)

ScholarDollarz is an AI-powered scholarship discovery platform built to help students — especially those from underserved communities — find scholarships they actually qualify for. The platform eliminates the noise of generic scholarship searches by combining intelligent filtering, deadline tracking, and AI-verified source links.

---

## The Problem

Most scholarship databases are outdated, inaccurate, and overwhelming. Students waste hours chasing dead links or scholarships they're ineligible for. ScholarDollarz was built to change that.

---

## What It Does

- **4,600+ scholarships** indexed, researched, and filterable by state, eligibility, deadline, and award amount
- **AI verification pipeline** — NVIDIA NIM LLM validates scholarship URLs, deadlines, and eligibility details
- **Smart filtering** — state-aware boolean columns for precise geographic filtering without false exclusions
- **Admin panel** — hidden curator interface for managing scholarship data directly in MongoDB Atlas
- **Deadline intelligence** — upcoming deadlines surfaced automatically, expired scholarships suppressed

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | FastAPI (Python) |
| Frontend | Streamlit |
| Database | MongoDB Atlas |
| AI / Verification | NVIDIA NIM (`meta/llama-3.3-70b-instruct`) |
| Search | SearXNG (self-hosted) |
| Hosting | Railway |

---

## Engineering Highlights

- Built a **bulk scholarship research tool** using SearXNG feeding into NVIDIA's LLM for structured data extraction across 4,700+ scholarships
- Implemented a **three-tier URL confidence system** (HIGH / LOW / DEAD) to surface link quality without removing unverified entries
- Solved a critical **state filter bug** using pre-computed per-state boolean columns and a reverse-exclusion approach — eliminating false negatives that blocked legitimate matches
- Migrated from CSV to **MongoDB Atlas** as single source of truth, enabling real-time admin edits without redeployment
- Added **GitHub Actions keep-alive workflow** to prevent Render free-tier spin-down

---

## Impact

ScholarDollarz serves students across all 50 states, with a focus on making scholarship opportunity searchable, accurate, and accessible — regardless of a student's starting point.

---

[← Back to Projects](/projects/){: .btn .btn--inverse}
[Visit ScholarDollarz](https://scholardollarz.com){: .btn .btn--primary}
