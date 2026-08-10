# Local Sports League Manager

## What is this project?

This is a mini DBMS project built using **MySQL** for a local sports league. It manages teams, players, venues, referees, matches, player match statistics, team sponsors, and fan feedback.

The main idea is to replace manual scoresheets and spreadsheets with a database system where match results, player performance, and league standings can be tracked and queried directly.

---

## Why I built this

Local sports leagues (college leagues, community leagues, five-a-side tournaments) usually track scores and stats by hand or in loose spreadsheets. This makes it hard to answer simple questions quickly, and standings/top-scorer tables have to be recalculated manually every time.

This project keeps all the important league information connected in one MySQL database. It can answer questions such as:

- Which teams have played the most matches?
- What is the current league standings table?
- Who is the top scorer in the league?
- Which team has the best win percentage?
- What was the result the last time two specific teams played each other?
- Which players have never played a match?
- Which matches were postponed or cancelled?
- Which referee has officiated the most matches?
- Which players have picked up the most disciplinary cards?
- What do fans think of each match?

This project also demonstrates how **ER modeling, strong/weak entities, relationships, cardinality, self-referencing foreign keys, many-to-many relationships, and SQL queries** are converted into a working MySQL database.

---

## Project Name

**Local Sports League Manager**

## Database Name

`SportsLeagueDB`

## Technology Used

- **Database:** MySQL
- **Language:** SQL
- **Concepts:** DBMS, ER Diagram, Primary Key, Foreign Key, Self-Referencing Foreign Key, Normalization, Joins, Aggregate Functions, Subqueries, , Window Functions, Views, DML and Transactions

---

## Files in this project

| File | Description |
|---|---|
| `sports_league.sql` | Creates the `SportsLeagueDB` database, tables, sample data, and practice queries |
| `README_Local_Sports_League.md` | Project documentation |

---

## Tables (Entities) used

| Table | What it stores | Type |
|---|---|---|
| `Team` | Team details | Strong entity |
| `Player` | Player details | Strong entity |
| `PlayerPhone` | One or more phone numbers of a player | Weak entity |
| `Venue` | Stadium/ground details | Strong entity |
| `Referee` | Match official details | Strong entity |
| `Match` | Fixture information between two teams | Strong entity |
| `PlayerMatchStats` | Per-player performance in a match | Associative entity |
| `TeamSponsor` | Sponsorship/payment details for each team | Strong entity |
| `Feedback` | Fan feedback for a match | Weak entity |

---

## Strong Entity vs Weak Entity

A **strong entity** can exist independently and has its own primary key.

Examples:
- `Team`
- `Player`
- `Venue`
- `Referee`
- `Match`
- `TeamSponsor`

A **weak entity** depends on another entity for its meaning/identification.

### PlayerPhone

A player can have multiple phone numbers. `PlayerPhone` uses the combination of:

`PlayerID + PhoneNumber`

as its primary key. `PhoneNumber` acts as the partial key.

### Feedback

Feedback is connected to a match. It cannot be meaningful without knowing which match it refers to.

---

## Relationships

```
Team 1 ────── N Player
Player 1 ───── N PlayerPhone
Venue 1 ────── N Match
Referee 1 ──── N Match

Team 1 ────── N Match   (as Home Team)
Team 1 ────── N Match   (as Away Team)

Match M ────── N Player
        through PlayerMatchStats

Team 1 ────── N TeamSponsor
Match 1 ────── N Feedback
```

### Relationship explanation

- One team can have many players.
- One player can have many phone numbers.
- One venue can host many matches.
- One referee can officiate many matches.
- One team can play many matches as the home team, and many matches as the away team — this is a **self-referencing relationship**, since `Match` has two foreign keys pointing back to the same `Team` table.
- One match can involve many players, and one player can appear in many matches (across the season). This **many-to-many** relationship is resolved using `PlayerMatchStats`.
- One team can have many sponsorship records.
- One match can receive many pieces of fan feedback.

---

## Attributes used

### Team
- `TeamID` — Primary Key
- `TeamName`
- `City`
- `CoachName`
- `FoundedYear`

### Player
- `PlayerID` — Primary Key
- `FirstName`
- `LastName`
- `DOB`
- `Position`
- `JerseyNumber`
- `TeamID` — Foreign Key

`FirstName + LastName` can be considered a composite name attribute in the ER model.
`Age` is a derived attribute calculated from `DOB` and is not stored in the table.

### PlayerPhone
- `PlayerID` — Foreign Key
- `PhoneNumber` — Partial Key

### Venue
- `VenueID` — Primary Key
- `VenueName`
- `City`
- `Capacity`

### Referee
- `RefereeID` — Primary Key
- `FirstName`
- `LastName`
- `Phone`
- `YearsExperience`

### Match
- `MatchID` — Primary Key
- `HomeTeamID` — Foreign Key (→ Team)
- `AwayTeamID` — Foreign Key (→ Team)
- `VenueID` — Foreign Key
- `RefereeID` — Foreign Key
- `MatchDate`
- `MatchTime`
- `HomeScore`
- `AwayScore`
- `Status`

### PlayerMatchStats
- `MatchID` — Foreign Key
- `PlayerID` — Foreign Key
- `Goals`
- `Assists`
- `YellowCards`
- `RedCards`
- `MinutesPlayed`

Primary Key: `MatchID + PlayerID`

### TeamSponsor
- `SponsorID` — Primary Key
- `TeamID` — Foreign Key
- `SponsorName`
- `Amount`
- `PaymentMode`
- `PaymentStatus`
- `PaymentDate`

### Feedback
- `FeedbackID` — Primary Key
- `MatchID` — Foreign Key
- `FanName`
- `Rating`
- `Comments`
- `FeedbackDate`

---

## Important Database Features

### Primary Keys

Primary keys uniquely identify records.

```
TeamID
PlayerID
VenueID
RefereeID
MatchID
SponsorID
FeedbackID
```

### Foreign Keys

Foreign keys connect related tables.

```
Player.TeamID → Team.TeamID
PlayerPhone.PlayerID → Player.PlayerID
Match.HomeTeamID → Team.TeamID
Match.AwayTeamID → Team.TeamID
Match.VenueID → Venue.VenueID
Match.RefereeID → Referee.RefereeID
PlayerMatchStats.MatchID → Match.MatchID
PlayerMatchStats.PlayerID → Player.PlayerID
TeamSponsor.TeamID → Team.TeamID
Feedback.MatchID → Match.MatchID
```

### Self-Referencing Foreign Key

`Match` has **two** foreign keys pointing to the same `Team` table — one for the home team and one for the away team. This is a classic self-join pattern used to calculate standings, head-to-head records, and win/loss counts per team.

```
Match.HomeTeamID → Team.TeamID
Match.AwayTeamID → Team.TeamID
```

### Many-to-Many Relationship

`Match` and `Player` have a many-to-many relationship.

```
Match
   |
   | 1:N
   |
PlayerMatchStats
   |
   | N:1
   |
Player
```

`PlayerMatchStats` stores each player's `Goals`, `Assists`, `YellowCards`, `RedCards`, and `MinutesPlayed` for that specific match.

---

## Sample Data

The SQL project contains sample records including:

- 6 teams
- 30 players (5 per team)
- 30 player phone records
- 3 venues
- 4 referees
- 15 matches
- 60 player-match-stat records
- 6 team sponsor records
- 12 feedback records

Example teams:
- Riverside FC
- Northside United
- Eastwood Rangers
- Harborview Athletic
- Summit City FC
- Westgate Warriors

Example positions:
- Goalkeeper
- Defender
- Midfielder
- Forward

---

## Match and Status Management

Match status can be:

```
Scheduled
Live
Completed
Postponed
Cancelled
```

Sponsor payment status can be:

```
Pending
Paid
Overdue
```

Sponsor payment modes:

```
Bank Transfer
Cheque
UPI
Cash
```

---

## Feedback Management

Fans can leave feedback after a match.

Feedback contains:

- Rating from 1 to 5
- Comments
- Feedback date

This can be used to find the highest-rated matches and understand fan sentiment.

---

## What kind of information can I get from this database?

The SQL file contains practice queries covering:

- Full league standings table (wins, losses, draws, points) calculated live
- Top scorer across the season
- Top 3 players by combined goals + assists
- Team with the best win percentage
- Head-to-head results between two specific teams
- Players who have never played a match
- Number of matches played by each team
- Matches that ended 0–0
- Average goals scored per match by venue
- Referee who has officiated the most matches
- Players who have scored more than the league-average goals
- Team with the most players
- Full stat line for a specific match
- Total goals scored by each team across the season
- Matches that received no fan feedback
- Fans who gave a 5-star rating
- Player with the most disciplinary cards (yellow + red)
- Matches that were postponed or cancelled
- Teams and their total sponsorship income
- Sponsors with an overdue payment
- Updating a match status
- Deleting an expired/invalid feedback record
- Creating and using a live `Standings` view
- Ranking teams by points using `RANK()` / `DENSE_RANK()`
- Each team's longest current unbeaten streak (window function)

---

## View used in the project

A reusable view called `Standings` is created to display the live league table.

It combines:

```
Match
Team
```

The view displays information such as:

- Team name
- Matches played
- Wins
- Draws
- Losses
- Goals for / against
- Goal difference
- Points

This makes it easy to display the current league table at any point in the season without recalculating it manually.

---

## How to run this project

### Step 1: Install MySQL

Install MySQL Server and MySQL Command Line Client, or use MySQL Workbench.

### Step 2: Open MySQL

```
mysql -u root -p
```

Enter your MySQL password.

### Step 3: Run the SQL file

```
mysql -u root -p < sports_league.sql
```

Or open the SQL file in **MySQL Workbench**, select all the code, and click **Execute**.

### Step 4: Select the database

```
USE SportsLeagueDB;
```

### Step 5: Check the tables

```
SHOW TABLES;
```

You should see the project tables.

---

## Useful commands

### View all teams
```
SELECT * FROM Team;
```

### View all players
```
SELECT * FROM Player;
```

### View all matches
```
SELECT * FROM Match_;
```

### View the standings table
```
SELECT * FROM Standings;
```

### View player match stats
```
SELECT * FROM PlayerMatchStats;
```

### View feedback
```
SELECT * FROM Feedback;
```

---

## What I learned from this project

- How to design a real-world database using MySQL.
- How to convert an ER diagram into relational tables.
- Difference between strong and weak entities.
- How primary keys and foreign keys connect tables.
- How to handle multivalued attributes such as player phone numbers.
- How to resolve many-to-many relationships using an associative table.
- How to design and query a **self-referencing foreign key** (home team / away team).
- How to use `JOIN`, `GROUP BY`, `HAVING`, subqueries and aggregate functions.
- How to use CTEs and window functions.
- How to create and use SQL views.
- How to perform `INSERT`, `UPDATE` and `DELETE` operations.
- How `ON DELETE CASCADE` helps maintain related data.
- How a full league standings table can be calculated live from raw match results.
- How database design can solve a practical local sports league problem.

---

## Project Summary

**Local Sports League Manager** is a MySQL-based DBMS project designed to manage the complete flow of a local sports league.

The database connects teams, players, venues, referees, matches, player statistics, sponsors, and fan feedback in one structured system. The project demonstrates practical DBMS concepts such as **ER modeling, self-referencing relationships, keys, normalization, SQL queries, views, transactions and data management**.

