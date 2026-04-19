# Recursive Cognitive Engine (RCE)

> A production‑grade n8n workflow that transforms your Obsidian vault into a self‑organising, AI‑curated second brain.

RCE watches an `00_Inbox` folder. When you drop in a PDF, Markdown, or text file, it:

- Extracts the text and truncates it safely for Claude's context window.
- Takes a snapshot of your vault to understand existing notes.
- Calls Claude to decompose the source into **8–12 interconnected Obsidian notes**.
- Creates a **staging preview** for human review (optional).
- Writes notes into `01_Wiki_Nodes`, **merging** with existing notes when semantic overlap is detected.
- Runs a **Council Debate** (Feynman, Bezos, Kahneman, Aurelius) to critique the synthesis.
- Archives the source file to `05_Sources`.
- Performs a **weekly janitor scan** (file‑age pruning + TF‑IDF duplicate detection).

**Zero API cost for maintenance** – the janitor uses local TF‑IDF, not LLM calls.

## Features

- 🔒 **Hardened file operations** – path traversal blocked, atomic writes, file locking.
- 🧠 **Intelligent merging** – new notes are appended as "Update" sections to existing ones with ≥70% conceptual overlap.
- 👁️ **Human‑in‑the‑loop review** – pause execution, inspect a preview, and approve/reject via webhook.
- 📊 **Observability** – truncation log, processing log with token counts, error log.
- 🔁 **Exponential backoff + jitter** – survives API rate limits and transient failures.
- 💸 **Cost‑aware** – TF‑IDF semantic deduplication runs locally; no API calls for maintenance.

## Prerequisites

- [n8n](https://n8n.io/) (self‑hosted or cloud)
- [Obsidian](https://obsidian.md/) vault
- [Anthropic API key](https://console.anthropic.com/)

## Installation

1. **Clone this repository** and import the workflow JSON into n8n.
2. **Set the required environment variables** (see [`.env.example`](.env.example)).
3. **Create the Obsidian folder structure**:vault/
├── 00_Inbox/
├── 01_Wiki_Nodes/
├── 02_The_Council/
├── 03_Lesson_Cemetery/
├── 04_Meta/
└── 05_Sources/4. **Provide a master prompt** (see [`prompts/MASTER_ARCHITECT_PROMPT.example.txt`](prompts/MASTER_ARCHITECT_PROMPT.example.txt)).
5. **Activate the workflow** in n8n.

## Configuration

All behaviour is controlled via environment variables:

| Variable | Description | Default |
|----------|-------------|---------|
| `OBSIDIAN_VAULT_PATH` | Absolute path to your Obsidian vault | *Required* |
| `ANTHROPIC_API_KEY` | Your Anthropic API key | *Required* |
| `CLAUDE_MODEL` | Model to use (recommended: `claude-opus-4-7` for decompose, falls back to `claude-sonnet-4-6` for council) | `claude-opus-4-7` ||
| `MASTER_ARCHITECT_PROMPT_PATH` | Path to file containing the decomposition prompt | Falls back to inline minimal prompt |
| `HUMAN_REVIEW_ENABLED` | `true` to pause for manual approval | `false` |
| `SNAPSHOT_CACHE_MINUTES` | How long to cache vault snapshot | `5` |
| `SOURCE_TEXT_MAX_CHARS` | Soft truncation limit | `60000` |
| `MAX_NOTE_VERSIONS` | Number of versioned backups to keep | `5` |
| `JANITOR_STALE_DAYS` | Age (days) after which a draft note is considered stale | `90` |
| `SEMANTIC_SIM_THRESHOLD` | Cosine similarity threshold for duplicate detection | `0.72` |

## Workflow Architecture
Inbox Trigger → Lock → Validate Type → Read/Extract → Vault Snapshot → Token Guard
↓
Claude Decompose (with retry)
↓
Parse & Validate JSON
↓
Write Staging Preview
↓
Review Gate (optional wait for webhook)
↓
Split Notes → Write Note (with merge intelligence)
↓ ↓
Council Debate (if summary exists) Archive Source
↓ ↓
Write Council Note Completion Notifier
A **weekly schedule** triggers the Janitor: scans for stale/orphan notes and runs local TF‑IDF duplicate detection.

## The Council of Advisors

RCE includes a unique "Council Debate" feature where four distinct personas critique the synthesis:

| Persona | Role |
|---------|------|
| **Richard Feynman** | Clarity enforcer, first‑principles auditor |
| **Jeff Bezos** | Scalability judge, working‑backwards strategist |
| **Daniel Kahneman** | Cognitive bias detector, probability calibrator |
| **Marcus Aurelius** | Virtue tester, resilience stress‑tester |

Full persona definitions are available in [`council_personas/`](council_personas/). You can copy these into your Obsidian vault under `02_The_Council/` to integrate them with the generated Council notes.
## Security

- All file writes are **contained within the vault root**; path traversal attempts are blocked.
- Prompt injection vectors are mitigated by placing untrusted content in the **system** role and sanitising control characters.
- API keys are never hardcoded; they are read from environment variables.

## Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md). Bug reports and improvements are welcome.

## License

MIT – see [`LICENSE`](LICENSE).

## Acknowledgements

Built with [n8n](https://n8n.io/) and [Anthropic Claude](https://anthropic.com/).
