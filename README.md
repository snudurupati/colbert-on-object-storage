# colbert-on-object-storage

An implementation of ColBERT late interaction on object storage using turbopuffer.

## Setup

1. Clone the repo
2. Create a virtual environment and install dependencies:
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

3. Create a `.env` file with your turbopuffer API key:

```TURBOPUFFER_API_KEY=your_key_here```

4. Open and run the notebook of your choice (see below)

## Notebooks

**`late_interaction_turbopuffer.ipynb`** — Main guide
- Indexes 1,000 Quora questions into turbopuffer with ColBERT token vectors (float32 JSON)
- Dense retrieval using `all-MiniLM-L6-v2`
- ColBERT late interaction re-ranking using `colbert-ir/colbertv2.0`
- Analysis comparing result quality, latency, and cost
- Roadmap suggestions for native multi-vector support in turbopuffer

**`late_interaction_turbopuffer_Q8.ipynb`** — int8 quantization experiment
- Same as main guide but token vectors quantized to int8 before JSON serialization
- 82% payload size reduction (72KB → 12.8KB per document)
- 34% end-to-end latency improvement (325ms → 214ms warm)
- Storage reduction from 19.63MB to ~3.5MB for 1k documents

**`late_interaction_msmarco.ipynb`** — MS MARCO experiment
- 10,000 multi-sentence passages from MS MARCO v2.1 — ColBERT's original benchmark dataset
- Token vectors stored as base64 float32 — no precision loss, 87% cheaper than float32 JSON
- ColBERT ties dense at Recall@1 (0.91) and wins at Recall@5 (1.00 vs 0.99) on 100 queries
- Confirms dataset choice matters more than model choice — ColBERT shines on long multi-concept documents

## Key Findings

| | Quora (1K) | MS MARCO (10K) |
|---|---|---|
| Dense Recall@1 | 0.94 | 0.91 |
| ColBERT Recall@1 | 0.84 | 0.91 |
| Dense warm latency | 70ms | 44ms |
| ColBERT warm latency | 325ms | 267ms |
| ColBERT bytes returned/query | 3.5MB | 4.3MB |

## References

- [ColBERT paper — Khattab & Zaharia, SIGIR 2020](https://arxiv.org/abs/2004.12832)
- [ColBERTv2 paper — Santhanam et al., 2022](https://arxiv.org/abs/2112.01488)
- [turbopuffer architecture](https://turbopuffer.com/docs/architecture)
- [turbopuffer hybrid search guide](https://turbopuffer.com/docs/hybrid)
- [Quora Duplicates dataset](https://huggingface.co/datasets/sentence-transformers/quora-duplicates)
- [MS MARCO dataset](https://huggingface.co/datasets/microsoft/ms_marco)
- [fastembed ColBERT support](https://qdrant.tech/documentation/fastembed/fastembed-colbert/)