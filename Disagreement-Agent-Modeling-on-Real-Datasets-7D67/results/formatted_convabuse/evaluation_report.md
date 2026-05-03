# Disagreement-Aware Agent Evaluation Report

*Generated: 2026-01-28 16:16:13*

## Data Summary

- **Total annotations**: 9,571
- **Unique items**: 2,894
- **Unique agents**: 2
- **Unique annotators**: 8
- **Average annotations per item**: 3.31
- **Unanimous agreement rate**: 78.4%
- **Average pairwise agreement**: 84.4%

### Label Distribution

- Label 0: 7,839 (81.9%)
- Label 1: 702 (7.3%)
- Label 2: 1,030 (10.8%)

## Scoring Methods

Three scoring methods were evaluated:

1. **Majority Vote (MV)**: Simple baseline using most common label per item
2. **Dawid-Skene Hard (DS)**: EM algorithm initialized with majority vote labels
3. **Posterior Expected Credit (PEC)**: Probabilistic scoring with learned annotator models

## Agent Scores Comparison

| Agent | MV Score | DS Score | PEC Score | Δ(PEC-MV) | Items |
|-------|----------|----------|-----------|-----------|-------|
| E.L.I.Z.A. | 0.154 | 0.186 | 0.193 | +0.039 | 2547 |
| CarbonBot | 0.029 | 0.039 | 0.043 | +0.014 | 347 |

## Agent Rankings

| Agent | MV Rank | DS Rank | PEC Rank | Rank Change (MV→PEC) |
|-------|---------|---------|----------|----------------------|
| E.L.I.Z.A. | 1 | 1 | 1 | — |
| CarbonBot | 2 | 2 | 2 | — |

## Ranking Stability Analysis

Lower values indicate more stable rankings across annotator subsets.

| Method | Mean Rank Std Dev | Mean Rank Range |
|--------|-------------------|-----------------|
| Majority Vote | 0.000 | 0.000 |
| Dawid-Skene (Hard) | 0.000 | 0.000 |
| Posterior Expected Credit | 0.000 | 0.000 |

## Annotator Quality Analysis

Top annotators by learned accuracy:

| Annotator | Accuracy | Leniency | Strictness |
|-----------|----------|----------|------------|
| Annotator1 | 0.799 | 0.363 | 0.399 |
| Annotator4 | 0.798 | 0.374 | 0.463 |
| Annotator5 | 0.782 | 0.477 | 0.329 |
| Annotator2 | 0.760 | 0.338 | 0.473 |
| Annotator6 | 0.751 | 0.347 | 0.506 |
| Annotator8 | 0.733 | 0.364 | 0.499 |
| Annotator3 | 0.647 | 0.258 | 0.567 |
| Annotator7 | 0.519 | 0.181 | 0.745 |

## Most Ambiguous Items

Items with highest uncertainty in true label:

| Item ID | Ambiguity | Confidence | Predicted Class |
|---------|-----------|------------|-----------------|
| convabuse_298297.0_E.L.I.Z.A. | 0.564 | 0.436 | 1 |
| convabuse_193305.0_E.L.I.Z.A. | 0.497 | 0.503 | 1 |
| convabuse_104335.0_E.L.I.Z.A. | 0.491 | 0.509 | 1 |
| convabuse_261585.0_E.L.I.Z.A. | 0.488 | 0.512 | 1 |
| convabuse_321553.0_E.L.I.Z.A. | 0.486 | 0.514 | 2 |
| convabuse_301675.0_E.L.I.Z.A. | 0.483 | 0.517 | 1 |
| convabuse_244402.0_E.L.I.Z.A. | 0.471 | 0.529 | 1 |
| convabuse_444222.0_E.L.I.Z.A. | 0.469 | 0.531 | 1 |
| convabuse_92200.0_E.L.I.Z.A. | 0.468 | 0.532 | 1 |
| convabuse_24975.0_E.L.I.Z.A. | 0.462 | 0.538 | 1 |

## Key Findings

- **Score correlation (MV vs PEC)**: 1.000
- **Maximum score change**: 0.039 (Agent: E.L.I.Z.A.)
- **Agents with rank changes**: 0 / 2
- **Average item ambiguity**: 0.049
- **Highly ambiguous items (>0.3)**: 107

## Conclusion

The disagreement-aware methods (Dawid-Skene and Posterior Expected Credit) 
provide refined agent scores that account for annotator reliability and item ambiguity. 
The ranking stability analysis shows comparable results across methods.
