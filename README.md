# 🧠 Recursive Cognitive Engine (RCE)

> **Turn messy documents into a clean, organized second brain—automatically.**

Imagine you have a pile of books, PDFs, and articles you want to learn from. Normally, you'd spend hours reading, highlighting, and typing up notes. **RCE does that work for you while you sleep.**

You drop a file into a folder. The AI reads it, figures out the key ideas, and writes beautiful, linked notes directly into your Obsidian knowledge base. You just review and approve. That's it.

---

## 🤔 What Problem Does This Solve?

| The Old Way | With RCE |
| :--- | :--- |
| Read a 300‑page book and spend 6 hours making notes. | Drop the PDF in a folder. Go have dinner. Notes are waiting for you. |
| Worry that the AI will make up facts or break your files. | RCE has **safety checks** built in. Nothing gets written without your approval. |
| End up with a messy folder full of duplicate notes. | RCE **checks your existing notes** and only adds new, relevant information. |
| Accidentally delete something important. | RCE keeps **automatic backups** of every version. |

---

## 🎬 How It Works (In Plain English)

1. **You drop a file** (PDF, Word document, text file) into a special `00_Inbox` folder.
2. **RCE locks the file** so it doesn't get messed up while being read.
3. **The AI reads the entire document** and understands what's important.
4. **RCE looks at your existing notes** to avoid creating duplicates.
5. **The AI proposes 8‑12 new notes** and puts them in a **"Pending Review"** area.
6. **You get a notification** (optional) and can approve or reject the batch.
7. **Approved notes are written safely** to your Obsidian vault.
8. **The original file is archived** so you never lose the source material.

Every Sunday night, a **digital janitor** wakes up, cleans out temporary files, and makes sure everything is running smoothly.

---

## 🛡️ Why This Is Different From "Just Using ChatGPT"

Most AI automations are fragile. If the internet cuts out mid‑write, your file gets corrupted. If the AI returns gibberish, it still gets saved.

**RCE was built with seatbelts and airbags:**

- ✅ **Human Review Gate:** You see exactly what the AI wants to write *before* it touches your vault.
- ✅ **Self‑Healing:** If something goes wrong (disk full, power outage), RCE cleans up after itself and alerts you.
- ✅ **Atomic Writes:** Files are written in a way that prevents corruption—even if your computer crashes mid‑save.
- ✅ **Version History:** Every time a note is updated, the old version is saved. You can always go back.

---

## 📁 The Vault Structure (What You Get)

After processing, your Obsidian vault will look like this:
Your Vault/
├── 00_Inbox/ ← Drop files here
├── 01_Wiki_Nodes/ ← Your beautiful, AI‑generated notes
├── 02_Council_Debates/← AI debates the quality of the ideas
├── 03_Lesson_Cemetery/← Ideas that were refuted or disproven
├── 04_Meta/ ← Logs and staging previews
├── 05_Sources/ ← Archived original documents
└── 06_Visuals/ ← Mermaid diagrams of complex concepts
---

## 🧰 What's Inside This Repository?

This repository contains **six modular n8n workflows** that work together as a complete system.

| Workflow | What It Does |
| :--- | :--- |
| **Ingestion** | Watches the inbox, reads files, and extracts text. |
| **Decompose** | Sends text to Claude AI and asks for notes. |
| **Write Vault** | Safely writes the approved notes to your Obsidian vault. |
| **Council** | Runs a second AI pass to critique and refine the ideas. |
| **Error Handler** | Catches any problems and prevents file corruption. |
| **Janitor** | Weekly maintenance—cleans up old files and logs. |

> ⚠️ **Disclaimer:** This repository is a **portfolio reference architecture**. It's not a one‑click install tool. It demonstrates how to build reliable, human‑in‑the‑loop AI systems using n8n.

---

## 👤 Who Is This For?

- **Knowledge workers** who want to spend less time organizing and more time thinking.
- **Students and researchers** drowning in PDFs and articles.
- **Business professionals** who need to synthesize information from multiple sources.
- **AI Automation Engineers** looking for a production‑grade reference implementation.

---

## 🙋‍♀️ Frequently Asked Questions

**Q: Do I need to know how to code to use this?**  
A: No. Once set up, you just drag and drop files. Setup requires basic familiarity with n8n and Obsidian.

**Q: What does it cost to run?**  
A: You pay for the Claude API calls (approximately $0.10–$0.50 per book, depending on length). n8n can be run for free on your own computer.

**Q: Can I trust the AI not to make things up?**  
A: The human review gate ensures you see everything before it's saved. You can edit, reject, or approve each batch.

---

*This project is actively used in the author's personal knowledge management system. It represents hundreds of hours of refinement and real‑world testing.*
