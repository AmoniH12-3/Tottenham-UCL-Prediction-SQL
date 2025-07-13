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

## 🔝 The campaign types with the most conversions
+----------------+-------------+---------------+
| total_matches  | total_wins  | win_percentage|
+----------------+-------------+---------------+
|     38         |     22      |     57.89     |
+----------------+-------------+---------------+

+--------+----------------+---------------+--------------------+
| result | avg_goal_diff  | avg_xg_diff   | avg_possession     |
+--------+----------------+---------------+--------------------+
|   W    |     1.67       |     0.80      |      58.22         |
|   D    |     0.00       |     0.10      |      51.78         |
|   L    |    -1.30       |    -0.70      |      47.50         |
+--------+----------------+---------------+--------------------+

+----------+-------------------+----------------+
| year_won | winner            | goals_scored   |
+----------+-------------------+----------------+
| 2023     | Man City          |      26        |
| 2022     | Real Madrid       |      25        |
| 2021     | Chelsea           |      21        |
| ...      | ...               |      ...       |
+----------+-------------------+----------------+

+------------+----------------+-------------+------------------+
| formation  | total_matches  | total_wins  | win_percentage   |
+------------+----------------+-------------+------------------+
| 4-2-3-1    |      20        |     12      |     60.00        |
| 3-4-3      |      10        |      7      |     70.00        |
| 4-3-3      |       8        |      4      |     50.00        |
+------------+----------------+-------------+------------------+

+---------------+-------------+---------------+----------------+------------------+
| total_record  | total_wins  | total_losses  | total_draws    | win_percentage   |
+---------------+-------------+---------------+----------------+------------------+
|      6        |     2       |      3        |      1         |     33.33        |
+---------------+-------------+---------------+----------------+------------------+

#### SQL Code: 
``` sql
CREATE TABLE match_results (
    match_date DATE,
    opponent TEXT,
    home_away TEXT,              -- Could also be a VARCHAR(4) if always "Home"/"Away"
    competition TEXT,
    comp_round TEXT,                  -- e.g., "Matchweek 1" — text is safest
    result CHAR(1),              -- 'W', 'L', 'D' – just a single character
    goals_for INT,
    goals_against INT,
    xg NUMERIC(4,2),             -- Expected Goals: e.g., 1.20
    xga NUMERIC(4,2),            -- Expected Goals Against: e.g., 1.00
    possession_percent NUMERIC(5,2),  -- e.g., 70.00%
    pass_accuracy_percent NUMERIC(5,2), -- e.g., 87.20%
    shots INT,
    yellow_cards INT,
    red_cards INT,
    goal_difference INT
);

SELECT * FROM match_results;



\copy match_results FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\tottenham_match_results.csv'
DELIMITER ','
CSV HEADER;

SELECT * FROM match_results;

-- \copy match_results FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\tottenham_match_results.csv' WITH (FORMAT csv, HEADER true, DELIMITER ',', ENCODING 'UTF8');
SELECT * FROM match_results;

DELETE FROM match_results WHERE match_date = '2023-10-01' AND opponent = 'TEAM B';
ALTER TABLE match_results
ALTER COLUMN result TYPE VARCHAR(5);

DROP TABLE IF EXISTS match_results;

CREATE TABLE match_results (
    match_date DATE,
    opponent VARCHAR(50),
    home_away VARCHAR(10), -- "Home" or "Away"
    competition VARCHAR(50),
    match_round VARCHAR(30),
    result VARCHAR(5), -- "W", "L", "D", "--", etc.
    goals_for INTEGER,
    goals_against INTEGER,
    xg NUMERIC(4,2), -- Expected goals, e.g. 1.20
    xga NUMERIC(4,2), -- Expected goals against, e.g. 1.00
    possession_percent NUMERIC(5,2), -- e.g. 70.00
    pass_accuracy_percent NUMERIC(5,2), -- e.g. 87.20
    shots INTEGER,
    yellow_cards INTEGER,
    red_cards INTEGER,
    goal_difference INTEGER
);
SELECT * FROM match_results;
\copy match_results FROM 'C:/Users/Amoni/Downloads/Tottenham Hotspur UCL Prediction/Tottenham Hotspur 2026 UCL Prediction - Match Results(By League_Competition) (1).csv' WITH (FORMAT csv, HEADER true, DELIMITER ',');
\copy match_results FROM '[C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\Tottenham Hotspur 2026 UCL Prediction - Match Results(By League_Competition) (1).csv' WITH (FORMAT csv, HEADER true, DELIMITER ',', ENCODING 'UTF8');
copy match_results FROM 'C:/Users/Amoni/Downloads/Tottenham Hotspur UCL Prediciton/Tottenham Hotspur 2026 UCL Prediction - Match Results(By League_Competition) (1).csv' WITH (FORMAT csv, HEADER true, DELIMITER ',');
SELECT * FROM match_results;
SELECT 
  COUNT(*) AS total_matches,
  SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS total_wins,
  ROUND(100.0 * SUM(CASE WHEN Result = 'W' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
FROM match_results;
SELECT 
 result,
  AVG(goal_difference) AS avg_goal_diff,
  AVG(xg - xga) AS avg_xg_diff,
  AVG(possession_percent) AS avg_possession
FROM match_results
GROUP BY result;

CREATE TABLE IF NOT EXISTS player_stats (
    name VARCHAR(50),
    position VARCHAR(20),
    matches_played INT,
    minutes_played INT,
    goals INT,
    assists INT,
    xg NUMERIC(4,2),             -- Expected Goals: e.g., 1.20
    aerials_won NUMERIC(4,2),       -- e.g., 3.50
    tackles NUMERIC(4,2),            -- e.g., 2.00
    shots INT,
    passing_percentage NUMERIC(5,2), -- e.g., 87.20
    rating NUMERIC(3,1)          -- e.g., 7.5
);
ALTER TABLE player_stats
ALTER COLUMN aerials_won TYPE NUMERIC(4,2)
USING aerials_won::NUMERIC;
SELECT * FROM player_stats;

ALTER TABLE player_stats
ALTER COLUMN tackles TYPE NUMERIC(4,2)
USING tackles::NUMERIC;
SELECT * FROM player_stats;

\copy player_stats FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\Tottenham Hotspur 2026 UCL Prediction - Player Stats (1).csv' WITH (FORMAT csv, HEADER true, DELIMITER ',');
CREATE TABLE IF NOT EXISTS past_ucl_winners (
    year_won INT,
    winner VARCHAR(50),
    goals_scored INT
);
\copy past_ucl_winners FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\Tottenham Hotspur 2026 UCL Prediction - Past UCL Winners (Last 10 Years) (1).csv' WITH (FORMAT csv, HEADER true, DELIMITER ',');
ALTER TABLE past_ucl_winners
RENAME COLUMN date TO year_won;
SELECT * FROM past_ucl_winners;

ALTER TABLE past_ucl_winners
ALTER COLUMN year_won TYPE INTEGER
USING year_won::INTEGER;
-- Optional: Change column type to INT (if it was DATE or TEXT)
ALTER TABLE past_ucl_winners
ALTER COLUMN year_won TYPE INT
USING EXTRACT(YEAR FROM year_won)::INT;  -- only if it was previously DATE
SELECT * FROM past_ucl_winners;

CREATE TABLE IF NOT EXISTS tactics (
    match_date DATE,
    opponent VARCHAR(50),
    formation INT,  -- e.g., "4-2-3-1"
    match_result VARCHAR(5),       -- e.g., "Player A, Player B"
    opponent_score TEXT,  -- e.g., "Substituted Player C for Player D at 60'"
    competition TEXT              -- Additional notes about the match
);
SELECT * FROM tactics;
\copy tactics FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\Tottenham Hotspur 2026 UCL Prediction - Tactics (1).csv' WITH (FORMAT csv, HEADER true, DELIMITER ',');
DROP TABLE IF EXISTS tactics;
CREATE TABLE IF NOT EXISTS ucl_contenders (
    team_name VARCHAR(50),
    avg_xg NUMERIC(4,2),  -- Average Expected Goals: e.g., 1.20
    avg_xga NUMERIC(4,2), -- Average Expected Goals Against: e.g., 1.00
    possession_percent NUMERIC(4,2), -- Average Possession Percentage: e.g., 70.00%
    ppda NUMERIC(5,2), -- Passes Per Defensive Action: e.g., 10.50
    record VARCHAR(6),
    star_player VARCHAR(50), -- e.g., "Player A"
    star_player_goals VARCHAR(50) -- e.g., "10 goals"
);
ALTER TABLE ucl_contenders
ALTER COLUMN RECORD TYPE VARCHAR(10)
USING record::VARCHAR(10);

\copy ucl_contenders FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\Tottenham Hotspur 2026 UCL Prediction - UCL Contender Stats (2).csv'WITH (FORMAT csv, HEADER true, DELIMITER ',');

\copy ucl_contenders FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\Tottenham Hotspur 2026 UCL Prediction - UCL Contender Stats (2).csv' WITH (
    FORMAT csv,
    ENCODING 'WIN1252'
);
\copy ucl_contenders FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\Tottenham Hotspur 2026 UCL Prediction - UCL Contender Stats (2).csv' WITH (FORMAT csv, ENCODING 'WIN1252', HEADER);
SELECT * FROM past_ucl_winners;

CREATE TABLE IF NOT EXISTS tactics (
    match_date DATE,
    opponent VARCHAR(50),
    formation VARCHAR(10),  -- e.g., "4-2-3-1"
    match_result VARCHAR(5),       -- e.g., "W", "L", "D"
    score TEXT,  -- e.g., "1-2"
    competition TEXT              -- Additional notes about the match
);

\copy tactics FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\Tottenham Hotspur 2026 UCL Prediction - Tactics (3).csv' WITH (FORMAT csv, HEADER true, DELIMITER ',');
DROP TABLE IF EXISTS tactics;

CREATE TABLE tactics (
    match_date DATE,             -- for values like '2023-04-21'
    formation VARCHAR(50),       -- flexible for things like '4-3-3' or '3-4-2-1'
    match_result VARCHAR(20),    -- e.g., 'Win', 'Loss', 'Draw', or even 'Leicester' if it's mislabeled
    opponent VARCHAR(100),       -- team names
    score VARCHAR(10),           -- e.g., '2-1', '0-0'
    competition VARCHAR(100)     -- competition name like 'Premier League'
);

\copy tactics FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\Tottenham Hotspur 2026 UCL Prediction - Tactics (3).csv' WITH (FORMAT csv, HEADER);
SELECT * FROM tactics;
UPDATE tactics
SET score = '2-1'
WHERE opponent = 'Man City' AND match_date = '2024-10-30';
SELECT * FROM tactics;

UPDATE tactics
SET score = '4-0'
WHERE opponent = 'Man City' AND match_date = ' 2024-11-23';
SELECT * FROM tactics;
UPDATE tactics SET score = '1-0' WHERE opponent = 'Man City' AND match_date = ' 2025-02-26';
SELECT * FROM tactics;

SELECT
formation,
    COUNT(*) AS total_matches,
    SUM(CASE WHEN match_result = 'W' THEN 1 ELSE 0 END) AS total_wins,
    ROUND(100.0 * SUM(CASE WHEN match_result = 'W' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
FROM tactics
GROUP BY formation;
SELECT
COUNT(DISTINCT formation) AS unique_formations
FROM tactics;

SELECT * FROM ucl_contenders;
DROP TABLE ucl_contenders;

CREATE TABLE ucl_contenders (
    team_name VARCHAR(50),
    avg_xg NUMERIC(4,2),  -- Average Expected Goals: e.g., 1.20
    avg_xga NUMERIC(4,2), -- Average Expected Goals Against: e.g., 1.00
    possession_percent NUMERIC(5,2), -- Average Possession Percentage: e.g., 70.00%
    ppda NUMERIC(5,2), -- Passes Per Defensive Action: e.g., 10.50
    record VARCHAR(10), -- e.g., "W-D-L"
    star_player VARCHAR(50), -- e.g., "Player A"
    star_player_goals VARCHAR(50) -- e.g., " Christian Pulisic (4)"
);

\copy ucl_contenders FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\Tottenham Hotspur 2026 UCL Prediction - UCL Contender Stats (3).csv' WITH (FORMAT csv, HEADER);

SELECT * FROM MATCH_RESULTS;
SELECT 
COUNT(*) AS total_matches,
    SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS total_wins,
    ROUND(100.0 * SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
FROM match_results;
SELECt * FROM match_results;
SELECT
ROUND(AVG(goals_for), 2) AS avg_goals_for
   FROM match_results
;
-- Calculate the average goals scored, rounded to 2 decimal places
SELECT ROUND(AVG(goals_for), 2) AS avg_goals_for
FROM match_results;

SELECT * FROM past_ucl_winners;

SELECT 
ROUND(AVG(goals_scored), 2) AS avg_goals_scored
FROM past_ucl_winners;

SELECT 
COUNT(goals_for)
FROM match_results
WHERE goals_for > 0;

SELECT * FROM match_results;
SELECT COUNT(goals_for) AS total_goals_for
FROM match_results
WHERE goals_for > 0 AND competition = 'Premier League';
SELECT
COUNT(goals_for) AS total_goals_for
FROM match_results
WHERE goals_for > 0 AND competition = 'Europa Lg';
CREATE TABLE ucl_contender_stats (
    match_date DATE, -- Match Date: e.g., '2023-10-01'
    opponent VARCHAR(50),
    goals_for INT, -- Goals Scored: e.g., 2
    goals_against INT, -- Goals Conceded: e.g., 1
    competition VARCHAR(50), -- Competition Name: e.g., 'Premier League'
    result VARCHAR(10) -- Result: e.g., 'Win', 'Loss', 'Draw'
);

DROP TABLE IF EXISTS ucl_contender_stats;

CREATE TABLE performance_against_ucl_winners (
    match_date DATE, -- Match Date: e.g., '2023-10-01'
    opponent VARCHAR(50),
    goals_for INT, -- Goals Scored: e.g., 2
    goals_against INT, -- Goals Conceded: e.g., 1
    competition VARCHAR(50), -- Competition Name: e.g., 'Premier League'
    result VARCHAR(10) -- Result: e.g., 'Win', 'Loss', 'Draw'
);
\copy performance_against_ucl_winners FROM 'C:\Users\Amoni\Downloads\Tottenham Hotspur UCL Prediciton\Tottenham Hotspur 2026 UCL Prediction - Performance against past UCL Winners.csv' WITH (FORMAT csv, HEADER);
SELECT * FROM performance_against_ucl_winners;

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
    SELECT 
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

SELECT 
COUNT(GOALS_FOR) AS total_goals_for
FROM match_results
WHERE competition = 'Europa Lg';

SELECT 
    COUNT(*) AS total_matches,
    SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS total_wins,
    SUM(CASE WHEN result = 'L' THEN 1 ELSE 0 END) AS total_losses,
    SUM(CASE WHEN result = 'D' THEN 1 ELSE 0 END) AS total_draws,
    ROUND(100.0 * SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) / COUNT(*), 2) AS win_percentage
    FROM performance_against_ucl_winners;

    SELECT from performance_against_ucl_winners
    WHERE opponent = 'Real Madrid' AND opponent = 'PSG';
```

### 💡 Insights:

- Tech campaigns earn the most referral traffic out of any other type of campaign.

##  What I Learned — Tottenham UCL Prediction (SQL Project)
- Match-level data (xG, xGA, possession, pass %, etc.) gives deep insight into team consistency and quality of play — far beyond the win/loss result.
- Creating multiple relational tables (e.g., match_results, player_stats, tactics, ucl_contenders) helped simulate a scouting-style model.
- Aggregating team performance against past UCL winners was especially helpful in understanding competitiveness at a European level.
- I reinforced my skills in joining data, conditionally counting (using CASE WHEN), and calculating percentage-based metrics to evaluate performance.
- Using the ROUND(), SUM(), and COUNT() functions allowed for easy creation of stat tables and summary performance dashboards.
### 🧾 Conclusion
By analyzing Tottenham’s recent performances alongside UCL winner trends and competitor stats, I built a SQL-driven model to assess their chances in the upcoming UEFA Champions League. This analytical approach blends historical context, player performance, tactical insight, and advanced metrics — providing fans or analysts with a realistic, data-based prediction of success potential.
- This project shows how data analysts can use SQL to analyze PR campaigns across multiple marketing KPIs. By ranking campaigns by conversion efficiency, media reach, and backlinks-to-conversion ratio, I was able to provide a clearer picture of which campaigns  were most effective. These insights could support future campaign ideations, budget allocation, and optimization strategies for future UK-based digital PR efforts.
