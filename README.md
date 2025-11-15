# Library-management-sql-project
This SQL query uses:  CTEs (Common Table Expressions)  Window functions (RANK OVER PARTITION)  Aggregations (COUNT, AVG)  Filtering computed values (AvgDelay > 2)  Category-wise ranking  It is ideal for:  DBMS lab records  GitHub portfolio projects  Internship/placement SQL assignments  Mini project on Library Management

# 📘 Library Management System – Advanced SQL Project

This project demonstrates an advanced SQL analysis on a Library Management System
using CTEs, Window Functions, Ranking, Aggregations, and Analytical Queries.

---

## 📌 Database Tables

### 1. Books
| Column     | Type        | Description         |
|------------|-------------|---------------------|
| BookID     | INT (PK)    | Unique book ID      |
| Title      | VARCHAR     | Book title          |
| Author     | VARCHAR     | Author name         |
| Category   | VARCHAR     | Subject category    |
| Copies     | INT         | Available copies    |

### 2. Members
| Column     | Type        | Description         |
|------------|-------------|---------------------|
| MemberID   | INT (PK)    | Library member ID   |
| Name       | VARCHAR     | Member name         |
| JoinDate   | DATE        | Joining date        |

### 3. Borrow
| Column     | Type        | Description                  |
|------------|-------------|------------------------------|
| BorrowID   | INT (PK)    | Borrow transaction ID        |
| MemberID   | INT (FK)    | Member borrowing the book    |
| BookID     | INT (FK)    | Borrowed book                |
| BorrowDate | DATE        | Date of issue                |
| ReturnDate | DATE        | Date of return               |

---

## 🎯 Task

Write an advanced SQL query to determine the **Top 3 most frequently borrowed books**
in the last **6 months**, showing:

- Number of times borrowed  
- Average return delay (in days)  
- Total members who borrowed the book  
- Ranking within category  
- Show only books where **AvgDelay > 2 days**

---

## 🚀 Final Advanced SQL Query

```sql
WITH BorrowStats AS (
    SELECT 
        b.BookID,
        bk.Title,
        bk.Category,
        COUNT(*) AS TimesBorrowed,
        AVG(DATEDIFF(ReturnDate, BorrowDate)) AS AvgDelay,
        COUNT(DISTINCT b.MemberID) AS UniqueMembers
    FROM Borrow b
    JOIN Books bk ON b.BookID = bk.BookID
    WHERE BorrowDate >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
    GROUP BY b.BookID, bk.Title, bk.Category
),
CategoryRank AS (
    SELECT
        *,
        RANK() OVER(
            PARTITION BY Category
            ORDER BY TimesBorrowed DESC
        ) AS CatRank
    FROM BorrowStats
)
SELECT 
    BookID,
    Title,
    Category,
    TimesBorrowed,
    AvgDelay,
    UniqueMembers,
    CatRank
FROM CategoryRank
WHERE CatRank <= 3
  AND AvgDelay > 2
ORDER BY Category, CatRank;
