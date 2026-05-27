<p align="center">
  <img src="./assets/icon/paperbite_icon.png" alt="PaperBite icon" width="360"/>
</p>

<h1 align="center">PaperBite</h1>

<p align="center"><strong>一口一篇论文 · Bite-sized paper notes</strong></p>

<p align="center">
  <a href="README.md">中文</a> |
  <a href="README_EN.md">English</a>
</p>

<p align="center">
  <img alt="ResearchFlow derived" src="https://img.shields.io/badge/ResearchFlow-derived-1f6feb?style=flat-square"/>
  <img alt="Markdown vault" src="https://img.shields.io/badge/Markdown-evidence%20vault-0f766e?style=flat-square"/>
  <img alt="License CC BY-NC 4.0" src="https://img.shields.io/badge/License-CC--BY--NC--4.0-111827?style=flat-square"/>
</p>

PaperBite is a public evidence repository for paper reading and reuse. It compresses each paper into a readable Markdown bite with source anchors, structured metadata, links back to the original paper, plus generated indexes and manifests that make repository-style distribution and incremental sync practical.

The default GitHub checkout ships only the text layers: `analysis/`, `index/`, and `manifests/` are distributed through Git, while `assets/` and `paperPDFs/` remain optional large-file layers. They are intentionally excluded from the default checkout and should only be synced from directories that already exist outside this repository, private local storage, or a separate media mirror.

PaperBite and [ResearchFlow](https://github.com/RipeMangoBox/ResearchFlow) divide responsibilities by layer. PaperBite publishes the upstream public evidence layer, mainly covering `L0-L3` paper assets, while ResearchFlow remains the workflow for collection, parsing, analysis, retrieval, and downstream research decisions.

## Included by Default

- `analysis/ICLR_2026/`: structured paper analysis notes in Obsidian-friendly Markdown.
- `index/`: generated navigation and index pages.
- `manifests/paperbite_manifest.jsonl`: one row per Markdown note with title, venue, year, acceptance, OpenReview id, and checksum.
- `manifests/paperbite_summary.json`: repository-level summary.

## Optional Layers

- `assets/`: extracted figures and tables referenced by Markdown embeds.
- `paperPDFs/`: source PDFs referenced by Markdown embeds.

These large local layers are not included in the GitHub checkout. Copy them in from directories that already exist outside this repository, a published media mirror, or content you download or regenerate separately.

## Incremental Sync

Use ResearchFlow as the source of truth. A safe sync updates the text layers first:

```bash
rsync -a --delete ../obsidian-vault/analysis/ ./analysis/
rsync -a --delete ../obsidian-vault/index/ ./index/
```

Then refresh manifests from the synced Markdown files. Keep `manifests/paperbite_manifest.jsonl` as the text-note manifest, and add media manifests only when publishing a larger snapshot:

- `manifests/paperbite_assets_manifest.jsonl`: one row per exported figure or table under `assets/`, with path, size, checksum, and source note.
- `manifests/paperbite_pdfs_manifest.jsonl`: one row per PDF under `paperPDFs/`, with path, size, checksum, source URL when available, and source note.

For local maintenance of optional media, sync only from directories that already exist outside this Git checkout, such as a private ResearchFlow vault or a separately downloaded or generated media mirror:

```bash
rsync -a ../obsidian-vault/assets/ ./assets/
rsync -a ../obsidian-vault/paperPDFs/ ./paperPDFs/
```

For public user updates, keep Git as the lightweight text channel and publish large media through a stable mirror indexed by the two media manifests. The mirror should preserve repository-relative paths, for example:

```text
assets/figures/papers/<task_id>/...
paperPDFs/<Venue_Year>/<Paper>.pdf
```

Maintainer flow: generate the media manifests, compare path plus checksum against the previous published version, upload only new or changed paths, then publish the new manifests alongside the text update. User flow: pull Git for Markdown and index changes, compare local media files against the latest manifests, and download only missing or changed paths from the mirror. Do not commit large PDFs or extracted figure folders unless a release explicitly needs a bundled offline snapshot.

## Current Snapshot

- Venue slice: ICLR 2026
- Paper count: see `manifests/paperbite_summary.json`
- Default format: Markdown plus generated indexes
- Optional media: figures / tables and PDFs

## Citation

If PaperBite helps your research, please cite the repository directly:

```bibtex
@misc{lin2026paperbite,
  title        = {{PaperBite}: Bite-sized paper notes},
  author       = {Jingzhong Lin and Ziheng Huang},
  year         = {2026},
  howpublished = {\url{https://github.com/RipeMangoBox/PaperBite}},
  note         = {GitHub repository}
}
```

## License

PaperBite is a knowledge-content repository, not a software framework. Its original Markdown notes, generated indexes, manifests, prompts, repository documentation, and other text artifacts are licensed under [Creative Commons Attribution-NonCommercial 4.0 International](LICENSE.md).

The upstream ResearchFlow software remains MIT licensed. This split is intentional: ResearchFlow is the reusable workflow and tooling layer, while PaperBite is the public evidence and parsed paper asset layer. Paper PDFs, paper figures, publisher content, OpenReview metadata, and other third-party materials are not relicensed by this repository.
