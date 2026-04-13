# PLAID-PRF Virtual Appendix

This repository is the Virtual Appendix for our paper **"PLAID-PRF — Pseudo-Relevance Feedback with Centroid-like Tokens in PLAID"**.

PLAID-PRF is a lightweight pseudo-relevance feedback (PRF) method for **quantised late-interaction retrieval**. It is designed for the **PLAID / ColBERTv2** retrieval pipeline and improves retrieval effectiveness by leveraging **indexing-time centroid codes** already stored in the compressed index, rather than performing expensive retrieval-time clustering or additional transformer inference.

In particular, this repository is intended to host:

- result files for the main experimental benchmarks reported in the paper;
- scripts or notebooks for reproducing the reported metrics;
- auxiliary artifacts such as configuration files, index statistics, and optional analysis outputs.

---

## Method Overview

PLAID-PRF performs pseudo-relevance feedback in four stages:

1. **Initial retrieval**
   - Run a first-pass PLAID search with the original query.
   - Collect the top-`fp` pseudo-relevant passages.

2. **Centroid usefulness scoring**
   - Each feedback token is represented in the PLAID/ColBERTv2 index by a **centroid code** plus a **quantised residual**.
   - PLAID-PRF counts centroid occurrences in the feedback set and scores them with a classical weighting model such as **TF-IDF**.
   - This gives high scores to centroid codes that are frequent in the feedback documents but rare in the corpus.

3. **Expansion embedding selection**
   - The centroid-level weights are mapped back to the reconstructed token embeddings.
   - A compact set of high-utility expansion embeddings is selected.
   - To reduce redundancy, PLAID-PRF uses **MMR-style selection** rather than naive top-k selection.

4. **Expanded-query retrieval**
   - The selected expansion embeddings are appended to the original query representation with a scaling factor `beta`.
   - PLAID is then rerun with the refined query representation.

This design makes PLAID-PRF **training-free**, **codebook-aware**, and **quantisation-compatible**.

---

## Why PLAID-PRF?

Compared with prior PRF methods for multi-vector retrieval, PLAID-PRF is designed to preserve the efficiency advantages of PLAID:

- it reuses the **existing PLAID codebook** rather than running per-query KMeans;
- it performs feedback reasoning in the **compressed index space**;
- it does **not** require an additional feedback encoder;
- it keeps latency low while improving early precision.

In the paper, PLAID-PRF improves over PLAID by up to **+4.3% nDCG@10** and **+7.3% MRR@10**, while maintaining a low end-to-end latency of around **102 ms/query** at the reported operating point.

---

## Experimental Setting

The paper evaluates PLAID-PRF on:

- **TREC Deep Learning 2019**
- **TREC Deep Learning 2020**
- **DL-HARD**
- selected **BEIR** datasets:
  - DBPedia
  - NFCorpus
  - TREC-COVID
  - Touche-2020

The reported PLAID configuration uses residual compression with **2-bit residual quantisation** and the recommended PLAID operating point:

- `ncells = 4`
- `tcs = 0.40`
- `ndocs = 4096`

The main PLAID-PRF setting reported in the paper is:

- `fp = 3`
- `fe = 14`
- `beta = 0.7`
- `lambda = 0.3`

---

## Repository Structure

A suggested layout for this repository is:

```text
.
├── README.md
├── results/
│   ├── TREC2019/
│   ├── TREC2020/
│   ├── DL-HARD/
│   └── BEIR/
├── scripts/
│   ├── evaluate_runs.py
│   ├── reproduce_main_tables.py
│   └── export_results.sh
├── configs/
│   ├── plaid_prf.yaml
│   └── baselines.yaml
├── artifacts/
│   ├── centroid_df/
│   ├── query_logs/
│   └── latency_breakdown/
└── docs/
    └── paper_links.md
```

You can adapt this structure to match your actual released artifacts.

---

## Usage

The result files in this repository can be evaluated with PyTerrier.

Example:

```python
from pyterrier.measures import *
import pyterrier as pt

if not pt.started():
    pt.init()

dataset = pt.get_dataset("irds:msmarco-passage/trec-dl-2019/judged")
res = pt.io.read_results("results/TREC2019/plaid-prf.res.gz")

evalMeasuresDict = pt.Utils.evaluate(
    res,
    dataset.get_qrels(),
    metrics=[AP(rel=2)@1000, nDCG@10, RR(rel=2)@10]
)

print(evalMeasuresDict)
```

For TREC DL 2020 or other collections, replace the dataset identifier and run file path accordingly.

---

## Reproducibility Notes

To reproduce the paper faithfully, the following components should be aligned:

- the **ColBERTv2 checkpoint** used to build the PLAID index;
- the PLAID retrieval parameters;
- the centroid-level corpus statistics used for usefulness scoring;
- the PRF hyperparameters (`fp`, `fe`, `beta`, `lambda`);
- the evaluation collection and qrels.

If you release code alongside this appendix, it is useful to include:

- exact environment versions;
- commands for building the PLAID index;
- commands for generating centroid document-frequency statistics;
- commands for running first-pass retrieval, feedback construction, and second-pass retrieval;
- scripts for latency measurement.

---

## Main Takeaway

PLAID-PRF shows that pseudo-relevance feedback for late-interaction retrieval does not need to rely on expensive online clustering or additional large-model inference. By aligning feedback estimation with the **compressed centroid structure** already used by PLAID, it is possible to obtain an attractive **effectiveness-efficiency trade-off**.

---

## Citation

If you use this repository, please cite the paper:

```bibtex
@inproceedings{wang2026plaidprf,
  title={PLAID-PRF: Pseudo-Relevance Feedback with Centroid-like Tokens in PLAID},
  author={Wang, Xiao and Macdonald, Craig and Tonellotto, Nicola and Ounis, Iadh},
  booktitle={Proceedings of the 49th International ACM SIGIR Conference on Research and Development in Information Retrieval},
  year={2026}
}
```

> Replace the citation entry with the final publication metadata once available.

---

## Links

- Paper: `<paper-link>`
- Code: `<code-link>`
- Index artifacts / virtual appendix: `<artifact-link>`

> Replace the placeholders above with the final public URLs when the repository is released.
