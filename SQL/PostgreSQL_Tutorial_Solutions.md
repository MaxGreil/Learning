# My solutions for PostgreSQL tutorial

## Link PostgreSQL

https://pgexercises.com/

### Simple SQL Queries

1. Retrieve everything from a table

How can you retrieve all the information from the cd.facilities table?

```
SELECT * FROM cd.facilities;
```

2. Retrieve specific columns from a table

You want to print out a list of all of the facilities and their cost to members. How would you retrieve a list of only facility names and costs?

```
SELECT name, membercost FROM cd.facilities;
```

3. Control which rows are retrieved

How can you produce a list of facilities that charge a fee to members?

```
SELECT * FROM cd.facilities WHERE membercost > 0;
```

4. Control which rows are retrieved - part 2

How can you produce a list of facilities that charge a fee to members, and that fee is less than 1/50th of the monthly maintenance cost? Return the facid, facility name, member cost, and monthly maintenance of the facilities in question.

```
SELECT facid, name, membercost, monthlymaintenance FROM cd.facilities WHERE membercost < monthlymaintenance/50 AND membercost > 0;
```

5. Basic string searches

How can you produce a list of all facilities with the word 'Tennis' in their name?

```
SELECT * FROM cd.facilities WHERE name LIKE '%Tennis%';
```

6. Matching against multiple possible values

How can you retrieve the details of facilities with ID 1 and 5? Try to do it without using the OR operator.

```
SELECT * FROM cd.facilities WHERE facid IN (1,5);
```

7. Classify results into buckets

How can you produce a list of facilities, with each labelled as 'cheap' or 'expensive' depending on if their monthly maintenance cost is more than $100? Return the name and monthly maintenance of the facilities in question.

```
SELECT name,  CASE  WHEN monthlymaintenance > 100 THEN 'expensive'  WHEN monthlymaintenance < 100 THEN 'cheap' END AS cost FROM cd.facilities;
```

8. Working with dates

How can you produce a list of members who joined after the start of September 2012? Return the memid, surname, firstname, and joindate of the members in question.

```
SELECT memid, surname, firstname, joindate FROM cd.members WHERE joindate >= '2012-09-01 00:00:00';
```

9. Removing duplicates, and ordering results

How can you produce an ordered list of the first 10 surnames in the members table? The list must not contain duplicates.

```
SELECT DISTINCT surname FROM cd.members ORDER BY surname LIMIT 10;
```

10.  Combining results from multiple queries

You, for some reason, want a combined list of all surnames and all facility names. Yes, this is a contrived example :-). Produce that list!

```
SELECT surname FROM cd.members UNION SELECT name FROM cd.facilities;
```

11. Simple aggregation

You'd like to get the signup date of your last member. How can you retrieve this information?

```
SELECT MAX(joindate) AS latest FROM cd.members;
```

12. More aggregation

You'd like to get the first and last name of the last member(s) who signed up - not just the date. How can you do that?

```
SELECT firstname, surname, joindate FROM cd.members WHERE joindate = (SELECT MAX(joindate) FROM cd.members);
```

### Joins and Subqueries

1. Retrieve the start times of members' bookings

How can you produce a list of the start times for bookings by members named 'David Farrell'?

```
SELECT starttime FROM cd.bookings INNER JOIN cd.members ON cd.bookings.memid = cd.members.memid WHERE cd.members.firstname = 'David' AND cd.members.surname = 'Farrell';
```

2. Work out the start times of bookings for tennis courts

How can you produce a list of the start times for bookings for tennis courts, for the date '2012-09-21'? Return a list of start time and facility name pairings, ordered by the time.

```
SELECT starttime AS start, name FROM cd.bookings INNER JOIN cd.facilities ON cd.bookings.facid = cd.facilities.facid WHERE cd.bookings.starttime >= '2012-09-21 00:00:00' AND cd.bookings.starttime <= '2012-09-22 00:00:00' AND cd.facilities.name LIKE 'Tennis%' ORDER BY cd.bookings.starttime;
```

3. Produce a list of all members who have recommended another member

How can you output a list of all members who have recommended another member? Ensure that there are no duplicates in the list, and that results are ordered by (surname, firstname).

```
SELECT DISTINCT mem2.firstname, mem2.surname FROM cd.members mem1 INNER JOIN cd.members mem2 ON mem1.recommendedby = mem2.memid WHERE mem1.recommendedby IS NOT NULL ORDER BY mem2.surname;
```

4. Produce a list of all members, along with their recommender

How can you output a list of all members, including the individual who recommended them (if any)? Ensure that results are ordered by (surname, firstname).

```
SELECT mem.firstname AS memfname, mem.surname AS memsname, rec.firstname AS recfname, rec.surname AS recsname FROM cd.members mem LEFT OUTER JOIN cd.members rec ON mem.recommendedby = rec.memid ORDER BY memsname, memfname;
```

5. Produce a list of all members who have used a tennis court

How can you produce a list of all members who have used a tennis court? Include in your output the name of the court, and the name of the member formatted as a single column. Ensure no duplicate data, and order by the member name followed by the facility name.

```
SELECT DISTINCT mem.firstname || ' ' || mem.surname AS member, fac.name AS facility FROM cd.members mem INNER JOIN cd.bookings book ON mem.memid=book.memid INNER JOIN cd.facilities fac ON book.facid=fac.facid WHERE facility LIKE 'Tennis%' ORDER BY member, facility;
```

6. Produce a list of costly bookings

How can you produce a list of bookings on the day of 2012-09-14 which will cost the member (or guest) more than $30? Remember that guests have different costs to members (the listed costs are per half-hour 'slot'), and the guest user is always ID 0. Include in your output the name of the facility, the name of the member formatted as a single column, and the cost. Order by descending cost, and do not use any subqueries.

```
```

7. Produce a list of all members, along with their recommender, using no joins.

How can you output a list of all members, including the individual who recommended them (if any), without using any joins? Ensure that there are no duplicates in the list, and that each firstname + surname pairing is formatted as a column and ordered.

```

```

8.  Produce a list of costly bookings, using a subquery

The Produce a list of costly bookings exercise contained some messy logic: we had to calculate the booking cost in both the WHERE clause and the CASE statement. Try to simplify this calculation using subqueries. For reference, the question was:

How can you produce a list of bookings on the day of 2012-09-14 which will cost the member (or guest) more than $30? Remember that guests have different costs to members (the listed costs are per half-hour 'slot'), and the guest user is always ID 0. Include in your output the name of the facility, the name of the member formatted as a single column, and the cost. Order by descending cost.

```

```

### Modifying data

1. Insert some data into a table

The club is adding a new facility - a spa. We need to add it into the facilities table. Use the following values:

- facid: 9, Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800.

```
INSERT INTO cd.facilities(facid, name, membercost, guestcost, initialoutlay, monthlymaintenance) VALUES (9, 'Spa', 20, 30, 100000, 800);
```

2. Insert multiple rows of data into a table

In the previous exercise, you learned how to add a facility. Now you're going to add multiple facilities in one command. Use the following values:

- facid: 9, Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800.
- facid: 10, Name: 'Squash Court 2', membercost: 3.5, guestcost: 17.5, initialoutlay: 5000, monthlymaintenance: 80.

```
INSERT INTO cd.facilities(facid, name, membercost, guestcost, initialoutlay, monthlymaintenance) VALUES (9, 'Spa', 20, 30, 100000, 800), (10,'Squash Court 2', 3.5, 17.5, 5000, 80);
```

3. Insert calculated data into a table

Let's try adding the spa to the facilities table again. This time, though, we want to automatically generate the value for the next facid, rather than specifying it as a constant. Use the following values for everything else:

- Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800.

```
INSERT INTO cd.facilities(facid, name, membercost, guestcost, initialoutlay, monthlymaintenance) VALUES (SELECT MAX(facid) + 1 FROM cd.facilities), 'Spa', 20, 30, 100000, 800);
```

4. Update some existing data

We made a mistake when entering the data for the second tennis court. The initial outlay was 10000 rather than 8000: you need to alter the data to fix the error.

```
UPDATE cd.facilities SET initialoutlay = 10000 WHERE name LIKE 'Tennis Court 2';
```

5. Update multiple rows and columns at the same time

We want to increase the price of the tennis courts for both members and guests. Update the costs to be 6 for members, and 30 for guests.

```
UPDATE cd.facilities SET guestcost = 30, membercost = 6 WHERE name LIKE 'Tennis%';
```

6. Update a row based on the contents of another row

We want to alter the price of the second tennis court so that it costs 10% more than the first one. Try to do this without using constant values for the prices, so that we can reuse the statement if we want to.

```
UPDATE cd.facilities SET membercost = (SELECT membercost*1.1 FROM cd.facilities WHERE name LIKE 'Tennis Court 1'), guestcost = (SELECT guestcost*1.1 FROM cd.facilities WHERE name LIKE 'Tennis Court 1') WHERE name LIKE 'Tennis Court 2';

UPDATE cd.facilities facs SET membercost = facs2.membercost * 1.1, guestcost = facs2.guestcost * 1.1 FROM (SELECT * FROM cd.facilities WHERE facid = 0) facs2 WHERE facs.facid = 1;
```

7. Delete all bookings

As part of a clearout of our database, we want to delete all bookings from the cd.bookings table. How can we accomplish this?

```
DELETE FROM cd.bookings;
```

8. Delete a member from the cd.members table

We want to remove member 37, who has never made a booking, from our database. How can we achieve that?

```
DELETE FROM cd.members WHERE memid = 37;
```

9. Delete based on a subquery

In our previous exercises, we deleted a specific member who had never made a booking. How can we make that more general, to delete all members who have never made a booking?

```
DELETE FROM cd.members WHERE memid NOT IN (SELECT memid FROM cd.bookings);
```

### Aggregation

1. Count the number of facilities

or our first foray into aggregates, we're going to stick to something simple. We want to know how many facilities exist - simply produce a total count.

```
```
