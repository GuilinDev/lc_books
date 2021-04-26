# SQL\_2

## 620 Not Boring Movies

 X city opened a new cinema, many people would like to go to this cinema. The cinema also gives out a poster indicating the movies’ ratings and descriptions.

Please write a SQL query to output movies with an odd numbered ID and a description that is not 'boring'. Order the result by rating.

For example, table `cinema`:

```text
+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   1     | War       |   great 3D   |   8.9     |
|   2     | Science   |   fiction    |   8.5     |
|   3     | irish     |   boring     |   6.2     |
|   4     | Ice song  |   Fantacy    |   8.6     |
|   5     | House card|   Interesting|   9.1     |
+---------+-----------+--------------+-----------+
```

For the example above, the output should be:

```text
+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   5     | House card|   Interesting|   9.1     |
|   1     | War       |   great 3D   |   8.9     |
+---------+-----------+--------------+-----------+
```

### Schema

```sql
Create table If Not Exists cinema (id int, movie varchar(255), description varchar(255), rating float(2, 1))
Truncate table cinema
insert into cinema (id, movie, description, rating) values ('1', 'War', 'great 3D', '8.9')
insert into cinema (id, movie, description, rating) values ('2', 'Science', 'fiction', '8.5')
insert into cinema (id, movie, description, rating) values ('3', 'irish', 'boring', '6.2')
insert into cinema (id, movie, description, rating) values ('4', 'Ice song', 'Fantacy', '8.6')
insert into cinema (id, movie, description, rating) values ('5', 'House card', 'Interesting', '9.1')
```

### Solution

```sql
Select *
From cinema
Where description not like '%boring%' # description <> 'boring'
And id % 2 != 0
Order by rating DESC
```

## 1211 Queries Quality and Percentage

Table: `Queries`

```text
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| query_name  | varchar |
| result      | varchar |
| position    | int     |
| rating      | int     |
+-------------+---------+
There is no primary key for this table, it may have duplicate rows.
This table contains information collected from some queries on a database.
The position column has a value from 1 to 500.
The rating column has a value from 1 to 5. Query with rating less than 3 is a poor query.
```

We define query `quality` as:

> The average of the ratio between query rating and its position.

We also define `poor query percentage` as:

> The percentage of all queries with rating less than 3.

Write an SQL query to find each `query_name`, the `quality` and `poor_query_percentage`.

Both `quality` and `poor_query_percentage` should be **rounded to 2 decimal places**.

The query result format is in the following example:

```text
Queries table:
+------------+-------------------+----------+--------+
| query_name | result            | position | rating |
+------------+-------------------+----------+--------+
| Dog        | Golden Retriever  | 1        | 5      |
| Dog        | German Shepherd   | 2        | 5      |
| Dog        | Mule              | 200      | 1      |
| Cat        | Shirazi           | 5        | 2      |
| Cat        | Siamese           | 3        | 3      |
| Cat        | Sphynx            | 7        | 4      |
+------------+-------------------+----------+--------+

Result table:
+------------+---------+-----------------------+
| query_name | quality | poor_query_percentage |
+------------+---------+-----------------------+
| Dog        | 2.50    | 33.33                 |
| Cat        | 0.66    | 33.33                 |
+------------+---------+-----------------------+

Dog queries quality is ((5 / 1) + (5 / 2) + (1 / 200)) / 3 = 2.50
Dog queries poor_ query_percentage is (1 / 3) * 100 = 33.33

Cat queries quality equals ((2 / 5) + (3 / 3) + (4 / 7)) / 3 = 0.66
Cat queries poor_ query_percentage is (1 / 3) * 100 = 33.33
```

### Schema

```sql
Create table If Not Exists Queries (query_name varchar(30), result varchar(50), position int, rating int)
Truncate table Queries
insert into Queries (query_name, result, position, rating) values ('Dog', 'Golden Retriever', '1', '5')
insert into Queries (query_name, result, position, rating) values ('Dog', 'German Shepherd', '2', '5')
insert into Queries (query_name, result, position, rating) values ('Dog', 'Mule', '200', '1')
insert into Queries (query_name, result, position, rating) values ('Cat', 'Shirazi', '5', '2')
insert into Queries (query_name, result, position, rating) values ('Cat', 'Siamese', '3', '3')
insert into Queries (query_name, result, position, rating) values ('Cat', 'Sphynx', '7', '4')
```

### Solution

```sql
SELECT 
	query_name,
	ROUND(AVG(rating / position), 2) AS quality,
	ROUND(AVG(rating < 3) * 100, 2) AS poor_query_percentage 
FROM 
	Queries
GROUP BY 
	query_name
```

## 1677 Product's Worth Over Invoices

Table: `Product`

```text
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_id  | int     |
| name        | varchar |
+-------------+---------+
product_id is the primary key for this table.
This table contains the ID and the name of the product. The name consists of only lowercase English letters. No two products have the same name.
```

Table: `Invoice`

```text
+-------------+------+
| Column Name | Type |
+-------------+------+
| invoice_id  | int  |
| product_id  | int  |
| rest        | int  |
| paid        | int  |
| canceled    | int  |
| refunded    | int  |
+-------------+------+
invoice_id is the primary key for this table and the id of this invoice.
product_id is the id of the product for this invoice.
rest is the amount left to pay for this invoice.
paid is the amount paid for this invoice.
canceled is the amount canceled for this invoice.
refunded is the amount refunded for this invoice.
```

Write an SQL query that will, for all products, return each product name with total amount due, paid, canceled, and refunded across all invoices.

Return the result table ordered by `product_name`.

The query result format is in the following example:

```text
Product table:
+------------+-------+
| product_id | name  |
+------------+-------+
| 0          | ham   |
| 1          | bacon |
+------------+-------+
Invoice table:
+------------+------------+------+------+----------+----------+
| invoice_id | product_id | rest | paid | canceled | refunded |
+------------+------------+------+------+----------+----------+
| 23         | 0          | 2    | 0    | 5        | 0        |
| 12         | 0          | 0    | 4    | 0        | 3        |
| 1          | 1          | 1    | 1    | 0        | 1        |
| 2          | 1          | 1    | 0    | 1        | 1        |
| 3          | 1          | 0    | 1    | 1        | 1        |
| 4          | 1          | 1    | 1    | 1        | 0        |
+------------+------------+------+------+----------+----------+
Result table:
+-------+------+------+----------+----------+
| name  | rest | paid | canceled | refunded |
+-------+------+------+----------+----------+
| bacon | 3    | 3    | 3        | 3        |
| ham   | 2    | 4    | 5        | 3        |
+-------+------+------+----------+----------+
- The amount of money left to pay for bacon is 1 + 1 + 0 + 1 = 3
- The amount of money paid for bacon is 1 + 0 + 1 + 1 = 3
- The amount of money canceled for bacon is 0 + 1 + 1 + 1 = 3
- The amount of money refunded for bacon is 1 + 1 + 1 + 0 = 3
- The amount of money left to pay for ham is 2 + 0 = 2
- The amount of money paid for ham is 0 + 4 = 4
- The amount of money canceled for ham is 5 + 0 = 5
- The amount of money refunded for ham is 0 + 3 = 3
```

### Schema

```sql
Create table If Not Exists Product(product_id int, name varchar(15))
Create table If Not Exists Invoice(invoice_id int,product_id int,rest int, paid int, canceled int, refunded int)
Truncate table Product
insert into Product (product_id, name) values ('0', 'ham')
insert into Product (product_id, name) values ('1', 'bacon')
Truncate table Invoice
insert into Invoice (invoice_id, product_id, rest, paid, canceled, refunded) values ('23', '0', '2', '0', '5', '0')
insert into Invoice (invoice_id, product_id, rest, paid, canceled, refunded) values ('12', '0', '0', '4', '0', '3')
insert into Invoice (invoice_id, product_id, rest, paid, canceled, refunded) values ('1', '1', '1', '1', '0', '1')
insert into Invoice (invoice_id, product_id, rest, paid, canceled, refunded) values ('2', '1', '1', '0', '1', '1')
insert into Invoice (invoice_id, product_id, rest, paid, canceled, refunded) values ('3', '1', '0', '1', '1', '1')
insert into Invoice (invoice_id, product_id, rest, paid, canceled, refunded) values ('4', '1', '1', '1', '1', '0')
```

### Solution

```sql
# 1.Use GROUP BY to group results by product (product_id or product_name in mysql and product_name in other RDBMS)
# 2.Use ORDER BY to order the results
# 3.Use Sum to aggregate the values
SELECT 
    p.name AS name, 
	sum(i.rest) AS rest,
	sum(i.paid) AS paid, 
	sum(i.canceled) AS canceled, 
	sum(i.refunded) AS refunded 
FROM Product p INNER JOIN Invoice i ON p.product_id = i.product_id 
GROUP BY p.product_id 
ORDER BY p.name;
```

## 1809 Ad-Free Sessions 

Table: `Playback`

```text
+-------------+------+
| Column Name | Type |
+-------------+------+
| session_id  | int  |
| customer_id | int  |
| start_time  | int  |
| end_time    | int  |
+-------------+------+
session_id is the primary key for this table.
customer_id is the ID of the customer watching this session.
The session runs during the inclusive interval between start_time and end_time.
It is guaranteed that start_time <= end_time and that two sessions for the same customer do not intersect.
```

Table: `Ads`

```text
+-------------+------+
| Column Name | Type |
+-------------+------+
| ad_id       | int  |
| customer_id | int  |
| timestamp   | int  |
+-------------+------+
ad_id is the primary key for this table.
customer_id is the ID of the customer viewing this ad.
timestamp is the moment of time at which the ad was shown.
```

Write an SQL query to report all the sessions that did not get shown any ads.

Return the result table in **any order**.

The query result format is in the following example:

```text
Playback table:
+------------+-------------+------------+----------+
| session_id | customer_id | start_time | end_time |
+------------+-------------+------------+----------+
| 1          | 1           | 1          | 5        |
| 2          | 1           | 15         | 23       |
| 3          | 2           | 10         | 12       |
| 4          | 2           | 17         | 28       |
| 5          | 2           | 2          | 8        |
+------------+-------------+------------+----------+

Ads table:
+-------+-------------+-----------+
| ad_id | customer_id | timestamp |
+-------+-------------+-----------+
| 1     | 1           | 5         |
| 2     | 2           | 17        |
| 3     | 2           | 20        |
+-------+-------------+-----------+

Result table:
+------------+
| session_id |
+------------+
| 2          |
| 3          |
| 5          |
+------------+
The ad with ID 1 was shown to user 1 at time 5 while they were in session 1.
The ad with ID 2 was shown to user 2 at time 17 while they were in session 4.
The ad with ID 3 was shown to user 2 at time 20 while they were in session 4.
We can see that sessions 1 and 4 had at least one ad. Sessions 2, 3, and 5 did not have any ads, so we return them.
```

### Schema

```sql
Create table If Not Exists Playback(session_id int,customer_id int,start_time int,end_time int)
Create table If Not Exists Ads (ad_id int, customer_id int, timestamp int)
Truncate table Playback
insert into Playback (session_id, customer_id, start_time, end_time) values ('1', '1', '1', '5')
insert into Playback (session_id, customer_id, start_time, end_time) values ('2', '1', '15', '23')
insert into Playback (session_id, customer_id, start_time, end_time) values ('3', '2', '10', '12')
insert into Playback (session_id, customer_id, start_time, end_time) values ('4', '2', '17', '28')
insert into Playback (session_id, customer_id, start_time, end_time) values ('5', '2', '2', '8')
Truncate table Ads
insert into Ads (ad_id, customer_id, timestamp) values ('1', '1', '5')
insert into Ads (ad_id, customer_id, timestamp) values ('2', '2', '17')
insert into Ads (ad_id, customer_id, timestamp) values ('3', '2', '20')
```

### Solution

```sql
SELECT session_id
FROM playback pb 
LEFT JOIN ads ad
ON pb.customer_id = ad.customer_id AND ad.timestamp BETWEEN start_time and end_time
WHERE ad.customer_id IS NULL
```

## 1543 Fix Product Name Format

Table: `Sales`

```text
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| sale_id      | int     |
| product_name | varchar |
| sale_date    | date    |
+--------------+---------+
sale_id is the primary key for this table.
Each row of this table contains the product name and the date it was sold.
```

Since table Sales was filled manually in the year `2000`, `product_name` may contain leading and/or trailing white spaces, also they are case-insensitive.

Write an SQL query to report

* `product_name` in lowercase without leading or trailing white spaces.
* `sale_date` in the format `('YYYY-MM')`.
* `total` the number of times the product was sold in this month.

Return the result table ordered by `product_name` in **ascending order**. In case of a tie, order it by `sale_date` in **ascending order**.

The query result format is in the following example.

```text
Sales
+---------+--------------+------------+
| sale_id | product_name | sale_date  |
+---------+--------------+------------+
| 1       | LCPHONE      | 2000-01-16 |
| 2       | LCPhone      | 2000-01-17 |
| 3       | LcPhOnE      | 2000-02-18 |
| 4       | LCKeyCHAiN   | 2000-02-19 |
| 5       | LCKeyChain   | 2000-02-28 |
| 6       | Matryoshka   | 2000-03-31 |
+---------+--------------+------------+

Result table:
+--------------+-----------+-------+
| product_name | sale_date | total |
+--------------+-----------+-------+
| lckeychain   | 2000-02   | 2     |
| lcphone      | 2000-01   | 2     |
| lcphone      | 2000-02   | 1     |
| matryoshka   | 2000-03   | 1     |
+--------------+-----------+-------+
In January, 2 LcPhones were sold, please note that the product names are not case sensitive and may contain spaces.
In Februery, 2 LCKeychains and 1 LCPhone were sold.
In March, 1 matryoshka was sold.
```

### Schema

```sql
Create table If Not Exists Sales (sale_id int, product_name varchar(30), sale_date date)
Truncate table Sales
insert into Sales (sale_id, product_name, sale_date) values ('1', 'LCPHONE', '2000-01-16')
insert into Sales (sale_id, product_name, sale_date) values ('2', 'LCPhone', '2000-01-17')
insert into Sales (sale_id, product_name, sale_date) values ('3', 'LcPhOnE', '2000-02-18')
insert into Sales (sale_id, product_name, sale_date) values ('4', 'LCKeyCHAiN', '2000-02-19')
insert into Sales (sale_id, product_name, sale_date) values ('5', 'LCKeyChain', '2000-02-28')
insert into Sales (sale_id, product_name, sale_date) values ('6', 'Matryoshka', '2000-03-31')
```

### Solution

```sql
SELECT LOWER(TRIM(product_name)) As product_name, DATE_FORMAT(sale_date, "%Y-%m") sale_date, count(sale_id) total
FROM sales
GROUP BY 1, 2
ORDER BY 1, 2
```

## 1294 Weather Type in Each Country 

Table: `Countries`

```text
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| country_id    | int     |
| country_name  | varchar |
+---------------+---------+
country_id is the primary key for this table.
Each row of this table contains the ID and the name of one country.
```

Table: `Weather`

```text
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| country_id    | int     |
| weather_state | varchar |
| day           | date    |
+---------------+---------+
(country_id, day) is the primary key for this table.
Each row of this table indicates the weather state in a country for one day.
```

Write an SQL query to find the type of weather in each country for November 2019.

The type of weather is **Cold** if the average `weather_state` is less than or equal 15, **Hot** if the average `weather_state` is greater than or equal 25 and **Warm** otherwise.

Return result table in any order.

The query result format is in the following example:

```text
Countries table:
+------------+--------------+
| country_id | country_name |
+------------+--------------+
| 2          | USA          |
| 3          | Australia    |
| 7          | Peru         |
| 5          | China        |
| 8          | Morocco      |
| 9          | Spain        |
+------------+--------------+
Weather table:
+------------+---------------+------------+
| country_id | weather_state | day        |
+------------+---------------+------------+
| 2          | 15            | 2019-11-01 |
| 2          | 12            | 2019-10-28 |
| 2          | 12            | 2019-10-27 |
| 3          | -2            | 2019-11-10 |
| 3          | 0             | 2019-11-11 |
| 3          | 3             | 2019-11-12 |
| 5          | 16            | 2019-11-07 |
| 5          | 18            | 2019-11-09 |
| 5          | 21            | 2019-11-23 |
| 7          | 25            | 2019-11-28 |
| 7          | 22            | 2019-12-01 |
| 7          | 20            | 2019-12-02 |
| 8          | 25            | 2019-11-05 |
| 8          | 27            | 2019-11-15 |
| 8          | 31            | 2019-11-25 |
| 9          | 7             | 2019-10-23 |
| 9          | 3             | 2019-12-23 |
+------------+---------------+------------+
Result table:
+--------------+--------------+
| country_name | weather_type |
+--------------+--------------+
| USA          | Cold         |
| Austraila    | Cold         |
| Peru         | Hot          |
| China        | Warm         |
| Morocco      | Hot          |
+--------------+--------------+
Average weather_state in USA in November is (15) / 1 = 15 so weather type is Cold.
Average weather_state in Austraila in November is (-2 + 0 + 3) / 3 = 0.333 so weather type is Cold.
Average weather_state in Peru in November is (25) / 1 = 25 so weather type is Hot.
Average weather_state in China in November is (16 + 18 + 21) / 3 = 18.333 so weather type is Warm.
Average weather_state in Morocco in November is (25 + 27 + 31) / 3 = 27.667 so weather type is Hot.
We know nothing about average weather_state in Spain in November so we don't include it in the result table. 
```

### Schema

```sql
Create table If Not Exists Countries (country_id int, country_name varchar(20))
Create table If Not Exists Weather (country_id int, weather_state int, day date)
Truncate table Countries
insert into Countries (country_id, country_name) values ('2', 'USA')
insert into Countries (country_id, country_name) values ('3', 'Australia')
insert into Countries (country_id, country_name) values ('7', 'Peru')
insert into Countries (country_id, country_name) values ('5', 'China')
insert into Countries (country_id, country_name) values ('8', 'Morocco')
insert into Countries (country_id, country_name) values ('9', 'Spain')
Truncate table Weather
insert into Weather (country_id, weather_state, day) values ('2', '15', '2019-11-01')
insert into Weather (country_id, weather_state, day) values ('2', '12', '2019-10-28')
insert into Weather (country_id, weather_state, day) values ('2', '12', '2019-10-27')
insert into Weather (country_id, weather_state, day) values ('3', '-2', '2019-11-10')
insert into Weather (country_id, weather_state, day) values ('3', '0', '2019-11-11')
insert into Weather (country_id, weather_state, day) values ('3', '3', '2019-11-12')
insert into Weather (country_id, weather_state, day) values ('5', '16', '2019-11-07')
insert into Weather (country_id, weather_state, day) values ('5', '18', '2019-11-09')
insert into Weather (country_id, weather_state, day) values ('5', '21', '2019-11-23')
insert into Weather (country_id, weather_state, day) values ('7', '25', '2019-11-28')
insert into Weather (country_id, weather_state, day) values ('7', '22', '2019-12-01')
insert into Weather (country_id, weather_state, day) values ('7', '20', '2019-12-02')
insert into Weather (country_id, weather_state, day) values ('8', '25', '2019-11-05')
insert into Weather (country_id, weather_state, day) values ('8', '27', '2019-11-15')
insert into Weather (country_id, weather_state, day) values ('8', '31', '2019-11-25')
insert into Weather (country_id, weather_state, day) values ('9', '7', '2019-10-23')
insert into Weather (country_id, weather_state, day) values ('9', '3', '2019-12-23')
```

### Solution

```sql
SELECT a.country_name,
CASE 
    WHEN AVG(weather_state)<=15 THEN "Cold"
    WHEN AVG(weather_State)>=25 THEN "Hot"
    ELSE "Warm"
END
AS weather_type 
FROM Countries AS a
JOIN Weather AS b
ON a.country_id = b.country_id
WHERE MONTH(b.day) = 11
GROUP BY a.country_id
```

## 1075 Project Employees I

Table: `Project`

```text
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| project_id  | int     |
| employee_id | int     |
+-------------+---------+
(project_id, employee_id) is the primary key of this table.
employee_id is a foreign key to Employee table.
```

Table: `Employee`

```text
+------------------+---------+
| Column Name      | Type    |
+------------------+---------+
| employee_id      | int     |
| name             | varchar |
| experience_years | int     |
+------------------+---------+
employee_id is the primary key of this table.
```

Write an SQL query that reports the **average** experience years of all the employees for each project, **rounded to 2 digits**.

The query result format is in the following example:

```text
Project table:
+-------------+-------------+
| project_id  | employee_id |
+-------------+-------------+
| 1           | 1           |
| 1           | 2           |
| 1           | 3           |
| 2           | 1           |
| 2           | 4           |
+-------------+-------------+

Employee table:
+-------------+--------+------------------+
| employee_id | name   | experience_years |
+-------------+--------+------------------+
| 1           | Khaled | 3                |
| 2           | Ali    | 2                |
| 3           | John   | 1                |
| 4           | Doe    | 2                |
+-------------+--------+------------------+

Result table:
+-------------+---------------+
| project_id  | average_years |
+-------------+---------------+
| 1           | 2.00          |
| 2           | 2.50          |
+-------------+---------------+
The average experience years for the first project is (3 + 2 + 1) / 3 = 2.00 and for the second project is (3 + 2) / 2 = 2.50
```

### Schema

```sql
Create table If Not Exists Project (project_id int, employee_id int)
Create table If Not Exists Employee (employee_id int, name varchar(10), experience_years int)
Truncate table Project
insert into Project (project_id, employee_id) values ('1', '1')
insert into Project (project_id, employee_id) values ('1', '2')
insert into Project (project_id, employee_id) values ('1', '3')
insert into Project (project_id, employee_id) values ('2', '1')
insert into Project (project_id, employee_id) values ('2', '4')
Truncate table Employee
insert into Employee (employee_id, name, experience_years) values ('1', 'Khaled', '3')
insert into Employee (employee_id, name, experience_years) values ('2', 'Ali', '2')
insert into Employee (employee_id, name, experience_years) values ('3', 'John', '1')
insert into Employee (employee_id, name, experience_years) values ('4', 'Doe', '2')
```

### Solution

```sql
Select project_id, round(avg(experience_years), 2) As average_years
From project p
Left Join Employee e On p.employee_id = e.employee_id
Group By 1
```

## 603 Consecutive Available Seats

 Several friends at a cinema ticket office would like to reserve consecutive available seats.  
Can you help to query all the consecutive available seats order by the seat\_id using the following `cinema` table?

```text
| seat_id | free |
|---------|------|
| 1       | 1    |
| 2       | 0    |
| 3       | 1    |
| 4       | 1    |
| 5       | 1    |
```

 Your query should return the following result for the sample case above.

```text
| seat_id |
|---------|
| 3       |
| 4       |
| 5       |
```

**Note**:

* The seat\_id is an auto increment int, and free is bool \('1' means free, and '0' means occupied.\).
* Consecutive available seats are more than 2\(inclusive\) seats consecutively available.

### Schema

```sql
Create table If Not Exists cinema (seat_id int primary key auto_increment, free bool)
Truncate table cinema
insert into cinema (seat_id, free) values ('1', '1')
insert into cinema (seat_id, free) values ('2', '0')
insert into cinema (seat_id, free) values ('3', '1')
insert into cinema (seat_id, free) values ('4', '1')
insert into cinema (seat_id, free) values ('5', '1')
```

### Solution

```sql
Select distinct a.seat_id 
From cinema a, cinema b
Where a.free = 1 And b.free = 1 # 空闲
    And (a.seat_id + 1 = b.seat_id Or a.seat_id = b.seat_id + 1) # 连续
Order by a.seat_id asc
```

```sql
Select distinct a.seat_id 
From cinema a, cinema b
Where a.free = true And b.free = true # 空闲，0不为true
    And abs(a.seat_id - b.seat_id) = 1 # 连续
Order by a.seat_id asc
```

## 1113 Reported Posts

Table: `Actions`

```text
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| post_id       | int     |
| action_date   | date    | 
| action        | enum    |
| extra         | varchar |
+---------------+---------+
There is no primary key for this table, it may have duplicate rows.
The action column is an ENUM type of ('view', 'like', 'reaction', 'comment', 'report', 'share').
The extra column has optional information about the action such as a reason for report or a type of reaction. 
```

Write an SQL query that reports the number of posts reported yesterday for each report reason. Assume today is **2019-07-05**.

The query result format is in the following example:

```text
Actions table:
+---------+---------+-------------+--------+--------+
| user_id | post_id | action_date | action | extra  |
+---------+---------+-------------+--------+--------+
| 1       | 1       | 2019-07-01  | view   | null   |
| 1       | 1       | 2019-07-01  | like   | null   |
| 1       | 1       | 2019-07-01  | share  | null   |
| 2       | 4       | 2019-07-04  | view   | null   |
| 2       | 4       | 2019-07-04  | report | spam   |
| 3       | 4       | 2019-07-04  | view   | null   |
| 3       | 4       | 2019-07-04  | report | spam   |
| 4       | 3       | 2019-07-02  | view   | null   |
| 4       | 3       | 2019-07-02  | report | spam   |
| 5       | 2       | 2019-07-04  | view   | null   |
| 5       | 2       | 2019-07-04  | report | racism |
| 5       | 5       | 2019-07-04  | view   | null   |
| 5       | 5       | 2019-07-04  | report | racism |
+---------+---------+-------------+--------+--------+

Result table:
+---------------+--------------+
| report_reason | report_count |
+---------------+--------------+
| spam          | 1            |
| racism        | 2            |
+---------------+--------------+ 
Note that we only care about report reasons with non zero number of reports.
```

### Schema

```sql
Create table If Not Exists Actions (user_id int, post_id int, action_date date, action ENUM('view', 'like', 'reaction', 'comment', 'report', 'share'), extra varchar(10))
Truncate table Actions
insert into Actions (user_id, post_id, action_date, action, extra) values ('1', '1', '2019-07-01', 'view', 'None')
insert into Actions (user_id, post_id, action_date, action, extra) values ('1', '1', '2019-07-01', 'like', 'None')
insert into Actions (user_id, post_id, action_date, action, extra) values ('1', '1', '2019-07-01', 'share', 'None')
insert into Actions (user_id, post_id, action_date, action, extra) values ('2', '4', '2019-07-04', 'view', 'None')
insert into Actions (user_id, post_id, action_date, action, extra) values ('2', '4', '2019-07-04', 'report', 'spam')
insert into Actions (user_id, post_id, action_date, action, extra) values ('3', '4', '2019-07-04', 'view', 'None')
insert into Actions (user_id, post_id, action_date, action, extra) values ('3', '4', '2019-07-04', 'report', 'spam')
insert into Actions (user_id, post_id, action_date, action, extra) values ('4', '3', '2019-07-02', 'view', 'None')
insert into Actions (user_id, post_id, action_date, action, extra) values ('4', '3', '2019-07-02', 'report', 'spam')
insert into Actions (user_id, post_id, action_date, action, extra) values ('5', '2', '2019-07-04', 'view', 'None')
insert into Actions (user_id, post_id, action_date, action, extra) values ('5', '2', '2019-07-04', 'report', 'racism')
insert into Actions (user_id, post_id, action_date, action, extra) values ('5', '5', '2019-07-04', 'view', 'None')
insert into Actions (user_id, post_id, action_date, action, extra) values ('5', '5', '2019-07-04', 'report', 'racism')
```

### Solution

```sql
Select extra as report_reason, count(distinct post_id) as report_count
From Actions
Where action = 'report' And action_date = '2019-07-04' # action_date = subdate(curdate_date, 1)
Group by 1
```

## 607 Sales Person 

**Description**

Given three tables: `salesperson`, `company`, `orders`.  
Output all the **names** in the table `salesperson`, who didn’t have sales to company 'RED'.

**Example**  
**Input**

Table: `salesperson`

```text
+----------+------+--------+-----------------+-----------+
| sales_id | name | salary | commission_rate | hire_date |
+----------+------+--------+-----------------+-----------+
|   1      | John | 100000 |     6           | 4/1/2006  |
|   2      | Amy  | 120000 |     5           | 5/1/2010  |
|   3      | Mark | 65000  |     12          | 12/25/2008|
|   4      | Pam  | 25000  |     25          | 1/1/2005  |
|   5      | Alex | 50000  |     10          | 2/3/2007  |
+----------+------+--------+-----------------+-----------+
```

The table `salesperson` holds the salesperson information. Every salesperson has a **sales\_id** and a **name**.

Table: `company`

```text
+---------+--------+------------+
| com_id  |  name  |    city    |
+---------+--------+------------+
|   1     |  RED   |   Boston   |
|   2     | ORANGE |   New York |
|   3     | YELLOW |   Boston   |
|   4     | GREEN  |   Austin   |
+---------+--------+------------+
```

The table `company` holds the company information. Every company has a **com\_id** and a **name**.

Table: `orders`

```text
+----------+------------+---------+----------+--------+
| order_id | order_date | com_id  | sales_id | amount |
+----------+------------+---------+----------+--------+
| 1        |   1/1/2014 |    3    |    4     | 100000 |
| 2        |   2/1/2014 |    4    |    5     | 5000   |
| 3        |   3/1/2014 |    1    |    1     | 50000  |
| 4        |   4/1/2014 |    1    |    4     | 25000  |
+----------+----------+---------+----------+--------+
```

The table `orders` holds the sales record information, salesperson and customer company are represented by **sales\_id** and **com\_id**.

**output**

```text
+------+
| name | 
+------+
| Amy  | 
| Mark | 
| Alex |
+------+
```

**Explanation**

According to order '3' and '4' in table `orders`, it is easy to tell only salesperson 'John' and 'Pam' have sales to company 'RED',  
so we need to output all the other **names** in the table `salesperson`.

### Schema

```sql
Create table If Not Exists salesperson (sales_id int, name varchar(255), salary int,commission_rate int, hire_date varchar(255))
Create table If Not Exists company (com_id int, name varchar(255), city varchar(255))
Create table If Not Exists orders (order_id int, order_date varchar(255), com_id int, sales_id int, amount int)
Truncate table salesperson
insert into salesperson (sales_id, name, salary, commission_rate, hire_date) values ('1', 'John', '100000', '6', '4/1/2006')
insert into salesperson (sales_id, name, salary, commission_rate, hire_date) values ('2', 'Amy', '12000', '5', '5/1/2010')
insert into salesperson (sales_id, name, salary, commission_rate, hire_date) values ('3', 'Mark', '65000', '12', '12/25/2008')
insert into salesperson (sales_id, name, salary, commission_rate, hire_date) values ('4', 'Pam', '25000', '25', '1/1/2005')
insert into salesperson (sales_id, name, salary, commission_rate, hire_date) values ('5', 'Alex', '5000', '10', '2/3/2007')
Truncate table company
insert into company (com_id, name, city) values ('1', 'RED', 'Boston')
insert into company (com_id, name, city) values ('2', 'ORANGE', 'New York')
insert into company (com_id, name, city) values ('3', 'YELLOW', 'Boston')
insert into company (com_id, name, city) values ('4', 'GREEN', 'Austin')
Truncate table orders
insert into orders (order_id, order_date, com_id, sales_id, amount) values ('1', '1/1/2014', '3', '4', '10000')
insert into orders (order_id, order_date, com_id, sales_id, amount) values ('2', '2/1/2014', '4', '5', '5000')
insert into orders (order_id, order_date, com_id, sales_id, amount) values ('3', '3/1/2014', '1', '1', '50000')
insert into orders (order_id, order_date, com_id, sales_id, amount) values ('4', '4/1/2014', '1', '4', '25000')
```

### Solution

```sql
Select s.name 
From salesperson s
Where s.sales_id not in (
    Select o.sales_id
    From orders o
        Left Join Company c
        On o.com_id = c.com_id
    Where c.name = 'RED' # where也可用于join on之后
)
```

## 182 Duplicate Emails

Write a SQL query to find all duplicate emails in a table named `Person`.

```text
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
```

For example, your query should return the following for the above table:

```text
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```

**Note**: All emails are in lowercase.

### Schema

```sql
Create table If Not Exists Person (Id int, Email varchar(255))
Truncate table Person
insert into Person (Id, Email) values ('1', 'a@b.com')
insert into Person (Id, Email) values ('2', 'c@d.com')
insert into Person (Id, Email) values ('3', 'a@b.com')
```

### Solution

```sql
Select Email
From Person
Group by Email
having count(*) > 1
```

```sql
Select Email from Person Where Id not in (
    Select tmp.Id from (
        Select min(Id) as Id 
        from Person 
            Group By email
    ) tmp
)
```

## 1667 Fix Names in a Table 

Table: `Users`

```text
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| user_id        | int     |
| name           | varchar |
+----------------+---------+
user_id is the primary key for this table.
This table contains the ID and the name of the user. The name consists of only lowercase and uppercase characters.
```

Write an SQL query to fix the names so that only the first character is uppercase and the rest are lowercase.

Return the result table ordered by `user_id`.

The query result format is in the following example:

```text
Users table:
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | aLice |
| 2       | bOB   |
+---------+-------+

Result table:
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | Alice |
| 2       | Bob   |
+---------+-------+
```

### Schema

```sql
Create table If Not Exists Users (user_id int, name varchar(40))
Truncate table Users
insert into Users (user_id, name) values ('1', 'aLice')
insert into Users (user_id, name) values ('2', 'bOB')
```

### Solution

```sql

```

## 1527 Patients With a Condition 

### Schema

```sql

```

### Solution

```sql

```

## 181 Employees Earning More Than Their Managers 

### Schema

```sql

```

### Solution

```sql

```

## 1322 Ads Performance

### Schema

```sql

```

### Solution

```sql

```

## 183 Customers Who Never Order

### Schema

```sql

```

### Solution

```sql

```

