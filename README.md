# PLAID-PRF Virtual Appendix

This repository is the Virtual Appendix for our paper **"PLAID-PRF — Pseudo-Relevance Feedback with Centroid-like Tokens in PLAID"**.

PLAID-PRF is a lightweight pseudo-relevance feedback (PRF) method for **quantised late-interaction retrieval**. It is designed for the **PLAID / ColBERTv2** retrieval pipeline and improves retrieval effectiveness by leveraging **indexing-time centroid codes** already stored in the compressed index, rather than performing expensive retrieval-time clustering or additional transformer inference.

In particular, this repository is intended to host:

- result files for the main experimental benchmarks reported in the paper;
- notebooks for reproducing the reported metrics;

---

## Usage

The result files in this repository can be evaluated with PyTerrier.

Example:

```python
from pyterrier.measures import *
import pyterrier as pt

if not pt.started():
    pt.java.init()

dataset = pt.get_dataset("irds:msmarco-passage/trec-dl-2019/judged")
res = pt.io.read_results("./PLAID-PRF/plaid_prf.dl19.res.gz")

evalMeasuresDict = pt.Evaluate(
    res,
    dataset.get_qrels(),
    metrics=[AP(rel=2)@1000, nDCG@10, RR(rel=2)@10, R(rel=2)@1000 ]
)

print(evalMeasuresDict)
```

For TREC DL 2020 or other collections, replace the dataset identifier and run file path accordingly.



---

## Citation

If you use this repository, please cite the paper:

```bibtex
@inproceedings{wang2026plaidprf,
  author    =   {Wang, Xiao and MacAvaney, Sean and Macdonald, Craig},
  title     = {{PLAID-PRF} -- {Pseudo-Relevance Feedback with Centroid-like Tokens in PLAID}},
  year      = {2026},
  booktitle = {Proceedings of the 49th International ACM SIGIR Conference on Research and Development in Information Retrieval},
  series    = {SIGIR '26},
  month     = jul,
  numpages  = {12},
  location  = {Melbourne, VIC, Australia},
  publisher = {ACM},
  address   = {New York, NY, USA},
  doi       = {10.1145/3805712.3809690},
  url       = {https://doi.org/10.1145/3805712.3809690}
}
```



---

## Links

- Paper: `<paper-link>`
- Code: `<code-link>`
- Index artifacts / virtual appendix: `<artifact-link>`

