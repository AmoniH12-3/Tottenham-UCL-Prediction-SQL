# Can Tottenham Win the 2026 UCL Final?
## Introduction
- This project uses SQL to assess whether Tottenham Hotspur can realistically win the UEFA Champions League by analyzing their performance data, player contributions, tactical success, and head-to-head records vs elite European clubs.
- 🧠 Historical win rate against past UCL winners
- 📊 Average xG, possession, goal difference across matches
- ⚔️ Tactical formations with the highest win %
- 👥 Star player stats compared to UCL contender benchmarks
## Background
### Tools I used
- **PostgreSQL** — data loading, cleaning, and querying
- **pgAdmin** — managing database views
- **Excel** (supporting analysis)
-  ## The Analysis
-  Multiple tables were joined and queried to uncover performance trends, tactical impact, and likelihood of success based on past UCL-winning team metrics.
## 🗂️ Data Schema
- `match_results`: Match-level data (result, xG, possession, goals, etc.)
- `player_stats`: Individual player metrics (xG, tackles, passing, rating)
- `ucl_contenders`: Stats of other Champions League contenders
-`tactics`: Formation usage and match results
- `performance_against_ucl_winners`: Record vs elite clubs like Real Madrid, Man City

## 🔝 Results from All Comps/Against past UCL Winners

## 📊 Win Percentage for All Competitions combined

| Total Matches   | Total_Wins     | Win_Percentage |     
|-----------------|----------------|----------------|
|  60             | 26             |     43.33%     |

## 📊 Record in All Competitions combined
| Total Matches   | Total_Wins     | Total Loses    |Total Draws        |
|-----------------|----------------|----------------|----------------|
|  60             | 26             |     26         |8               |

## Goals In All Competitions
| Total Goals     |
|-----------------|
|  49             |

## Record Against Past Six UCL Winners
| Total Matches   | Total Wins     | Total Loses |Total Draws        |Win Percentage|
|-----------------|----------------|----------------|----------------|----------------|
|  11             | 3              |     7          |1               |27.27%

## Total Goals in Europa League
| Total Goals| 
|-----------------|
|  28             | 

## Europa League Record
| Total Matches   | Total Wins     | Total Loses    |Total Draws     |Win Percentage|
|-----------------|----------------|----------------|----------------|----------------|
|  15             | 10             |     2          |3               |66.67%

## Premier League Record
| Total Matches   | Total Wins     | Total Loses    |Total Draws     |Win Percentage|
|-----------------|----------------|----------------|----------------|----------------|
|  38             |     11         |     22         |   5            |28.95%

## Premier League Goals
| Total Matches   |
|-----------------|
|  64             |

## Angeball Record 
| Favorite Formation| Total Matches  | Total Wins     |Total Loses     |Total Draws     |Win Percentage|
|-----------------  |----------------|----------------|----------------|----------------|---------------|
|  4-3-3            |    46          |     21         |   19           |     6          |45.65%
#### SQL Code: 
``` sql
SELECT 
COUNT(*) AS total_record,
    SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) AS total_wins,
    ROUND(100.0 * SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
FROM performance_against_ucl_winners
 WHERE opponent = 'Real Madrid';

 SELECT 
    COUNT(*) AS total_record,
    SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) AS total_wins,
    SUM(CASE WHEN result = 'Loss' THEN 1 ELSE 0 END) AS total_losses,
    SUM(CASE WHEN result = 'Draw' THEN 1 ELSE 0 END) AS total_draws,
    ROUND(100.0 * SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
    FROM performance_against_ucl_winners
    WHERE opponent = 'Real Madrid';

    SELECT
    COUNT(*) AS total_record,
    SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) AS total_wins,
    SUM(CASE WHEN result = 'Loss' THEN 1 ELSE 0 END) AS total_losses,
    SUM(CASE WHEN result = 'Draw' THEN 1 ELSE 0 END) AS total_draws,
    ROUND(100.0 * SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
    FROM performance_against_ucl_winners
    WHERE opponent = 'PSG';

    SELECT 
    COUNT(*) AS total_record,
    SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) AS total_wins,
    SUM(CASE WHEN result = 'Loss' THEN 1 ELSE 0 END) AS total_losses,
    SUM(CASE WHEN result = 'Draw' THEN 1 ELSE 0 END) AS total_draws,
    ROUND(100.0 * SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
    FROM performance_against_ucl_winners
    WHERE opponent = 'Man City';

    SELECT
    COUNT(*) AS total_record,
    SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) AS total_wins,
    SUM(CASE WHEN result = 'Loss' THEN 1 ELSE 0 END) AS total_losses,
    SUM(CASE WHEN result = 'Draw' THEN 1 ELSE 0 END) AS total_draws,
    ROUND(100.0 * SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
    FROM performance_against_ucl_winners
    WHERE opponent = 'Chelsea';

SELECT * FROM performance_against_ucl_winners;
    SELECT
    COUNT(*) AS total_record,
    SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) AS total_wins,
    SUM(CASE WHEN result = 'Loss' THEN 1 ELSE 0 END) AS total_losses,
    SUM(CASE WHEN result = 'Draw' THEN 1 ELSE 0 END) AS total_draws,
    ROUND(100.0 * SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
    FROM performance_against_ucl_winners
    WHERE opponent = 'Bayern Munich';

SELECT COUNT (*) AS total_record,
    SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) AS total_wins,
    SUM(CASE WHEN result = 'Loss' THEN 1 ELSE 0 END) AS total_losses,
    SUM(CASE WHEN result = 'Draw' THEN 1 ELSE 0 END) AS total_draws,
    ROUND(100.0 * SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
FROM performance_against_ucl_winners
WHERE opponent = 'Liverpool';
SELECT * FROM past_ucl_winners;

SELECT 
COUNT(goals_for) AS total_goals_for
FROM match_results
WHERE goals_for > 0 AND competition = 'Europa Lg';
SELECT 
COUNT(result) AS total_record,
    SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS total_wins,
    SUM(CASE WHEN result = 'L' THEN 1 ELSE 0 END) AS total_losses,
    SUM(CASE WHEN result = 'D' THEN 1 ELSE 0 END) AS total_draws
FROM match_results
WHERE competition = 'Europa Lg';

SELECT 
COUNT (*) AS total_matches,
    SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS total_wins,
    ROUND(100.0 * SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
    FROM match_results;
   SELECT *
FROM match_results;

SELECT 
COUNT(*) AS total_matches,
    SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS total_wins,
    SUM(CASE WHEN result = 'L' THEN 1 ELSE 0 END) AS total_losses,
    SUM(CASE WHEN result = 'D' THEN 1 ELSE 0 END) AS total_draws
    FROM match_results;

SELECT
    COUNT(*) AS total_record_and_goals,
    SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS total_wins,
    SUM(CASE WHEN result = 'L' THEN 1 ELSE 0 END) AS total_losses,
    SUM(CASE WHEN result = 'D' THEN 1 ELSE 0 END) AS total_draws,
    ROUND(100.0 * SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage,
    COUNT(goals_for) AS total_goals_for
FROM match_results
WHERE competition = 'Europa Lg';

Select 
    Count(goals_for) AS total_goals_for
    FROM match_results
WHERE competition = 'Europa Lg';
SELECT * FROM match_results
WHERE competition = 'Europa Lg';

SELECT 
    COUNT(*) AS total_matches,
    SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS total_wins,
    SUM(CASE WHEN result = 'L' THEN 1 ELSE 0 END) AS total_losses,
    SUM(CASE WHEN result = 'D' THEN 1 ELSE 0 END) AS total_draws,
    ROUND(100.0 * SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage,
    SUM(goals_for) AS total_goals_for
FROM match_results
WHERE competition = 'Europa Lg';
```

### 💡 Insights:

- Tottenham failed to have a winning record and underperformed in majority of their competitions besides the Europa League in which they won.
- Angeball wasn't as effective as they failed to win even 50% of their matches in the 4-3-3 which was the most used formation in the season.
- Tottenham have struggled against previous UCL winners which may be a problem when facing these clubs in next year's Champions League.

##  What I Learned — Tottenham UCL Prediction (SQL Project)
- Match-level data (xG, xGA, possession, pass %, etc.) gives deep insight into team consistency and quality of play — far beyond the win/loss result.
- Creating multiple relational tables (e.g., match_results, player_stats, tactics, ucl_contenders) helped simulate a scouting-style model.
- Aggregating team performance against past UCL winners was especially helpful in understanding competitiveness at a European level.
- I reinforced my skills in joining data, conditionally counting (using CASE WHEN), and calculating percentage-based metrics to evaluate performance.
- Using the ROUND(), SUM(), and COUNT() functions allowed for easy creation of stat tables and summary performance dashboards.
### 🧾 Conclusion
By analyzing Tottenham’s recent performances alongside UCL winner trends and competitor stats, I built a SQL-driven model to assess their chances in the upcoming UEFA Champions League. This analytical approach blends historical context, player performance, tactical insight, and advanced metrics — providing fans or analysts with a realistic, data-based prediction of success potential.
- Tottenham struggled in majority of the competitions which puts into question the effectiveness of the tactics, but with a successful transfer window, coaching change, and different tactics, there's a small but valid chance for Tottenham to pull off an upset and win next year's Champions League Final. COYS!!
