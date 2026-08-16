# Apply Claude Code Skills to ARS Production Pipeline

**Status:** Open
**Created:** 2026-04-17
**Owner:** James Gilmore (CSI/Velocity)
**Related:** `plans/refactor-deposit-analysis-section-23.md`

---

## Summary

Install and integrate Anthropic + Composio Claude Code skills into the TXN-v4 ARS production pipeline. Three workstreams: (1) stakeholder deck template, (2) client xlsx deliverable template, (3) custom skill encoding ODDD/ZAccounts conventions.

## Background

Skills now installed globally at `~/.claude/skills/`:

- **Anthropic suite:** `pptx`, `xlsx`, `pdf`, `docx`, `theme-factory`, `doc-coauthoring`, `internal-comms`, `skill-creator`, `canvas-design`, `algorithmic-art`, `slack-gif-creator`, `mcp-builder`, `webapp-testing`, `web-artifacts-builder`, `brand-guidelines`, `frontend-design`, `claude-api`
- **Composio override:** `pptx` (replaced with more detailed version — includes `html2pptx.md`, `ooxml` helpers)
- **Pre-existing relevant:** `executive-data-presentation`, `data-inventory`

This issue narrows the surface area to the skills that compound with the existing TXN-v4 deposit analysis work.

---

## Workstream 1 — Stakeholder Deck Template (Section 23/24)

**Goal:** Reproducible pptx template for Section 23 (Personal deposits) and Section 24 (Business deposits) quarterly/monthly stakeholder decks.

**Skills used:** `pptx` + `executive-data-presentation` + `theme-factory`

**Tasks:**
- [ ] Draft outline (one deck covers both sections, or two parallel decks — decide)
- [ ] Select theme via `theme-factory` — pick palette that matches CSI/Velocity brand (avoid default blue)
- [ ] Per section: funnel narrative slide (all accts → depositors → ARS responder behavior — matches `project_deposit_funnel_narrative.md`)
- [ ] One chart per slide, no per-slide multi-chart violations (matches `feedback_one_chart_per_cell.md`)
- [ ] KPI stat-callout slides (big-number style) for each section
- [ ] No cross-comparison slides between Personal and Business (matches `feedback_no_comparison.md`)
- [ ] Template stored at `plans/templates/section-23-24-deck.pptx` with a `make-deck.py` script that regenerates it from current dataframes
- [ ] QA per `pptx` skill: extract text with markitdown, render slides as images, visual subagent inspection

**Acceptance criteria:**
- Running the generator against the current `deposits_personal_df` / `deposits_business_df` produces a stakeholder-ready deck with zero placeholder text
- Visual QA pass finds no overlapping elements, text overflow, or low-contrast issues
- Funnel narrative slide matches the `oddd` mailer campaign bracketing approach (per `reference_deposit_snapshot_mapping.md`)

---

## Workstream 2 — Client xlsx Deliverable Template

**Goal:** Reproducible xlsx template that exports Section 23/24 analysis as a polished, formula-driven deliverable.

**Skills used:** `xlsx`

**Tasks:**
- [ ] Define sheet structure (suggested: `Summary`, `Personal Accounts`, `Business Accounts`, `ARS Responder Behavior`, `Methodology`)
- [ ] Use openpyxl for formula-driven cells (calculated KPIs should be formulas, not static values)
- [ ] Formatting: consistent Arial/Calibri, conditional formatting on rate columns, frozen panes on data tabs
- [ ] Run `scripts/recalc.py` (LibreOffice) after generation so opened files show values immediately
- [ ] Clear separation: ODDD MTD goes on its own tab and is labeled "Overdraft Items, NOT Deposits" (per `feedback_mtd_not_deposits.md`)
- [ ] RDI columns (RDI33/RDIAmt33) labeled "Return Deposit Items — bounced checks, returned ACH" (per `reference_rdi_return_deposit_item.md`)
- [ ] Template stored at `plans/templates/section-23-24-deliverable.xlsx` with generator script

**Acceptance criteria:**
- Output opens cleanly in Excel without warnings
- All formulas recalculate correctly (no `#REF!`, `#NAME?`, etc.)
- Labels leave no ambiguity about ODDD MTD vs deposits
- Sample run against current data produces a deliverable ready for client distribution

---

## Workstream 3 — Custom `txn-v4-conventions` Skill

**Goal:** Encode ODDD / ZAccounts / ZTrends conventions into a Claude Code skill so future analysis sessions auto-apply them without re-explaining.

**Skills used:** `skill-creator` (builds it)

**Tasks:**
- [ ] Scaffold new skill at `~/.claude/skills/txn-v4-conventions/SKILL.md`
- [ ] Encode non-negotiable rules (all from existing memory):
  - Join: ODDD `Acct Number` ↔ ZAccounts `HASHED_ACCOUNT`
  - Personal vs Business: use `business_flag` split from cells 08-09
  - ODDD MTD ≠ deposits (deposits ONLY in ZAccounts/ZTrends)
  - RDI columns are return/bounced items, not deposits
  - One chart per notebook cell
  - No comparison sections between Personal and Business
  - Ask before guessing data column conventions
- [ ] Reference the ODDD paired Mail/Resp column convention (per `reference_oddd_mailer_columns.md`)
- [ ] Reference the deposit snapshot bracketing approach (per `reference_deposit_snapshot_mapping.md`)
- [ ] Test trigger description — should activate on mentions of ODDD, ZAccounts, ZTrends, deposit analysis, ARS responder
- [ ] Validate with a dry run in a fresh session

**Acceptance criteria:**
- Starting a new session in `/Users/jgmbp/Projects/TXN-v4-complete` and mentioning "analyze ODDD" surfaces the skill
- Skill correctly blocks the common mistake of treating ODDD MTD as deposit volume
- Skill's conventions file survives updates to individual memory files (single source of truth for pipeline rules)

---

## Dependencies

- LibreOffice (`soffice`) — required by both `pptx` and `xlsx` recalc scripts
- `pip install "markitdown[pptx]" Pillow openpyxl`
- `npm install -g pptxgenjs` (if using pptxgenjs path instead of python-pptx)
- Poppler (`pdftoppm`) — for pptx visual QA

## Open Questions

1. Should Workstream 1 produce one combined deck or two parallel decks (Section 23 / Section 24)?
2. Does CSI/Velocity have brand colors and fonts to encode in the `theme-factory` preset?
3. Are the xlsx deliverables delivered to internal stakeholders only, or to external credit union clients (affects polish bar)?

---

## References

- `plans/refactor-deposit-analysis-section-23.md` — the underlying refactor this tooling supports
- `~/.claude/projects/-Users-jgmbp/memory/` — all referenced memory files
- `~/.claude/skills/pptx/SKILL.md` — Composio version, primary reference
- `~/.claude/skills/xlsx/SKILL.md` — Anthropic version
- `~/.claude/skills/skill-creator/SKILL.md` — for Workstream 3
