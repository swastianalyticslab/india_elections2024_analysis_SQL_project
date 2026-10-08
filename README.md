# India Elections 2024 – SQL Data Analysis

## Project Overview

This project analyzes the **India Elections 2024** dataset using **SQL** to answer election-related analytical questions at the constituency, state, party, and alliance levels.

The project focuses on transforming raw election data into meaningful insights using SQL concepts such as:

- `SELECT`, `WHERE`, `GROUP BY`, `ORDER BY`
- Aggregate functions such as `COUNT()` and `SUM()`
- `CASE WHEN` statements
- `INNER JOIN`
- Filtering and ranking
- Calculated fields
- Candidate-level and constituency-level analysis
- Alliance-level classification and analysis

The analysis contains **16 business/election questions**, ranging from the total number of seats and alliance performance to candidate votes, constituency winners/runners-up, and state-level election statistics.

## Key Questions Answered

1. What are the total number of seats in the election?
2. What is the total number of seats in each state?
3. What is the total number of seats won by the NDA Alliance?
4. What are the seats won by individual NDA alliance parties?
5. What is the total number of seats won by I.N.D.I.A Alliance parties?
6. What are the seats won by individual I.N.D.I.A Alliance parties?
7. How can parties be classified into NDA, I.N.D.I.A, and OTHER alliances?
8. What is the total number of seats won by each alliance?
9. What is the detailed breakdown of seats won by each alliance?
10. Who was the winning candidate, which party did they represent, how many votes did they receive, and what was their victory margin for a selected constituency?
11. What is the distribution of EVM votes versus postal votes for candidates in a selected constituency?
12. Which parties won the most seats in a selected state?
13. What is the state-wise breakdown of seats won by NDA, I.N.D.I.A, and OTHER?
14. Which candidates received the highest number of EVM votes across constituencies (Top 10)?
15. Which candidate won and which candidate was the runner-up in each constituency of a selected state?
16. For Maharashtra, what are the total seats, candidates, parties, total votes, EVM votes, and postal votes?

## Dataset Description

The project uses five CSV files.

| Dataset | Rows | Columns | Description |
|---|---:|---:|---|
| `constituencywise_details.csv` | 8,902 | 8 | Candidate-level voting details for constituencies |
| `constituencywise_results.csv` | 543 | 8 | Winning candidate and constituency-level results |
| `partywise_results.csv` | 42 | 3 | Party-wise seats won |
| `states.csv` | 36 | 2 | State master/reference table |
| `statewise_results.csv` | 543 | 9 | State/constituency-level election results |

**Total records across the five source files:** 10,066 rows.

The analysis identifies **543 total parliamentary seats** in the election.

## Data Dictionary

### 1. `constituencywise_details.csv`

| Column | Description |
|---|---|
| `S.N.` | Serial number of the candidate record |
| `Candidate` | Name of the candidate |
| `Party` | Political party represented by the candidate |
| `EVM Votes` | Votes received through EVMs |
| `Postal Votes` | Votes received through postal ballots |
| `Total Votes` | Total votes received by the candidate |
| `% of Votes` | Percentage of votes received |
| `Constituency ID` | Unique identifier for the constituency |

### 2. `constituencywise_results.csv`

| Column | Description |
|---|---|
| `S.No` | Serial number |
| `Parliament Constituency` | Parliamentary constituency identifier/name |
| `Constituency Name` | Name of the constituency |
| `Winning Candidate` | Candidate who won the constituency |
| `Total Votes` | Total votes received by the winning candidate |
| `Margin` | Victory margin |
| `Constituency ID` | Unique constituency identifier |
| `Party ID` | Identifier of the winning candidate's party |

### 3. `partywise_results.csv`

| Column | Description |
|---|---|
| `Party` | Political party name |
| `Won` | Number of seats won by the party |
| `Party ID` | Unique party identifier |

### 4. `states.csv`

| Column | Description |
|---|---|
| `State ID` | Unique state identifier |
| `State` | State/Union Territory name |

### 5. `statewise_results.csv`

| Column | Description |
|---|---|
| `Constituency` | Constituency reference |
| `Const. No.` | Constituency number |
| `Parliament Constituency` | Parliamentary constituency |
| `Leading Candidate` | Leading candidate |
| `Trailing Candidate` | Trailing candidate |
| `Margin` | Current/result margin |
| `Status` | Result/leading status |
| `State ID` | State identifier |
| `State` | State name |

## Tools Used

| Tool | Purpose |
|---|---|
| **SQL Server / SQL** | Data analysis, aggregation, filtering and joins |
| **SQL Server Management / Query Editor** | Writing and executing SQL queries |
| **CSV** | Source data format |

## SQL Analysis and Queries Used

### 1. Total Number of Seats

The total number of parliamentary constituencies was calculated using `COUNT(DISTINCT ...)`.

```sql
SELECT
    DISTINCT COUNT(Parliament_Constituency) AS Total_Seats
FROM constituencywise_results;
```

**Result:** 543 seats.

### 2. Total Seats in Each State

The constituency results were joined with state-wise results and the state master table.

```sql
SELECT
    s.State AS State_Name,
    COUNT(cr.Parliament_Constituency) AS Total_Seats
FROM constituencywise_results cr
INNER JOIN statewise_results sr
    ON cr.Parliament_Constituency = sr.Parliament_Constituency
INNER JOIN states s
    ON sr.State_ID = s.State_ID
GROUP BY
    s.State;
```

### 3. Total Seats Won by NDA

A `CASE WHEN` statement was used to classify NDA parties and sum their seats.

```sql
SELECT
    SUM(
        CASE
            WHEN Party IN (
                'Bharatiya Janata Party - BJP',
                'Telugu Desam - TDP',
                'Janata Dal (United) - JD(U)',
                'Shiv Sena - SHS',
                'AJSU Party - AJSUP',
                'Apna Dal (Soneylal) - ADAL',
                'Asom Gana Parishad - AGP',
                'Hindustani Awam Morcha (Secular) - HAMS',
                'Janasena Party - JnP',
                'Janata Dal (Secular) - JD(S)',
                'Lok Janshakti Party(Ram Vilas) - LJPRV',
                'Nationalist Congress Party - NCP',
                'Rashtriya Lok Dal - RLD',
                'Sikkim Krantikari Morcha - SKM'
            )
            THEN Won
            ELSE 0
        END
    ) AS NDA_Total_Seats_Won
FROM partywise_results;
```

**Result:** 292 seats.

### 4. Seats Won by NDA Parties

The NDA parties were filtered and ordered by seats won.

```sql
SELECT
    Party AS Party_Name,
    Won AS Seats_Won
FROM partywise_results
WHERE Party IN (
    -- NDA party list
)
ORDER BY
    Seats_Won DESC;
```

### 5. Total Seats Won by I.N.D.I.A Alliance

A similar `CASE WHEN` approach was used for I.N.D.I.A alliance parties.

**Result:** 234 seats.

### 6. Seats Won by I.N.D.I.A Alliance Parties

The alliance parties were filtered and ranked using:

```sql
ORDER BY Won DESC;
```

This identifies the parties contributing the highest number of seats to the alliance.

### 7. Creating the Party Alliance Column

A new column was added to classify each party into:

- `NDA`
- `I.N.D.I.A`
- `OTHER`

```sql
ALTER TABLE partywise_results
ADD party_alliance VARCHAR(50);
```

The alliance classification was then populated using `UPDATE` statements.

### 8. Total Seats Won by Each Alliance

```sql
SELECT
    party_alliance,
    SUM(Won) AS seats_won
FROM partywise_results
GROUP BY
    party_alliance;
```

**Result:**

| Alliance | Seats Won |
|---|---:|
| I.N.D.I.A | 234 |
| NDA | 292 |
| OTHER | 17 |

### 9. Alliance-wise Seat Breakdown

The project further breaks down the seats within each alliance by filtering `party_alliance` and ordering parties by seats won.

```sql
SELECT
    Party,
    Won
FROM partywise_results
WHERE party_alliance = 'NDA'
ORDER BY Won DESC;
```

The same approach was applied to `I.N.D.I.A`.

### 10. Winning Candidate, Party, Votes and Victory Margin

Multiple tables were joined to identify the winning candidate and party for a selected constituency.

```sql
SELECT
    cr.Winning_Candidate,
    pr.Party,
    cr.Total_Votes,
    cr.Margin,
    s.State,
    cr.Constituency_Name
FROM constituencywise_results cr
INNER JOIN partywise_results pr
    ON cr.Party_ID = pr.Party_ID
INNER JOIN statewise_results sr
    ON cr.Parliament_Constituency = sr.Parliament_Constituency
INNER JOIN states s
    ON sr.State_ID = s.State_ID
WHERE cr.Constituency_Name = 'BARAMATI';
```

### 11. EVM Votes vs Postal Votes

Candidate-level voting data was joined with constituency results.

```sql
SELECT
    cd.EVM_Votes,
    cd.Postal_Votes,
    cd.Total_Votes,
    cd.Candidate,
    cr.Constituency_Name
FROM constituencywise_results cr
JOIN constituencywise_details cd
    ON cr.Constituency_ID = cd.Constituency_ID
WHERE cr.Constituency_Name = 'AMETHI';
```

This provides a candidate-level comparison of EVM and postal votes.

### 12. Highest-Performing Parties in a State

Party and constituency results were joined to calculate seats won by parties in a selected state.

The result can be ordered using:

```sql
ORDER BY Seats_Won DESC;
```

### 13. Alliance-wise Seats by State

Conditional aggregation was used to calculate NDA, I.N.D.I.A and OTHER seats within each state.

```sql
SELECT
    s.State AS State_Name,
    SUM(CASE WHEN p.party_alliance = 'NDA' THEN 1 ELSE 0 END) AS NDA_Seats_Won,
    SUM(CASE WHEN p.party_alliance = 'I.N.D.I.A' THEN 1 ELSE 0 END) AS INDIA_Seats_Won,
    SUM(CASE WHEN p.party_alliance = 'OTHER' THEN 1 ELSE 0 END) AS OTHER_Seats_Won
FROM constituencywise_results cr
JOIN partywise_results p
    ON cr.Party_ID = p.Party_ID
JOIN statewise_results sr
    ON cr.Parliament_Constituency = sr.Parliament_Constituency
JOIN states s
    ON sr.State_ID = s.State_ID
GROUP BY
    s.State
ORDER BY
    s.State;
```

### 14. Top 10 Candidates by EVM Votes

```sql
SELECT TOP 10
    cr.Constituency_Name,
    cr.Constituency_ID,
    cd.Candidate,
    cd.EVM_Votes
FROM constituencywise_details cd
JOIN constituencywise_results cr
    ON cd.Constituency_ID = cr.Constituency_ID
ORDER BY
    cd.EVM_Votes DESC;
```

### 15. Winner and Runner-up Analysis

```sql
WITH RankedCandidates AS (
    SELECT 
        cd.Constituency_ID,
        cd.Candidate,
        cd.Party,
        cd.EVM_Votes,
        cd.Postal_Votes,
        cd.EVM_Votes + cd.Postal_Votes AS Total_Votes,
        ROW_NUMBER() OVER (PARTITION BY cd.Constituency_ID ORDER BY cd.EVM_Votes + cd.Postal_Votes DESC) AS VoteRank
    FROM 
        constituencywise_details cd
    JOIN 
        constituencywise_results cr ON cd.Constituency_ID = cr.Constituency_ID
    JOIN 
        statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
    JOIN 
        states s ON sr.State_ID = s.State_ID
    WHERE 
        s.State = 'Maharashtra'
)

SELECT 
    cr.Constituency_Name,
    MAX(CASE WHEN rc.VoteRank = 1 THEN rc.Candidate END) AS Winning_Candidate,
    MAX(CASE WHEN rc.VoteRank = 2 THEN rc.Candidate END) AS Runnerup_Candidate
FROM 
    RankedCandidates rc
JOIN 
    constituencywise_results cr ON rc.Constituency_ID = cr.Constituency_ID
GROUP BY 
    cr.Constituency_Name
ORDER BY 
    cr.Constituency_Name;

```

### 16. Maharashtra Election Summary

```sql
SELECT 
    COUNT(DISTINCT cr.Constituency_ID) AS Total_Seats,
    COUNT(DISTINCT cd.Candidate) AS Total_Candidates,
    COUNT(DISTINCT p.Party) AS Total_Parties,
    SUM(cd.EVM_Votes + cd.Postal_Votes) AS Total_Votes,
    SUM(cd.EVM_Votes) AS Total_EVM_Votes,
    SUM(cd.Postal_Votes) AS Total_Postal_Votes
FROM 
    constituencywise_results cr
JOIN 
    constituencywise_details cd ON cr.Constituency_ID = cd.Constituency_ID
JOIN 
    statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
JOIN 
    states s ON sr.State_ID = s.State_ID
JOIN 
    partywise_results p ON cr.Party_ID = p.Party_ID
WHERE 
    s.State = 'Maharashtra';
```

| Metric | Result |
|---|---:|
| Total Seats | 48 |
| Total Candidates | 1,114 |
| Total Parties | 7 |
| Total Votes | 57,179,133 |
| Total EVM Votes | 56,969,710 |
| Total Postal Votes | 209,423 |

## Key Insights

### 1. Overall Election

The analysis identifies **543 parliamentary seats** in the 2024 election.

### 2. Alliance Performance

The alliance classification produced the following seat distribution:

- **NDA – 292 seats**
- **I.N.D.I.A – 234 seats**
- **OTHER – 17 seats**

This shows that NDA had the largest seat share among the three alliance categories used in the analysis.

### 3. Leading Individual Parties

The party-level results show:

| Party | Seats Won |
|---|---:|
| Bharatiya Janata Party (BJP) | 240 |
| Indian National Congress (INC) | 99 |
| Samajwadi Party (SP) | 37 |
| All India Trinamool Congress (AITC) | 29 |
| Dravida Munnetra Kazhagam (DMK) | 22 |
| Telugu Desam (TDP) | 16 |
| Janata Dal (United) – JD(U) | 12 |

### 4. Candidate-Level Voting

The candidate-level dataset contains **8,902 records**, allowing analysis of EVM votes, postal votes, total votes and vote share at the candidate/constituency level.

The Top-10 EVM analysis identifies **RAKIBUL HUSSAIN** as having the highest EVM vote count in the supplied dataset, with **1,468,549 EVM votes**.

### 5. Maharashtra

The Maharashtra analysis covers **48 seats and 1,114 candidates**, with more than **57 million total votes** recorded across EVM and postal voting.

## Conclusion

This project demonstrates how SQL can be used to perform an end-to-end analysis of election data across multiple relational datasets.

The project particularly demonstrates practical skills in:

- Relational data understanding
- Joining multiple tables
- Aggregation and grouping
- Conditional aggregation
- `CASE WHEN`
- Data classification
- Filtering
- Ranking
- Window-function based analysis
- Candidate-level analysis
- State-level analysis
- Party and alliance performance analysis
- Translating analytical questions into SQL queries

The project provides a strong example of using **SQL for real-world public-sector/election data analysis**, moving from raw CSV datasets to structured analytical insights.

### Author
Swasti
