# Hermes Post-Mortem Resource Inventory Prompt

Use this prompt when Hermes has just failed a task, used a paid or metered API unexpectedly, or is still in setup mode and needs to learn which free/local tools are available.

```text
Run a post-mortem resource inventory grounded in the actual failures from last night. Do not start with an abstract tool list; start with what broke.

Do not call any paid or metered external API during this inventory unless the inventory itself proves that no free/local option can answer a required question and the user explicitly confirms the paid call.

Inventory mode: [full inventory]
Reason: setup/post-mortem mode after failed or partially failed tasks.

Step 1 — Enumerate the concrete failures.
List every task from last night that failed, partially failed, required a workaround, or hit a paid/metered API when a free/local option should have been considered first. Include at least these known failure categories if they occurred:

- Telegram message deletion: the user asked to delete thread messages containing sensitive resume/cover-letter wording, but Telegram/API limits prevented deletion.
- Job spreadsheet workflow: the user asked for a spreadsheet of jobs, application links, sources such as Indeed or ZipRecruiter, cover letters, and recommended resume versions.
- Email delivery workflow: the user asked to email the spreadsheet to the confirmed Gmail address, but the workflow did not complete.
- Web search/tool routing: the task needed free web search and page fetching, but the agent appeared to route work through an LLM provider instead of a free/local search tool.
- Paid LLM usage failure: Anthropic/Claude calls retried and failed because credits were exhausted, showing that paid API use was happening before the free/local resource check was complete.
- File/tool lookup: the agent referenced local skills or files, but did not clearly prove which local tools were available, free, working, or appropriate.

For each failure, write:

- What the user asked for.
- What Hermes actually did.
- Where it broke down.
- Whether the breakdown was caused by a missing tool, unavailable credentials, wrong tool choice, insufficient preflight, API/platform limitation, or paid API dependency.
- What evidence Hermes has for that conclusion.

Step 2 — Extract the capabilities required.
From the failures above, list the capabilities the tasks actually required. Be specific. Consider:

- Telegram API operations and platform-limit awareness.
- Free web search.
- Web page fetching.
- Job source tracking and citation capture.
- Spreadsheet creation/editing.
- Local file reading and writing.
- Email sending with attachments.
- OCR or document parsing, if resumes or PDFs were involved.
- Code execution or local scripts.
- LLM reasoning/generation.
- Embeddings or semantic search, if needed for matching jobs to resumes.
- Secrets/credentials access.
- Logging and audit trail capture.

Step 3 — Inventory relevant available resources.
List only tools/resources relevant to the required capabilities. For each resource, include:

- Name.
- Capability covered.
- Free/local, paid/metered, or unknown.
- Status: available, unavailable, or untested.
- Confidence level.
- How status is known without causing side effects.

Do not run availability tests that send messages, emails, web requests, paid API calls, or other side-effecting operations just to check whether a tool works. If a safe local check is possible, describe it before running it.

Step 4 — Match needs to resources.
For each required capability, map it to the cheapest suitable available resource. Prefer free/local tools when they meet the quality and safety requirements.

Use this order of preference:

1. Existing conversation/context or local memory, if sufficient.
2. Local files, scripts, command-line tools, and installed libraries.
3. Free/self-hosted services already configured, such as SearXNG, DuckDuckGo HTML search, local fetchers, local parsers, or local spreadsheet libraries.
4. Free-tier external services only if they are not metered in a way the user is trying to avoid.
5. Paid or metered APIs only after explicit justification and user confirmation.

Step 5 — Identify gaps and safe fallbacks.
For every capability with no adequate free/local match, state:

- What is missing.
- Why the current tool/resource is insufficient.
- What free/local option could fill the gap, such as SearXNG for search, curl/httpx for fetching, pandas/openpyxl for spreadsheets, Tesseract or pdfplumber for OCR/PDF parsing, Ollama or llama.cpp for local LLM tasks, Chroma/Qdrant plus sentence-transformers for semantic search, or SMTP/local mail tools for email.
- Whether the original task can proceed without that capability.
- Whether a paid fallback is genuinely required.

Step 6 — Paid or metered API gate.
Before any paid or metered API call, write one sentence in this exact format:

Paid API justification: [specific paid/metered API] is required because [specific task requirement] cannot be met by [specific free/local options checked] due to [specific reason].

If that sentence is not clearly true, do not make the paid call. Ask the user before proceeding.

Step 7 — Corrective plan.
End with a short action plan:

- What Hermes should do differently on the next attempt.
- Which free/local tool should be tried first for each failed task type.
- Which platform limitations must be stated up front.
- Which credentials or configs need to be verified by the user.
- What should be logged so the user can audit tool choice and paid API avoidance.

Output format:
Keep the inventory concise but complete. Use headings for each step, bullets under each heading, and mark unknowns as unknown instead of guessing.
```

## Short deployment note

Commit this prompt to the repo and deploy it wherever Hermes reads its system or operator prompts. If Hermes runs on a VPS, the VPS must pull the repo change and restart/reload Hermes before this policy affects Telegram or other remote-device interactions.
