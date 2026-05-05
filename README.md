# STABLEVAL: Disagreement-Aware and Stable Evaluation of AI Systems

**ICML 2026** — *Proceedings of the 43rd International Conference on Machine Learning, Seoul, South Korea. PMLR 306.*

[![arXiv](https://img.shields.io/badge/arXiv-2605.02122-b31b1b.svg)](https://arxiv.org/abs/2605.02122)

> Akash Bonagiri\*, Gerard Janno Anderias\*, Saee Patil, Angelina Lai, Devang Borkar, Gezheng Kang, Ishant Gandhi, Setareh Rafatirad, Houman Homayoun
>
> Department of Computer Science, University of California, Davis &nbsp;|&nbsp; \*Equal contribution
>
> Correspondence: `sbonagiri@ucdavis.edu`

---

## Abstract

Human evaluation remains the primary standard for assessing modern AI systems, yet annotator disagreement, bias, and variability make system rankings fragile under standard majority vote aggregation. Majority vote discards annotator reliability and item-level ambiguity, often yielding unstable comparisons across annotator subsets. We introduce **STABLEVAL**, a disagreement-aware evaluation framework that models latent item correctness and annotator-specific confusion patterns to produce posterior expected item credit and calibrated agent-level scores. Unlike label-denoising approaches such as Dawid–Skene, STABLEVAL is explicitly designed for stable and uncertainty-aware system evaluation rather than hard label recovery. We formalize ranking stability as a first-class evaluation objective and demonstrate that majority vote exhibits increasing score error and ranking instability under annotator heterogeneity and adversarial noise, while STABLEVAL yields more stable and statistically grounded system rankings.

---

## Key Idea

Disagreement is not noise to suppress — it is signal to model. The central challenge in AI evaluation is not merely recovering a denoised label, but producing **stable and uncertainty-aware system comparisons** under realistic annotator variability.

STABLEVAL models each annotator with a confusion matrix $\pi_r[c, o] = P(y_{ir} = o \mid z_i = c)$ and runs EM to infer per-item posterior correctness distributions. Agent scores are then **Posterior Expected Credit (PEC)**:

$$\text{credit}(i) = \sum_{c=0}^{K-1} \gamma_{ic} \cdot v(c), \qquad S(a) = \frac{1}{|\mathcal{I}_a|} \sum_{i \in \mathcal{I}_a} \text{credit}(i)$$

where $\gamma_{ic} = P(z_i = c \mid \text{observations})$ retains the full posterior rather than collapsing to a hard label. This smooth, reliability-weighted aggregation mitigates the discrete ranking discontinuities that make majority vote inherently unstable under annotator subsampling (Proposition 1), while STABLEVAL achieves asymptotically stable rankings under its generative model (Proposition 2).

---

## Results

### MT-Bench — Agent Scores (high-disagreement regime)

| Agent | MV | DS | PEC | Δ(PEC−MV) |
|---|---|---|---|---|
| gpt-4 | 0.791 | 0.856 | 0.847 | +0.056 |
| gpt-3.5-turbo | 0.781 | 0.834 | 0.840 | +0.059 |
| claude-v1 | 0.744 | 0.794 | 0.778 | +0.035 |
| vicuna-13b-v1.2 | 0.478 | 0.619 | 0.622 | +0.144 |
| alpaca-13b | 0.150 | 0.284 | 0.290 | +0.140 |
| llama-13b | 0.084 | 0.113 | 0.131 | +0.046 |

### MT-Bench — Ranking Stability (lower = more stable)

| Method | Mean Rank Std | Mean Rank Range |
|---|---|---|
| Majority Vote | 0.259 | 1.000 |
| Dawid-Skene (Hard) | 0.223 | 0.667 |
| **Posterior Expected Credit** | **0.197** | **0.667** |

### Synthetic Stress Tests — MSE (×10⁻³)

| Configuration | Setting | MV | DS | PEC |
|---|---|---|---|---|
| Adversarial Annotators | 40% | 8.42 | **3.47** | 5.54 |
| Strict Annotators | 40% | 3.65 | **1.69** | 2.60 |
| Lenient Annotators | 40% | 4.44 | **1.87** | 2.86 |
| Hard Items | 20% | 2.62 | **1.56** | 2.36 |
| Labels per Item | 3 | 4.78 | **3.59** | 5.71 |

PEC consistently outperforms MV and achieves near-perfect Kendall's τ stability (e.g., τ = 1.000 in the no-hard-item regime). Stability gains are most pronounced in high-disagreement settings; in high-consensus regimes (ConvAbuse, QAGS), all methods converge.

---

## Repository Structure

```
STABLEVAL/
├── Disagreement-Agent-Modeling-on-Real-Datasets-7D67/   # Real-dataset evaluation
│   ├── run_evaluation.py          # Main entry point
│   ├── src/
│   │   ├── disagreement_model.py  # EM inference (Dawid-Skene / PEC)
│   │   ├── scoring.py             # Agent scoring & bootstrap CIs
│   │   ├── majority_vote.py       # Baseline aggregation
│   │   ├── data_loader.py         # CSV ingestion & preprocessing
│   │   └── visualization.py       # Plots & diagnostics
│   ├── data/                      # Place your annotation CSVs here
│   └── requirements.txt
│
└── Disagreement_synthetic_modeling-40E0/                # Synthetic controlled study
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
| `--bootstrap N` | 10 | Bootstrap iterations for 95% CIs (paper uses 1,000) |
| `--stability N` | 50 | Annotator-subset samples for ranking stability |
| `--n-classes K` | auto | Number of label classes |
| `--combine` | false | Merge all CSVs into one dataset |
| `--seed` | 42 | Random seed for reproducibility |

**Outputs** (saved to `results/<dataset>/`):

| File | Contents |
|---|---|
| `agent_scores_comparison.csv` | MV / DS / PEC scores per agent with rank changes |
| `item_ambiguity.csv` | Posterior entropy per item |
| `annotator_quality.csv` | Learned accuracy, leniency, strictness per annotator |
| `ranking_stability.csv` | Kendall's τ under annotator subsampling |
| `bootstrap_*.csv` | 95% confidence intervals |
| `evaluation_report.md` | Auto-generated summary |
| `plots/` | Score comparison, ranking, ambiguity, annotator diagnostics |

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

# Hard item probability (0.0, 0.1, 0.2)
python scripts/run_ablation.py configs/ablation_hard_prob/*.yaml

# Labels per item (3, 5, 7, 9) — with stability metrics
python scripts/run_ablation.py configs/ablation_labels/*.yaml --compute-stability

# Agent quality gaps (tight vs. wide)
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

The complete ablation study runs in approximately 5 hours on a single Intel Core i9 machine using Python's `multiprocessing` module for parallelism.

---

## Methods

| Method | Description |
|---|---|
| **Majority Vote (MV)** | Mode of raw annotations — the standard baseline |
| **Dawid–Skene Hard (DS)** | EM initialized from MV labels; optimizes latent label recovery |
| **Posterior Expected Credit (PEC)** | EM with soft initialization; preserves full posterior for stable scoring |

STABLEVAL = PEC. The key distinction from Dawid–Skene: rather than collapsing the posterior $\gamma_{ic}$ to a hard label via $\arg\max$, STABLEVAL retains the full distribution. This is not incidental — evaluation stability requires preserving uncertainty rather than discarding it.

---

## Benchmarks

| Dataset | Domain | Annotators | Pairwise Agreement | Regime |
|---|---|---|---|---|
| MT-Bench | Preference (6 models, QA / summarization) | 65 | 53.0% | High disagreement |
| ConvAbuse | Conversational safety (E.L.I.Z.A., CarbonBot) | 8 | 84.4% | High consensus |
| QAGS | Summarization factuality (CNN, XSUM) | 169 | 77.1% | High consensus |
| MSLR | Medical evidence summarization | 2 | 69.2% | Sparse annotators |

---

## Evaluation Metrics

- **MSE** — mean squared error of agent scores vs. ground truth (synthetic only)
- **Kendall's τ** — rank correlation between method-induced and ground-truth rankings
- **Ranking Stability** — expected Kendall's τ under random annotator subsampling (the primary objective)
- **Annotator Diagnostics** — learned per-annotator accuracy, leniency, and strictness from confusion matrices
- **Item Ambiguity** — entropy of posterior correctness distribution per item

---

## Dataset Licenses

| Dataset | License |
|---|---|
| lmsys/mt\_bench\_human\_judgments | CC BY-NC-SA 4.0 |
| ConvAbuse | CC BY-NC-SA 4.0 |
| allenai/mslr-shared-task | Apache 2.0 |
| QAGS | CC BY-NC-SA 4.0 |

---

## Citation

```bibtex
@inproceedings{bonagiri2026stableval,
  title     = {STABLEVAL: Disagreement-Aware and Stable Evaluation of AI Systems},
  author    = {Bonagiri, Akash and Anderias, Gerard Janno and Patil, Saee and Lai, Angelina and
               Borkar, Devang and Kang, Gezheng and Gandhi, Ishant and
               Rafatirad, Setareh and Homayoun, Houman},
  booktitle = {Proceedings of the 43rd International Conference on Machine Learning},
  series    = {PMLR 306},
  year      = {2026},
  address   = {Seoul, South Korea},
  eprint    = {2605.02122},
  archivePrefix = {arXiv},
  primaryClass  = {cs.LG},
  url       = {https://arxiv.org/abs/2605.02122}
}
```

---

## License

See [LICENSE](LICENSE).
