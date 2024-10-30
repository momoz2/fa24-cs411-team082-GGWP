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