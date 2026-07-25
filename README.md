# World Cup Final Winner Predictor

A machine-learning project that estimates the outcome probabilities of a hypothetical **Spain vs Argentina** FIFA World Cup final. The model predicts the probability of a Spain win, a draw, and an Argentina win using historical international football results.

## Overview

The project builds a three-class XGBoost classifier from international match data. It combines match context, Elo ratings, recent team form, rest time, and head-to-head history to produce an interpretable match prediction and a probability chart.

> This project is an analytical experiment, not an official forecast. Football outcomes are inherently uncertain.

## Features

- Downloads and caches historical international match results.
- Normalizes selected country names across datasets.
- Calculates pre-match Elo ratings with home advantage and score-margin adjustments.
- Creates recent-form features from the previous 5 and 10 matches.
- Calculates rest days for each team.
- Adds head-to-head match count, win rate, and goal-difference features.
- Assigns tournament importance weights.
- Trains a multiclass XGBoost model for **home win**, **draw**, and **away win** outcomes.
- Evaluates the validation set with accuracy and multiclass log loss.
- Produces a PNG probability chart in the `prediction/` directory.

## Project Structure

```text
.
├── spain_argentina.py          # Main training, evaluation, and prediction script
├── data_cache/
│   └── fixtures.csv            # Fixture information used to locate the final
└── prediction/
    └── <match-date>/           # Generated probability chart output
```

## Requirements

- Python 3.9 or newer
- Packages listed in `requirements.txt`

## Installation

1. Clone the repository:

```bash
git clone https://github.com/kozyurt/world_cup_winner_.git
cd world_cup_winner_
```

2. Create and activate a virtual environment (recommended):

```bash
python -m venv .venv
```

macOS / Linux:

```bash
source .venv/bin/activate
```

Windows:

```bash
.venv\Scripts\activate
```

3. Install dependencies:

```bash
pip install -r requirements.txt
```

## Data Setup

The script downloads historical match results from the public `martj42/international_results` dataset and stores them in `data_cache/results.csv`.

Before running the script, make sure `data_cache/fixtures.csv` exists and includes a row for the Spain vs Argentina fixture. The script expects the following columns:

```text
teams, match_number, group, stadium, date_dt
```

The `teams` value must use the format:

```text
Spain v Argentina
```

## Usage

Run the predictor from the project root:

```bash
python spain_argentina.py
```

The script will:

1. Load historical international match data.
2. Engineer Elo, form, rest-day, and head-to-head features.
3. Train and validate an XGBoost classifier.
4. Predict Spain win, draw, and Argentina win probabilities.
5. Save a chart as `prediction/<match-date>/viz_Spain_vs_Argentina.png`.

## Model Inputs

The model uses the following feature groups:

| Category | Features |
| --- | --- |
| Match context | Neutral venue indicator and tournament weight |
| Team strength | Home Elo, away Elo, and Elo difference |
| Recent form | Win rate and goal difference over the last 5 matches; win rate over the last 10 matches |
| Availability proxy | Days of rest since each team's previous match |
| Head-to-head history | Number of prior meetings, home-team win rate, and average goal difference |

## Configuration

Key settings are defined at the top of `spain_argentina.py`:

- `HOME_TEAM` and `AWAY_TEAM`: teams used for the prediction.
- `MATCH_NEUTRAL`: whether the match is played at a neutral venue.
- `MATCH_WEIGHT`: importance assigned to the match.
- `TRAIN_START` and `VAL_START`: date boundaries for training and validation.
- `ELO_BASE`, `ELO_K`, and `ELO_HOME_BONUS`: Elo-rating parameters.

To predict another matchup, update the team constants and ensure the corresponding fixture exists in `data_cache/fixtures.csv`.

## Notes and Limitations

- Results depend on the coverage and quality of the underlying historical dataset.
- The model does not include player-level availability, injuries, squad selection, tactical systems, betting markets, or live match events.
- A draw is modeled as a final match result category; knockout-match extra time and penalty-shootout rules are not separately modeled.
- Predictions should be interpreted as estimated probabilities rather than guarantees.

## LinkedIn Post

Project post: [World Cup Final Data Science and Machine Learning Project](https://www.linkedin.com/posts/koray-%C3%B6zyurt-772263230_worldcupfinal-datascience-machinelearning-share-7484655066734579712-PgX6/?utm_source=share&utm_medium=member_desktop&rcm=ACoAADmz1oYBrE1Ctouuzzj0z72AZsaQ-2v1xIk)

