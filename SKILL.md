---
name: vault-inbox-triage
description: Triage a knowledge-vault inbox: classify, move, fix metadata.
version: 1.0
license: MIT
metadata:
  tags: [obsidian, knowledge-management, git]
---

# Vault Inbox Triage

## When to Use
Load when the user asks to review, sort, clean up or "process" the inbox of a
personal knowledge vault (Obsidian, Logseq, markdown+git): the accumulation of
clipped articles, auto-generated digests and quick notes that has not been
classified yet.

## Setup assumptions (confirm first)
- Vault is a git repo; folders like `00-inbox/`, `10-sources/`, `20-notes/`,
  `30-projects/`, `90-meta/` (adjust to the actual layout — read it, don't assume).
- Notes carry YAML frontmatter (id, title, status, tags). A tags dictionary file
  exists (e.g. `90-meta/tags.md`).

## Procedure
1. **Survey, don't touch.** List all inbox files; read each. No decisions from
   filenames alone.
2. **Classify every file** into: (a) atomic insight → promote to notes folder as an
   evergreen candidate; (b) raw source dump / digest → sources folder; (c)
   low-confidence bot failure (truncated, hallucination flags) → fix in place or
   flag for re-ingest; (d) genuinely uncertain → leave in inbox with a note.
3. **Move with history preserved**: `git mv` only — never copy+delete. One logical
   change per commit.
4. **Patch frontmatter after the move** (status, path-derived fields). Read every
   edited file back immediately: on Windows/CRLF the vault tooling can leak stray
   characters into YAML blocks and silently break parsing.
5. **Tags**: only values present in the dictionary. A new needed tag = add it to
   the dictionary first, in the same commit batch, not invented per-note.
6. **Report**: counts by target folder × by status distribution; list files left in
   inbox and why. Suggest next batch if backlog is large.

## Safety rules
- Destructive deletion is out of scope: archive folders only; physical delete
  requires explicit written user command.
- Secrets/PII found in notes → redact in place (`REDACTED`) and tell the user;
  never propagate them into new files or commit messages.
- >10 files changed: print the dry-run plan and wait for approval first.
- Never rewrite `id`/filename timestamps; they key other references.
