<p align="center">
  <img src="https://raw.githubusercontent.com/RipeMangoBox/BITE/main/assets/paperbite_icon.png" alt="BITE logo" width="240"/>
</p>

<h1 align="center">PaperBite → BITE</h1>

<p align="center"><strong>PaperBite 已并入 BITE 项目。</strong></p>

---

PaperBite 的公开证据层（Markdown 分析笔记、图表、索引、manifests）现已发布至 HuggingFace dataset：

**[RipeMangoBox/PaperBite-Assets](https://huggingface.co/datasets/RipeMangoBox/PaperBite-Assets)**

### 获取内容

```bash
# 克隆 BITE 工具仓库
git clone https://github.com/RipeMangoBox/BITE.git
cd BITE

# 从 HuggingFace 增量同步 PaperBite 资产
pip install huggingface_hub
python scripts/sync_assets_from_hf.py --dry-run   # 检查需要下载的内容
python scripts/sync_assets_from_hf.py              # 增量下载并解压
```

### 了解更多

- [BITE 项目主页](https://github.com/RipeMangoBox/BITE) — 分析工具、工作流、完整文档
- [PaperBite-Assets on HuggingFace](https://huggingface.co/datasets/RipeMangoBox/PaperBite-Assets) — 结构化论文资产

---

<p align="center">BITE = <strong>B</strong>ibliographic <strong>I</strong>ntelligence for <strong>T</strong>hought <strong>E</strong>mergence</p>
