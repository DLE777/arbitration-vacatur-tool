# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A single-page Streamlit app that drafts a Motion to Vacate Arbitration Award under the Federal Arbitration Act (9 U.S.C. § 10). Users select applicable grounds, enter the award date, and download a pre-populated `.docx` brief.

## Running the App

```bash
pip install -r requirements.txt
streamlit run app.py
```

## Architecture

Everything lives in `app.py`, organized into four logical sections (marked by numbered comments):

1. **Date logic** — `add_months()` correctly handles month-boundary edge cases when computing the 3-month filing deadline from the award date. Do not replace with `timedelta`.
2. **Knowledge base** — `KNOWLEDGE_BASE` dict keyed by FAA section codes (`10a1`–`10a4`). Each entry holds the argument header, controlling case citation, and standard of proof. Add new grounds here.
3. **Document generator** — `generate_doc()` builds the `.docx` in memory via `BytesIO` using `python-docx`. The document is never written to disk; it is returned as a buffer for `st.download_button`.
4. **Streamlit UI** — `main()` wires together the date picker, ground checkboxes, live preview, and download button.

## Key Conventions

- The four vacatur grounds map to FAA codes `10a1`–`10a4`. New grounds must follow the same key format and dict schema (`title`, `section`, `argument_header`, `case_cite`, `standard`).
- Filing deadline is always award date + 3 months, computed by `add_months()` — not `timedelta` — to handle month-length variation correctly.
- The download button and draft preview are conditionally rendered only when at least one ground is selected.
- The app is fully stateless between runs — no database, no server-side persistence.
