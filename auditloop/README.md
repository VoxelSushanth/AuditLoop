# AuditLoop

**A reconciliation agent that never lets an LLM have the final say on a financial match.**

Multi-source reconciliation agent for Razorpay settlements, bank statements,
and internal ledgers. A deterministic matching engine runs first; an LLM is
only invoked to explain and propose resolutions for unresolved exceptions —
it can never commit a match directly, every proposal is re-verified
deterministically before it counts. Every decision, matched or not, is
logged to an audit trail. Accuracy is measured against a known ground-truth
batch, not demoed on cherry-picked examples.

Settlements data is pulled live from Razorpay's test-mode Settlement Recon
API; bank statement and ledger data are synthetic (clearly tagged where
used) since no equivalent sandbox exists for those.

## Project Structure

```
auditloop/
├── data/
│   ├── fetch_settlements.py     # Real Razorpay test-mode API pull
│   ├── generate_data.py         # Synthetic bank + ledger data, linked to live pull
│   ├── ground_truth.json        # Answer key for measuring precision/recall
│   └── sample_batch/            # Committed example batch (runs with zero API keys)
├── engine/
│   ├── matcher.py               # Deterministic exact + fuzzy matching (Stage 1 & 2)
│   └── exceptions.py            # Stage 3 — dispatch unresolved records to the LLM
├── llm/
│   ├── client.py                 # Claude API wrapper
│   ├── schemas.py                 # Pydantic response models (strict validation)
│   └── prompts.py                 # Scoped system + tool prompts
├── audit/
│   ├── store.py                   # SQLite audit log (append-only)
│   └── models.py
├── metrics/
│   └── evaluate.py                # Precision / recall / match-rate vs ground truth
├── dashboard/
│   └── app.py                     # Streamlit reviewer dashboard
├── tests/
│   ├── test_matcher.py
│   ├── test_llm_schema_validation.py
│   └── test_end_to_end_metrics.py
├── docs/
│   ├── architecture.png
│   └── DESIGN_DECISIONS.md        # Why deterministic-first, thresholds, tradeoffs
├── README.md
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
└── .env.example
```

## Tech Stack

Python · FastAPI · pandas · RapidFuzz · Anthropic Claude (tool calling +
Pydantic schemas) · Razorpay API · SQLite · Streamlit · pytest · Docker
