# Disagreement-Aware Agent Evaluation Report

*Generated: 2026-01-28 16:17:35*

## Data Summary

- **Total annotations**: 1,405
- **Unique items**: 281
- **Unique agents**: 4
- **Unique annotators**: 465
- **Average annotations per item**: 5.00
- **Unanimous agreement rate**: 100.0%
- **Average pairwise agreement**: 100.0%

### Label Distribution

- Label 0: 710 (50.5%)
- Label 1: 155 (11.0%)
- Label 2: 540 (38.4%)

## Scoring Methods

Three scoring methods were evaluated:

1. **Majority Vote (MV)**: Simple baseline using most common label per item
2. **Dawid-Skene Hard (DS)**: EM algorithm initialized with majority vote labels
3. **Posterior Expected Credit (PEC)**: Probabilistic scoring with learned annotator models

## Agent Scores Comparison

| Agent | MV Score | DS Score | PEC Score | Δ(PEC-MV) | Items |
|-------|----------|----------|-----------|-----------|-------|
| 1 | 0.564 | 0.564 | 0.538 | -0.026 | 70 |
| 3 | 0.432 | 0.364 | 0.441 | +0.008 | 59 |
| 2 | 0.381 | 0.328 | 0.421 | +0.040 | 67 |
| 0 | 0.388 | 0.365 | 0.417 | +0.029 | 85 |

## Agent Rankings

| Agent | MV Rank | DS Rank | PEC Rank | Rank Change (MV→PEC) |
|-------|---------|---------|----------|----------------------|
| 1 | 1 | 1 | 1 | — |
| 3 | 2 | 3 | 2 | — |
| 2 | 4 | 4 | 3 | +1 |
| 0 | 3 | 2 | 4 | -1 |

## Ranking Stability Analysis

Lower values indicate more stable rankings across annotator subsets.

| Method | Mean Rank Std Dev | Mean Rank Range |
|--------|-------------------|-----------------|
| Majority Vote | 0.000 | 0.000 |
| Dawid-Skene (Hard) | 0.236 | 0.500 |
| Posterior Expected Credit | 0.314 | 1.250 |

## Annotator Quality Analysis

Top annotators by learned accuracy:

| Annotator | Accuracy | Leniency | Strictness |
|-----------|----------|----------|------------|
| 7350 | 0.993 | 0.334 | 0.332 |
| 3520 | 0.993 | 0.334 | 0.332 |
| 4090 | 0.992 | 0.332 | 0.336 |
| 1671 | 0.992 | 0.332 | 0.336 |
| 4656 | 0.992 | 0.332 | 0.336 |
| 8363 | 0.992 | 0.332 | 0.335 |
| 4755 | 0.992 | 0.332 | 0.335 |
| 10077 | 0.991 | 0.336 | 0.336 |
| 9259 | 0.991 | 0.335 | 0.336 |
| 3846 | 0.990 | 0.335 | 0.336 |

## Most Ambiguous Items

Items with highest uncertainty in true label:

| Item ID | Ambiguity | Confidence | Predicted Class |
|---------|-----------|------------|-----------------|
| mhs_43618_3 | 0.531 | 0.469 | 0 |
| mhs_21665_2 | 0.531 | 0.469 | 0 |
| mhs_27206_2 | 0.530 | 0.470 | 0 |
| mhs_2546_0 | 0.526 | 0.474 | 0 |
| mhs_31761_2 | 0.525 | 0.475 | 0 |
| mhs_22704_2 | 0.525 | 0.475 | 0 |
| mhs_49143_3 | 0.525 | 0.475 | 0 |
| mhs_47777_3 | 0.523 | 0.477 | 0 |
| mhs_19104_0 | 0.523 | 0.477 | 0 |
| mhs_45499_3 | 0.520 | 0.480 | 0 |

## Key Findings

- **Score correlation (MV vs PEC)**: 0.994
- **Maximum score change**: 0.040 (Agent: 2)
- **Agents with rank changes**: 2 / 4
- **Average item ambiguity**: 0.127
- **Highly ambiguous items (>0.3)**: 50

## Conclusion

The disagreement-aware methods (Dawid-Skene and Posterior Expected Credit) 
provide refined agent scores that account for annotator reliability and item ambiguity. 
The ranking stability analysis shows comparable results across methods.
