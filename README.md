# Customer Complaint Analysis: What Makes Customers Unhappy?

## Business question
Local businesses collect thousands of reviews but cannot read them all. Across about 11,500 Phoenix-area businesses (restaurants, shops and services), what do unhappy customers complain about most, which complaints make them angriest, and where should an operator focus first?

## Data
Yelp Phoenix Academic Dataset (training review file) from the Kaggle "RecSys Challenge 2013: Yelp business rating prediction" competition: https://www.kaggle.com/c/yelp-recsys-2013
Used under the academic dataset terms of use. The raw data is not included in this repo. To run the notebook, download the review and business files from Kaggle, upload them to Google Drive and set their paths in the notebook.
The business file from the same dataset (categories) was joined to the reviews to separate food businesses (category "Restaurants" or "Food") from all others.

## Methods used
Text cleaning, tokenization, stop-word handling (negations kept), lemmatization, bag of words, n-grams, TF-IDF, sentiment classification (Logistic Regression), topic modelling (LDA), keyword-based theme extraction, logistic regression for complaint severity.

## What I did
- Cleaned 229,907 reviews (2005-2013): removed 6 empty reviews and 242 duplicates, leaving 229,659 reviews from 11,537 businesses and 45,980 reviewers.
- Built a text classifier (TF-IDF + Logistic Regression) that detects negative (1-2 star) reviews. It flags **92% of truly negative reviews** (recall) with 93.5% accuracy on 38,865 unseen reviews. For reference, always guessing "positive" would give 80.2% accuracy. Negation words such as "not" and "never" were kept, because "not good" means the opposite of "good".
- The classifier shows that review text alone can identify unhappy customers, which is useful where there is no star rating (survey comments, support emails, chat). The theme analysis then uses 1-2 star reviews as the labelled set.
- Used topic modelling (LDA) on the 38,398 negative reviews to explore what they talk about. The topics were mixed because the data covers many business types, so I used keyword rules to measure the main complaint themes.
- Checked the keyword rules by hand: 27 of 32 randomly sampled reviews (8 per theme) matched their theme.
- Measured how common each complaint is, how often it appears in 1-star reviews, and how it changed by year.
- Joined the dataset's business file to split the results into food businesses (category "Restaurants" or "Food") and all other businesses.

## Results
![Complaint themes](complaint_themes.png)

| Complaint theme | Share of negative reviews | Share that are 1-star | Effect on 1-star odds* |
|---|---|---|---|
| Food quality | 23.5% | 41.9% | 0.85x |
| Slow service / waiting | 22.8% | 47.4% | 1.06x |
| Rude or unhelpful staff | 11.8% | 61.8% | 2.08x |
| Cleanliness and hygiene | 5.4% | 59.0% | 1.79x |

*From a logistic regression on the four themes, among negative reviews. About 45.5% of all negative reviews are 1-star. This shows association, not cause.

## Key findings
1. Food quality and slow service are the most common complaints (about a quarter of negative reviews each).
2. Rude staff and dirty conditions are less common but far more severe: 62% and 59% of those reviews are 1-star, and they raise the odds of a 1-star rating by 2.1x and 1.8x.
3. Slow service is common but does not make reviews noticeably angrier on its own (odds ratio about 1.06).
4. Rude-staff complaints rose from 8.9% of negative reviews in 2008 to 13.8% in 2012. Slow service stayed flat (about 22-23%).
5. The main complaint depends on the type of business: food quality for food businesses, and waiting and rude staff for other businesses (see the table below).

## What the model learned
The words that push a review towards "negative" were: worst, bland, mediocre, not worth, rude, terrible, disappointing, overpriced, awful, not good, disgusting. Words pushing towards "positive" were: great, delicious, amazing, love, awesome, excellent, best, friendly. "Not disappointed" ranked as positive, which shows why negation words were kept.

## How complaints changed over time (% of each year's negative reviews)
| Year | Slow service | Rude staff | Food quality | Cleanliness |
|---|---|---|---|---|
| 2008 | 21.4 | 8.9 | 24.1 | 6.5 |
| 2010 | 22.7 | 11.3 | 24.9 | 4.8 |
| 2012 | 23.5 | 13.8 | 22.2 | 5.6 |

Rude-staff complaints rose steadily; slow service stayed roughly flat. 2013 is left out because it has only 277 reviews.

## Food businesses vs other businesses
Joining the business categories shows that the main complaint depends on the type of business (% of negative reviews in each group mentioning the theme):

| Theme | Food businesses (28,930 negative reviews) | Other businesses (9,468 negative reviews) |
|---|---|---|
| Food quality | 29.2% | 5.9% |
| Slow service / waiting | 23.4% | 21.0% |
| Rude or unhelpful staff | 9.9% | 17.4% |
| Cleanliness and hygiene | 5.0% | 6.5% |

For food businesses, food quality and waiting dominate. For other businesses (shops, services and so on), waiting and rude staff lead, and rude-staff reviews are very severe (72.9% are 1-star, against 55.4% for food businesses).

## Recommendation (what this analysis would suggest)
The data is from 2005-2013, so the exact percentages may not match today's customers. The value here is the method and the kinds of findings it produces; the same analysis can be re-run on current reviews before acting. These are market-wide patterns, not advice for one specific business.

### Two different goals, two different priorities
The four complaints matter in different ways, so I would not rank them on one list.

**1. Fix first for reach: waiting times and food quality.**
These are the two most common complaints. Slow service appears in 8,759 negative reviews (22.8%) and food quality in 9,011 (23.5%), so about one unhappy customer in four mentions each. Fixing them reaches the most customers. They do not, however, make reviews noticeably angrier: the odds of a 1-star rating are about the same with or without them (1.06x for waiting and 0.85x for food quality). They upset many people a little, not a few people a lot.

**2. Fix next for damage: staff conduct and cleanliness.**
These are smaller (rude or unhelpful staff in 11.8% of negative reviews, cleanliness in 5.4%) but far more severe. 62% of rude-staff reviews and 59% of cleanliness reviews are 1-star, and they raise the odds of a 1-star rating by 2.1x and 1.8x. Rude-staff complaints also grew from 8.9% of negative reviews in 2008 to 13.8% in 2012, so the problem was getting bigger, not smaller. Both are usually cheaper to prevent than to repair afterwards (training, regular audits). The staff-conduct problem is bigger outside food businesses (17.4% of their negative reviews vs 9.9%, and 72.9% of those are 1-star).

### Ideas to test and how to measure them
The data shows what customers complain about, not what fixes it, so these actions are hypotheses to test.

| Complaint | Idea to pilot | Measure of success |
|---|---|---|
| Slow service / waiting | Peak-hour staffing, quoted wait times | Share of 1-2 star reviews mentioning waiting |
| Food quality | Kitchen quality checks, menu review | Share of 1-2 star reviews mentioning food quality |
| Rude or unhelpful staff | Service-recovery training | Share of 1-2 star reviews mentioning staff conduct |
| Cleanliness | Regular hygiene audits | Share of 1-2 star reviews mentioning cleanliness |

### How I would test it
Run each idea at a group of pilot locations and compare the change in the measure against similar locations without the change, over the same period. Because the average business here has only about 20 reviews, a real pilot would need many locations pooled together (for example, a chain). Review-based measures can also shift because of who chooses to write reviews, so they would be read alongside other measures such as repeat visits or complaints handled. This is a design only; I did not run it.

## Limitations
- The data covers the Phoenix, Arizona area only and is old (2005-2013), so results may not apply elsewhere or today.
- "Food business" means a business with the category "Restaurants" or "Food". Bars and other businesses without those tags, or with no category, count as "other". The food-quality rule also matches 5.9% of non-food reviews, probably words like "dry" or "raw" used about other things.
- The theme rules are keyword-based and approximate. They matched 27 of 32 sampled reviews, so the shares are best read as rough size, not exact counts.
- 49.6% of negative reviews match none of the four themes, and a review can belong to more than one theme.
- The classifier wrongly flags some positive reviews as negative (precision for negative reviews is 79%), and it misses mild or mixed negative reviews.
- Findings show association, not cause.

## Further analysis
- **User file** (reviewer's review count and average rating): check whether frequent or generally harsh reviewers complain about different things.
- **Check-in file** (check-ins by hour and day per business): explore whether waiting complaints are more common at busy businesses. These counts are per business, so this link would be indicative only.
- Compare complaint patterns across more detailed categories (cafes, bars, retail) and neighbourhoods.
- Re-run the pipeline on current reviews to check whether the shares and the rude-staff trend still hold, and run the pilot test described above.

## Files
- `customer_complaint_analysis.ipynb`: the full analysis (cleaning, exploration, model, themes, food vs other businesses)
- `complaint_themes.png`: the main chart
- `DECISIONS.md`: why I made each choice

Tools: Python, pandas, scikit-learn, NLTK, matplotlib.