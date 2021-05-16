Oracle SQL
==========

2 - Introduction to SQL
-----------------------

### 2.1 - Overview of SQL

#### Data Definition

* CREATE
* ALTER
* DROP

#### Data Manipulation and Transactions

* INSERT
* UPDATE
* DELETE
* Transaction commands:  
    * COMMIT - to confirm all pending changes of the current transaction
    * ROLLBACK - to cancel all pending changes and restore the original situation

#### Retrieval

* SELECT - which columns do you want to see in the result
    * FROM - which table is needed for retrieval
    * WHERE - what is the condition to filter the rows
    * GROUP BY - how should the rows be groupped/aggregated
    * HAVING - what is the condition to filter the aggregated groups
    * ORDER BY - in which order do you want to see the resulting rows
    
#### Security

* CREATE USER - define new database users
* ALTER USER - change properties (privileges and password) of existing users
* DROP USER - remove user


### 2.2 - Basic SQL Concepts and Terminology

#### Constants

| Type                  | Example   |
|-----------------------|-----------|
| Numeric               | 42    |
|                       | 8.75  |
|                       | 7.75F |
| Alphanumeric          | 'JOneS'   |
|                       | '42'      |
| Dates and intervals   | DATE '2004-12-09'             |
|                       | TIMESTAMP '2004-09-05 11.42.59.00000' |
|                       | INTERVAL '2' SECOND           |
|                       | INTERVAL '1-3' YEAR TO MONTH  |

#### Variables

System Variables:  
* SYSDATE
* CURRENT_DATE
* SYSTIMESTAMP
* LOCALTIMESTAMP
* USER


3 - Data Definition
-------------------

### 3.3 - Data Types
* Number - 38 digits precison
* Character Datatype maximum sizes
    * VARCHAR2 - 4000(standard) or 32767(extended)
    * CHAR - 2000
    * CLOB - up to 4GB
* Date Datatype

### 3.5 - The Data Dictionary

```
SELECT * FROM dict ORDER BY table_name;
SELECT * FROM cat;  -- lists the objects in the current schema
```

4 - Retrieval: The Basics
-------------------------

### Order of processing
1. FROM
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. ORDER BY

### 4.7 - CASE Expressions

Simple CASE expression example:
```sql
select attendee, begindate, evaluation, 
    case evaluation
        when 1 then 'bad'
        when 2 then 'mediocre'
        when 3 then 'ok'
        when 4 then 'good'
        when 5 then 'excellent'
        else 'not filled in'
    end
from registrations;
```

Searched CASE expression example:
```sql
select ename, job,
case 
    when job = 'TRAINER' then '  10%'
    when job = 'MANAGER' then '  20%'
    when ename = 'SMITH' then '  30%'
    else '   0%'
end as raise
from employees;
```

### 4.8 - Subqueries

```sql
select * from registrations 
where course in (
    select code from courses where category = 'GEN'
);
```
```sql
select * from registrations where (course, begindate) in (
    select course, begindate from offerings where location = 'CHICAGO'
);
```
```sql
select * from employees where bdate >= (
    select bdate from employees where empno = 7876
);
```

