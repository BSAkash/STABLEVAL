# Disagreement-Aware Agent Evaluation Report

*Generated: 2026-01-28 17:53:31*

## Data Summary

- **Total annotations**: 2,859
- **Unique items**: 953
- **Unique agents**: 2
- **Unique annotators**: 169
- **Average annotations per item**: 3.00
- **Unanimous agreement rate**: 65.6%
- **Average pairwise agreement**: 77.1%

### Label Distribution

- Label 0: 968 (33.9%)
- Label 1: 1,891 (66.1%)

## Scoring Methods

Three scoring methods were evaluated:

1. **Majority Vote (MV)**: Simple baseline using most common label per item
2. **Dawid-Skene Hard (DS)**: EM algorithm initialized with majority vote labels
3. **Posterior Expected Credit (PEC)**: Probabilistic scoring with learned annotator models

## Agent Scores Comparison

| Agent | MV Score | DS Score | PEC Score | Δ(PEC-MV) | Items |
|-------|----------|----------|-----------|-----------|-------|
| CNN | 0.744 | 0.723 | 0.722 | -0.022 | 714 |
| XSUM | 0.485 | 0.531 | 0.529 | +0.044 | 239 |

## Agent Rankings

| Agent | MV Rank | DS Rank | PEC Rank | Rank Change (MV→PEC) |
|-------|---------|---------|----------|----------------------|
| CNN | 1 | 1 | 1 | — |
| XSUM | 2 | 2 | 2 | — |

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
| worker_171 | 0.999 | 0.500 | 0.500 |
| worker_13 | 0.998 | 0.501 | 0.499 |
| worker_85 | 0.998 | 0.500 | 0.500 |
| worker_160 | 0.998 | 0.501 | 0.499 |
| worker_155 | 0.998 | 0.501 | 0.499 |
| worker_82 | 0.997 | 0.499 | 0.501 |
| worker_158 | 0.997 | 0.500 | 0.500 |
| worker_54 | 0.997 | 0.501 | 0.499 |
| worker_73 | 0.997 | 0.500 | 0.500 |
| worker_74 | 0.996 | 0.501 | 0.499 |

## Most Ambiguous Items

Items with highest uncertainty in true label:

| Item ID | Ambiguity | Confidence | Predicted Class |
|---------|-----------|------------|-----------------|
| cnn_227_sent2_CNN | 0.500 | 0.500 | 1 |
| xsum_171_sent0_XSUM | 0.496 | 0.504 | 1 |
| cnn_143_sent0_CNN | 0.477 | 0.523 | 1 |
| cnn_21_sent1_CNN | 0.477 | 0.523 | 1 |
| cnn_65_sent2_CNN | 0.473 | 0.527 | 0 |
| cnn_81_sent2_CNN | 0.470 | 0.530 | 0 |
| cnn_111_sent0_CNN | 0.466 | 0.534 | 0 |
| cnn_87_sent1_CNN | 0.465 | 0.535 | 0 |
| xsum_28_sent0_XSUM | 0.462 | 0.538 | 0 |
| cnn_115_sent0_CNN | 0.460 | 0.540 | 0 |

## Key Findings

- **Score correlation (MV vs PEC)**: 1.000
- **Maximum score change**: 0.044 (Agent: XSUM)
- **Agents with rank changes**: 0 / 2
- **Average item ambiguity**: 0.047
- **Highly ambiguous items (>0.3)**: 51

## Conclusion

The disagreement-aware methods (Dawid-Skene and Posterior Expected Credit) 
provide refined agent scores that account for annotator reliability and item ambiguity. 
The ranking stability analysis shows comparable results across methods.
