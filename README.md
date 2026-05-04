# STABLEVAL: Disagreement-Aware and Stable Evaluation of AI Systems

**ICML 2026** — *Proceedings of the 43rd International Conference on Machine Learning, Seoul, South Korea*

> Akash Bonagiri\*, Gerard Janno Anderias\*, Saee Patil, Angelina Lai, Devang Borkar, Gezheng Kang, Ishant Gandhi, Setareh Rafatirad, Houman Homayoun
>
> University of California, Davis &nbsp;|&nbsp; \*Equal contribution
>
> Correspondence: `sbonagiri@ucdavis.edu`

---

## Overview

Human evaluation is the gold standard for assessing AI systems — but annotators disagree. Standard majority vote aggregation discards annotator reliability and item-level ambiguity, producing agent rankings that shift under small changes in annotator composition.

**STABLEVAL** is a disagreement-aware evaluation framework that:
- Models latent item correctness and annotator-specific confusion patterns
- Computes **Posterior Expected Credit (PEC)** — a probabilistic score that preserves graded correctness and uncertainty
- Formalizes **ranking stability** as a first-class evaluation objective, demonstrating that recovering latent labels ≠ producing stable rankings
- Produces agent rankings that are robust to annotator subsampling and heterogeneous noise

Across synthetic stress tests and four real-world benchmarks (MT-Bench, ConvAbuse, QAGS, MSLR), STABLEVAL consistently outperforms majority vote and Dawid–Skene in ranking stability under annotator heterogeneity and adversarial noise.

---

## Key Idea

Majority vote collapses multi-annotator judgments into a single hard label, losing two critical signals:

| Signal | What majority vote discards | How STABLEVAL uses it |
|---|---|---|
| **Annotator reliability** | Treats all annotators equally | Learns per-annotator confusion matrices |
| **Item ambiguity** | Forces a single binary outcome | Preserves full posterior over correctness levels |

The core scoring formula is:

$$\text{credit}(i) = \sum_{c=0}^{K-1} \gamma_{ic} \cdot v(c)$$

where $\gamma_{ic} = P(z_i = c \mid \text{observations})$ is inferred via EM and $v(c)$ maps correctness levels to numeric credit (e.g., $[0, 0.5, 1]$ for incorrect / partial / correct).

Agent scores are the mean item credit over all evaluated outputs, naturally incorporating annotator reliability and uncertainty.

---

## Repository Structure

```
STABLEVAL/
├── Disagreement-Agent-Modeling-on-Real-Datasets-7D67/   # Real-dataset evaluation
│   ├── run_evaluation.py          # Main entry point
│   ├── src/
│   │   ├── disagreement_model.py  # EM inference (Dawid-Skene / PEC)
│   │   ├── scoring.py             # Agent scoring & bootstrap CI
│   │   ├── majority_vote.py       # Baseline aggregation
│   │   ├── data_loader.py         # CSV ingestion & preprocessing
│   │   └── visualization.py       # Plots & diagnostics
│   ├── data/                      # Place your annotation CSVs here
│   └── requirements.txt
│
└── Disagreement_synthetic_modeling-40E0/                # Synthetic study
    ├── scripts/
    │   ├── run_ablation.py        # Ablation over noise configurations
    │   ├── run_single.py          # Single-config run
    │   └── generate_plots.py      # MSE / Kendall τ / stability plots
    ├── src/
    │   ├── agents.py              # Synthetic agent & true-label generation
    │   ├── aggregation.py         # MV / DS / PEC implementations
    │   ├── metrics.py             # MSE, Kendall τ, stability
    │   └── config.py              # Config dataclass
    ├── configs/                   # YAML configs for each ablation axis
    └── requirements.txt
```

---

## Quickstart

### Real-Dataset Evaluation

```bash
cd Disagreement-Agent-Modeling-on-Real-Datasets-7D67

python -m venv venv && source venv/bin/activate
pip install -r requirements.txt

# Place annotation CSV(s) in data/processed/
python run_evaluation.py --data-dir data/processed --output-dir results
```

**Input format** — one CSV per dataset:

| Column | Type | Description |
|---|---|---|
| `item_id` | string | Unique (task, agent, output) identifier |
| `agent_id` | string | Which agent produced the output |
| `annotator_id` | string | Which annotator labeled it |
| `label` | int | Annotation label (e.g. 0=incorrect, 1=partial, 2=correct) |

**Key flags:**

| Flag | Default | Description |
|---|---|---|
| `--bootstrap N` | 10 | Bootstrap iterations for 95% CIs |
| `--stability N` | 50 | Annotator-subset samples for stability |
| `--n-classes K` | auto | Number of label classes |
| `--combine` | false | Merge all CSVs into one dataset |

**Outputs** (saved to `results/<dataset>/`):

- `agent_scores_comparison.csv` — MV / DS / PEC scores per agent
- `item_ambiguity.csv` — posterior entropy per item
- `annotator_quality.csv` — learned accuracy, leniency, strictness
- `ranking_stability.csv` — Kendall τ under annotator subsampling
- `bootstrap_*.csv` — 95% confidence intervals
- `evaluation_report.md` — auto-generated summary
- `plots/` — score comparison, ranking, ambiguity, annotator diagnostics

---

### Synthetic Ablation Study

```bash
cd Disagreement_synthetic_modeling-40E0

python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
```

Run any of the six ablation axes from the paper:

```bash
# Adversarial annotator fraction (0%–40%)
python scripts/run_ablation.py configs/ablation_adversarial/*.yaml

# Strict / lenient annotator fractions
python scripts/run_ablation.py configs/ablation_strict/*.yaml
python scripts/run_ablation.py configs/ablation_lenient/*.yaml

# Hard item probability
python scripts/run_ablation.py configs/ablation_hard_prob/*.yaml

# Labels per item (with stability metrics)
python scripts/run_ablation.py configs/ablation_labels/*.yaml --compute-stability

# Agent quality gaps
python scripts/run_ablation.py configs/ablation_gaps/*.yaml

# Then generate plots
python scripts/generate_plots.py results/comparison_TIMESTAMP/
```

**Key flags:**

| Flag | Default | Description |
|---|---|---|
| `--n-repetitions N` | 100 | Monte Carlo repetitions per config |
| `--workers N` | 1 | Parallel CPU workers |
| `--compute-stability` | false | Include stability metrics (slower) |

**Default synthetic configuration:**

| Parameter | Value |
|---|---|
| Agents | 6 with qualities `[0.85, 0.80, 0.70, 0.55, 0.35, 0.20]` |
| Items per agent | 500 |
| Annotators | 30 (18 normal, 6 strict, 4 lenient, 2 adversarial) |
| Labels per item | 5 |
| Hard item probability | 0.2 |

---

## Methods

Three aggregation methods are compared throughout:

| Method | Description |
|---|---|
| **Majority Vote (MV)** | Mode of raw annotations — the standard baseline |
| **Dawid–Skene Hard (DS)** | EM initialized from MV labels; optimizes latent label recovery |
| **Posterior Expected Credit (PEC)** | EM with soft initialization; preserves full posterior for stable scoring |

STABLEVAL = PEC. Unlike DS, it does not collapse posteriors to hard labels, making it explicitly designed for evaluation stability rather than label denoising.

---

## Evaluation Metrics

- **MSE** — mean squared error of agent scores vs. ground truth (synthetic only)
- **Kendall's τ** — rank correlation between method-induced and ground-truth rankings
- **Ranking Stability** — expected Kendall's τ under random annotator subsampling
- **Annotator Diagnostics** — learned per-annotator accuracy, leniency, and strictness
- **Item Ambiguity** — entropy of posterior correctness distribution

---

## Benchmarks

| Dataset | Domain | Annotators | Pairwise Agreement |
|---|---|---|---|
| MT-Bench | Preference (6 models, QA/summarization) | 65 | 53.0% |
| ConvAbuse | Conversational safety | 8 | 84.4% |
| QAGS | Summarization factuality | 169 | — |
| MSLR | Medical literature review | — | — |

STABLEVAL yields the most stable rankings on MT-Bench (high disagreement) and matches other methods in high-consensus settings (ConvAbuse), confirming that disagreement-aware modeling adds value precisely when disagreement is informative.

---

## Citation

```bibtex
@inproceedings{bonagiri2026stableval,
  title     = {STABLEVAL: Disagreement-Aware and Stable Evaluation of AI Systems},
  author    = {Bonagiri, Akash and Anderias, Gerard Janno and Patil, Saee and Lai, Angelina and Borkar, Devang and Kang, Gezheng and Gandhi, Ishant and Rafatirad, Setareh and Homayoun, Houman},
  booktitle = {Proceedings of the 43rd International Conference on Machine Learning},
  series    = {PMLR 306},
  year      = {2026},
  address   = {Seoul, South Korea}
}
```

---

## License

See [LICENSE](LICENSE).
