# Database Implementation and Indexing

## Relational Schema

![13061730318691_ pic](https://github.com/user-attachments/assets/21fbc00f-4989-4c20-b0ca-ff91a77317a7)


## DDL Table Commands
```sql
CREATE DATABASE Rec;
USE Rec;

-- 1. Users table
CREATE TABLE Users(
    Username VARCHAR(255) PRIMARY KEY, 
    Email VARCHAR(255)
);

-- 2. States table
CREATE TABLE States (
    StateName VARCHAR(225) PRIMARY KEY,
    CityCount VARCHAR(225),
    Region VARCHAR(225),
    Population VARCHAR(225),
    TotalArea VARCHAR(225)
);

-- 3. Recreations table
CREATE TABLE Recreation (
    RecName VARCHAR(225) PRIMARY KEY,
    RecType VARCHAR(225),
    StateName VARCHAR(225),
    Address VARCHAR(225),
    FOREIGN KEY (StateName) REFERENCES States(StateName)
);

-- 4. Discounts table
CREATE TABLE Discounts (
    DiscountId VARCHAR(255) PRIMARY KEY,
    RecName VARCHAR(255),
    DiscountType VARCHAR(255),
    Description VARCHAR(255),
    Eligibility VARCHAR(255),
    FOREIGN KEY (RecName) REFERENCES Recreation(RecName)
);

CREATE TABLE Provides (
    DiscountId VARCHAR(255),
    RecName VARCHAR(255),
    PRIMARY KEY (DiscountId, RecName),
    FOREIGN KEY (RecName) REFERENCES Recreation(RecName),
    FOREIGN KEY (DiscountId) REFERENCES Discounts(DiscountId)
);

-- 5. Favorites table
CREATE TABLE Favorites (
    Username VARCHAR(255),
    RecName VARCHAR(255),
    Status BOOLEAN,
    PRIMARY KEY (Username, RecName),
    FOREIGN KEY (Username) REFERENCES Users(Username),
    FOREIGN KEY (RecName) REFERENCES Recreation(RecName)
);

-- 6. Comments table
CREATE TABLE Comments (
    CommentId REAL PRIMARY KEY,
    Username VARCHAR(255),
    RecName VARCHAR(255), 
    Comment VARCHAR(255),
    DatePosted VARCHAR(255),
    FOREIGN KEY (Username) REFERENCES Users(Username),
    FOREIGN KEY (RecName) REFERENCES Recreation(RecName)
);
```


## Inserting Data
![13071730318715_ pic](https://github.com/user-attachments/assets/9244cea4-0d4e-4f5c-a2ac-6ced6bc1a197)
![13081730318752_ pic](https://github.com/user-attachments/assets/4adda4ce-5cf4-417f-8cd0-b78d8da48ca1)



## At least 1000 rows in at least 3 tables
We have 2,002 users, 3,952 recreation activities, 1,001 favorites, 1,000 comments.
![12111730233715_ pic](https://github.com/user-attachments/assets/06fc5a99-3ebd-449f-8522-eb1b159bc216)
![12121730233732_ pic](https://github.com/user-attachments/assets/dfc74918-5386-4738-8229-f38386b23538)


## Advanced Query Implementation, Testing and Analysis

**Subquery 1**

Select the fifteen recreational activities with the most comments, organised by region (alphabetical ascending) and number of comments (descending).  
```sql
SELECT R.RecName, S.StateName, COUNT(F.RecName) AS TotalFavorites
FROM Recreation R
JOIN Favorites F ON R.RecName = F.RecName
JOIN States S ON R.StateName = S.StateName
GROUP BY R.RecName, S.StateName
ORDER BY S.StateName, TotalFavorites DESC
LIMIT 15;
```
![13101730319267_ pic](https://github.com/user-attachments/assets/eb157ada-1811-40ad-a164-8dabfdbd5709)



**Subquery 2**

Select the fifteen recreational activities with the most favorites, organised by region (alphabetical ascending) and number of favorites (descending).
```sql
SELECT R.RecName, S.StateName, COUNT(F.RecName) AS TotalFavorites
FROM Recreation R
JOIN Favorites F ON R.RecName = F.RecName
JOIN States S ON R.StateName = S.StateName
GROUP BY R.RecName, S.StateName
ORDER BY S.StateName, TotalFavorites DESC
LIMIT 5;
```
![8731730236895_ pic](https://github.com/user-attachments/assets/1b0cacc7-fd90-4b44-9a71-f2cc783cceaf)


**Subquery 3**

Select the recreation activities with the most discounts, organised by number of discounts (descending).
```sql
SELECT R.RecName, R.RecType, R.StateName, COUNT(F.Username) AS FavoriteCount
FROM Recreation R
JOIN Favorites F ON R.RecName = F.RecName
GROUP BY R.RecName, R.RecType, R.StateName
ORDER BY FavoriteCount DESC
LIMIT 15;
```
![WechatIMG977](https://github.com/user-attachments/assets/f14c766d-b7c5-4435-95f7-3af9c3b43bc9)

Select the the top 15 recreational activities's RecName, RecType, StateName, and total favorite count with the most favorites across all states 
**Subquery 4**

For each state and the region they occupy, select their name and region info and total number of recreations. Organise by number of recreations (descending).
```sql
SELECT S.StateName, S.Region, COUNT(R.RecName) AS TotalRecreation
FROM States S
JOIN Recreation R ON S.StateName = R.StateName
GROUP BY S.StateName, S.Region
ORDER BY TotalRecreation DESC;
```

<img width="257" alt="image" src="https://github.com/user-attachments/assets/16761f46-bb54-4bd8-9c40-0531e7e9dcea">


# Indexing
```sql
CREATE INDEX idx_states_region ON States (Region);
Query OK, 0 rows affected (0.13 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
Index 1: idx_states_region on the States table
We added an index on Region in the States table by using:

We chose to create an index on the Region attribute of the States table because:

The Region column is used in the WHERE clause for filtering records.
Indexing this attribute helps reduce the number of rows scanned when filtering for distinct regions across states.
In this specific query, the Region is also part of the GROUP BY clause, which further benefits from an index when aggregating data by region.
Observations from the EXPLAIN ANALYZE output:

The total execution time decreased slightly after applying the index, improving the query's efficiency when scanning the States table.
The query now performs an "Index lookup" on States using idx_states_region, which reduces the cost of scanning the table.
Before the index: The query had to perform a full table scan on the States table to filter by region.
After the index: The query uses an index range scan to find matching regions, which decreases the number of rows scanned.
Performance Impact:

Execution time: Improved slightly, especially when filtering distinct regions.
Cost reduction: The overall cost of scanning the States table was reduced, as evidenced by the shift from a full table scan to an indexed lookup.

```sql
CREATE INDEX idx_favorites_recname ON Favorites (RecName);
Query OK, 0 rows affected (0.22 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
Index 2: idx_favorites_recname on the Favorites table
We added an index on RecName in the Favorites table by using:

We chose to create an index on the RecName attribute of the Favorites table because:

The RecName column is used in the JOIN condition with the Recreation table, making it a key candidate for indexing. This helps speed up the join operation between the two tables.
The query frequently accesses RecName when counting total favorites for each recreation, and indexing helps in reducing the number of rows scanned during aggregation.
Observations from the EXPLAIN ANALYZE output:

The query now performs a "Covering index scan" on Favorites using idx_favorites_recname, which significantly improved the efficiency of the join operation with the Recreation table.
The Nested loop inner join operation's actual time was reduced as the query was able to quickly locate matching RecName values through the index rather than performing a full scan.
Before the index: The query needed to perform a full table scan to find matching RecName values between Favorites and Recreation.
After the index: The index lookup improved the performance by directly retrieving the relevant records using the index, significantly reducing query execution time.
Performance Impact:

Execution time: Decreased noticeably, especially in the nested loop join between Favorites and Recreation, as shown in the "Covering index scan" step.
Cost reduction: The query cost decreased by optimizing how records are fetched in the join, as evidenced by the shift from a full scan to an index lookup.

```sql
CREATE INDEX idx_discounts_recname ON Discounts (RecName);
Query OK, 0 rows affected (0.19 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
Index 3: idx_discounts_recname on the Discounts table
We added an index on RecName in the Discounts table by using:

We chose to create an index on the RecName attribute of the Discounts table because:

The RecName column is used in the JOIN clause with the Recreation table, so indexing it allows the query to perform faster lookups during the join operation.
This query aggregates discounts by recreation name, and indexing RecName helps in efficiently counting the discounts for each recreational activity.
Observations from the EXPLAIN ANALYZE output:

The query now performs a "Covering index scan" on Discounts using idx_discounts_recname, which drastically reduces the cost of fetching RecName from the Discounts table during the join with Recreation.
Before the index: The query had to scan more rows to find matching RecName values, resulting in higher costs for the join operation.
After the index: The query uses an index scan on Discounts, allowing for a much faster match on RecName values, improving the performance of the JOIN operation.
Performance Impact:

Execution time: Decreased significantly, especially for the nested loop join between Discounts and Recreation.
Cost reduction: The cost of the join operation decreased, as seen from the efficient index scan on RecName rather than performing a full table scan.

```sql
CREATE INDEX idx_recreation_statename ON Recreation (StateName);
Query OK, 0 rows affected (0.19 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
Index 4: idx_recreation_statename on the Recreation table
We added an index on StateName in the Recreation table by using:

We chose to create an index on the StateName attribute of the Recreation table because:

The StateName column is frequently used in the JOIN operation between the Recreation and States tables. Indexing this column allows the query to quickly find recreation activities based on their associated state.
This query aggregates the total number of recreation activities for each state and region, and indexing the StateName helps improve the speed of the aggregation process by reducing the time spent finding recreation records.
Observations from the EXPLAIN ANALYZE output:

The query now performs a "Covering index lookup" on the Recreation table using idx_recreation_statename, which significantly reduces the time and cost associated with finding recreation activities based on StateName.
Before the index: The query had to perform a full scan on the Recreation table to match StateName with the States table.
After the index: The indexed lookup speeds up the process, as it can directly retrieve the relevant recreation records associated with each state, reducing the time spent in the join operation.
Performance Impact:

Execution time: Decreased significantly due to the index improving the efficiency of the nested loop join between the States and Recreation tables.
Cost reduction: The overall cost of the join operation decreased, as seen in the lower cost associated with the indexed scan compared to a full table scan.
