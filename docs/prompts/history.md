# Prompt History

This file tracks all refined prompts executed in this workspace, creating a knowledge base of successful patterns and lessons learned.

---

## Purpose

- **Audit Trail:** Complete record of all Copilot interactions
- **Learning Resource:** Reference for effective prompt patterns
- **Quality Control:** Track refinement effectiveness and outcomes
- **Team Knowledge:** Share successful approaches across team members
- **Continuous Improvement:** Identify patterns that work and avoid those that don't

---

## How to Use This File

### For Copilot (Automated)
- Append new entry after executing each refined prompt
- Include all required fields (timestamp, model, prompts, outcome)
- Follow the template format exactly

### For Developers (Manual)
- Review past prompts before making similar requests
- Learn from successful refinements
- Identify reusable patterns
- Understand rationale behind design decisions
- Track project evolution over time

---

## Entry Template

```markdown
## [YYYY-MM-DD HH:MM:SS] — Model: [Model Name]

### Original Prompt
```
[User's exact request verbatim]
```

### Refined Prompt
```
[Copilot's detailed, refined version]
```

### Refinement Rationale
- Added: [what was added and why]
- Clarified: [ambiguities resolved]
- Scoped: [boundaries defined]
- Referenced: [docs/patterns consulted]

### Execution Result
- Files created:
  - [path/to/file1.ext]
  - [path/to/file2.ext]
- Files modified:
  - [path/to/file3.ext] (changes: [brief description])
- Status: ✓ Success / ✗ Failed / ⚠ Partial
- Errors: [error messages if any]
- Notes: [observations, lessons learned, improvements for future]

---
```

---

## Search & Filter Guide

To find specific types of prompts in this file:

- **By Date:** Search for `[2025-10-` to find October 2025 entries
- **By Model:** Search for `Model: GPT-5` to find GPT-5 interactions
- **By Status:** Search for `✓ Success` or `✗ Failed` to review outcomes
- **By Topic:** Search for keywords like "database", "API", "authentication", etc.
- **By File:** Search for specific paths like `services/api/` to see related changes

---

## Statistics & Insights

This section is manually updated monthly to track trends.

### Current Month: October 2025
- Total prompts refined: 0
- Successful executions: 0
- Failed executions: 0
- Partial completions: 0
- Most common refinements: (to be tracked)
- Average refinement additions: (to be tracked)

### Common Patterns Identified
(This section grows as history accumulates)

- Pattern 1: [Description]
- Pattern 2: [Description]

### Areas for Improvement
(Updated based on failures and partial completions)

- Improvement 1: [Description]
- Improvement 2: [Description]

---

## Archive Information

When this file exceeds 10,000 lines, archive entries older than 3 months:

- Archive location: `docs/prompts/archive/YYYY-MM-history.md`
- Keep index in `docs/prompts/archive/INDEX.md`
- Compress archived files with gzip

---

## History Entries

<!-- New entries are appended below this line in reverse chronological order (newest first) -->

---

<!-- Future entries will be added above this line -->
