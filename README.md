# PaperBite

PaperBite means "一口一篇论文": each paper is compressed into a readable Markdown bite with source anchors, structured metadata, and links back to the original paper.

This repository is a lightweight local vault derived from ResearchFlow outputs. The default checkout is text-first and keeps large binaries optional.

## Included by Default

- `analysis/ICLR_2026/`: structured paper analysis notes in Obsidian-friendly Markdown.
- `index/`: generated navigation and index pages.
- `manifests/paperbite_manifest.jsonl`: one row per Markdown note with title, venue, year, acceptance, OpenReview id, and checksum.
- `manifests/paperbite_summary.json`: repository-level summary.

## Optional Layers

- `assets/`: extracted figures and tables referenced by Markdown embeds.
- `paperPDFs/`: source PDFs referenced by Markdown embeds.

These layers can be copied in when needed, but they are ignored by git by default so the repository stays small.

## Incremental Sync

Use ResearchFlow as the source of truth. A safe sync updates only the text layers first:

```bash
rsync -a --delete ../obsidian-vault/analysis/ ./analysis/
rsync -a --delete ../obsidian-vault/index/ ./index/
```

Then refresh manifests from the synced Markdown files. Pull optional layers only for papers you plan to inspect visually:

```bash
rsync -a ../obsidian-vault/assets/ ./assets/
rsync -a ../obsidian-vault/paperPDFs/ ./paperPDFs/
```

Do not commit large PDFs or extracted figure folders unless a release explicitly needs a bundled offline snapshot.

## Current Snapshot

- Venue slice: ICLR 2026
- Paper count: see `manifests/paperbite_summary.json`
- Default format: Markdown plus generated indexes
- Optional media: figures/tables and PDFs
