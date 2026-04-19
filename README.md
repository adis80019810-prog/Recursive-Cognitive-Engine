# 🧠 Recursive Cognitive Engine (RCE)

> **Turn messy documents into a clean, organized second brain—automatically.**

Drop a PDF, Word doc, EPUB, or text file into a folder. AI reads it, finds the key ideas, checks for duplicates, and writes beautiful linked notes into your Obsidian vault. You review and approve before anything goes live. A weekly janitor cleans up the mess. **Everything is self‑healing.**

---

## 🤔 What Problem Does This Solve?

| The Old Way | With RCE |
| :--- | :--- |
| Spend hours reading and taking notes. | Drop the file. Walk away. Notes are waiting. |
| Worry the AI will hallucinate or corrupt files. | Human review gate + atomic writes prevent data loss. |
| End up with duplicate or conflicting notes. | Fuzzy deduplication checks your entire vault first. |
| Manually clean up temporary files and old drafts. | A weekly janitor prunes and recovers automatically. |

---

## 🎬 How It Works (In Plain English)

1. **You drop a file** (PDF, DOCX, EPUB, HTML, TXT, MD) into `00_Inbox`.
2. **RCE locks the file** so nothing else touches it while processing.
3. **The file type is verified** by reading its actual bytes—not just trusting the extension.
4. **The AI (Claude) reads the entire document** and extracts core ideas.
5. **RCE scans your existing notes** to avoid creating duplicates.
6. **A Council of Advisors debates the quality** of the AI's summary (see below).
7. **The proposed notes are staged** for your review.
8. **You approve or reject** via webhook (optional; can auto‑approve).
9. **Approved notes are written safely** using atomic operations (no partial writes).
10. **The original file is archived** with sidecar metadata for full auditability.
11. **Every Sunday at 3 AM**, a janitor cleans up stale files, frees orphaned locks, and rotates logs.

---

## 🏛️ The Council of Advisors (Unique Feature)

Before notes are finalized, a **council of four distinct advisors** debates the AI's summary. Each brings a specific worldview:

| Advisor | Perspective | What They Challenge |
| :--- | :--- | :--- |
| **Kautilya (Chanakya)** | Ancient strategist, *Arthashastra* | Power dynamics, pragmatism, realpolitik |
| **Richard Feynman** | Nobel physicist and educator | Technical clarity and explanatory depth |
| **Jeff Bezos** | Founder of Amazon | Scalability, ROI, and build‑worthiness |
| **Daniel Kahneman** | Nobel economist, *Thinking, Fast and Slow* | Cognitive biases and blind spots |

**How the debate works:**
1. **Critic Pass:** Claude critiques the original summary as a devil's advocate.
2. **Synthesis Pass:** Claude synthesizes the original summary with the critique into a balanced assessment.
3. **Permanent Record:** The full debate is saved to `02_Council_Debates/`.

This forces the AI to question itself from multiple worldviews *before* you ever see the output.

---

## 🔧 Technical Features (For Engineers)

This is not a simple "AI wrapper." It's a production‑grade automation with defense‑in‑depth.

**Reliability & Safety**
- POSIX atomic file locking (`O_EXCL`) – prevents race conditions
- Stale lock detection and auto‑recovery (30‑minute TTL)
- Atomic writes via temp file + rename – no partial writes
- Self‑healing error recovery – dedicated workflow releases locks and quarantines failed files
- Idempotent processing – `processed_index.json` prevents duplicate runs

**Security & Validation**
- Magic‑byte MIME validation – verifies PDF/DOCX/EPUB, not just extensions
- Path traversal hardening – `validateVaultPath()` blocks directory escapes
- Unsupported file quarantine – moves unknown files to `_unsupported/`

**AI & Content Quality**
- Byte‑level token truncation with binary search – prevents API payload errors
- SHA‑256 content hashing – skips exact duplicate writes
- Fuzzy semantic deduplication – 75% word‑overlap threshold
- Two‑pass Council critique – critic → synthesis
- Persona‑based adversarial review (Kautilya, Feynman, Bezos, Kahneman)

**Observability & Maintenance**
- Structured JSON logging (`structured.log`) – machine‑readable audit trail
- Sidecar metadata for archived sources (`.meta.json`)
- Weekly janitor: prunes stale staging files, failed files, orphaned locks, abandoned `.processing` files
- Log rotation at 10 MB
- Health checks on vault directories
- Environment variable validation on startup

**Supported Formats**
- PDF (native extraction)
- DOCX (extracts from `word/document.xml`)
- EPUB (extracts from `OEBPS/content.opf`)
- HTML (strips scripts/styles, decodes entities)
- Plain text / Markdown

---

## 📁 Vault Structure (What You Get)
Your Vault/
├── 00_Inbox/ ← Drop files here
│ ├── _failed/ ← Files that errored out
│ ├── _rejected/ ← Batches you rejected
│ ├── _recovered/ ← Abandoned .processing files rescued by Janitor
│ └── _unsupported/ ← Unsupported file types
├── 01_Wiki_Nodes/ ← AI‑generated atomic notes
├── 02_Council_Debates/ ← Permanent records of advisor debates
├── 03_Lesson_Cemetery/ ← Refuted claims and ideas
├── 04_Meta/
│ ├── _staging/ ← Pending review previews
│ ├── completed/ ← Completion summaries
│ ├── errors/ ← Human‑readable error notes
│ ├── .snapshot_cache.json
│ ├── processed_index.json
│ ├── processing_log.jsonl
│ ├── structured.log ← JSON Lines audit log
│ └── error_log.jsonl
├── 05_Sources/ ← Archived original files + .meta.json sidecars
└── 06_Visuals/ ← Mermaid diagrams (if generated)
---

## 🧰 Workflow Architecture (Modular)

The system is split into **six independent n8n workflows** for resilience and maintainability.

| Workflow | File | Responsibility |
| :--- | :--- | :--- |
| **Error Handler** | `workflows/00-error-handler/` | Global catch‑all; releases locks, quarantines files, logs errors |
| **Ingestion** | `workflows/01-ingestion/` | File watching, atomic locking, MIME validation, text extraction |
| **Decompose** | `workflows/02-decompose/` | Vault snapshot, Claude API call, response parsing, staging preview, human review gate |
| **Write Vault** | `workflows/03-write-vault/` | Splits notes, atomic writes, versioning, fuzzy dedup, archival, lock release |
| **Council** | `workflows/04-council/` | Two‑pass critic + synthesis, Mermaid visuals, Lesson Cemetery, 24h experiments |
| **Janitor** | `workflows/janitor/` | Weekly health check, prunes stale files, frees orphaned locks, rotates logs |

> ⚠️ **Disclaimer:** This repository is a **reference architecture portfolio piece**. It is not a one‑click install tool. It demonstrates advanced patterns for building reliable, human‑in‑the‑loop AI systems using n8n.

---

## 🔑 Environment Variables Required

| Variable | Description |
| :--- | :--- |
| `OBSIDIAN_VAULT_PATH` | Absolute path to your Obsidian vault root |
| `ANTHROPIC_API_KEY` | Claude API key (must start with `sk-`) |
| `CLAUDE_MODEL` | Model name (e.g., `claude-sonnet-4-6`, `claude-opus-4-7`) |
| `SOURCE_TEXT_MAX_CHARS` | Max characters to send to Claude (default: 60000) |
| `CLAUDE_MAX_TOKENS` | Max tokens for Claude response (default: 8192) |
| `CLAUDE_MAX_RETRIES` | Number of retries on rate limit (default: 4) |
| `SNAPSHOT_CACHE_MINUTES` | How long to cache vault snapshot (default: 30) |
| `MAX_NOTE_VERSIONS` | Number of old versions to keep (default: 5) |
| `HUMAN_REVIEW_ENABLED` | `true` / `false` – whether to wait for webhook approval |
| `COUNCIL_DEBATE_ENABLED` | `true` / `false` – whether to run the Council workflow |
| `JANITOR_WEBHOOK_URL` | (Optional) URL for completion and error alerts |

---

## 🙋‍♀️ Frequently Asked Questions

**Q: Do I need to know how to code to use this?**  
A: No. Once set up, you just drag and drop files. Setup requires basic familiarity with n8n and Obsidian.

**Q: What does it cost to run?**  
A: You pay for Claude API calls (approx. $0.10–$0.50 per book, depending on length). n8n can be run for free locally.

**Q: Can I trust the AI not to make things up?**  
A: The human review gate ensures you see everything before it's saved. The Council of Advisors also critiques the output before you even see it.

---

*This project is actively used in the author's personal knowledge management system. It represents hundreds of hours of refinement and real‑world testing.*
