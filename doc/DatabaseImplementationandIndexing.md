
-- Table 1:Users
CREATE TABLE Users(
Username VARCHAR(255) PRIMARY KEY, 
Email VARCHAR(255)
)

–- Table 2:Favorites 
CREATE TABLE Favorites (
Username VARCHAR(255),
RecName VARCHAR(255),
Status BOOLEAN,
PRIMARY KEY(Favorites.Username, Users.Username),
FOREIGN KEY(Username) REFERENCES Users(Username)
FOREIGN KEY(RecName) REFERENCES Recreation(RecName)
);

–- Table 3:Comments
CREATE TABLE Comments (
CommentId REAL PRIMARY KEY,
Username VARCHAR(255),
RecName VARCHAR(255), 
Comment VARCHAR(255),
DatePosted VARCHAR(255),
FOREIGN KEY (Username) REFERENCES Users(Username),
FOREIGN KEY (RecName) REFERENCES Recreation(RecName)
);


–- Table 4:Recreation
Create Table Recreation {
RecName VARCHAR(225),
RecType VARCHAR(225),
StateName VARCHAR(225),
Address VARCHAR(225),
PRIMARY KEY(RecName)
FOREIGN KEY(DiscountId) REFERENCES Discounts (DiscountId)
FOREIGN KEY(StateName) REFERENCES States(StateName)
);


CREATE TABLE Provides(
DiscountId VARCHAR(255),
RecName VARCHAR(255),
PRIMARY KEY(Discounts.DiscountId, Recreation.RecName),
FOREIGN KEY (RecName) REFERENCE Recreation(RecName),
FOREIGN KEY(DiscountId) REFERENCE Discounts(DiscountId) 
)


–- Table 5:Discounts
Create Table Discounts(
DiscountId VARCHAR(255) PRIMARY KEY,
RecName VARCHAR(255),
DiscountType VARCHAR(255),
Description VARCHAR(255),
Eligibility VARCHAR(255),
FOREIGN KEY (RecName) REFERENCES Recreation(RecName)
);


–- Table 6:States
Create Table States (
  StateName VARCHAR(225),
  CityCount VARCHAR(225),
  Region VARCHAR(225),
  Population VARCHAR(225),
  TotalArea VARCHAR(225),
  Primary
)


