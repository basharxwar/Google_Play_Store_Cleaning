# Google Play Store Apps: Data Cleaning (Milestone 2)

## Dataset
- **Source:** Kaggle, "Google Play Store Apps" by Lavanya Gupta (file `googleplaystore.csv`). License: CC BY-SA 4.0.
- **Citation:** L. Gupta, "Google Play Store Apps," Feb 2019. [Online]. Available: https://www.kaggle.com/lava18/google-play-store-apps
- **Size:** 10,841 rows and 13 columns. Each row is one app on the Google Play Store (data collected in 2018).
- **Columns:** App, Category, Rating, Reviews, Size, Installs, Type, Price, Content Rating, Genres, Last Updated, Current Ver, Android Ver. The data dictionary is in the notebook.

## What was wrong before cleaning
- **A broken row:** one row has its columns shifted (its Rating is 19 and its Installs is "Free").
- **Repeated apps:** 1,181 rows repeat an app that is already in the data (for example Instagram appears more than once, each copy with a slightly different number of reviews).
- **Numbers stored as text:** Reviews, Size (`19M`, `201k`, `Varies with device`), Installs (`1,000,000+`) and Price (`$4.99`).
- **Missing values:** Rating is missing in 1,474 rows (13.6%). Type and Content Rating are empty in one row each.
- **Small problems:** Content Rating is `Unrated` in 2 rows.

## Cleaning decisions
| # | Decision | Rows | Reason |
|---|---|---|---|
| 1 | Removed the broken row (Installs = `Free`) | 1 | Its columns are shifted, so its values cannot be used. A rating of 19 is impossible |
| 2 | Turned Reviews, Installs and Price into numbers (removed `+`, `,` and `$`) | all | They were text, so we could not calculate with them |
| 3 | Turned Size into MB: `M` kept, `k` divided by 1024, `Varies with device` left empty | all (1,227 empty after cleaning) | Sizes came in two units. We do not guess a size |
| 4 | Removed Content Rating `Unrated` | 2 | Only 2 rows, and no usable age group |
| 5 | Removed the row with no Type | 1 | We cannot tell if the app is Free or Paid |
| 6 | For repeated apps, kept the copy with the most reviews | 1,181 | Otherwise the same app is counted many times. Reviews only grow over time, so this copy is likely the most recent |
| 7 | Filled missing Rating with the median (4.3) and marked these rows in the column `Rating_was_missing` | 1,461 | So the charts can use every app. Risk: these ratings are guesses, so the mark lets anyone leave them out |
| 8 | Kept the expensive apps (up to 400 dollars) and the very large Installs and Reviews | 0 | We looked at them and did not delete them only because they are large. We did not verify them one by one |

Total removed: 1,185 rows (10.9%).

## Before and after
| Metric | Before | After |
|---|---|---|
| Rows | 10,841 | 9,656 |
| Repeated app names | 1,181 | 0 |
| Missing Rating | 1,474 | 0 (1,461 filled and marked) |
| Missing Size | 0 (it was text) | 1,227 |
| Rating: highest value | 19 | 5 |
| Rating: mean and standard deviation | 4.19 and 0.54 | 4.19 and 0.50 |
| Size: highest value | not a number | 100 MB |
| Reviews, Installs, Price, Size | text | numbers |

The standard deviation of Rating is lower after cleaning because the filled ratings are all equal to the median.

## Remaining issues (not fixed) and why
1. **Filled ratings:** 1,461 apps (15.1%) got the median rating. They are guesses. Any analysis of ratings should leave out the rows where `Rating_was_missing` is True.
2. **Installs are ranges** (like `1,000+`), so any total of installs is approximate (a lower limit).
3. **Size is empty for 1,227 apps (12.7%)** because their size is `Varies with device`. We do not invent a size.
4. **Repeated apps:** we kept one copy per app, so the other copies are gone.
5. **Large values kept:** prices up to 400 dollars, up to 1 billion installs and up to about 78 million reviews.
6. **Columns not cleaned:** Last Updated, Current Ver (8 empty) and Android Ver (2 empty) are still text. We do not use them.
7. **Content Rating:** the Kaggle page lists different values than the real file. We trust the real file.
8. **Old data:** collected in 2018, so the results may not match the market today.

## Files
- `Google_Play_Store_Cleaning.ipynb`: every step, with the decision and the reason.
- `googleplaystore_clean.csv`: the clean dataset (9,656 rows and 15 columns: the 13 original ones plus `Rating_was_missing` and `Price_Range`).
- The original `googleplaystore.csv` is not included. Download it from the Kaggle link above.

## How to run
1. Download `googleplaystore.csv` from Kaggle and put it in Google Drive (My Drive).
2. Open the notebook in Google Colab.
3. Run all cells from the top (Runtime > Run all).

## License
The data is under CC BY-SA 4.0. The clean file is a modified copy, so it is shared under the same license, with credit to the original author (see the citation above).
