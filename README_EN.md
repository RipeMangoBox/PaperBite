<p align="center">
  <img src="https://raw.githubusercontent.com/RipeMangoBox/BITE/main/assets/paperbite_icon.png" alt="BITE logo" width="240"/>
</p>

<h1 align="center">PaperBite → BITE</h1>

<p align="center"><strong>PaperBite has been merged into the BITE project.</strong></p>

---

PaperBite's public evidence layer (Markdown analysis notes, figures, indexes, manifests) is now published on HuggingFace dataset:

**[RipeMangoBox/PaperBite-Assets](https://huggingface.co/datasets/RipeMangoBox/PaperBite-Assets)**

### Get the Content

```bash
# Clone the BITE tools repo
git clone https://github.com/RipeMangoBox/BITE.git
cd BITE

# Incrementally sync PaperBite assets from HuggingFace
pip install huggingface_hub
python scripts/sync_assets_from_hf.py --local-dir .
```

### Learn More

- [BITE Project Home](https://github.com/RipeMangoBox/BITE) — analysis tools, workflows, full documentation
- [PaperBite-Assets on HuggingFace](https://huggingface.co/datasets/RipeMangoBox/PaperBite-Assets) — structured paper assets

---

<p align="center">BITE = <strong>B</strong>ibliographic <strong>I</strong>ntelligence for <strong>T</strong>hought <strong>E</strong>mergence</p>
