<p align="center">
  <img src="./assets/icon/paperbite_icon.svg" alt="PaperBite icon" width="128"/>
</p>

<h1 align="center">PaperBite</h1>

<p align="center"><strong>Bite-sized Paper Notes for Bibliographic Intelligence for Thought Emergence</strong></p>

<p align="center">
  <img alt="ResearchFlow derived" src="https://img.shields.io/badge/ResearchFlow-derived-1f6feb?style=flat-square"/>
  <img alt="Markdown vault" src="https://img.shields.io/badge/Markdown-evidence%20vault-0f766e?style=flat-square"/>
  <img alt="License CC BY-NC 4.0" src="https://img.shields.io/badge/License-CC--BY--NC--4.0-111827?style=flat-square"/>
</p>

PaperBite means "一口一篇论文": each paper is compressed into a readable Markdown bite with source anchors, structured metadata, and links back to the original paper.

Full name:

```text
BITE = Bibliographic Intelligence for Thought Emergence
PaperBite = bite-sized paper notes for BITE
```

PaperBite is the evidence-deposition layer of the broader BITE idea. It does
not try to be the full idea-generation system by itself. Instead, it keeps
paper-level evidence clean, compact, traceable, and ready for downstream
ResearchFlow agents.

This repository is a lightweight local vault derived from
[ResearchFlow](https://github.com/RipeMangoBox/ResearchFlow) outputs. The
default checkout is text-first and keeps large binaries optional.

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

## License

PaperBite is a knowledge-content repository, not a software framework. Its
original Markdown notes, generated indexes, manifests, prompts, repository
documentation, and other text artifacts are licensed under
[Creative Commons Attribution-NonCommercial 4.0 International](LICENSE.md).

Preferred attribution:

```text
PaperBite: bite-sized paper notes for BITE
(Bibliographic Intelligence for Thought Emergence),
derived from ResearchFlow by ripemangobox.
https://github.com/RipeMangoBox/PaperBite
https://github.com/RipeMangoBox/ResearchFlow
```

The upstream ResearchFlow software remains MIT licensed. This split is
intentional: ResearchFlow is the reusable workflow and tooling layer, while
PaperBite is a generated evidence vault. Paper PDFs, paper figures, publisher
content, OpenReview metadata, and other third-party materials are not relicensed
by this repository.
