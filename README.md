# PLAID-PRF Virtual Appendix

This repository is the Virtual Appendix for our paper **"PLAID-PRF — Pseudo-Relevance Feedback with Centroid-like Tokens in PLAID"**.

PLAID-PRF is a lightweight pseudo-relevance feedback (PRF) method for **quantised late-interaction retrieval**. It is designed for the **PLAID / ColBERTv2** retrieval pipeline and improves retrieval effectiveness by leveraging **indexing-time centroid codes** already stored in the compressed index, rather than performing expensive retrieval-time clustering or additional transformer inference.

In particular, this repository is intended to host:

- result files for the main experimental benchmarks reported in the paper;
- notebooks for reproducing the reported metrics;
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
