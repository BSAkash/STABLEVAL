# Disagreement-Aware Agent Evaluation Report

*Generated: 2026-01-28 16:20:35*

## Data Summary

- **Total annotations**: 6,710
- **Unique items**: 960
- **Unique agents**: 6
- **Unique annotators**: 65
- **Average annotations per item**: 6.99
- **Unanimous agreement rate**: 16.6%
- **Average pairwise agreement**: 53.0%

### Label Distribution

- Label 0: 2,575 (38.4%)
- Label 1: 1,560 (23.2%)
- Label 2: 2,575 (38.4%)

## Scoring Methods

Three scoring methods were evaluated:

1. **Majority Vote (MV)**: Simple baseline using most common label per item
2. **Dawid-Skene Hard (DS)**: EM algorithm initialized with majority vote labels
3. **Posterior Expected Credit (PEC)**: Probabilistic scoring with learned annotator models

## Agent Scores Comparison

| Agent | MV Score | DS Score | PEC Score | Δ(PEC-MV) | Items |
|-------|----------|----------|-----------|-----------|-------|
| gpt-4 | 0.791 | 0.856 | 0.847 | +0.056 | 160 |
| gpt-3.5-turbo | 0.781 | 0.834 | 0.840 | +0.059 | 160 |
| claude-v1 | 0.744 | 0.794 | 0.778 | +0.035 | 160 |
| vicuna-13b-v1.2 | 0.478 | 0.619 | 0.622 | +0.144 | 160 |
| alpaca-13b | 0.150 | 0.284 | 0.290 | +0.140 | 160 |
| llama-13b | 0.084 | 0.113 | 0.131 | +0.046 | 160 |

## Agent Rankings

| Agent | MV Rank | DS Rank | PEC Rank | Rank Change (MV→PEC) |
|-------|---------|---------|----------|----------------------|
| gpt-4 | 1 | 1 | 1 | — |
| gpt-3.5-turbo | 2 | 2 | 2 | — |
| claude-v1 | 3 | 3 | 3 | — |
| vicuna-13b-v1.2 | 4 | 4 | 4 | — |
| alpaca-13b | 5 | 5 | 5 | — |
| llama-13b | 6 | 6 | 6 | — |

## Ranking Stability Analysis

Lower values indicate more stable rankings across annotator subsets.

| Method | Mean Rank Std Dev | Mean Rank Range |
|--------|-------------------|-----------------|
| Majority Vote | 0.259 | 1.000 |
| Dawid-Skene (Hard) | 0.223 | 0.667 |
| Posterior Expected Credit | 0.197 | 0.667 |

## Annotator Quality Analysis

Top annotators by learned accuracy:

| Annotator | Accuracy | Leniency | Strictness |
|-----------|----------|----------|------------|
| expert_3 | 0.821 | 0.296 | 0.356 |
| expert_53 | 0.806 | 0.281 | 0.357 |
| expert_55 | 0.778 | 0.276 | 0.388 |
| expert_46 | 0.771 | 0.239 | 0.352 |
| expert_26 | 0.754 | 0.336 | 0.337 |
| author_2 | 0.753 | 0.286 | 0.308 |
| expert_2 | 0.748 | 0.275 | 0.355 |
| expert_27 | 0.746 | 0.311 | 0.386 |
| expert_40 | 0.742 | 0.316 | 0.366 |
| expert_9 | 0.742 | 0.216 | 0.354 |

## Most Ambiguous Items

Items with highest uncertainty in true label:

| Item ID | Ambiguity | Confidence | Predicted Class |
|---------|-----------|------------|-----------------|
| mtbench_87_turn2_claude-v1 | 0.597 | 0.403 | 1 |
| mtbench_125_turn1_claude-v1 | 0.565 | 0.435 | 2 |
| mtbench_101_turn2_alpaca-13b | 0.551 | 0.449 | 2 |
| mtbench_86_turn1_llama-13b | 0.550 | 0.450 | 1 |
| mtbench_95_turn1_alpaca-13b | 0.543 | 0.457 | 1 |
| mtbench_90_turn1_claude-v1 | 0.539 | 0.461 | 1 |
| mtbench_117_turn1_claude-v1 | 0.513 | 0.487 | 2 |
| mtbench_86_turn1_claude-v1 | 0.505 | 0.495 | 1 |
| mtbench_130_turn2_alpaca-13b | 0.499 | 0.501 | 1 |
| mtbench_139_turn1_alpaca-13b | 0.497 | 0.503 | 0 |

## Key Findings

- **Score correlation (MV vs PEC)**: 0.989
- **Maximum score change**: 0.144 (Agent: vicuna-13b-v1.2)
- **Agents with rank changes**: 0 / 6
- **Average item ambiguity**: 0.065
- **Highly ambiguous items (>0.3)**: 79

## Conclusion

The disagreement-aware methods (Dawid-Skene and Posterior Expected Credit) 
provide refined agent scores that account for annotator reliability and item ambiguity. 
The PEC method shows improved ranking stability compared to majority vote.
