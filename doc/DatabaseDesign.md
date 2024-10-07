# Database Design

## ER Diagram
<img width="1010" alt="image" src="https://github.com/user-attachments/assets/0d3d0a64-9991-47a2-bb1b-6cd5dcd957b3">

States was modeled as a separate entity instead of within the recreation entity to reduce redundancy, since many tuples would have the same state in the recreation entity. Similarly, the favorites entity was modeled separate from the users entity to avoid redundancy with identical favorited recreation among several users. 

Features and recreation have a many-to-many relation, since a feature such as a veteran discount can be applicable to more than one recreation and vice versa - one recreation can have multiple features such as having both a veteran discount and a student discount. One recreation is allowed exactly one state, since there is only one location, though one state (location) can have many recreational activities.

One comment is only allowed one recreation, since a user’s message cannot be applied to multiple recreational activities; this can also minimize fake reviews. One recreation is allowed 0 or more comments, to allow for a variety of user comments. Similarly, one user can leave multiple comments for various recreation but one comment can only be contributed by one user. 


One favorite by a user can only have one recreation (there can be multiple rows with same user but not one row with multiple recreation), while one recreation can be favorited by multiple users. With similar logic, for the “likes” relation - one user can have multiple favorites, but one favorite can only have exactly one user.

## Normalization - 3NF Decomposition
Relations from ER Diagram
```
Users (Username, Email)
Favorites (Username, RecName, Status)
Features (RecName, FeatureType, Eligibility, Description)
Recreation (RecName, RecType, State, Address)
States(StateName, CityCount, Region, TotalArea, Population)
Comments (CommentId, Username, RecName, Comment, DatePosted)
```

Functional Dependencies


## Relational Schema
```
Users (
  Username: VARCHAR(255) [PK],
  Email: VARCHAR(255)
)

Favorites (
  Username: VARCHAR(255) [PK] [FK to Users.Username]
  RecName: VARCHAR(255) [FK to Recreation.RecName],
  Status: BOOLEAN
)

Features (
  RecName: VARCHAR(255) [PK] [FK.Recreation.RecName]
  FeatureType: VARCHAR(255),
  Eligibility: VARCHAR(255),
  Description: VARCHAR(255)
)

Recreation (
  RecName: VARCHAR(255) [PK],
  RecType: VARCHAR(15),
  State: VARCHAR(2) [FK.States.StateName],
  Address: VARCHAR(255)
)

States  (
  StateName: VARCHAR(255) [PK],
  CityCount: INT,
  Region: VARCHAR(255),
  TotalArea: REAL,
  Population: INT
)

Comments (
  CommentId: REAL [PK],
  Username: VARCHAR(255) [FK to Users.Username],
  RecName: VARCHAR(255) [FK to Recreation.RecName],
  Comment: VARCHAR(255),
  DatePosted:VARCHAR(255)
)
```
