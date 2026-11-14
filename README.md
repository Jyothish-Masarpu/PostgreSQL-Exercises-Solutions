# PostgreSQL Exercises – Solutions

A collection of PostgreSQL practice questions and solutions from [PGExercises](https://pgexercises.com/).

This repository contains solutions with simple explanations and references to the concepts used. It is intended for learning, revision and practice.

## Table of Contents

1. [Introduction](#introduction)
2. [Database Overview](#database-overview)
3. [Simple SQL Queries](#1-simple-sql-queries)
4. [Joins and Subqueries](#2-joins-and-subqueries)
5. [Modifying Data](#3-modifying-data)
6. [Aggregation](#4-aggregation)
7. [Working with Timestamps](#5-working-with-timestamps)
8. [String Operations](#6-string-operations)
9. [Recursive Queries](#7-recursive-queries)

---

## Introduction

[PGExercises](https://pgexercises.com/) is a website that provides SQL exercises using a country club database.

The database contains information about:
- Members
- Facilities
- Bookings

The exercises start with basic SELECT queries and gradually introduce joins, subqueries, aggregation, date functions, string functions and recursive queries.

**Basics and concepts reference:**  
[PostgreSQL Basics and Concepts](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts)

---

## Database Overview

The exercises mainly use three tables.

### 1. cd.members

Stores information about club members.

| Column | Description |
|---|---|
| memid | Member ID |
| surname | Member's surname |
| firstname | Member's first name |
| address | Address |
| zipcode | ZIP code |
| telephone | Telephone number |
| recommendedby | ID of the member who recommended them |
| joindate | Date the member joined |

### 2. cd.facilities

Stores information about club facilities.

| Column | Description |
|---|---|
| facid | Facility ID |
| name | Facility name |
| membercost | Cost for members |
| guestcost | Cost for guests |
| initialoutlay | Initial setup cost |
| monthlymaintenance | Monthly maintenance cost |

### 3. cd.bookings

Stores information about facility bookings.

| Column | Description |
|---|---|
| bookid | Booking ID |
| facid | Facility ID |
| memid | Member ID |
| starttime | Booking start time |
| slots | Number of half-hour slots |

**Important:** One booking slot represents 30 minutes, or 0.5 hours. Member ID `0` represents a guest.

---

# 1. Simple SQL Queries

Official exercises: [Basic SQL Queries](https://pgexercises.com/questions/basic/)

## Question 1: Retrieve everything from a table

**Question:** How can you retrieve all the information from the `cd.facilities` table?

### SQL Solution

```sql
SELECT *
FROM cd.facilities;
```

**Explanation:**

- `SELECT *` retrieves all columns.
- `FROM` specifies the table.
- Every record from the facilities table is returned.

**Concepts used:** SELECT, FROM.

**Reference:** [SELECT](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#2-basic-sql-commands)

---

## Question 2: Retrieve specific columns from a table

**Question:** Retrieve only the facility names and their costs to members.

### SQL Solution

```sql
SELECT name, membercost
FROM cd.facilities;
```

**Explanation:**

We specify only the columns we need instead of retrieving the entire table.

**Concepts used:** SELECT, column selection.

**Reference:** [Basic SQL Commands](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#2-basic-sql-commands)

---

## Question 3: Control which rows are retrieved

**Question:** Retrieve all facilities that charge a fee to members.

### SQL Solution

```sql
SELECT *
FROM cd.facilities
WHERE membercost > 0;
```

**Explanation:**

`WHERE` filters the rows. Only facilities with a member cost greater than zero are returned.

**Concepts used:** WHERE, comparison operators.

**Reference:** [WHERE and Operators](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#4-sql-operators)

---

## Question 4: Control which rows are retrieved - Part 2

**Question:** Find facilities that charge a fee to members, where the fee is less than 1/50th of the monthly maintenance cost. Return facility ID, name, member cost and monthly maintenance.

### SQL Solution

```sql
SELECT
    facid,
    name,
    membercost,
    monthlymaintenance
FROM cd.facilities
WHERE membercost > 0
  AND membercost < monthlymaintenance / 50.0;
```

**Explanation:**

- `membercost > 0` checks that the facility charges members.
- `AND` requires both conditions to be true.
- The second condition compares the member cost with one-fiftieth of the maintenance cost.
- `50.0` ensures decimal division.

**Concepts used:** WHERE, AND, comparison operators, arithmetic.

**Reference:** [SQL Operators](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#4-sql-operators)

---

## Question 5: Basic string searches

**Question:** Find all facilities whose names contain the word `Tennis`.

### SQL Solution

```sql
SELECT *
FROM cd.facilities
WHERE name LIKE '%Tennis%';
```

**Explanation:**

- `%` matches zero or more characters.
- `%Tennis%` searches for Tennis anywhere in the name.

**Concepts used:** LIKE, pattern matching.

**Reference:** [Pattern Matching](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#5-pattern-matching)

---

## Question 6: Matching against multiple possible values

**Question:** Retrieve the details of facilities with IDs 1 and 5 without using OR.

### SQL Solution

```sql
SELECT *
FROM cd.facilities
WHERE facid IN (1, 5);
```

**Explanation:**

`IN` checks whether the facility ID matches any value in the given list.

**Alternative:**

```sql
SELECT *
FROM cd.facilities
WHERE facid = 1 OR facid = 5;
```

**Concepts used:** IN, OR, WHERE.

**Reference:** [SQL Operators](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#4-sql-operators)

---

## Question 7: Classify results into buckets

**Question:** Label facilities as `cheap` or `expensive` depending on whether their monthly maintenance cost is more than 100.

### SQL Solution

```sql
SELECT
    name,
    CASE
        WHEN monthlymaintenance > 100 THEN 'expensive'
        ELSE 'cheap'
    END AS cost
FROM cd.facilities;
```

**Explanation:**

`CASE WHEN` works like an if-else condition.

- If maintenance is greater than 100, the result is expensive.
- Otherwise, the result is cheap.
- `AS cost` gives a name to the output column.

**Concepts used:** CASE, WHEN, ELSE, AS.

**Reference:** [Conditional Expressions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#13-conditional-expressions)

---

## Question 8: Working with dates

**Question:** Find members who joined on or after September 1, 2012. Return member ID, surname, first name and join date.

### SQL Solution

```sql
SELECT
    memid,
    surname,
    firstname,
    joindate
FROM cd.members
WHERE joindate >= '2012-09-01';
```

**Explanation:**

The condition checks whether the join date is greater than or equal to September 1, 2012.

**Concepts used:** WHERE, date comparison.

**Reference:** [Date and Time Functions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#15-date-and-time-functions)

---

## Question 9: Removing duplicates and ordering results

**Question:** Retrieve the first 10 unique surnames from the members table in alphabetical order.

### SQL Solution

```sql
SELECT DISTINCT surname
FROM cd.members
ORDER BY surname
LIMIT 10;
```

**Explanation:**

- `DISTINCT` removes duplicate surnames.
- `ORDER BY` sorts the results.
- `LIMIT 10` returns only the first 10 rows.

**Concepts used:** DISTINCT, ORDER BY, LIMIT.

**Reference:** [Basic SQL Commands](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#2-basic-sql-commands)

---

## Question 10: Combining results from multiple queries

**Question:** Produce a combined list of all member surnames and facility names.

### SQL Solution

```sql
SELECT surname
FROM cd.members

UNION

SELECT name
FROM cd.facilities;
```

**Explanation:**

`UNION` combines the results of two SELECT statements and removes duplicates.

Both queries must return the same number of columns with compatible data types.

**Alternative:**

```sql
SELECT surname
FROM cd.members

UNION ALL

SELECT name
FROM cd.facilities;
```

`UNION ALL` keeps duplicates.

**Concepts used:** UNION, UNION ALL.

**Reference:** [Set Operations](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#14-set-operations)

---

## Question 11: Simple aggregation

**Question:** Find the signup date of the last member who joined the club.

### SQL Solution

```sql
SELECT MAX(joindate) AS latest
FROM cd.members;
```

**Explanation:**

`MAX()` returns the highest value in a column. Here, it returns the latest join date.

**Concepts used:** MAX, aggregate functions, alias.

**Reference:** [Aggregate Functions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#9-aggregate-functions)

---

## Question 12: More aggregation

**Question:** Find the first name, surname and join date of the last member or members who signed up.

### SQL Solution

```sql
SELECT
    firstname,
    surname,
    joindate
FROM cd.members
WHERE joindate = (
    SELECT MAX(joindate)
    FROM cd.members
);
```

**Explanation:**

1. The inner query finds the latest join date.
2. The outer query retrieves the member details matching that date.

**Concepts used:** MAX, subquery, WHERE.

**Reference:** [Subqueries](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#12-subqueries)

---

# 2. Joins and Subqueries

Official exercises: [Joins and Subqueries](https://pgexercises.com/questions/joins/)

## Question 1: Retrieve the start times of members' bookings

**Question:** Find the booking start times for the member named David Farrell.

### SQL Solution

```sql
SELECT b.starttime
FROM cd.bookings b
INNER JOIN cd.members m
    ON b.memid = m.memid
WHERE m.firstname = 'David'
  AND m.surname = 'Farrell';
```

**Explanation:**

- `bookings` contains booking times.
- `members` contains member names.
- `INNER JOIN` connects the tables using `memid`.
- `WHERE` selects David Farrell's bookings.

**Concepts used:** INNER JOIN, ON, WHERE.

**Reference:** [Joins](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#11-joins)

---

## Question 2: Work out the start times of bookings for tennis courts

**Question:** Find bookings for tennis courts on September 21, 2012. Return the start time and facility name, ordered by start time.

### SQL Solution

```sql
SELECT
    b.starttime AS start,
    f.name
FROM cd.bookings b
INNER JOIN cd.facilities f
    ON b.facid = f.facid
WHERE f.name LIKE 'Tennis Court%'
  AND b.starttime >= '2012-09-21'
  AND b.starttime < '2012-09-22'
ORDER BY b.starttime;
```

**Explanation:**

- Join bookings with facilities using `facid`.
- `LIKE 'Tennis Court%'` selects tennis courts.
- The date range selects bookings on September 21.
- `ORDER BY` sorts by booking start time.

**Concepts used:** INNER JOIN, LIKE, date filtering, ORDER BY.

**Reference:** [Joins](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#11-joins)

---

## Question 3: Produce a list of all members who have recommended another member

**Question:** Find members who have recommended someone. Remove duplicates and order by surname and first name.

### SQL Solution

```sql
SELECT DISTINCT
    m.firstname,
    m.surname
FROM cd.members m
INNER JOIN cd.members recommended
    ON m.memid = recommended.recommendedby
ORDER BY m.surname, m.firstname;
```

**Explanation:**

This is a self join because the members table is joined with itself.

The join finds members whose IDs appear in another member's `recommendedby` column.

`DISTINCT` removes duplicates.

**Alternative using IN:**

```sql
SELECT DISTINCT
    firstname,
    surname
FROM cd.members
WHERE memid IN (
    SELECT recommendedby
    FROM cd.members
)
ORDER BY surname, firstname;
```

**Concepts used:** SELF JOIN, INNER JOIN, DISTINCT, IN, subquery.

**Reference:** [Joins and Subqueries](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#11-joins)

---

## Question 4: Produce a list of all members, along with their recommender

**Question:** Display all members and the details of the members who recommended them, if available. Order by surname and first name.

### SQL Solution

```sql
SELECT
    m.firstname AS memfname,
    m.surname AS memsname,
    r.firstname AS recfname,
    r.surname AS recsname
FROM cd.members m
LEFT JOIN cd.members r
    ON m.recommendedby = r.memid
ORDER BY m.surname, m.firstname;
```

**Explanation:**

- A self join connects members with their recommenders.
- `LEFT JOIN` keeps all members.
- If a member has no recommender, the recommender columns contain NULL.

**Concepts used:** LEFT JOIN, SELF JOIN, aliases.

**Reference:** [Joins](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#11-joins)

---

## Question 5: Produce a list of all members who have used a tennis court

**Question:** Find members who have used a tennis court. Display the member's full name and facility name. Remove duplicates and order by member name.

### SQL Solution

```sql
SELECT DISTINCT
    m.firstname || ' ' || m.surname AS member,
    f.name AS facility
FROM cd.bookings b
INNER JOIN cd.members m
    ON b.memid = m.memid
INNER JOIN cd.facilities f
    ON b.facid = f.facid
WHERE f.name LIKE 'Tennis Court%'
ORDER BY member;
```

**Explanation:**

Three tables are joined:

1. Bookings provides booking details.
2. Members provides member names.
3. Facilities provides facility names.

Only tennis court bookings are selected.

**Concepts used:** Multiple INNER JOINs, concatenation, DISTINCT, LIKE.

**Reference:** [Joins](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#11-joins)

---

## Question 6: Produce a list of costly bookings

**Question:** Find bookings on September 14, 2012, that cost more than 30. Calculate the cost differently for members and guests. Display facility name, member name and cost, ordered by descending cost. Do not use subqueries.

### SQL Solution

```sql
SELECT
    f.name AS facility,
    m.firstname || ' ' || m.surname AS member,
    CASE
        WHEN b.memid = 0 THEN b.slots * f.guestcost
        ELSE b.slots * f.membercost
    END AS cost
FROM cd.bookings b
INNER JOIN cd.facilities f
    ON b.facid = f.facid
INNER JOIN cd.members m
    ON b.memid = m.memid
WHERE b.starttime >= '2012-09-14'
  AND b.starttime < '2012-09-15'
  AND (
      CASE
          WHEN b.memid = 0 THEN b.slots * f.guestcost
          ELSE b.slots * f.membercost
      END
  ) > 30
ORDER BY cost DESC;
```

**Explanation:**

- Join the three tables.
- Guest ID is `0`.
- Guests pay `guestcost`.
- Members pay `membercost`.
- Multiply the cost by the number of slots.
- Keep bookings costing more than 30.

**Concepts used:** INNER JOIN, CASE, arithmetic, date filtering, ORDER BY.

**Reference:** [Conditional Expressions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#13-conditional-expressions)

---

## Question 7: Produce a list of all members, along with their recommender, using no joins

**Question:** Display every member's full name and their recommender's full name, if available. Do not use JOIN syntax. Remove duplicates and order by member name.

### SQL Solution

```sql
SELECT DISTINCT
    m.firstname || ' ' || m.surname AS member,
    (
        SELECT r.firstname || ' ' || r.surname
        FROM cd.members r
        WHERE r.memid = m.recommendedby
    ) AS recommender
FROM cd.members m
ORDER BY member;
```

**Explanation:**

The subquery finds the recommender's name using the `recommendedby` value.

If there is no recommender, the subquery returns NULL.

**Concepts used:** Correlated subquery, SELECT, aliases, DISTINCT.

**Reference:** [Subqueries](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#12-subqueries)

---

## Question 8: Produce a list of costly bookings, using a subquery

**Question:** Simplify the costly bookings calculation by calculating the booking cost inside a subquery first. Return bookings on September 14, 2012, costing more than 30, ordered by descending cost.

### SQL Solution

```sql
SELECT
    booking_data.member,
    booking_data.facility,
    booking_data.cost
FROM (
    SELECT
        m.firstname || ' ' || m.surname AS member,
        f.name AS facility,
        CASE
            WHEN b.memid = 0 THEN b.slots * f.guestcost
            ELSE b.slots * f.membercost
        END AS cost
    FROM cd.bookings b
    INNER JOIN cd.facilities f
        ON b.facid = f.facid
    INNER JOIN cd.members m
        ON b.memid = m.memid
    WHERE b.starttime >= '2012-09-14'
      AND b.starttime < '2012-09-15'
) AS booking_data
WHERE booking_data.cost > 30
ORDER BY booking_data.cost DESC;
```

**Explanation:**

The inner query calculates the booking cost once and gives it the name `cost`.

The outer query filters the calculated cost.

**Concepts used:** Subquery, INNER JOIN, CASE, aliases.

**Reference:** [Subqueries](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#12-subqueries)

---

# 3. Modifying Data

Official exercises: [Modifying Data](https://pgexercises.com/questions/updates/)

**Important:** These queries change or delete database records. Run them only in your practice database.

## Question 1: Insert some data into a table

**Question:** Add a new facility named Spa with the given values.

### SQL Solution

```sql
INSERT INTO cd.facilities
    (facid, name, membercost, guestcost, initialoutlay, monthlymaintenance)
VALUES
    (9, 'Spa', 20, 30, 100000, 800);
```

**Explanation:**

`INSERT INTO` adds a new record to the facilities table.

**Concepts used:** INSERT INTO, VALUES.

---

## Question 2: Insert multiple rows of data into a table

**Question:** Insert the Spa and Squash Court 2 facilities in one command.

### SQL Solution

```sql
INSERT INTO cd.facilities
    (facid, name, membercost, guestcost, initialoutlay, monthlymaintenance)
VALUES
    (9, 'Spa', 20, 30, 100000, 800),
    (10, 'Squash Court 2', 3.5, 17.5, 5000, 80);
```

**Explanation:**

Multiple rows can be inserted using one INSERT statement by separating the value sets with commas.

**Concepts used:** INSERT, multiple VALUES.

---

## Question 3: Insert calculated data into a table

**Question:** Insert the Spa facility while automatically generating the next facility ID.

### SQL Solution

```sql
INSERT INTO cd.facilities
    (facid, name, membercost, guestcost, initialoutlay, monthlymaintenance)
SELECT
    (SELECT MAX(facid) + 1 FROM cd.facilities),
    'Spa',
    20,
    30,
    100000,
    800;
```

**Explanation:**

The subquery finds the highest facility ID and adds 1. That value is used for the new facility.

**Concepts used:** INSERT, SELECT, MAX, subquery.

---

## Question 4: Update some existing data

**Question:** Correct the initial outlay of the second tennis court to 10000.

### SQL Solution

```sql
UPDATE cd.facilities
SET initialoutlay = 10000
WHERE facid = 1;
```

**Explanation:**

`UPDATE` changes existing records.

`WHERE` ensures that only the facility with ID 1 is updated.

**Concepts used:** UPDATE, SET, WHERE.

---

## Question 5: Update multiple rows and columns at the same time

**Question:** Set the member cost to 6 and guest cost to 30 for both tennis courts.

### SQL Solution

```sql
UPDATE cd.facilities
SET
    membercost = 6,
    guestcost = 30
WHERE facid IN (0, 1);
```

**Explanation:**

Both columns are updated in the same statement. `IN` selects facility IDs 0 and 1.

**Concepts used:** UPDATE, SET, IN.

---

## Question 6: Update a row based on the contents of another row

**Question:** Make the second tennis court cost 10% more than the first tennis court.

### SQL Solution

```sql
UPDATE cd.facilities
SET
    membercost = (
        SELECT membercost * 1.1
        FROM cd.facilities
        WHERE facid = 0
    ),
    guestcost = (
        SELECT guestcost * 1.1
        FROM cd.facilities
        WHERE facid = 0
    )
WHERE facid = 1;
```

**Explanation:**

The subqueries retrieve the first tennis court's prices and increase them by 10%.

Those calculated values are assigned to the second tennis court.

**Concepts used:** UPDATE, subqueries, arithmetic.

---

## Question 7: Delete all bookings

**Question:** Delete all records from the bookings table.

### SQL Solution

```sql
DELETE FROM cd.bookings;
```

**Explanation:**

`DELETE` removes records from a table.

Without a WHERE condition, all rows are deleted.

**Concepts used:** DELETE.

---

## Question 8: Delete a member from the members table

**Question:** Delete member ID 37, who has never made a booking.

### SQL Solution

```sql
DELETE FROM cd.members
WHERE memid = 37;
```

**Explanation:**

The WHERE condition ensures that only member 37 is deleted.

**Concepts used:** DELETE, WHERE.

---

## Question 9: Delete based on a subquery

**Question:** Delete all members who have never made a booking.

### SQL Solution

```sql
DELETE FROM cd.members
WHERE memid NOT IN (
    SELECT memid
    FROM cd.bookings
);
```

**Explanation:**

The subquery returns member IDs that have bookings.

`NOT IN` selects members whose IDs are not in that result.

**Concepts used:** DELETE, NOT IN, subquery.

**Reference:** [Subqueries](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#12-subqueries)

---

# 4. Aggregation

Official exercises: [Aggregation](https://pgexercises.com/questions/aggregates/)

## Question 1: Count the number of facilities

**Question:** Find the total number of facilities.

### SQL Solution

```sql
SELECT COUNT(*)
FROM cd.facilities;
```

**Explanation:**

`COUNT(*)` counts all rows in the table.

**Concepts used:** COUNT.

**Reference:** [Aggregate Functions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#9-aggregate-functions)

---

## Question 2: Count the number of expensive facilities

**Question:** Count facilities that charge guests 10 or more.

### SQL Solution

```sql
SELECT COUNT(*)
FROM cd.facilities
WHERE guestcost >= 10;
```

**Explanation:**

First, WHERE filters facilities with a guest cost of at least 10. COUNT then counts the remaining rows.

**Concepts used:** COUNT, WHERE.

---

## Question 3: Count the number of recommendations each member makes

**Question:** Count recommendations made by each member. Order by member ID.

### SQL Solution

```sql
SELECT
    recommendedby,
    COUNT(*) AS total
FROM cd.members
WHERE recommendedby IS NOT NULL
GROUP BY recommendedby
ORDER BY recommendedby;
```

**Explanation:**

- NULL values are excluded.
- GROUP BY creates one group for each recommender.
- COUNT calculates the number of recommendations.

**Concepts used:** COUNT, GROUP BY, IS NOT NULL.

**Reference:** [Grouping](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#10-grouping)

---

## Question 4: List the total slots booked per facility

**Question:** Find the total number of booking slots for each facility. Display facility ID and total slots, ordered by facility ID.

### SQL Solution

```sql
SELECT
    facid,
    SUM(slots) AS total_slots
FROM cd.bookings
GROUP BY facid
ORDER BY facid;
```

**Explanation:**

SUM adds all booking slots for each facility. GROUP BY separates the bookings by facility ID.

**Concepts used:** SUM, GROUP BY, ORDER BY.

---

## Question 5: List the total slots booked per facility in a given month

**Question:** Find the total booking slots for each facility in September 2012. Sort by total slots.

### SQL Solution

```sql
SELECT
    facid,
    SUM(slots) AS total_slots
FROM cd.bookings
WHERE starttime >= '2012-09-01'
  AND starttime < '2012-10-01'
GROUP BY facid
ORDER BY total_slots;
```

**Explanation:**

The date conditions select September bookings. SUM calculates the total slots for each facility.

**Concepts used:** SUM, WHERE, GROUP BY, date filtering.

---

## Question 6: List the total slots booked per facility per month

**Question:** Find the total booking slots for every facility in each month of 2012. Order by facility ID and month.

### SQL Solution

```sql
SELECT
    facid,
    EXTRACT(MONTH FROM starttime) AS month,
    SUM(slots) AS total_slots
FROM cd.bookings
WHERE EXTRACT(YEAR FROM starttime) = 2012
GROUP BY
    facid,
    EXTRACT(MONTH FROM starttime)
ORDER BY facid, month;
```

**Explanation:**

The query groups bookings by both facility and month. SUM calculates the total slots in each group.

**Concepts used:** EXTRACT, SUM, GROUP BY, ORDER BY.

**Reference:** [Date Functions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#15-date-and-time-functions)

---

## Question 7: Find the count of members who have made at least one booking

**Question:** Find the total number of different members who have made at least one booking.

### SQL Solution

```sql
SELECT COUNT(DISTINCT memid)
FROM cd.bookings;
```

**Explanation:**

A member can make multiple bookings. DISTINCT ensures each member is counted only once.

**Concepts used:** COUNT, DISTINCT.

---

## Question 8: List facilities with more than 1000 slots booked

**Question:** Find facilities with more than 1000 total booking slots. Display facility ID and total slots.

### SQL Solution

```sql
SELECT
    facid,
    SUM(slots) AS total_slots
FROM cd.bookings
GROUP BY facid
HAVING SUM(slots) > 1000
ORDER BY facid;
```

**Explanation:**

- GROUP BY creates a group for each facility.
- SUM calculates the total slots.
- HAVING filters groups where the total is greater than 1000.

**Concepts used:** SUM, GROUP BY, HAVING.

**Reference:** [WHERE vs HAVING](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#10-grouping)

---

## Question 9: Find the total revenue of each facility

**Question:** Calculate the total revenue generated by each facility. Remember that members and guests have different prices.

### SQL Solution

```sql
SELECT
    f.name,
    SUM(
        CASE
            WHEN b.memid = 0 THEN f.guestcost * b.slots
            ELSE f.membercost * b.slots
        END
    ) AS revenue
FROM cd.facilities f
INNER JOIN cd.bookings b
    ON f.facid = b.facid
GROUP BY f.facid, f.name
ORDER BY revenue;
```

**Explanation:**

- Join facilities with bookings.
- Check whether the booking belongs to a guest or member.
- Multiply the correct cost by the number of slots.
- SUM calculates the total revenue for each facility.

**Concepts used:** INNER JOIN, CASE, SUM, GROUP BY.

---

## Question 10: Find facilities with a total revenue less than 1000

**Question:** Find facilities whose total revenue is below 1000. Display the facility name and revenue.

### SQL Solution

```sql
SELECT
    f.name,
    SUM(
        CASE
            WHEN b.memid = 0 THEN f.guestcost * b.slots
            ELSE f.membercost * b.slots
        END
    ) AS revenue
FROM cd.facilities f
INNER JOIN cd.bookings b
    ON f.facid = b.facid
GROUP BY f.facid, f.name
HAVING SUM(
    CASE
        WHEN b.memid = 0 THEN f.guestcost * b.slots
        ELSE f.membercost * b.slots
    END
) < 1000
ORDER BY revenue;
```

**Explanation:**

The query calculates revenue for each facility and uses HAVING to keep only facilities with revenue below 1000.

**Concepts used:** JOIN, CASE, SUM, GROUP BY, HAVING.

---

## Question 11: Output the facility ID that has the highest number of slots booked

**Question:** Find the facility ID with the highest total number of booking slots.

### SQL Solution

```sql
SELECT
    facid,
    SUM(slots) AS total_slots
FROM cd.bookings
GROUP BY facid
ORDER BY total_slots DESC
LIMIT 1;
```

**Explanation:**

- SUM calculates the total slots for each facility.
- DESC places the highest total first.
- LIMIT 1 returns the first row.

**Alternative using a CTE:**

```sql
WITH facility_slots AS (
    SELECT
        facid,
        SUM(slots) AS total_slots
    FROM cd.bookings
    GROUP BY facid
)
SELECT facid, total_slots
FROM facility_slots
WHERE total_slots = (
    SELECT MAX(total_slots)
    FROM facility_slots
);
```

The second version also returns all facilities if there is a tie.

**Concepts used:** SUM, GROUP BY, ORDER BY, LIMIT, CTE.

---

## Question 12: List total slots per facility per month, part 2

**Question:** Calculate total slots for every facility and month in 2012. Also include totals for each facility across all months and a grand total for all facilities.

### SQL Solution

```sql
SELECT
    facid,
    EXTRACT(MONTH FROM starttime) AS month,
    SUM(slots) AS total_slots
FROM cd.bookings
WHERE EXTRACT(YEAR FROM starttime) = 2012
GROUP BY ROLLUP (
    facid,
    EXTRACT(MONTH FROM starttime)
)
ORDER BY facid, month;
```

**Explanation:**

`ROLLUP` creates:
- Totals for each facility and month.
- Totals for each facility across all months.
- A grand total for all facilities.

NULL values in the grouping columns represent subtotal or grand-total rows.

**Alternative:** The same result can be built using multiple SELECT statements combined with UNION.

**Concepts used:** GROUP BY, ROLLUP, SUM, EXTRACT.

---

## Question 13: List the total hours booked per named facility

**Question:** Find the total hours booked for each facility. One slot is half an hour. Display facility ID, facility name and hours, rounded to two decimal places.

### SQL Solution

```sql
SELECT
    b.facid,
    f.name,
    ROUND(SUM(b.slots) * 0.5, 2) AS total_hours
FROM cd.bookings b
INNER JOIN cd.facilities f
    ON b.facid = f.facid
GROUP BY b.facid, f.name
ORDER BY b.facid;
```

**Explanation:**

Every slot represents 0.5 hours. SUM calculates all slots, and multiplying by 0.5 converts them into hours.

**Concepts used:** JOIN, SUM, ROUND, GROUP BY.

---

## Question 14: List each member's first booking after September 1st 2012

**Question:** Find each member's first booking on or after September 1, 2012. Display surname, first name, member ID and booking time.

### SQL Solution

```sql
SELECT
    m.surname,
    m.firstname,
    b.memid,
    MIN(b.starttime) AS first_booking
FROM cd.members m
INNER JOIN cd.bookings b
    ON m.memid = b.memid
WHERE b.starttime >= '2012-09-01'
GROUP BY
    m.memid,
    m.surname,
    m.firstname
ORDER BY b.memid;
```

**Explanation:**

- JOIN connects members and bookings.
- WHERE filters bookings after the specified date.
- MIN finds the earliest booking for each member.
- GROUP BY creates a group for each member.

**Concepts used:** JOIN, MIN, GROUP BY, date filtering.

---

## Question 15: Produce a list of member names, with each row containing the total member count

**Question:** Display every member's full name along with the total number of members.

### SQL Solution

```sql
SELECT
    firstname,
    surname,
    COUNT(*) OVER() AS total_members
FROM cd.members;
```

**Explanation:**

`COUNT(*) OVER()` calculates the total number of rows without grouping them into one row.

Every member remains in the output, and each row displays the same total count.

**Concepts used:** COUNT, window functions, OVER.

**Reference:** [Window Functions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#16-window-functions)

---

## Question 16: Produce a numbered list of members

**Question:** Generate an increasing numbered list of members ordered by their joining date. Do not depend on member IDs being sequential.

### SQL Solution

```sql
SELECT
    ROW_NUMBER() OVER (ORDER BY joindate) AS row_number,
    firstname,
    surname
FROM cd.members
ORDER BY joindate;
```

**Explanation:**

`ROW_NUMBER()` generates a sequence number based on the joining date.

**Concepts used:** ROW_NUMBER, OVER, ORDER BY.

---

## Question 17: Output the facility ID that has the highest number of slots booked, again

**Question:** Find the facility with the highest total booking slots. If multiple facilities have the same highest total, return all of them.

### SQL Solution

```sql
WITH facility_slots AS (
    SELECT
        facid,
        SUM(slots) AS total_slots
    FROM cd.bookings
    GROUP BY facid
)
SELECT
    facid,
    total_slots
FROM facility_slots
WHERE total_slots = (
    SELECT MAX(total_slots)
    FROM facility_slots
);
```

**Explanation:**

The CTE calculates total slots per facility.

The outer query compares every total with the maximum total. This returns all tied facilities.

**Concepts used:** CTE, SUM, MAX, subquery.

**Reference:** [CTEs](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#17-common-table-expressions)

---

## Question 18: Rank members by rounded hours used

**Question:** Calculate the total hours booked by each member, round the result to the nearest ten hours, and rank members by that value.

### SQL Solution

```sql
WITH member_hours AS (
    SELECT
        m.firstname,
        m.surname,
        ROUND(SUM(b.slots) * 0.5, -1) AS hours
    FROM cd.members m
    INNER JOIN cd.bookings b
        ON m.memid = b.memid
    GROUP BY m.memid, m.firstname, m.surname
)
SELECT
    firstname,
    surname,
    hours,
    RANK() OVER (ORDER BY hours DESC) AS ranking
FROM member_hours
ORDER BY ranking, surname, firstname;
```

**Explanation:**

- Each slot represents half an hour.
- SUM calculates the total slots.
- ROUND with `-1` rounds to the nearest ten.
- RANK assigns positions based on hours.

**Concepts used:** CTE, JOIN, SUM, ROUND, RANK.

---

## Question 19: Find the top three revenue generating facilities

**Question:** Find the top three revenue-generating facilities, including ties. Display facility name and rank.

### SQL Solution

```sql
WITH facility_revenue AS (
    SELECT
        f.name,
        SUM(
            CASE
                WHEN b.memid = 0 THEN f.guestcost * b.slots
                ELSE f.membercost * b.slots
            END
        ) AS revenue
    FROM cd.facilities f
    INNER JOIN cd.bookings b
        ON f.facid = b.facid
    GROUP BY f.facid, f.name
),
ranked_facilities AS (
    SELECT
        name,
        revenue,
        RANK() OVER (ORDER BY revenue DESC) AS ranking
    FROM facility_revenue
)
SELECT name, ranking
FROM ranked_facilities
WHERE ranking <= 3
ORDER BY ranking, name;
```

**Explanation:**

First, calculate revenue for every facility. Then use RANK to rank facilities from highest to lowest revenue.

The condition `ranking <= 3` keeps the top three ranks, including ties.

**Concepts used:** CTE, CASE, SUM, RANK, JOIN.

---

## Question 20: Classify facilities by value

**Question:** Divide facilities into three equally sized groups based on revenue: high, average and low.

### SQL Solution

```sql
WITH facility_revenue AS (
    SELECT
        f.name,
        SUM(
            CASE
                WHEN b.memid = 0 THEN f.guestcost * b.slots
                ELSE f.membercost * b.slots
            END
        ) AS revenue
    FROM cd.facilities f
    INNER JOIN cd.bookings b
        ON f.facid = b.facid
    GROUP BY f.facid, f.name
),
classified AS (
    SELECT
        name,
        NTILE(3) OVER (ORDER BY revenue DESC) AS category
    FROM facility_revenue
)
SELECT
    name,
    CASE
        WHEN category = 1 THEN 'high'
        WHEN category = 2 THEN 'average'
        ELSE 'low'
    END AS revenue_category
FROM classified
ORDER BY category, name;
```

**Explanation:**

- Calculate revenue for each facility.
- NTILE(3) divides the ordered rows into three groups.
- CASE assigns a label to each group.

**Concepts used:** CTE, NTILE, CASE, SUM, window functions.

---

## Question 21: Calculate the payback time for each facility

**Question:** Calculate how many months each facility needs to recover its initial cost, considering its monthly maintenance. Use the three complete months of booking data.

### SQL Solution

```sql
SELECT
    f.name,
    f.initialoutlay / (
        SUM(
            CASE
                WHEN b.memid = 0 THEN f.guestcost * b.slots
                ELSE f.membercost * b.slots
            END
        ) / 3.0 - f.monthlymaintenance
    ) AS months
FROM cd.facilities f
INNER JOIN cd.bookings b
    ON f.facid = b.facid
GROUP BY
    f.facid,
    f.name,
    f.initialoutlay,
    f.monthlymaintenance
ORDER BY f.name;
```

**Explanation:**

1. Calculate total booking revenue.
2. Divide by 3 to get average monthly revenue.
3. Subtract monthly maintenance.
4. Divide initial investment by the remaining monthly amount.

**Concepts used:** JOIN, SUM, CASE, GROUP BY, arithmetic.

---

## Question 22: Calculate a rolling average of total revenue

**Question:** For every day in August 2012, calculate the rolling average of revenue over the previous 15 days. Include days with zero revenue.

### SQL Solution

```sql
WITH daily_revenue AS (
    SELECT
        days.day,
        COALESCE(revenue.total, 0) AS revenue
    FROM (
        SELECT generate_series(
            DATE '2012-07-15',
            DATE '2012-08-31',
            INTERVAL '1 day'
        )::date AS day
    ) days
    LEFT JOIN (
        SELECT
            b.starttime::date AS day,
            SUM(
                b.slots *
                CASE
                    WHEN b.memid = 0 THEN f.guestcost
                    ELSE f.membercost
                END
            ) AS total
        FROM cd.bookings b
        INNER JOIN cd.facilities f
            ON b.facid = f.facid
        GROUP BY b.starttime::date
    ) revenue
        ON days.day = revenue.day
),
rolling_revenue AS (
    SELECT
        day,
        AVG(revenue) OVER (
            ORDER BY day
            ROWS BETWEEN 14 PRECEDING AND CURRENT ROW
        ) AS revenue
    FROM daily_revenue
)
SELECT day, revenue
FROM rolling_revenue
WHERE day >= DATE '2012-08-01'
ORDER BY day;
```

**Explanation:**

- `generate_series` creates a row for every date.
- LEFT JOIN adds the revenue for each day.
- COALESCE changes missing revenue into zero.
- AVG with a window calculates the rolling average.
- The final query displays August dates.

**Concepts used:** CTE, generate_series, LEFT JOIN, COALESCE, AVG, window functions.

**Reference:** [Window Functions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#16-window-functions)

---

# 5. Working with Timestamps

Official exercises: [Working with Timestamps](https://pgexercises.com/questions/date/)

## Question 1: Produce a timestamp for 1 a.m. on August 31, 2012

### SQL Solution

```sql
SELECT TIMESTAMP '2012-08-31 01:00:00' AS timestamp;
```

**Explanation:**

The TIMESTAMP keyword tells PostgreSQL to interpret the value as a timestamp.

**Concepts used:** TIMESTAMP, date and time types.

---

## Question 2: Subtract timestamps from each other

**Question:** Subtract July 30, 2012 at 1 a.m. from August 31, 2012 at 1 a.m.

### SQL Solution

```sql
SELECT
    TIMESTAMP '2012-08-31 01:00:00'
    - TIMESTAMP '2012-07-30 01:00:00' AS difference;
```

**Explanation:**

Subtracting two timestamps returns an interval representing the time difference.

**Concepts used:** Timestamp arithmetic, INTERVAL.

---

## Question 3: Generate a list of all the dates in October 2012

### SQL Solution

```sql
SELECT generate_series(
    DATE '2012-10-01',
    DATE '2012-10-31',
    INTERVAL '1 day'
) AS date;
```

**Explanation:**

`generate_series` generates dates starting from October 1 and increases each value by one day until October 31.

**Concepts used:** generate_series, INTERVAL.

---

## Question 4: Get the day of the month from a timestamp

**Question:** Extract the day from August 31, 2012.

### SQL Solution

```sql
SELECT EXTRACT(
    DAY FROM TIMESTAMP '2012-08-31'
);
```

**Explanation:**

EXTRACT gets a specific part of a date or timestamp.

**Concepts used:** EXTRACT.

---

## Question 5: Work out the number of seconds between timestamps

**Question:** Find the number of seconds between August 31, 2012 at 1 a.m. and September 2, 2012 at midnight.

### SQL Solution

```sql
SELECT EXTRACT(
    EPOCH FROM (
        TIMESTAMP '2012-09-02 00:00:00'
        - TIMESTAMP '2012-08-31 01:00:00'
    )
) AS seconds;
```

**Explanation:**

- Subtract the timestamps to get an interval.
- EPOCH converts the interval into seconds.

**Concepts used:** EXTRACT, EPOCH, timestamp arithmetic.

---

## Question 6: Work out the number of days in each month of 2012

### SQL Solution

```sql
SELECT
    EXTRACT(MONTH FROM month_start)::integer AS month,
    (
        month_start + INTERVAL '1 month'
        - month_start
    ) AS length
FROM generate_series(
    TIMESTAMP '2012-01-01',
    TIMESTAMP '2012-12-01',
    INTERVAL '1 month'
) AS months(month_start);
```

**Explanation:**

- Generate the first day of every month.
- Add one month to get the next month's starting date.
- Subtract the dates to find the length of the month.

**Concepts used:** generate_series, EXTRACT, INTERVAL, timestamp arithmetic.

---

## Question 7: Work out the number of days remaining in the month

**Question:** Find the number of days remaining in February using `2012-02-11 01:00:00` as the example timestamp. Count the current day as a full day.

### SQL Solution

```sql
SELECT
    DATE_TRUNC(
        'month',
        day + INTERVAL '1 month'
    )
    - DATE_TRUNC('day', day) AS remaining
FROM (
    SELECT TIMESTAMP '2012-02-11 01:00:00' AS day
) AS input;
```

**Explanation:**

- DATE_TRUNC moves the timestamp to the beginning of its month or day.
- The first expression finds the beginning of the next month.
- Subtracting the beginning of the current day gives the remaining interval.

**Concepts used:** DATE_TRUNC, INTERVAL, subquery.

---

## Question 8: Work out the end time of bookings

**Question:** Return the start and end times of the last 10 bookings. Order by end time and then start time.

### SQL Solution

```sql
SELECT
    starttime,
    starttime + MAKE_INTERVAL(mins => slots * 30) AS endtime
FROM cd.bookings
ORDER BY endtime DESC, starttime DESC
LIMIT 10;
```

**Explanation:**

Each slot represents 30 minutes.

MAKE_INTERVAL creates the required time interval, which is added to the booking start time.

**Concepts used:** MAKE_INTERVAL, timestamp arithmetic, ORDER BY, LIMIT.

---

## Question 9: Return a count of bookings for each month

### SQL Solution

```sql
SELECT
    DATE_TRUNC('month', starttime) AS month,
    COUNT(*) AS total_bookings
FROM cd.bookings
GROUP BY DATE_TRUNC('month', starttime)
ORDER BY month;
```

**Explanation:**

DATE_TRUNC groups timestamps by month. COUNT calculates the number of bookings in each month.

**Concepts used:** DATE_TRUNC, COUNT, GROUP BY.

---

## Question 10: Work out the utilisation percentage for each facility by month

**Question:** Calculate monthly utilisation for each facility. Opening time is 8 a.m. and closing time is 8:30 p.m. Round the result to one decimal place.

### SQL Solution

```sql
WITH monthly_hours AS (
    SELECT
        f.name,
        DATE_TRUNC('month', b.starttime) AS month,
        SUM(b.slots * 0.5) AS booked_hours
    FROM cd.bookings b
    INNER JOIN cd.facilities f
        ON b.facid = f.facid
    GROUP BY f.name, DATE_TRUNC('month', b.starttime)
)
SELECT
    name,
    month,
    ROUND(
        (
            booked_hours /
            (
                EXTRACT(
                    DAY FROM month + INTERVAL '1 month' - month
                ) * 12.5
            )
        ) * 100,
        1
    ) AS utilisation
FROM monthly_hours
ORDER BY name, month;
```

**Explanation:**

- Convert booking slots into hours.
- One slot represents 0.5 hours.
- The club is open for 12.5 hours per day.
- Multiply the number of days in the month by 12.5 to get the available hours.
- Divide booked hours by available hours and multiply by 100.

**Concepts used:** CTE, JOIN, SUM, DATE_TRUNC, EXTRACT, ROUND.

---

# 6. String Operations

Official exercises: [String Operations](https://pgexercises.com/questions/string/)

## Question 1: Format the names of members

**Question:** Display all member names in the format `Surname, Firstname`.

### SQL Solution

```sql
SELECT
    surname || ', ' || firstname AS name
FROM cd.members;
```

**Explanation:**

The `||` operator joins strings together.

The comma and space are added between the surname and first name.

**Concepts used:** String concatenation, aliases.

**Reference:** [String Functions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#7-string-functions)

---

## Question 2: Find facilities by a name prefix

**Question:** Find facilities whose names begin with `Tennis`. Retrieve all columns.

### SQL Solution

```sql
SELECT *
FROM cd.facilities
WHERE name LIKE 'Tennis%';
```

**Explanation:**

`Tennis%` means the name must start with Tennis. The percent symbol allows any characters after it.

**Alternative using regular expressions:**

```sql
SELECT *
FROM cd.facilities
WHERE name ~ '^Tennis';
```

**Concepts used:** LIKE, pattern matching, regular expressions.

**Reference:** [Pattern Matching](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#5-pattern-matching)

---

## Question 3: Perform a case-insensitive search

**Question:** Find facilities whose names begin with `tennis`, ignoring uppercase and lowercase differences. Retrieve all columns.

### SQL Solution

```sql
SELECT *
FROM cd.facilities
WHERE name ILIKE 'tennis%';
```

**Explanation:**

ILIKE performs case-insensitive pattern matching.

It can match Tennis, tennis, TENNIS and other combinations.

**Alternative:**

```sql
SELECT *
FROM cd.facilities
WHERE UPPER(name) LIKE 'TENNIS%';
```

**Concepts used:** ILIKE, UPPER, LIKE.

**Reference:** [Pattern Matching](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#5-pattern-matching)

---

## Question 4: Find telephone numbers with parentheses

**Question:** Find telephone numbers containing parentheses. Return member ID and telephone number, ordered by member ID.

### SQL Solution

```sql
SELECT
    memid,
    telephone
FROM cd.members
WHERE telephone ~ '[()]'
ORDER BY memid;
```

**Explanation:**

- `~` performs regular expression matching.
- `[()]` matches either an opening or closing parenthesis.
- ORDER BY sorts the results by member ID.

**Alternative using LIKE:**

```sql
SELECT
    memid,
    telephone
FROM cd.members
WHERE telephone LIKE '%(%'
   OR telephone LIKE '%)%'
ORDER BY memid;
```

**Concepts used:** Regular expressions, LIKE, OR, ORDER BY.

**Reference:** [Regular Expressions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#6-regular-expressions)

---

## Question 5: Pad zip codes with leading zeroes

**Question:** Retrieve ZIP codes and add leading zeroes where needed to make every ZIP code five characters long. Order by the formatted ZIP code.

### SQL Solution

```sql
SELECT
    LPAD(CAST(zipcode AS TEXT), 5, '0') AS zip
FROM cd.members
ORDER BY zip;
```

**Explanation:**

- CAST converts the numeric ZIP code into text.
- LPAD adds zeroes to the left until the length becomes five.
- ORDER BY sorts the formatted ZIP codes.

**Alternative using PostgreSQL casting syntax:**

```sql
SELECT
    LPAD(zipcode::TEXT, 5, '0') AS zip
FROM cd.members
ORDER BY zip;
```

**Concepts used:** CAST, LPAD, ORDER BY.

**Reference:** [Type Conversion](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#8-type-conversion)

---

## Question 6: Count members whose surname starts with each letter

**Question:** Count how many members have surnames beginning with each letter. Sort by letter.

### SQL Solution

```sql
SELECT
    SUBSTRING(surname FROM 1 FOR 1) AS letter,
    COUNT(*) AS total
FROM cd.members
GROUP BY letter
ORDER BY letter;
```

**Explanation:**

- SUBSTRING extracts the first character of each surname.
- GROUP BY creates groups for each first letter.
- COUNT counts members in each group.
- ORDER BY sorts alphabetically.

**Alternative:**

```sql
SELECT
    LEFT(surname, 1) AS letter,
    COUNT(*) AS total
FROM cd.members
GROUP BY LEFT(surname, 1)
ORDER BY letter;
```

**Concepts used:** SUBSTRING, LEFT, COUNT, GROUP BY.

**Reference:** [String Functions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#7-string-functions)

---

## Question 7: Clean up telephone numbers

**Question:** Remove hyphens, opening parentheses, closing parentheses and spaces from telephone numbers. Display member ID and cleaned telephone number.

### SQL Solution

```sql
SELECT
    memid,
    TRANSLATE(telephone, '-() ', '') AS telephone
FROM cd.members
ORDER BY memid;
```

**Explanation:**

TRANSLATE removes the listed characters because the replacement string is empty.

It is useful when we need to remove multiple individual characters.

**Alternative using REGEXP_REPLACE:**

```sql
SELECT
    memid,
    REGEXP_REPLACE(telephone, '[-() ]', '', 'g') AS telephone
FROM cd.members
ORDER BY memid;
```

The `g` flag replaces every matching character, not just the first one.

**Alternative using REPLACE:**

```sql
SELECT
    memid,
    REPLACE(
        REPLACE(
            REPLACE(
                REPLACE(telephone, '-', ''),
                '(', ''
            ),
            ')', ''
        ),
        ' ', ''
    ) AS telephone
FROM cd.members
ORDER BY memid;
```

**Concepts used:** TRANSLATE, REPLACE, REGEXP_REPLACE.

**Reference:** [String Functions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#7-string-functions)

---

# 7. Recursive Queries

Official exercises: [Recursive Queries](https://pgexercises.com/questions/recursive/)

## Question 1: Find the upward recommendation chain for member ID 27

**Question:** Find the members who recommended member 27, including the complete chain of recommenders.

### SQL Solution

```sql
WITH RECURSIVE recommendation_chain(memid, recommendedby) AS (
    SELECT
        memid,
        recommendedby
    FROM cd.members
    WHERE memid = 27

    UNION ALL

    SELECT
        m.memid,
        m.recommendedby
    FROM cd.members m
    INNER JOIN recommendation_chain r
        ON m.memid = r.recommendedby
    WHERE r.recommendedby IS NOT NULL
)
SELECT
    m.memid AS recommender,
    m.firstname,
    m.surname
FROM recommendation_chain r
INNER JOIN cd.members m
    ON r.recommendedby = m.memid
ORDER BY m.memid DESC;
```

**Explanation:**

This query moves upward through the recommendation relationship.

1. The first query starts with member 27.
2. The recursive query finds the person who recommended the current member.
3. The process continues until no recommender is available.

**Concepts used:** WITH RECURSIVE, UNION ALL, INNER JOIN.

**Reference:** [Common Table Expressions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#17-common-table-expressions)

---

## Question 2: Find the downward recommendation chain for member ID 1

**Question:** Find all members who were recommended by member 1, including members recommended by those people.

### SQL Solution

```sql
WITH RECURSIVE recommendation_chain(memid) AS (
    SELECT 1

    UNION ALL

    SELECT m.memid
    FROM cd.members m
    INNER JOIN recommendation_chain r
        ON m.recommendedby = r.memid
)
SELECT
    m.memid,
    m.firstname,
    m.surname
FROM recommendation_chain r
INNER JOIN cd.members m
    ON r.memid = m.memid
WHERE m.memid <> 1
ORDER BY m.memid;
```

**Explanation:**

This query moves downward through the recommendation relationship.

1. Start with member 1.
2. Find members whose `recommendedby` value is 1.
3. Find members recommended by those members.
4. Continue until no more members are found.

The final WHERE condition removes the starting member from the output.

**Concepts used:** WITH RECURSIVE, UNION ALL, INNER JOIN.

---

## Question 3: Produce a CTE that can return the upward recommendation chain for any member

**Question:** Create a reusable recursive CTE that can find the upward recommendation chain for members 12 and 22.

### SQL Solution

```sql
WITH RECURSIVE recommendation_chain(member_id, recommender_id) AS (
    SELECT
        memid,
        recommendedby
    FROM cd.members
    WHERE memid IN (12, 22)

    UNION ALL

    SELECT
        r.member_id,
        m.recommendedby
    FROM cd.members m
    INNER JOIN recommendation_chain r
        ON m.memid = r.recommender_id
    WHERE r.recommender_id IS NOT NULL
)
SELECT
    r.member_id,
    r.recommender_id,
    m.firstname,
    m.surname
FROM recommendation_chain r
INNER JOIN cd.members m
    ON r.recommender_id = m.memid
ORDER BY r.member_id, r.recommender_id DESC;
```

**Explanation:**

- The first part starts with members 12 and 22.
- The recursive part finds their recommenders.
- Each iteration continues moving upward.
- The final query displays the member, recommender ID and recommender name.

**Concepts used:** WITH RECURSIVE, UNION ALL, INNER JOIN, IN.

**Reference:** [Common Table Expressions](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts#17-common-table-expressions)

---

# Final Revision

The following table summarizes the main concepts used throughout the exercises.

| Concept | Purpose |
|---|---|
| SELECT | Retrieve data |
| WHERE | Filter rows |
| DISTINCT | Remove duplicates |
| ORDER BY | Sort results |
| LIMIT | Restrict returned rows |
| LIKE / ILIKE | Search text patterns |
| IN | Match multiple values |
| CASE | Apply conditional logic |
| UNION | Combine query results |
| COUNT | Count rows |
| SUM | Calculate totals |
| AVG | Calculate averages |
| MIN / MAX | Find minimum and maximum |
| GROUP BY | Group rows |
| HAVING | Filter groups |
| INNER JOIN | Retrieve matching records |
| LEFT JOIN | Keep all left-side records |
| SELF JOIN | Join a table with itself |
| Subquery | Use one query inside another |
| INSERT | Add records |
| UPDATE | Modify records |
| DELETE | Remove records |
| EXTRACT | Get part of a date or timestamp |
| DATE_TRUNC | Truncate date/time values |
| INTERVAL | Represent a time period |
| LPAD | Add characters to the left |
| SUBSTRING | Extract part of a string |
| TRANSLATE | Replace individual characters |
| REGEXP_REPLACE | Replace text using a pattern |
| ROW_NUMBER | Generate sequential numbers |
| RANK | Assign rankings |
| NTILE | Divide rows into groups |
| OVER | Define a window for calculations |
| WITH | Create a CTE |
| WITH RECURSIVE | Perform recursive queries |

---

## References

- [PGExercises – Official Website](https://pgexercises.com/)
- [PostgreSQL Basics and Concepts – My Reference Repository](https://github.com/Jyothish-Masarpu/PostgreSQL-Basics-and-Concepts)
- [PostgreSQL Official Documentation](https://www.postgresql.org/docs/)

---

## About This Repository

This repository contains my PostgreSQL practice solutions and explanations.

The purpose is to understand SQL concepts by solving practical database problems and to keep all solutions in one place for future reference.
