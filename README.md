# ATP Tennis Analytics and Match Predictor (2015–2026)

I built this in Excel to look at ATP tour match data and try to answer two questions:

1. What separates players who win matches from players who lose them?
2. Can a simple model predict who will win?

## Data source

The match data comes from the TennisMyLife database on GitHub: https://github.com/Tennismylife/TML-Database. I used its yearly ATP match files for 2015 to 2026. The raw file in this repository is that data after the small clean-up described below.

## Files

- ATP Tennis Match Analyser and Predictor.xlsx is the main workbook. Three sheets are visible: Match Analyser, Match Predictor and Model Results. The sheets that do the calculations are hidden. Right-click any tab and choose Unhide to look at them.
- ATP_Match_Data_2015-2026.xlsx is the raw match data (33,379 matches).

Open the workbook in desktop Excel and click Enable Editing. The yellow cells are dropdowns.

## What each sheet does

**Match Analyser.** Compares winners and losers across ten seasons (2016–2025). You can filter by surface, tournament level, round, indoor or outdoor, and year. It shows which pre-match measures (ranking, recent form, surface record and so on) are most often better for the winner, and how winners and losers differ on serve and break points during the match.

**Match Predictor.** Pick two players, a date, a surface and a round, and it gives each player's chance of winning. It also shows the numbers behind that chance: form, serve and return rates, head-to-head record and what pushed the prediction towards each player.

**Model Results.** How the model was built and tested, including which measures helped and which did not.

## How the model works

- 2015 is only used as history, so that players already have some record by early 2016. 2016–2025 is used to train the model and choose its inputs. 2026 (about 2,800 matches) was kept back and used once as a test.
- For every match, only earlier matches are used. Nothing from the match itself, or later, can leak in.
- The inputs are the differences between the two players in ranking, ranking points, win rate (overall, last 5, last 10 and on the surface), serve and return rates, age, height and handedness. If a player has few past matches, their rates are pulled towards the tour average so one lucky result does not look like a trend.
- The model is a logistic regression. I trained it in Python and loaded the results into Excel as fixed numbers, so the workbook uses the model but does not train it.
- I chose the inputs using 2024, then tested on 2026. The live predictor is refitted on 2016–2026.

## Results on the 2026 test season

- Always saying 50%: 50.0% of winners picked, log loss 0.693
- Ranking only: 64.5% picked, log loss 0.623
- Full model: 65.7% picked, log loss 0.610

Log loss scores the probabilities, not just the picks, and lower is better. The full model beats ranking alone by a small but real margin: log loss improves by 0.0135 (95% interval 0.0072 to 0.0194). Ranking points and surface win rate carry most of the signal. Serve and return statistics add almost nothing once ranking and form are known.

## Things to keep in mind

- Tennis is hard to predict. Even a good model picks only about two matches in three.
- Dates are tournament start dates, so matches within one tournament are ordered by round only.
- The results show links, not causes. Serve statistics from a match describe how it was won, and are not known beforehand.
- The model knows nothing about injuries, fatigue or personal history between players beyond what ranking and form capture.
- I left out 150 matches with no surface or a carpet surface, and 3 with an unusable round label.

