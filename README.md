# PostgreSQL Exercises – Database Reference

This repository contains the actual sample data used in [PGExercises](https://pgexercises.com/).

The database represents a country club. It maintains information about members, available facilities and bookings.

There are three main tables:

1. `cd.members`
2. `cd.facilities`
3. `cd.bookings`

These tables will be used to understand SQL queries, especially INNER JOIN, LEFT JOIN, RIGHT JOIN, SELF JOIN and other SQL concepts.

---


# Database Overview

| Table | Description |
|---|---|
| `cd.members` | Stores member information |
| `cd.facilities` | Stores available facilities and their costs |
| `cd.bookings` | Stores booking information |

---

# 1. Members Table

**Table name:** `cd.members`

The members table stores information about people who joined the country club.

## Actual Table Data

| memid | surname | firstname | address | zipcode | telephone | recommendedby | joindate |
|---:|---|---|---:|---:|---|---:|---|
| 0 | GUEST | GUEST | GUEST | 0 | (000) 000-0000 | NULL | 2012-07-01 00:00:00 |
| 1 | Smith | Darren | 8 Bloomsbury Close, Boston | 4321 | 555-555-5555 | NULL | 2012-07-02 12:02:05 |
| 2 | Smith | Tracy | 8 Bloomsbury Close, New York | 4321 | 555-555-5555 | NULL | 2012-07-02 12:08:23 |
| 3 | Rownam | Tim | 23 Highway Way, Boston | 23423 | (844) 693-0723 | NULL | 2012-07-03 09:32:15 |
| 4 | Joplette | Janice | 20 Crossing Road, New York | 234 | (833) 942-4710 | 1 | 2012-07-03 10:25:05 |
| 5 | Butters | Gerald | 1065 Huntingdon Avenue, Boston | 56754 | (844) 078-4130 | 1 | 2012-07-09 10:44:09 |
| 6 | Tracy | Burton | 3 Tunisia Drive, Boston | 45678 | (822) 354-9973 | NULL | 2012-07-15 08:52:55 |
| 7 | Dare | Nancy | 6 Hunting Lodge Way, Boston | 10383 | (833) 776-4001 | 4 | 2012-07-25 08:59:12 |
| 8 | Boothe | Tim | 3 Bloomsbury Close, Reading, 00234 | 234 | (811) 433-2547 | 3 | 2012-07-25 16:02:35 |


---

# 2. Facilities Table

**Table name:** `cd.facilities`

The facilities table stores information about the facilities available at the country club.

## Actual Table Data

| facid | name | membercost | guestcost | initialoutlay | monthlymaintenance |
|---:|---|---:|---:|---:|---:|
| 0 | Tennis Court 1 | 5 | 25 | 10000 | 200 |
| 1 | Tennis Court 2 | 5 | 25 | 8000 | 200 |
| 2 | Badminton Court | 0 | 15.5 | 4000 | 50 |
| 3 | Table Tennis | 0 | 5 | 320 | 10 |
| 4 | Massage Room 1 | 35 | 80 | 4000 | 3000 |
| 5 | Massage Room 2 | 35 | 80 | 4000 | 3000 |
| 6 | Squash Court | 3.5 | 17.5 | 5000 | 80 |
| 7 | Snooker Table | 0 | 5 | 450 | 15 |
| 8 | Pool Table | 0 | 5 | 400 | 15 |


---

# 3. Bookings Table

**Table name:** `cd.bookings`

The bookings table stores information about bookings made for different facilities.

The original dataset contains many booking records.

## Actual Table Data 

| bookid | facid | memid | starttime | slots |
|---:|---:|---:|---|---:|
| 0 | 3 | 1 | 2012-07-03 11:00:00 | 2 |
| 1 | 4 | 1 | 2012-07-03 08:00:00 | 2 |
| 2 | 6 | 0 | 2012-07-03 18:00:00 | 2 |
| 3 | 7 | 1 | 2012-07-03 19:00:00 | 2 |
| 4 | 8 | 1 | 2012-07-03 10:00:00 | 1 |
| 5 | 8 | 1 | 2012-07-03 15:00:00 | 1 |
| 6 | 0 | 2 | 2012-07-04 09:00:00 | 3 |
| 7 | 0 | 2 | 2012-07-04 15:00:00 | 3 |
| 8 | 4 | 3 | 2012-07-04 13:30:00 | 2 |
| 9 | 4 | 0 | 2012-07-04 15:00:00 | 2 |
| 10 | 4 | 0 | 2012-07-04 17:30:00 | 2 |


### Important observation

The bookings table connects the other two tables.

It contains:

- `memid`, which refers to `cd.members.memid`.
- `facid`, which refers to `cd.facilities.facid`.

One slot represents 30 minutes.

For example:

| slots | Duration |
|---:|---|
| 1 | 30 minutes |
| 2 | 1 hour |
| 3 | 1 hour 30 minutes |
| 4 | 2 hours |

Member ID `0` represents a guest booking.

---
