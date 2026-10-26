# DSA4060XA – Week 6 Guided Evaluation Lab

**Name:** Sundus Moheddin
**Student ID:** 668726

## Lab objective
Evaluate three recommender systems on a small movie-ratings dataset (12 users, 20 movies, 120 ratings) using a leakage-free train/test split (each user's most recent rating held out), and recommend a model for a stated business objective: *help users discover relevant movies without the same popular titles dominating every list.*

## Models compared
1. **Popularity baseline:** movie mean rating (rating prediction) and mean rating, then count (ranking).
2. **Content-based filtering:** genre one-hot features, rating-weighted user profile, cosine similarity mapped to a 1–5 score.
3. **Item-based collaborative filtering:** item–item cosine similarity on the training rating matrix, similarity-weighted average of the user's own ratings.

## Results (Top-K = 3)

| Model | MAE | RMSE | Precision@3 | Recall@3 | NDCG@3 | Coverage | Novelty | Diversity |
|---|---|---|---|---|---|---|---|---|
| Popularity Baseline | 1.086 | 1.201 | 0.067 | 0.20 | 0.10 | 0.35 | 1.689 | 0.736 |
| Content-Based | 1.094 | 1.381 | 0.067 | 0.20 | 0.20 | 0.70 | 1.344 | 0.542 |
| Item-Based CF | **0.924** | **1.070** | 0.000 | 0.00 | 0.00 | **0.85** | **1.695** | **0.780** |

## Best model for rating prediction
**Item-Based CF** has the lowest MAE (0.924) and RMSE (1.070). It beats the baseline by about 0.16 MAE. Content-Based does not beat the baseline.

## Best model for ranking
**Content-Based** has the highest NDCG@3 (0.20), tied with Popularity on Precision@3 and Recall@3 (one hit each). Item-CF had no Top-3 hits. Only 5 of 12 users had a relevant (rating ≥ 4) held-out item and each has one test item, so Precision@3 is capped at 0.33 and the differences amount to a single user.

## Coverage, novelty and diversity findings
- **Coverage:** Item-CF 85% > Content 70% > Popularity 35% (the popularity baseline recommends only 7 of 20 movies).
- **Novelty:** Item-CF (1.695) ≈ Popularity (1.689) > Content (1.344). The popularity baseline ranks by mean rating, not interaction count, so its top picks are not necessarily the most-rated.
- **Diversity:** Item-CF (0.780) > Popularity (0.736) > Content (0.542). Content-based lists are genre-homogeneous (filter-bubble effect).

## Final recommended model
**Item-Based Collaborative Filtering** for the streaming-service scenario. It has the best coverage, novelty, diversity and rating accuracy, which fits the goal of discovery without popular titles dominating. The trade-off is zero measured Top-3 hits here; Content-Based (or a hybrid) is the alternative if raw relevance is prioritised.

## Limitations
1. **Tiny data:** 12 users × 20 movies, one held-out item per user. Only 5 users have a relevant test item, so ranking metrics change with a single user's hit and are not statistically reliable.
2. **Single temporal split:** no repeated splits, cross-validation or confidence intervals.
3. **Genre-only item features:** the content model ignores cast, director, plot, etc., and the user profile is a rating-weighted mean that over-rewards frequently watched genres.
4. **Arbitrary relevance threshold** (rating ≥ 4) and a "popularity" baseline that ranks by mean rating with very few ratings for some movies.
5. **Cosine similarity with missing ratings filled as 0** in item-CF treats "unrated" as "disliked", which can distort similarities, and there is no cold-start handling.

## Extension challenge
Option C (compare personalisation): the popularity baseline produced only 8 distinct Top-3 lists for 12 users (it personalises only by removing seen items), whereas Content-Based and Item-CF produced 12 distinct lists.

## Reproducibility
Run `DSA4060XA_Week6_Guided_Evaluation_Lab.ipynb` top to bottom (restart kernel and run all). Requires `pandas`, `numpy`, `scikit-learn`, `matplotlib`. The two CSV files must be in the same folder.
