# DigiCow Farmer Adoption Prediction

## Overview

Access to high-quality agricultural training is only the first step toward improving smallholder farm productivity. The real challenge lies in understanding **which farmers actually adopt improved practices after receiving training**.

**DigiCow** supports smallholder farmers through digital tools, extension services, and targeted training programmes. However, adoption rates remain uneven, and identifying farmers who are likely to adopt early can help extension services deploy resources more effectively.

This project builds a **machine learning model to predict the probability that a farmer adopts a DigiCow-supported practice after training**.

The predictions enable DigiCow to:

- Prioritise follow-up support
- Target extension resources more effectively
- Identify farmers at risk of non-adoption
- Improve the design of agricultural training programmes

---

# Problem Statement

Given information available **at the time of a farmer’s first training**, the goal is to predict the probability that the farmer adopts the recommended practice within three time windows:

- **7 days**
- **90 days**
- **120 days**

This is a **multi-target probabilistic classification problem**, where the model must output **well-calibrated probabilities** for each horizon.

---

# Evaluation

The challenge uses a **multi-metric evaluation framework**.

Two metrics are computed for each target horizon:

### Log Loss (75%)

Log Loss measures the **quality of predicted probabilities**.  
It penalises confident but incorrect predictions more heavily, encouraging models to produce well-calibrated probabilities.

### ROC-AUC (25%)

ROC-AUC measures the model's ability to **rank adopters above non-adopters** across all thresholds.  
This metric is especially useful for **imbalanced datasets**.

### Final Score

The leaderboard score is calculated as a weighted average:

```

Final Score = 0.75 * LogLoss + 0.25 * ROC-AUC

```

---

# Model Performance

Out-of-fold (OOF) cross-validation results:

| Target | ROC-AUC | LogLoss |
|------|------|------|
| adopted_within_07_days | **0.9888** | **0.0222** |
| adopted_within_90_days | **0.9836** | **0.0280** |
| adopted_within_120_days | **0.9820** | **0.0347** |

**Weighted Competition Score**

```

0.965786

```

These results demonstrate strong predictive performance across all adoption horizons.

---

# Feature Engineering

Feature engineering focuses on capturing **farmer behaviour, training patterns, and contextual signals available at training time**.

### Farmer Activity Features

These features describe the historical training behaviour of each farmer:

- Number of trainings attended
- Diversity of training topics
- Trainer exposure
- Training frequency

These signals capture **farmer engagement and learning behaviour**.

---

### Trainer and Programme Features

These features capture the context of the training event:

- Trainer identity
- Training topic distribution
- Historical trainer effectiveness
- Training programme characteristics

These signals help capture **variability in training impact**.

---

### Aggregated Behavioural Signals

Group-based aggregation features were used to capture behavioural patterns:

- Farmer-level aggregates
- Trainer-level aggregates
- Topic-level statistics

These features help identify **patterns associated with adoption behaviour**.

---

# Key Modeling Insight

A critical discovery during experimentation was that:

**Directly using engineered date features significantly hurt model performance.**

Instead, the most effective strategy was:

- **Avoid explicit date-derived features**
- Use the **training day only for group-based aggregations**

Examples include:

- Farmer historical activity before the current training
- Trainer-level adoption trends
- Topic adoption behaviour

This approach preserved useful temporal signals **without introducing leakage or spurious patterns**.

---

# Modeling Approach

The final solution uses **gradient boosting models** for probabilistic prediction.

Separate models were trained for each target horizon:

- **7-day adoption**
- **90-day adoption**
- **120-day adoption**

Each model outputs calibrated probabilities representing the likelihood of adoption within the specified time window.

---

# Impact

Predicting farmer adoption allows DigiCow to:

- Improve extension service targeting
- Increase adoption of improved farming practices
- Maximise the impact of training programmes
- Support smallholder farmers more effectively

By identifying likely adopters early, extension programmes can **focus resources where they create the greatest impact**.

---

### Competition Leaderboard Performance

[[16th Place](https://zindi.africa/competitions/digicow-farmer-training-adoption-challenge/leaderboard)]


### My working- Feature Engineeering:

* Topic popularity(max_, min_, range_, ratio_, is_rare)
* Topic combination frequency- using **hashlib**
