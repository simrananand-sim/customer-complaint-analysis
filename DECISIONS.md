# Decisions

1. **Used the review file across all businesses.** The dataset covers about 11,500 Phoenix-area businesses with no category column, so I framed the question as market-wide complaint patterns, not advice for one business.
2. **Removed duplicates before modelling.** 242 duplicate review texts were dropped so the same review cannot sit in both the training and test sets.
3. **Kept negation words** ("not", "no", "never"). Standard stop-word lists remove them, which turns "not good" into "good".
4. **Dropped 3-star reviews from the classifier.** They are mixed, so the model learns a cleaner negative vs positive split. The theme analysis uses 1-2 star reviews only.
5. **Prioritised recall on negative reviews.** The goal is to find unhappy customers, so missing them costs more than a few false alarms. I used balanced class weights and compared against the 80.2% "always guess positive" baseline.
6. **Logistic Regression.** It is simple and fast, and I can read which words drive each prediction.
7. **LDA topics were mixed, so I did not rely on them for the final themes.** The data blends restaurants, shops and services, so topics blurred together. I used LDA to explore and keyword rules to measure.
8. **Tightened the keyword rules after checking samples.** The first version matched things like "wait staff" and "didn't seem too expensive". I rewrote the rules and checked 8 random reviews per theme (27 of 32 correct).
9. **Dropped the Price and Wrong-order themes.** Their rules were too noisy to report honestly.
10. **Used 1-star share and odds ratios, not only volume.** The most common complaint is not always the one that hurts most.
11. **No causal claims.** The data shows which complaints go with angry reviews, not what causes them. The recommended pilot test is a design only; I did not run it.
12. **Raw data kept out of the repo.** The dataset is used under the Yelp academic dataset terms, so the README links to Kaggle instead.