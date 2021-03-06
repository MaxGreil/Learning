# My solutions for PostgreSQL tutorial

## Link PostgreSQL

https://pgexercises.com/

## Simple SQL Queries

1. Retrieve everything from a table

How can you retrieve all the information from the cd.facilities table?

```
SELECT *
FROM cd.facilities;
```

2. Retrieve specific columns from a table

You want to print out a list of all of the facilities and their cost to members. How would you retrieve a list of only facility names and costs?

```
SELECT name, membercost
FROM cd.facilities;
```

3. Control which rows are retrieved

How can you produce a list of facilities that charge a fee to members?

```
SELECT *
FROM cd.facilities
WHERE membercost > 0;
```

4. Control which rows are retrieved - part 2

How can you produce a list of facilities that charge a fee to members, and that fee is less than 1/50th of the monthly maintenance cost? Return the facid, facility name, member cost, and monthly maintenance of the facilities in question.

```
SELECT facid, name, membercost, monthlymaintenance
FROM cd.facilities
WHERE membercost < monthlymaintenance/50 AND membercost > 0;
```

5. Basic string searches

How can you produce a list of all facilities with the word 'Tennis' in their name?

```
SELECT *
FROM cd.facilities
WHERE name LIKE '%Tennis%';
```

6. Matching against multiple possible values

How can you retrieve the details of facilities with ID 1 and 5? Try to do it without using the OR operator.

```
SELECT *
FROM cd.facilities
WHERE facid IN (1,5);
```

7. Classify results into buckets

How can you produce a list of facilities, with each labelled as 'cheap' or 'expensive' depending on if their monthly maintenance cost is more than $100? Return the name and monthly maintenance of the facilities in question.

```
SELECT name,  
CASE  WHEN monthlymaintenance > 100 THEN 'expensive'  WHEN monthlymaintenance < 100 THEN 'cheap' END AS cost
FROM cd.facilities;
```

8. Working with dates

How can you produce a list of members who joined after the start of September 2012? Return the memid, surname, firstname, and joindate of the members in question.

```
SELECT memid, surname, firstname, joindate
FROM cd.members
WHERE joindate >= '2012-09-01 00:00:00';
```

9. Removing duplicates, and ordering results

How can you produce an ordered list of the first 10 surnames in the members table? The list must not contain duplicates.

```
SELECT DISTINCT surname
FROM cd.members
ORDER BY surname
LIMIT 10;
```

10.  Combining results from multiple queries

You, for some reason, want a combined list of all surnames and all facility names. Yes, this is a contrived example :-). Produce that list!

```
SELECT surname
FROM cd.members
UNION SELECT name
FROM cd.facilities;
```

11. Simple aggregation

You'd like to get the signup date of your last member. How can you retrieve this information?

```
SELECT MAX(joindate) AS latest
FROM cd.members;
```

12. More aggregation

You'd like to get the first and last name of the last member(s) who signed up - not just the date. How can you do that?

```
SELECT firstname, surname, joindate
FROM cd.members
WHERE joindate = (
  SELECT MAX(joindate) FROM cd.members
);
```

## Joins and Subqueries

1. Retrieve the start times of members' bookings

How can you produce a list of the start times for bookings by members named 'David Farrell'?

```
SELECT starttime
FROM cd.bookings INNER JOIN cd.members ON cd.bookings.memid = cd.members.memid
WHERE cd.members.firstname = 'David' AND cd.members.surname = 'Farrell';
```

2. Work out the start times of bookings for tennis courts

How can you produce a list of the start times for bookings for tennis courts, for the date '2012-09-21'? Return a list of start time and facility name pairings, ordered by the time.

```
SELECT starttime AS start, name
FROM cd.bookings INNER JOIN cd.facilities ON cd.bookings.facid = cd.facilities.facid
WHERE cd.bookings.starttime >= '2012-09-21 00:00:00' AND cd.bookings.starttime <= '2012-09-22 00:00:00' AND cd.facilities.name LIKE 'Tennis%'
ORDER BY cd.bookings.starttime;
```

3. Produce a list of all members who have recommended another member

How can you output a list of all members who have recommended another member? Ensure that there are no duplicates in the list, and that results are ordered by (surname, firstname).

```
SELECT DISTINCT mem2.firstname, mem2.surname
FROM cd.members mem1 INNER JOIN cd.members mem2 ON mem1.recommendedby = mem2.memid
WHERE mem1.recommendedby IS NOT NULL
ORDER BY mem2.surname;
```

4. Produce a list of all members, along with their recommender

How can you output a list of all members, including the individual who recommended them (if any)? Ensure that results are ordered by (surname, firstname).

```
SELECT mem.firstname AS memfname, mem.surname AS memsname, rec.firstname AS recfname, rec.surname AS recsname
FROM cd.members mem LEFT OUTER JOIN cd.members rec ON mem.recommendedby = rec.memid
ORDER BY memsname, memfname;
```

5. Produce a list of all members who have used a tennis court

How can you produce a list of all members who have used a tennis court? Include in your output the name of the court, and the name of the member formatted as a single column. Ensure no duplicate data, and order by the member name followed by the facility name.

```
SELECT DISTINCT mem.firstname || ' ' || mem.surname AS member, fac.name AS facility
FROM cd.members mem INNER JOIN cd.bookings book ON mem.memid=book.memid INNER JOIN cd.facilities fac ON book.facid=fac.facid
WHERE facility LIKE 'Tennis%'
ORDER BY member, facility;
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

## Modifying data

1. Insert some data into a table

The club is adding a new facility - a spa. We need to add it into the facilities table. Use the following values:

- facid: 9, Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800.

```
INSERT INTO cd.facilities(facid, name, membercost, guestcost, initialoutlay, monthlymaintenance)
VALUES (9, 'Spa', 20, 30, 100000, 800);
```

2. Insert multiple rows of data into a table

In the previous exercise, you learned how to add a facility. Now you're going to add multiple facilities in one command. Use the following values:

- facid: 9, Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800.
- facid: 10, Name: 'Squash Court 2', membercost: 3.5, guestcost: 17.5, initialoutlay: 5000, monthlymaintenance: 80.

```
INSERT INTO cd.facilities(facid, name, membercost, guestcost, initialoutlay, monthlymaintenance)
VALUES (9, 'Spa', 20, 30, 100000, 800), (10,'Squash Court 2', 3.5, 17.5, 5000, 80);
```

3. Insert calculated data into a table

Let's try adding the spa to the facilities table again. This time, though, we want to automatically generate the value for the next facid, rather than specifying it as a constant. Use the following values for everything else:

- Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800.

```
INSERT INTO cd.facilities(facid, name, membercost, guestcost, initialoutlay, monthlymaintenance)
VALUES (
  SELECT MAX(facid) + 1 FROM cd.facilities
), 'Spa', 20, 30, 100000, 800);
```

4. Update some existing data

We made a mistake when entering the data for the second tennis court. The initial outlay was 10000 rather than 8000: you need to alter the data to fix the error.

```
UPDATE cd.facilities
SET initialoutlay = 10000
WHERE name = 'Tennis Court 2';
```

5. Update multiple rows and columns at the same time

We want to increase the price of the tennis courts for both members and guests. Update the costs to be 6 for members, and 30 for guests.

```
UPDATE cd.facilities
SET guestcost = 30, membercost = 6
WHERE name LIKE 'Tennis%';
```

6. Update a row based on the contents of another row

We want to alter the price of the second tennis court so that it costs 10% more than the first one. Try to do this without using constant values for the prices, so that we can reuse the statement if we want to.

```
UPDATE cd.facilities
SET membercost = (
  SELECT membercost*1.1 FROM cd.facilities WHERE name LIKE 'Tennis Court 1'
), guestcost = (
  SELECT guestcost*1.1 FROM cd.facilities WHERE name LIKE 'Tennis Court 1'
) WHERE name LIKE 'Tennis Court 2';

UPDATE cd.facilities facs
SET membercost = facs2.membercost * 1.1, guestcost = facs2.guestcost * 1.1
FROM (
  SELECT * FROM cd.facilities WHERE facid = 0
) facs2
WHERE facs.facid = 1;
```

7. Delete all bookings

As part of a clearout of our database, we want to delete all bookings from the cd.bookings table. How can we accomplish this?

```
DELETE
FROM cd.bookings;
```

8. Delete a member from the cd.members table

We want to remove member 37, who has never made a booking, from our database. How can we achieve that?

```
DELETE
FROM cd.members
WHERE memid = 37;
```

9. Delete based on a subquery

In our previous exercises, we deleted a specific member who had never made a booking. How can we make that more general, to delete all members who have never made a booking?

```
DELETE
FROM cd.members
WHERE memid NOT IN (
  SELECT memid FROM cd.bookings
);
```

## Aggregation

1. Count the number of facilities

For our first foray into aggregates, we're going to stick to something simple. We want to know how many facilities exist - simply produce a total count.

```
SELECT COUNT(*)
FROM cd.facilities;
```

2. Count the number of expensive facilities

Produce a count of the number of facilities that have a cost to guests of 10 or more.

```
SELECT COUNT(*)
FROM cd.facilities
WHERE guestcost >= 10;
```

3. Count the number of recommendations each member makes.

Produce a count of the number of recommendations each member has made. Order by member ID.

```
SELECT recommendedby, COUNT(*)
FROM cd.members
WHERE recommendedby IS NOT NULL
GROUP BY recommendedby
ORDER BY recommendedby;
```

4. List the total slots booked per facility

Produce a list of the total number of slots booked per facility. For now, just produce an output table consisting of facility id and slots, sorted by facility id.

```
SELECT facid, SUM(slots) AS "Total Slots"
FROM cd.bookings
GROUP BY facid
ORDER BY facid;
```

5. List the total slots booked per facility in a given month

Produce a list of the total number of slots booked per facility in the month of September 2012. Produce an output table consisting of facility id and slots, sorted by the number of slots.

```
SELECT facid, SUM(slots) AS "Total Slots"
FROM cd.bookings
WHERE starttime >= '2012-09-01 00:00:00' AND starttime <= '2012-10-01 00:00:00'
GROUP BY facid
ORDER BY "Total Slots";
```

6. List the total slots booked per facility per month

Produce a list of the total number of slots booked per facility per month in the year of 2012. Produce an output table consisting of facility id and slots, sorted by the id and month.

```
SELECT facid, EXTRACT(MONTH FROM starttime) AS month, SUM(slots) AS "Total Slots"
FROM cd.bookings WHERE starttime >= '2012-01-01 00:00:00' AND starttime < '2013-01-01 00:00:00'
GROUP BY facid, month
ORDER BY facid, month;

SELECT facid, EXTRACT(MONTH FROM starttime) AS month, SUM(slots) AS "Total Slots"
FROM cd.bookings
WHERE EXTRACT(YEAR FROM starttime) = 2012
GROUP BY facid, month
ORDER BY facid, month;
```

7. Find the count of members who have made at least one booking

Find the total number of members (including guests) who have made at least one booking.

```
SELECT COUNT (DISTINCT memid)
FROM cd.bookings
WHERE slots > 0;
```

8. List facilities with more than 1000 slots booked

Produce a list of facilities with more than 1000 slots booked. Produce an output table consisting of facility id and slots, sorted by facility id.

```
SELECT facid, SUM(slots) AS "Total Slots"
FROM cd.bookings
GROUP BY facid
HAVING SUM(slots) > 1000
ORDER BY facid;
```

9. Find the total revenue of each facility

Produce a list of facilities along with their total revenue. The output table should consist of facility name and revenue, sorted by revenue. Remember that there's a different cost for guests and members!

```
SELECT fac.name,
SUM(
  CASE
    WHEN memid > 0 THEN fac.membercost * book.slots
    ELSE fac.guestcost * book.slots
  END
) AS "revenue"
FROM cd.bookings book INNER JOIN cd.facilities fac ON book.facid = fac.facid
GROUP BY fac.name
ORDER BY "revenue";
```

10. Find facilities with a total revenue less than 1000

Produce a list of facilities with a total revenue less than 1000. Produce an output table consisting of facility name and revenue, sorted by revenue. Remember that there's a different cost for guests and members!

```
SELECT fac.name,
SUM(
  CASE
    WHEN memid > 0 THEN fac.membercost * book.slots
    ELSE fac.guestcost * book.slots
  END
) AS "revenue"
FROM cd.bookings book INNER JOIN cd.facilities fac ON book.facid = fac.facid
GROUP BY fac.name
HAVING SUM(
  CASE
    WHEN memid > 0 THEN fac.membercost * book.slots
    ELSE fac.guestcost * book.slots
  END
) < 1000
ORDER BY "revenue";
```

11. Output the facility id that has the highest number of slots booked

Output the facility id that has the highest number of slots booked. For bonus points, try a version without a LIMIT clause. This version will probably look messy!

```
SELECT facid, SUM(slots) AS "Total Slots"
FROM cd.bookings
GROUP BY facid
ORDER BY SUM(slots) DESC
LIMIT 1;
```

12. List the total slots booked per facility per month, part 2

Produce a list of the total number of slots booked per facility per month in the year of 2012. In this version, include output rows containing totals for all months per facility, and a total for all months for all facilities. The output table should consist of facility id, month and slots, sorted by the id and month. When calculating the aggregated values for all months and all facids, return null values in the month and facid columns.

```
SELECT facid, EXTRACT(MONTH FROM starttime) AS "month", SUM(slots)
FROM cd.bookings
WHERE starttime >= '2012-01-01 00:00:00' AND starttime < '2013-01-01 00:00:00'
GROUP BY
  ROLLUP (
  	facid,
  	EXTRACT(MONTH FROM starttime)
  )
ORDER BY facid, SUM(slots);
```

13. List the total hours booked per named facility

Produce a list of the total number of hours booked per facility, remembering that a slot lasts half an hour. The output table should consist of the facility id, name, and hours booked, sorted by facility id. Try formatting the hours to two decimal places.

```
SELECT book.facid, fac.name, TRIM(TO_CHAR(SUM(book.slots)*0.5, '999D99')) AS "Total Hours"
FROM cd.bookings book INNER JOIN cd.facilities fac ON book.facid = fac.facid
GROUP BY book.facid, fac.name
ORDER BY book.facid;
```

14. List each member's first booking after September 1st 2012

Produce a list of each member name, id, and their first booking after September 1st 2012. Order by member ID.

```
SELECT mem.surname, mem.firstname, mem.memid, MIN(book.starttime)
FROM cd.members mem INNER JOIN cd.bookings book ON mem.memid = book.memid
WHERE book.starttime > '2012-09-01 00:00:00'
GROUP BY mem.surname, mem.firstname, mem.memid
ORDER BY mem.memid;
```

15. Produce a list of member names, with each row containing the total member count

Produce a list of member names, with each row containing the total member count. Order by join date, and include guest members.

```
SELECT COUNT(*) OVER(), firstname, surname
FROM cd.members
GROUP BY firstname, surname, joindate
ORDER BY joindate;
```

16. Produce a numbered list of members

Produce a monotonically increasing numbered list of members (including guests), ordered by their date of joining. Remember that member IDs are not guaranteed to be sequential.

```
SELECT ROW_NUMBER(*) OVER(), firstname, surname
FROM cd.members
GROUP BY firstname, surname, joindate
ORDER BY joindate;
```

17. Output the facility id that has the highest number of slots booked, again

Output the facility id that has the highest number of slots booked. Ensure that in the event of a tie, all tieing results get output.

```
SELECT facid, total
FROM (
	SELECT facid, SUM(slots) total, RANK() OVER(ORDER BY SUM(slots) DESC) rank
	FROM cd.bookings
	GROUP BY facid
) AS ranked
WHERE rank = 1;  
```

18. Rank members by (rounded) hours used

Produce a list of members (including guests), along with the number of hours they've booked in facilities, rounded to the nearest ten hours. Rank them by this rounded figure, producing output of first name, surname, rounded hours, rank. Sort by rank, surname, and first name

```
SELECT mem.firstname, mem.surname, ROUND(SUM(book.slots)*0.5, -1) AS hours, RANK() OVER(ORDER BY ROUND(SUM(book.slots)*0.5, -1) DESC) rank
FROM cd.members mem INNER JOIN cd.bookings book ON mem.memid=book.memid
GROUP BY mem.firstname, mem.surname
ORDER BY rank, mem.surname, mem.firstname;
```

19. Find the top three revenue generating facilities

Produce a list of the top three revenue generating facilities (including ties). Output facility name and rank, sorted by rank and facility name.

```
SELECT fac.name, RANK() OVER(ORDER BY SUM(CASE WHEN memid > 0 THEN fac.membercost * book.slots ELSE fac.guestcost * book.slots END) DESC) rank
FROM cd.bookings book INNER JOIN cd.facilities fac ON book.facid = fac.facid
GROUP BY fac.name
LIMIT 3;
```

20. Classify facilities by value

Classify facilities into equally sized groups of high, average, and low based on their revenue. Order by classification and facility name.

```
```

21. Calculate the payback time for each facility

Based on the 3 complete months of data so far, calculate the amount of time each facility will take to repay its cost of ownership. Remember to take into account ongoing monthly maintenance. Output facility name and payback time in months, order by facility name. Don't worry about differences in month lengths, we're only looking for a rough value here!

```
```

22. Calculate a rolling average of total revenue

For each day in August 2012, calculate a rolling average of total revenue over the previous 15 days. Output should contain date and revenue columns, sorted by the date. Remember to account for the possibility of a day having zero revenue. This one's a bit tough, so don't be afraid to check out the hint!

```
```

## Date

1. Produce a timestamp for 1 a.m. on the 31st of August 2012

Produce a timestamp for 1 a.m. on the 31st of August 2012.

```
SELECT TIMESTAMP '2012-08-31 01:00:00';
```

2. Subtract timestamps from each other

Find the result of subtracting the timestamp '2012-07-30 01:00:00' from the timestamp '2012-08-31 01:00:00'

```
SELECT TIMESTAMP '2012-08-31 01:00:00' - TIMESTAMP '2012-07-30 01:00:00' AS interval;
```

3. Generate a list of all the dates in October 2012

Produce a list of all the dates in October 2012. They can be output as a timestamp (with time set to midnight) or a date.

```
SELECT * FROM generate_series('2012-10-01 00:00'::TIMESTAMP, '2012-10-31 00:00', '1 day') AS ts;

SELECT generate_series(TIMESTAMP '2012-10-01', TIMESTAMP '2012-10-31', interval '1 day') AS ts;    
```

4. Get the day of the month from a timestamp

Get the day of the month from the timestamp '2012-08-31' as an integer.

```
SELECT EXTRACT(DAY FROM TIMESTAMP '2012-08-31');
```

5. Work out the number of seconds between timestamps

Work out the number of seconds between the timestamps '2012-08-31 01:00:00' and '2012-09-02 00:00:00'

```
SELECT EXTRACT(EPOCH FROM TIMESTAMP '2012-09-02 00:00:00' - TIMESTAMP '2012-08-31 01:00:00');
```

6. Work out the number of days in each month of 2012

For each month of the year in 2012, output the number of days in that month. Format the output as an integer column containing the month of the year, and a second column containing an interval data type.

```
SELECT EXTRACT(MONTH FROM generate_series(TIMESTAMP '2012-01-01', TIMESTAMP '2012-12-31', interval '1 month')) AS month,
EXTRACT(DAYS FROM date_trunc('month', generate_series(TIMESTAMP '2012-01-01', TIMESTAMP '2012-12-31', interval '1 month')) + interval '1 month - 1 day') || ' ' || 'days' AS length;

SELECT EXTRACT(month FROM cal.month) AS month,
	(cal.month + interval '1 month') - cal.month AS length
	FROM
	(
		SELECT generate_series(TIMESTAMP '2012-01-01', TIMESTAMP '2012-12-01', interval '1 month') AS month
	) cal
ORDER BY month;  
```

7. Work out the number of days remaining in the month

For any given timestamp, work out the number of days remaining in the month. The current day should count as a whole day, regardless of the time. Use '2012-02-11 01:00:00' as an example timestamp for the purposes of making the answer. Format the output as a single interval value.

```
SELECT DATE_TRUNC('month', TIMESTAMP '2012-02-11 01:00:00') + interval '1 month' - DATE_TRUNC('day', TIMESTAMP '2012-02-11 01:00:00') AS remaining;

SELECT (DATE_TRUNC('month',ts.testts) + interval '1 month')
		- DATE_TRUNC('day', ts.testts) AS remaining
	FROM (SELECT timestamp '2012-02-11 01:00:00' AS testts) ts  
```

8. Work out the end time of bookings

Return a list of the start and end time of the last 10 bookings (ordered by the time at which they end, followed by the time at which they start) in the system.

```
SELECT starttime, starttime + slots*30 * interval '1 minute' AS endtime
FROM cd.bookings
ORDER BY endtime DESC, starttime DESC
LIMIT 10;
```

9. Return a count of bookings for each month

Return a count of bookings for each month, sorted by month

```
```

10. Work out the utilisation percentage for each facility by month

Work out the utilisation percentage for each facility by month, sorted by name and month, rounded to 1 decimal place. Opening time is 8am, closing time is 8.30pm. You can treat every month as a full month, regardless of if there were some dates the club was not open.

```
```

## String

1. Format the names of members

Output the names of all members, formatted as 'Surname, Firstname'

```
SELECT surname || ', ' || firstname
FROM cd.members;
```

2. Find facilities by a name prefix

Find all facilities whose name begins with 'Tennis'. Retrieve all columns.

```
SELECT *
FROM cd.facilities
WHERE name LIKE 'Tennis%';
```

3. Find telephone numbers with parentheses

You've noticed that the club's member table has telephone numbers with very inconsistent formatting. You'd like to find all the telephone numbers that contain parentheses, returning the member ID and telephone number sorted by member ID.

```
SELECT memid, telephone
FROM cd.members
WHERE telephone ~ '[()]'
ORDER BY memid;
```

4. Pad zip codes with leading zeroes

The zip codes in our example dataset have had leading zeroes removed from them by virtue of being stored as a numeric type. Retrieve all zip codes from the members table, padding any zip codes less than 5 characters long with leading zeroes. Order by the new zip code.

```
SELECT LPAD(CAST(zipcode AS char(5)),5,'0')
FROM cd.members;
```

5. Count the number of members whose surname starts with each letter of the alphabet

You'd like to produce a count of how many members you have whose surname starts with each letter of the alphabet. Sort by the letter, and don't worry about printing out a letter if the count is 0.

```
SELECT SUBSTR(surname,1,1) AS letter, COUNT(*)
FROM cd.members
GROUP BY letter
ORDER BY letter;
```

6. Clean up telephone numbers

The telephone numbers in the database are very inconsistently formatted. You'd like to print a list of member ids and numbers that have had '-','(',')', and ' ' characters removed. Order by member id.

```
SELECT memid, TRANSLATE(telephone,'() -', '')
FROM cd.members
ORDER BY memid;
```

## Recursive

1. Find the upward recommendation chain for member ID 27

Find the upward recommendation chain for member ID 27: that is, the member who recommended them, and the member who recommended that member, and so on. Return member ID, first name, and surname. Order by descending member id.

```
```

2. Find the downward recommendation chain for member ID 1

Find the downward recommendation chain for member ID 1: that is, the members they recommended, the members those members recommended, and so on. Return member ID and name, and order by ascending member id.

```
```

3. Produce a CTE that can return the upward recommendation chain for any member

Produce a CTE that can return the upward recommendation chain for any member. You should be able to select recommender from recommenders where member=x. Demonstrate it by getting the chains for members 12 and 22. Results table should have member and recommender, ordered by member ascending, recommender descending.

```
 ```
