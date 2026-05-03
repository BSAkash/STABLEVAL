# Disagreement-Aware Agent Evaluation Report

*Generated: 2026-01-28 16:17:48*

## Data Summary

- **Total annotations**: 78
- **Unique items**: 39
- **Unique agents**: 6
- **Unique annotators**: 2
- **Average annotations per item**: 2.00
- **Unanimous agreement rate**: 69.2%
- **Average pairwise agreement**: 69.2%

### Label Distribution

- Label 0: 6 (7.7%)
- Label 1: 42 (53.8%)
- Label 2: 30 (38.5%)

## Scoring Methods

Three scoring methods were evaluated:

1. **Majority Vote (MV)**: Simple baseline using most common label per item
2. **Dawid-Skene Hard (DS)**: EM algorithm initialized with majority vote labels
3. **Posterior Expected Credit (PEC)**: Probabilistic scoring with learned annotator models

## Agent Scores Comparison

| Agent | MV Score | DS Score | PEC Score | Δ(PEC-MV) | Items |
|-------|----------|----------|-----------|-----------|-------|
| VNCH8M | 0.700 | 0.900 | 0.848 | +0.148 | 5 |
| SPNXTA | 0.750 | 0.833 | 0.787 | +0.037 | 6 |
| PX7SGV | 0.688 | 0.688 | 0.691 | +0.003 | 8 |
| JB6Z8F | 0.562 | 0.688 | 0.653 | +0.091 | 8 |
| AQ85CE | 0.625 | 0.625 | 0.625 | -0.000 | 4 |
| 8FWF5T | 0.562 | 0.562 | 0.547 | -0.015 | 8 |

## Agent Rankings

| Agent | MV Rank | DS Rank | PEC Rank | Rank Change (MV→PEC) |
|-------|---------|---------|----------|----------------------|
| VNCH8M | 2 | 1 | 1 | +1 |
| SPNXTA | 1 | 2 | 2 | -1 |
| PX7SGV | 3 | 3 | 3 | — |
| JB6Z8F | 5 | 3 | 4 | +1 |
| AQ85CE | 4 | 5 | 5 | -1 |
| 8FWF5T | 5 | 6 | 6 | -1 |

## Ranking Stability Analysis

Lower values indicate more stable rankings across annotator subsets.

| Method | Mean Rank Std Dev | Mean Rank Range |
|--------|-------------------|-----------------|
| Majority Vote | 0.503 | 1.000 |
| Dawid-Skene (Hard) | 0.503 | 1.000 |
| Posterior Expected Credit | 0.587 | 1.167 |

## Annotator Quality Analysis

Top annotators by learned accuracy:

| Annotator | Accuracy | Leniency | Strictness |
|-----------|----------|----------|------------|
| annotator_0 | 0.794 | 0.253 | 0.219 |
| annotator_1 | 0.652 | 0.292 | 0.145 |

## Most Ambiguous Items

Items with highest uncertainty in true label:

| Item ID | Ambiguity | Confidence | Predicted Class |
|---------|-----------|------------|-----------------|
| mslr_Cochrane_CD001487_SPNXTA | 0.261 | 0.739 | 1 |
| mslr_Cochrane_CD002873_8FWF5T | 0.261 | 0.739 | 1 |
| mslr_Cochrane_CD008510_JB6Z8F | 0.257 | 0.743 | 2 |
| mslr_Cochrane_CD006883_VNCH8M | 0.257 | 0.743 | 2 |
| mslr_Cochrane_CD008493_SPNXTA | 0.257 | 0.743 | 2 |
| mslr_Cochrane_CD008493_JB6Z8F | 0.257 | 0.743 | 2 |
| mslr_Cochrane_CD003700_VNCH8M | 0.257 | 0.743 | 2 |
| mslr_Cochrane_CD003700_AQ85CE | 0.132 | 0.868 | 1 |
| mslr_Cochrane_CD003700_JB6Z8F | 0.132 | 0.868 | 1 |
| mslr_Cochrane_CD003700_PX7SGV | 0.132 | 0.868 | 1 |

## Key Findings

- **Score correlation (MV vs PEC)**: 0.824
- **Maximum score change**: 0.148 (Agent: VNCH8M)
- **Agents with rank changes**: 5 / 6
- **Average item ambiguity**: 0.103
- **Highly ambiguous items (>0.3)**: 0

## Conclusion

The disagreement-aware methods (Dawid-Skene and Posterior Expected Credit) 
provide refined agent scores that account for annotator reliability and item ambiguity. 
The ranking stability analysis shows comparable results across methods.
