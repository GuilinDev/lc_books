# SQL

## 176 Second Highest Salary

Write a SQL query to get the second highest salary from the `Employee` table.

```text
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

For example, given the above Employee table, the query should return `200` as the second highest salary. If there is no second highest salary, then the query should return `null`.

```text
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

### Schema

```sql
Create table If Not Exists Employee (Id int, Salary int)
Truncate table Employee
insert into Employee (Id, Salary) values ('1', '100')
insert into Employee (Id, Salary) values ('2', '200')
insert into Employee (Id, Salary) values ('3', '300')
```

### Solution

```sql
# Write your MySQL query statement below
# if need any 3rd or 4th or Nth value, can increase the first value followed by 
# LIMIT (n-1) ie. for 4th salary : LIMIT 3, 1;
select distinct(salary) as SecondHighestSalary from employee 
order by salary desc limit 1, 1
```

```sql
select max(salary) as SecondHighestSalary from employee 
where salary < (select max(salary) from employee)
```

## 175 Combine Two Tables



