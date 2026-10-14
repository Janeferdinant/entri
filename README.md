# entri: first-week engagement and purchase analysis of a learning platform's users

[![License: CC BY-NC-ND 4.0](https://img.shields.io/badge/License-CC%20BY--NC--ND%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

## Overview

An exploratory pandas notebook that looks at how new users of an online learning platform behave in their first week (track subscriptions, quiz attempts, video watching, study cards) and how that relates to whether and when they make a first purchase. It is a descriptive analysis of company-provided data with pseudonymous user IDs, and it ends in lists of unusually active users and counts, not in a predictive model.

## Demo

[`docs/index.html`](docs/index.html) is a static page that presents the problem, the charts exported from the notebook's saved outputs and the reported findings. It works on phones and desktops, and it is what GitHub Pages serves for this repository.

## Problem statement

The platform has users who joined and never paid, and users who paid. Which behaviours in the first seven days after joining stand out, and how do paid and unpaid users differ on them? The notebook defines the first week as the join date plus six days and asks:

- How many users bought within that first week?
- How many tracks (course subjects) do paid and unpaid users subscribe to?
- How often do users attempt, pause and complete quizzes, and how many unpaid users keep attempting them?
- How many users watch videos on more than one day of their first week, and how many watch 30% or 80% of a video?
- Which users are the heaviest quiz and study-card users?

## Approach

1. Load five CSV files (`track_subscription_data`, `user_purchase_data`, `user_quiz_attempt_data`, `user_studycard_data`, `user_video_activity_data`) with pandas.
2. Split users into unpaid (no first-purchase date) and paid, and merge each activity table with the join date.
3. Keep only events inside the first-week window (`date_joined` to `date_joined + 6 days`).
4. Aggregate per user with `groupby`: tracks per user, attempts per status, distinct watch days, completions, study-card events per track.
5. Flag users above simple thresholds (for example more than 2 tracks, more than 10 quiz rows, more than 50 completions) and plot the per-user counts with Matplotlib.

No statistical test or model is used.

## Data

| File | Rows | Distinct users |
|---|---|---|
| `track_subscription_data.csv` | 1,578 | 962 |
| `user_purchase_data.csv` | 1,000 | 1,000 |
| `user_quiz_attempt_data.csv` | 21,419 | 630 |
| `user_studycard_data.csv` | 3,480 | 386 |
| `user_video_activity_data.csv` | 12,907 | 755 |

This is a company-provided dataset (© its owner), credited in [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md). It is user-activity data with numeric pseudonymous user IDs and no names, e-mail addresses or other contact details. The repository owner has confirmed that it may stay public here. The CC BY-NC-ND 4.0 license in this repository covers the analysis and code, not the data. No new data was added in this update, and the dataset is not the repository owner's own.

## Results

The notebook does not state its results in text; these are the numbers printed in its saved outputs. Nothing was recomputed beyond a re-run to check that they reproduce.

| Finding | Value | Status | How measured | Date |
|---|---|---|---|---|
| Users with a recorded first purchase | 576 | Reported (notebook output) | `nunique` of user IDs after merging purchases with subscriptions | notebook cell 4 |
| ...of those, purchased within 7 days of joining | 478 | Reported (notebook output) | first purchase on or before join date + 6 days | notebook cell 6 |
| Users with no first-purchase date | 424 | Reported (notebook output) | rows of the unpaid-users table | notebook cell 2 |
| Unpaid users with more than 2 track subscriptions | 16 | Reported (notebook output) | group size over 2 | notebook cell 3 |
| Paid users (first week) with more than 4 track subscriptions | 21 | Reported (notebook output) | group size over 4 | notebook cell 7 |
| Quiz attempts by status | COMPLETE 13,833 · CREATE 6,950 · PAUSE 489 · PROGRESS 147 | Reported (notebook output) | `value_counts` of `status` | notebook cell 8 |
| Users who attempted a quiz / of them unpaid | 630 / 126 | Reported (notebook output) | distinct users, with and without a match in the unpaid list | notebook cell 8 |
| Unpaid users with more than 10 quiz rows in one status | 9 | Reported (notebook output) | group size over 10 | notebook cell 8 |
| Users who completed a quiz at least once | 528 | Reported (notebook output) | distinct users in `COMPLETE` rows | notebook cell 30 |
| Users with more than 50 completed attempts | 56 | Reported (notebook output) | group size over 50 | notebook cell 31 |
| Users who watched video on more than one day of their first week | 13 | Reported (notebook output) | distinct watch dates per user over 1 | notebook cell 12 |
| Users with study-card activity | 386 | Reported (notebook output) | `value_counts` length | notebook cell 37 |

Every number above was also reproduced on 2026-10-13 by re-running the notebook from a clean clone with the pinned versions (about 20 seconds); the run log is in [`docs/runs`](docs/runs/2026-10-13-rerun.md). That re-run is a check that the saved outputs are repeatable, not a new analysis. With pandas 3 one figure changes (126 becomes 83, see Limitations).

## Tech stack

Python 3.11, pandas 1.5.3, NumPy 1.26.4, Matplotlib 3.8.4, Jupyter (ipykernel, nbconvert).

## Getting started

Prerequisites: Python 3.11 and Git. The repository is about 2 MB.

```bash
git clone https://github.com/Janeferdinant/entri.git
cd entri
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --output entri.executed.ipynb "entri .ipynb"
```

Expected: about 20 seconds, then `entri.executed.ipynb` with the same printed numbers as the saved notebook. To open it interactively instead, run `pip install notebook` and then `jupyter notebook`. The notebook also writes `COMPLETE.csv`, `CREATE.csv`, `PAUSE.csv` and `PROGRESS.csv` (quiz attempts split by status) into the folder; they are git-ignored.

## Project structure

```
entri .ipynb                     the analysis notebook (file name has a space)
*.csv                            the five company-provided data files
docs/index.html                  static presentation page (GitHub Pages)
docs/img/                        charts exported from the notebook's saved outputs
docs/runs/                       dated re-run log
index.html                       redirects to docs/index.html
requirements.txt                 pinned versions that reproduce the saved outputs
THIRD_PARTY_NOTICES.md           data credit
```

## Real-world applications

The same first-week funnel view is standard product analytics for subscription and e-learning products: finding which early actions (a second track, a quiz completion, watching a video to the end) are common among users who convert, as a starting point for onboarding experiments or for choosing features for a conversion model.

## Limitations

- **Descriptive only.** No hypothesis tests, no confidence intervals and no causal claims. Heavy engagement and purchasing may both reflect an underlying motivation.
- **Thresholds are arbitrary** (more than 2 or 4 tracks, 30% or 80% of a video, more than 10 or 50 attempts) and were not tuned or justified in the notebook.
- **The charts are line plots** of per-user counts. Except for the first chart (drawn against user ID), the x axis is a row position in a table, not time or any ordered quantity, and the axes are unlabelled in the original outputs. They show spread and outliers only.
- **Some notebook comments disagree with its own printed outputs** (for example "14 unique users" next to an output listing 20, "473 users" next to 478, "526" next to 528, "7 users" next to 13). This page and README use the printed outputs.
- **pandas version matters.** Since pandas 2.0 the text `None` in `user_quiz_attempt_data.csv` (`started_at`) is read as a missing value. On pandas 3.0.6 the count of unpaid users who attempted a quiz becomes 83 instead of 126, because rows with a missing `started_at` are dropped by a later `dropna`. The pinned pandas 1.5.3 reproduces the saved 126.
- **One company, a few months of 2021 data, no demographic information**, so the findings do not generalise.
- Some cells are leftovers (repeated computations, an empty cell, `dfp.info()` output), kept unchanged because students may use the notebook.

## Future scope

- Label the chart axes, plot against ordered values (day since joining, rank) and add a funnel chart.
- Compare paid and unpaid users on the same measures with a test or a bootstrap interval.
- Fit a simple conversion model on the first-week features, with a time-based split.
- Make the `None` handling in the quiz file explicit so that the notebook gives the same numbers on current pandas.

## Credits & citation

- **Data.** Company-provided user-activity dataset (© its owner); see [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).
- **Analysis and code.** Jane Rose Ferdinant.

## License

Original work © Jane Rose Ferdinant, licensed CC BY-NC-ND 4.0. Third-party components retain their own licenses; see THIRD_PARTY_NOTICES.md. The license covers the analysis and code only, not the data.

## Author

Jane Rose Ferdinant · [Portfolio](https://janeferdinant.vercel.app) · [LinkedIn](https://www.linkedin.com/in/janerosenf/)
