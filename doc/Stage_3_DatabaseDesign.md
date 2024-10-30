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



## Printed Number of Rows in Each Table
![12111730233715_ pic](https://github.com/user-attachments/assets/06fc5a99-3ebd-449f-8522-eb1b159bc216)
![12121730233732_ pic](https://github.com/user-attachments/assets/dfc74918-5386-4738-8229-f38386b23538)


## Advanced Query Implementation, Testing and Analysis

**Subquery 1**

Select the top 15 recreational activities with the most comments, in ascending order by state and descending order of total number of favorites.  
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

Query the total number of discounts for each recreational activity that offers discounts (output is less than 15 rows).
```sql
SELECT R.RecName, COUNT(D.DiscountId) AS TotalDiscounts
FROM Recreation R
JOIN Discounts D ON R.RecName = D.RecName
GROUP BY R.RecName
ORDER BY TotalDiscounts DESC;
```
![WechatIMG876](https://github.com/user-attachments/assets/ea5c77af-2dcf-40ef-9a1d-cacbd3a6a025)


**Subquery 3**

Select the the top 15 recreational activities's RecName, RecType, StateName, and total favorite count with the most favorites across all states in descending order.
```sql
SELECT R.RecName, R.RecType, R.StateName, COUNT(F.Username) AS FavoriteCount
FROM Recreation R
JOIN Favorites F ON R.RecName = F.RecName
GROUP BY R.RecName, R.RecType, R.StateName
ORDER BY FavoriteCount DESC
LIMIT 15;
```
![WechatIMG977](https://github.com/user-attachments/assets/f14c766d-b7c5-4435-95f7-3af9c3b43bc9)


**Subquery 4**

Query the top 15 states with their region and total number of recreations in descending order of total number of recreations.
```sql
SELECT S.StateName, S.Region, COUNT(R.RecName) AS TotalRecreation
FROM States S
JOIN Recreation R ON S.StateName = R.StateName
GROUP BY S.StateName, S.Region
ORDER BY TotalRecreation DESC
limit 15;
```
<img width="571" alt="截圖 2024-10-30 16 25 40" src="https://github.com/user-attachments/assets/114326ca-c308-47e2-a70d-ae8b6814b4ab">





# Indexing

## Subquery 1
**Default Index**
- Cost: 802.05
- Time: 7.836..7.840

![image](https://github.com/user-attachments/assets/b90cf67e-465e-4f69-a84f-b9fde2594a5c)

**Index 1:**

We added an index on RecName and StateName by:
```sql
CREATE INDEX idx_recreation_recname_statename ON Recreation(RecName, StateName);
Query OK, 0 rows affected (0.16 sec)
Records: 0  Duplicates: 0  Warnings: 0```
```
![image](https://github.com/user-attachments/assets/aee3623a-8bdc-4700-a44e-655065b3b9fb)

Creating an index on the RecName and StateName attribute of the Recreation table because this field is used within the GROUP BY clause of the query, and indexing it could potentially speed up this frequently-used query by reducing the number of rows that need to be scanned.

The results:
    - The time for execution decreased somewhat, from 7.836..7.840 to 7.388..7.390
    - The cost remained the same at a constant 802.05

**Index 2:**
```sql
CREATE INDEX idx_favorites_recname_username ON Favorites(RecName, Username);
Query OK, 0 rows affected (0.14 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
- New Cost: 802.05
- New Time: 7.2

Creating an index on the RecName and StateName attribute of the Recreation table because this field is used within the GROUP BY clause of the query, and indexing it could potentially speed up this frequently-used query by reducing the number of rows that need to be scanned.

The results:
    - The time for execution decreased somewhat, from 7.836..7.840 to 7.388..7.390
    - The cost remained the same at a constant 802.05



![image](https://github.com/user-attachments/assets/4553c206-76e6-4410-af06-7322ec2d3ccd)

**Index 3:**
```sql
CREATE INDEX idx_states_statename_region ON States(StateName, Region);
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

- New Cost: 802.05
- New Time: 6.489

![image](https://github.com/user-attachments/assets/c8324a95-a620-421a-938c-b15b5c9067b2)

We chose to create a composite index on the StateName and Region columns in the States table. This decision was made because both fields are frequently used in the GROUP BY and ORDER BY clauses within the query, making them ideal candidates for indexing. By indexing these fields together, we aimed to speed up the sorting and grouping operations, reducing query execution time.

  
Findings and Explanation:
The actual time decreased from 7.198 to 6.489 seconds.The cost remained roughly the same, around 802. The indexing strategy effectively optimizes the query by improving the speed of joins, aggregations, and sorting operations, resulting in faster execution times and lower resource usage



## Subquery 2
**Default Index**

**Index 1:**

**Index 2:**

**Index 3:**



## Subquery 3
**Default Index**

![9300171136708425d64930321ac61d30](https://github.com/user-attachments/assets/9645fec7-bdb6-421d-803f-ef9d242e225f)


**Index 1:**

**Index 2:**

**Index 3:**




## Subquery 4
**Default Index**

**Index 1:**

**Index 2:**

**Index 3:**
