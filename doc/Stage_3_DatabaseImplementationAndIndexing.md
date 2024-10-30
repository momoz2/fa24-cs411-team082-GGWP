# Database Implementation and Indexing

## Tables in our terminal

![13061730318691_ pic](https://github.com/user-attachments/assets/21fbc00f-4989-4c20-b0ca-ff91a77317a7)


## DDL Table Commands
```
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
We have 2,002 users and 3,952 recreation activities and 1,001 favourites
![12111730233715_ pic](https://github.com/user-attachments/assets/06fc5a99-3ebd-449f-8522-eb1b159bc216)
We have 1,000 comments
![12121730233732_ pic](https://github.com/user-attachments/assets/dfc74918-5386-4738-8229-f38386b23538)


## Advanced Query Implementation, Testing and Analysis
**Subquery 1**
Select the fifteen recreational activities with the most comments, organised by region (alphabetical ascending) and number of comments (descending).  
```
SELECT R.RecName, S.Region, COUNT(C.CommentId) AS TotalComments 
FROM Recreation R JOIN States S ON R.StateName = S.StateName 
JOIN Comments C ON R.RecName = C.RecName 
WHERE S.Region IN (SELECT DISTINCT Region FROM States) 
GROUP BY R.RecName, S.Region 
ORDER BY S.Region, TotalComments DESC LIMIT 5;
```
![8691730236554_ pic](https://github.com/user-attachments/assets/506ae957-2446-482f-8b65-8116d0a877bd)


**Subquery 2**
Select the fifteen recreational activities with the most favorites, organised by region (alphabetical ascending) and number of favorites (descending).
```
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
```
SELECT R.RecName, COUNT(D.DiscountId) AS TotalDiscounts
FROM Recreation R
JOIN Discounts D ON R.RecName = D.RecName
GROUP BY R.RecName
ORDER BY TotalDiscounts DESC;
```
![8761730237084_ pic](https://github.com/user-attachments/assets/cfe7d47a-a9d2-4060-a85f-5752d35265eb)

**Subquery 4**
For each state and the region they occupy, select their name and region info and total number of recreations. Organise by number of recreations (descending).
```
SELECT S.StateName, S.Region, COUNT(R.RecName) AS TotalRecreation
FROM States S
JOIN Recreation R ON S.StateName = R.StateName
GROUP BY S.StateName, S.Region
ORDER BY TotalRecreation DESC;
```

<img width="257" alt="image" src="https://github.com/user-attachments/assets/16761f46-bb54-4bd8-9c40-0531e7e9dcea">
