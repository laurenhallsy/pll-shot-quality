# What Makes a Shot Dangerous in the PLL?

An expected goals (xG) model built on 7,745 Premier Lacrosse League shots to measure what actually makes a shot dangerous, and which players finish above or below the quality of their chances.

**[View the full analysis →](https://laurenhallsy.github.io/pll-shot-quality/)**

---

## Key Findings

- **Distance doesn't predict goals.** Once shot angle and assists are accounted for, distance is not significant (p = 0.60), which challenges the most common assumption about shot quality.
- **Angle and assists do.** Shot angle (p < 0.001) and assisted shots (p < 0.001) are both highly significant. Straight-on shots carry the highest xG (~30%), and goal probability drops sharply past 50°.
- **Assisted shots are more dangerous.** An assist raises the odds of scoring by about 55%, holding angle and distance constant.
- **Raw goal totals can mislead.** Eric Law converts nearly 19 percentage points above his expected rate, while several well-known names (including Rob Pannell and Kyle Harrison) get high-quality looks but finish below expectation.

---

## The Question

What separates a goal from a save in professional lacrosse? Most people would say distance. As a Division I goalie, I suspected it was more about angle, since a sharp-angle shot lets the goalie cover far more of the cage. This project tests that with data.

---

## Data

| Dataset | Rows | Seasons | Description |
|---|---|---|---|
| Shot data | 7,745 | 2019–2021 | One row per shot: location (X, Y), result, assisted/unassisted, shooter, goalie, team, college |
| Player stats | 1,000 | 2023–2026 | Per-game player statistics, used to join shooter position |

Raw data files are not included in this repo. See [`data/README.md`](data/README.md) for sources.

---

## Approach

1. **Clean and join.** Merged shot data with player positions and college data, converted percentage columns stored as blank strings to numeric, and resolved missing values.
2. **Engineer features.** Converted pixel coordinates to yards and calculated each shot's distance and angle to the goal.
3. **Explore.** Visualized outcome distributions, success rate by distance, shot distance by position, and shot locations on a PLL field.
4. **Model.** Fit a logistic regression predicting goal probability:

   ```r
   glm(is_goal ~ dist_yards + shot_angle + is_assisted, family = binomial)
   ```

5. **Evaluate players.** Created **SPOE (Shooting % Over Expectation)**:

   ```
   SPOE = actual shooting % − average xG of the player's shots
   ```

   Positive SPOE means a player finishes better than their shot quality predicts. Minimum 30 shots.

---

## Model Results

| Predictor | Estimate | p-value | Significant? |
|---|---|---|---|
| Distance (yards) | −0.001 | 0.60 | No |
| Shot angle (degrees) | −0.038 | < 0.001 | Yes |
| Assisted | +0.438 | < 0.001 | Yes |

---

## Player Insights

**Top overperformers (SPOE):** Eric Law, Jules Heningburg, Jeff Teat. Several Canadian players with box lacrosse backgrounds (Dhane Smith, Jordan MacIntosh) also rank highly, likely reflecting their experience finishing in tight spaces.

**Bottom underperformers:** Daniel Bucaro and Max Tuttle (~10 points below expectation), along with Rob Pannell and Kyle Harrison. These players aren't taking bad shots; they just aren't converting good ones at the expected rate.

**The takeaway:** Teams that evaluate shooters on raw goals or reputation may be overvaluing high-volume, below-expectation finishers and undervaluing efficient ones.

---

## Limitations

- Shot locations are pixel coordinates converted to yards, so distance and angle are approximations.
- Position data comes from 2023–2026 rosters, so about 35% of 2019–2021 shots couldn't be matched to a position.
- The model uses three predictors. Adding game state, shot type, or defensive pressure would likely improve it.

## Next Steps

- Add shot type, game situation, and 2-point shots to the model
- Evaluate model fit with a holdout set and calibration plot
- Apply the model to newer seasons to test whether SPOE is stable year to year

---

## Tools

R · tidyverse · ggplot2 · sportyR · Quarto

## About

Built by **Lauren Hall**, Business Information & Analytics student and Division I lacrosse goalie at the University of Denver.

[LinkedIn]([https://www.linkedin.com/in/laurenhalll/]) · [Email](mailto:lauren@chezhall.com)
