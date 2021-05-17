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

5 - Retriveal: Functions
------------------------

### 5.1 - Overview of Functions
* Group functions
    * SUM
    * AVG
    * COUNT
* Single-row functions
    * UPPER
    * LOWER
    * TRUNC
    * TO_CHAR

### 5.2 - Arithmetic functions
* ROUND(n[,m])
* TRUNC(n[,m])
* CEIL(n)
* FLOOR(n)
* ABS(n)
* SIGN(n)
* SQRT(n)
* EXP(n)
* LN(n), LOG(m, n)
* POWER(n, m)
* MOD(n, m)
* SIN(n), COS(n), TAN(n)
* ASIN(n), ACOS(n), ATAN(n)
* SINH(n), COSH(n), TANH(n)

### 5.3 - Text Functions
* LENGTH(t)
* ASCII(t)
* CHR(n)
* UPPER(t), LOWER(t)
* INITCAP(t)
* LTRIM(t[,k])
* RTRIM(t[,k])
* TRIM([[option][c FROM]] t)
* LPAD(t,n[,k])
* RPAD(t,n[,k])  -- Right-pad t with k to length n (the default k is a space)
* SUBSTR(t,n[,m]) -- Substring of t from position n, m characters long (the default for m is until end)
* INSTR(t, k) -- Position of the first occurrence of k in t
* INSTR(t, k, n)
* TRANSLATE(t,v,w)
* REPLACE(t,v)
* REPLACE(t,v,w) -- Replace each occurrence of v in t by w
* CONCAT(t1,t2)

__Note:__ When counting positions in strings, always start with one, not  with zero.

### 5.4 - Regular Expressions
* REGEXP_COUNT(source, pattern, ...)
* REGEXP_INSTR(source, pattern, position, occurrence, ...)
* REGEXP_SUBSTR(source, patter, position, occurrence, ...)
* REGEXP_REPLACE(source, patter, replace, pos, occurrence, ...)

### 5.5 Date Functions
* ADD_MONTHS(d, n)
* MONTHS_BETWEEN(d, e)
* LAST_DAY(d)   -- Last day of the month containing date d
* NEXT_DAY(d, weekday) -- The first weekday (mon, tue, etc.) after d
* NEW_TIME(d, z1, z2) -- Convert date/time from time zone z1 to z2
* ROUND(d[, fmt])
* TRUNC(d[, fmt])
* EXTRACT(c FROM d) -- Extract date/time component c from expression d

### 5.6 General Functions
* GREATEST(a, b, ...)
* LEAST(a, b, ...)
* NULLIF(a, b)  -- NULL if a = b; otherwise a
* COALESCE(a, b, ...) -- The first NOT NULL argument (and NULL if all arguments are NULL)
* NVL(x, y) -- y if x is NULL; otherwise x
* NVL2(x, y, z) -- y if x is not NULL; otherwise z
* CASE x when a1 then b1
when a2 then b2 ... else y
end

### 5.7 Conversion Functions
* TO_CHAR(n[,fmt])  -- Convert number n to a string
* TO_CHAR(d[,fmt])  -- Convert date/time expression d to a string
* TO_NUMBER(t)      -- Convert string t to a number
* TO_BINARY_FLOAT(e[,fmt]) -- Convert expression e to a floating-point number
* TO_BINARY_DOUBLE(e[,fmt]) -- Convert expression e to a double-precision, floating-point number
* TO_DATE(t[,fmt]) -- Convert string t to a date
* TO_YMINTERVAL(t) -- Convert string t to a YEAR TO MONTH interval
* TO_DSINTERVAL(t) -- Convert string t to a DAY TO SECOND interval
* TO_TIMESTAMP (t[,fmt]) -- Convert string t to a timestamp
* CAST(e AS t) -- Convert expression e to datatype t
