# 🤖 AI Assistant Instructions & Git Guidelines

This repository (`culture-fit-agent`) is a collaborative multi-agent system.
As an AI coding assistant working in this workspace, you MUST automatically follow these guidelines:

---

## 1. Git Context & Collaboration Rules
- **Inspect Git Before Changes**:
  - Whenever asked to write or modify code, check recent commit history (`git log`) and current changes (`git status` / `git diff`) to maintain consistency with the team's ongoing work.
- **Strict Domain Isolation**:
  - `notebooks/company/`: Exclusively for Company, Job Description (JD), and culture analysis.
  - `notebooks/applicant/`: Exclusively for Applicant, Resume, and candidate profile evaluation.
  - **CRITICAL**: NEVER modify, delete, or cross-edit files in `notebooks/company/` when fulfilling an applicant request, and vice versa.
- **Commit Conventions**:
  - When suggesting or drafting commits, use Conventional Commits: `feat:`, `fix:`, `docs:`, `chore:`, `refactor:`.
  - Keep commit messages concise, descriptive, and never mention unrelated project names.

---

## 2. Architecture & State Integrity
- **Shared State Contract (`src/state.py`)**:
  - `src/state.py` defines the shared data schema across agents.
  - NEVER rename or remove existing state keys.
  - Always maintain backward compatibility when adding new fields.
- **Prototyping vs Production**:
  - Use `notebooks/` for prompt testing and experimental logic.
  - Only promote thoroughly tested, clean functions into `src/nodes.py` or domain modules.

---

## 3. Security & File Rules
- **No Secret Exposure**:
  - NEVER hardcode API keys (OpenAI, LangSmith, Tavily) in any source file or notebook.
  - Always load secrets from environment variables or `.env`.
- **No Arbitrary Sample Files**:
  - Do NOT create arbitrary dummy or sample files (`sample_*`, mock text files) unless the user explicitly requests them.
- **Candidate Privacy**:
  - Real candidate resumes and personally identifiable information (PII) must never be committed to Git.

---

## 4. Windows & Cross-Platform Standards
- **UTF-8 Encoding**:
  - Always specify `open(..., encoding="utf-8")` in Python to prevent Windows `CP949` decoding errors.
- **Package Management**:
  - Always use `uv` (`uv add`, `uv sync`, `uv run`). Do not advise bare `pip install`.
