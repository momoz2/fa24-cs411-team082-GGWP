DROP DATABASE IF EXISTS Rec;
CREATE DATABASE Rec;
USE Rec;

CREATE TABLE Users(
    Username VARCHAR(255) PRIMARY KEY, 
    Email VARCHAR(255)
);

CREATE TABLE States (
    StateName VARCHAR(225) PRIMARY KEY,
    CityCount VARCHAR(225),
    Region VARCHAR(225),
    Population VARCHAR(225),
    TotalArea VARCHAR(225)
);

CREATE TABLE Recreation (
    RecName VARCHAR(225) PRIMARY KEY,
    RecType VARCHAR(225),
    StateName VARCHAR(225),
    Address VARCHAR(225),
    FOREIGN KEY (StateName) REFERENCES States(StateName)
);

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

CREATE TABLE Favorites (
    Username VARCHAR(255),
    RecName VARCHAR(255),
    Status BOOLEAN,
    PRIMARY KEY (Username, RecName),
    FOREIGN KEY (Username) REFERENCES Users(Username),
    FOREIGN KEY (RecName) REFERENCES Recreation(RecName)
);

CREATE TABLE Comments (
    CommentId REAL PRIMARY KEY,
    Username VARCHAR(255),
    RecName VARCHAR(255), 
    Comment VARCHAR(255),
    DatePosted VARCHAR(255),
    FOREIGN KEY (Username) REFERENCES Users(Username),
    FOREIGN KEY (RecName) REFERENCES Recreation(RecName)
);
