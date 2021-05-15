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
