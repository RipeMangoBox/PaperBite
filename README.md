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

PaperBite 是一个面向论文阅读与复用的公开证据仓库。它把每篇论文压缩成可读的 Markdown bite，并保留 source anchors、结构化元数据、原论文回链，以及配套生成的索引和清单，方便按仓库方式分发与增量同步。

默认 GitHub checkout 只分发文本层：`analysis/`、`index/` 和 `manifests/` 通过 Git 提供；`assets/` 与 `paperPDFs/` 属于可选大文件层，不随默认仓库一起提供，只能从仓库外部已经存在的目录、本地私有存储或独立媒体镜像同步进来。

与 [ResearchFlow](https://github.com/RipeMangoBox/ResearchFlow) 的关系是分层协作：PaperBite 负责对外发布上游公开证据层，主要承载 `L0-L3` 论文资产；ResearchFlow 保留采集、解析、分析、检索和下游研究决策工作流。

## 默认包含内容

- `analysis/ICLR_2026/`：Obsidian 友好的结构化论文分析笔记。
- `index/`：生成的导航页与索引页。
- `manifests/paperbite_manifest.jsonl`：每篇 Markdown 笔记一行，包含 title、venue、year、acceptance、OpenReview id 和 checksum。
- `manifests/paperbite_summary.json`：仓库级摘要。

## 可选层

- `assets/`：Markdown 嵌入引用的图表抽取结果。
- `paperPDFs/`：Markdown 引用的源 PDF。

这些大文件本地层不包含在 GitHub checkout 中。需要时请从仓库外部已有的本地目录、独立发布的媒体镜像，或你另行下载/再生成的目录同步进来。

## 增量同步

以 ResearchFlow 作为 source of truth。安全的同步顺序是先更新文本层：

```bash
rsync -a --delete ../obsidian-vault/analysis/ ./analysis/
rsync -a --delete ../obsidian-vault/index/ ./index/
```

然后再根据同步后的 Markdown 刷新 manifests。保留 `manifests/paperbite_manifest.jsonl` 作为文本笔记清单；如果要发布更大的快照，再额外维护媒体清单：

- `manifests/paperbite_assets_manifest.jsonl`：`assets/` 下每个导出图表一行，记录 path、size、checksum 和 source note。
- `manifests/paperbite_pdfs_manifest.jsonl`：`paperPDFs/` 下每个 PDF 一行，记录 path、size、checksum、可用时的 source URL，以及 source note。

本地维护可选媒体层时，只从这个 Git checkout 之外已经存在的目录同步，例如私有 ResearchFlow vault 或单独下载/生成好的媒体镜像：

```bash
rsync -a ../obsidian-vault/assets/ ./assets/
rsync -a ../obsidian-vault/paperPDFs/ ./paperPDFs/
```

对公共用户更新时，继续把 Git 作为轻量文本通道，把大媒体文件发布到由两份媒体 manifests 索引的稳定 mirror。mirror 应保留仓库相对路径，例如：

```text
assets/figures/papers/<task_id>/...
paperPDFs/<Venue_Year>/<Paper>.pdf
```

维护者流程：生成媒体 manifests，按 path + checksum 与上一次发布版本比较，只上传新增或变更路径，再随文本更新一起发布新 manifests。用户流程：拉取 Git 获取 Markdown / index 更新，对照最新 manifests 检查本地媒体文件，只下载缺失或发生变化的路径。除非某次 release 明确需要离线完整快照，否则不要提交大 PDF 或抽取出的图表目录。

## 当前快照

- Venue slice：ICLR 2026
- Paper count：见 `manifests/paperbite_summary.json`
- Default format：Markdown + generated indexes
- Optional media：figures / tables + PDFs

## 引用

如果 PaperBite 对你的研究有帮助，请直接引用本仓库：

```bibtex
@misc{lin2026paperbite,
  title        = {{PaperBite}: Bite-sized paper notes},
  author       = {Jingzhong Lin and Ziheng Huang},
  year         = {2026},
  howpublished = {\url{https://github.com/RipeMangoBox/PaperBite}},
  note         = {GitHub repository}
}
```

## 许可

[Creative Commons Attribution-NonCommercial 4.0 International](LICENSE.md)
