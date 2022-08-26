# SQL Test Assignment

Attached is a mysqldump of a database to be used during the test.

Below are the questions for this test. Please enter a full, complete, working SQL statement under each question. We do not want the answer to the question. We want the SQL command to derive the answer. We will copy/paste these commands to test the validity of the answer.

**Example:**

_Q. Select all users_

- Please return at least first_name and last_name

SELECT first_name, last_name FROM users;

---

**— Test Starts Here —**

1. Select users whose id is either 3,2 or 4

- Please return at least: all user fields
  select \* from users where id in (2,3) or id = 4;

2. Count how many basic and premium listings each active user has

- Please return at least: first_name, last_name, basic, premium

SELECT u.first_name, u.last_name, (SELECT COUNT(status) from listings WHERE status = 2) as Basic, (SELECT COUNT(status) from listings WHERE status = 3) as Premium
FROM users u
INNER JOIN `listings` l
where u.status = 2
GROUP BY u.first_name, u.last_name;

3. Show the same count as before but only if they have at least ONE premium listing

- Please return at least: first_name, last_name, basic, premium

SELECT u.first_name, u.last_name, (SELECT COUNT(status) from listings WHERE status = 2) as Basic, (SELECT COUNT(status) from listings WHERE status = 3) as Premium
FROM users u
INNER JOIN `listings` l
where u.status = 2 and (SELECT COUNT(status) from listings WHERE status = 3) >= 1
GROUP BY u.first_name, u.last_name;

4. How much revenue has each active vendor made in 2013

- Please return at least: first_name, last_name, currency, revenue

SELECT u.first_name, u.last_name, c.currency, SUM(c.price) AS Revenue FROM users u
INNER JOIN listings l
ON u.id = l.user_id
INNER JOIN clicks c
ON l.id = c.listing_id
WHERE Year(c.created) = 2013 AND u.status = 2
GROUP BY u.first_name,u.last_name, c.currency;

5. Insert a new click for listing id 3, at $4.00

- Find out the id of this new click. Please return at least: id

INSERT INTO clicks(listing_id,price,currency,created) VALUES(3,4,'USD', CURDATE());
SELECT LAST_INSERT_ID();

6. Show listings that have not received a click in 2013

- Please return at least: listing_name

select l.name from listings l LEFT JOIN clicks c ON l.id = c.listing_id where Year(c.created) != 2013 GROUP BY l.name;

7. For each year show number of listings clicked and number of vendors who owned these listings

- Please return at least: date, total_listings_clicked, total_vendors_affected

SELECT YEAR(c.created) AS Year, COUNT(l.id) AS Listings, COUNT(u.id) AS Vendors FROM listings l
INNER JOIN clicks c
ON
c.listing_id = l.id
INNER JOIN users u
ON
l.user_id = u.id
GROUP BY YEAR(c.created);

8. Return a comma separated string of listing names for all active vendors

- Please return at least: first_name, last_name, listing_names

SELECT GROUP_CONCAT(u.first_name,u.last_name,l.name) FROM listings l
INNER JOIN users u
ON l.user_id = u.id
WHERE u.status = 2;
