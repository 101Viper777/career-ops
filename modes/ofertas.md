# Mode: ofertas - Multi-Offer Comparison

Use a weighted matrix with 10 dimensions:

| Dimension | Weight | 1-5 Criteria |
| --- | --- | --- |
| North Star alignment | 25% | 5 = exact target role, 1 = unrelated |
| CV match | 15% | 5 = 90%+ match, 1 = below 40% |
| Level (seniority) | 15% | 5 = staff+, 4 = senior, 3 = mid-senior, 2 = mid, 1 = junior |
| Estimated comp | 10% | 5 = top quartile, 1 = below market |
| Growth trajectory | 10% | 5 = clear path, 1 = dead end |
| Remote quality | 5% | 5 = fully remote async, 1 = onsite only |
| Company reputation | 5% | 5 = strong signals, 1 = red flags |
| Tech stack modernity | 5% | 5 = modern AI/ML stack, 1 = legacy stack |
| Time to offer | 5% | 5 = fast process, 1 = 6+ months |
| Cultural signals | 5% | 5 = builder culture, 1 = heavy bureaucracy |

For each offer:
- score each dimension
- calculate weighted total

Return:
- final ranking
- recommendation with time-to-offer trade-off notes

If offers are missing from context, ask the user to provide text, URLs, or tracker references.
