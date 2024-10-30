# Database Implementation and Indexing

## DDL Table Commands
```
CREATE DATABASE Rec;
USE Rec;

-- Users table
CREATE TABLE Users(
    Username VARCHAR(255) PRIMARY KEY, 
    Email VARCHAR(255)
);

-- States table
CREATE TABLE States (
    StateName VARCHAR(225) PRIMARY KEY,
    CityCount VARCHAR(225),
    Region VARCHAR(225),
    Population VARCHAR(225),
    TotalArea VARCHAR(225)
);

-- Recreations table
CREATE TABLE Recreation (
    RecName VARCHAR(225) PRIMARY KEY,
    RecType VARCHAR(225),
    StateName VARCHAR(225),
    Address VARCHAR(225),
    FOREIGN KEY (StateName) REFERENCES States(StateName)
);

-- Discounts table
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

-- Favorites table
CREATE TABLE Favorites (
    Username VARCHAR(255),
    RecName VARCHAR(255),
    Status BOOLEAN,
    PRIMARY KEY (Username, RecName),
    FOREIGN KEY (Username) REFERENCES Users(Username),
    FOREIGN KEY (RecName) REFERENCES Recreation(RecName)
);

-- Comments table
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
![WechatIMG855](https://github.com/user-attachments/assets/fea4d8a4-c863-415f-9fb5-5a02a894f800)
![WechatIMG856](https://github.com/user-attachments/assets/4b18a538-394f-497d-8e5e-684a5ad009da)


## Advanced Query Implementation and Testing 
**Subquery 1**
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
```
ELECT R.RecName, COUNT(D.DiscountId) AS TotalDiscounts
FROM Recreation R
JOIN Discounts D ON R.RecName = D.RecName
GROUP BY R.RecName
ORDER BY TotalDiscounts DESC;
```
![8761730237084_ pic](https://github.com/user-attachments/assets/cfe7d47a-a9d2-4060-a85f-5752d35265eb)

**Subquery 4**
```
SELECT S.StateName, S.Region, COUNT(R.RecName) AS TotalRecreation
FROM States S
JOIN Recreation R ON S.StateName = R.StateName
GROUP BY S.StateName, S.Region
ORDER BY TotalRecreation DESC;
```

<img width="257" alt="image" src="https://github.com/user-attachments/assets/16761f46-bb54-4bd8-9c40-0531e7e9dcea">
