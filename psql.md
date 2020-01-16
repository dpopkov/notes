PostgreSQL utilities: psql, pg_dump, pg_restore
===============================================

In Command Line
---------------

psql -f [filename]              execute commands from file, then exit

psql -f demo.sql -U postgres    execute sql from demo.sql as user postgres

### Use in command line

```
psql --port=5432 --host=localhost --dbname=northwind --username=postgres
```
Or
```
psql -p 5432 -h localhost -d northwind -U postgres
```
Without __localhost__ pear authentication fails.

### How to eliminate typing connection parameters

1. Use Environment variables

```
export PGHOST=localhost
export PGPORT=5432
export PGUSER=postgres
export PGDATABASE=northwind
export PGPASSWORD=xxxxxxxx
```
You can put these environment variables in your `.bash_profile`.

2. Create `.pgpass` file in your home directory

Format:
```
hostname:port:database:username:password
```
Note: this option did __not__ work.

3. Use `.pg_service.conf` file


In psql
-------

Command	| Description
--------|------------
\q        | quit
\?        | help
\h [name] | help on syntax
\i file   | execute commands from file
\l        | list databases
\dn       | list schemas
\d        | list tables, views, and sequences
\d name   | descibe table, view, sequence, or index
\dt       | list tables
\dt schema-name.    | list tables in the schema-name
\dv       | list views
\di       | список индексов
\c [dbname]             | connect
\password [username]    | change password for a user
\cd [dir]               | change current directory
\timing [on|off]        | включение/выключение таймера, показывающее затраченное командой время
\! [command]            |  execute command in shell


Meta-Commands for Database Information
--------------------------------------

Command	| Description
--------|------------
\dt     | list tables
\dt+    | list tables expanded (display size and description)
\dt pattern     | list tables filtered by wildcard pattern
\di             | list indexes
\du             | list user accounts (roles)
\dv             | list views
\dx             | list installed extension


Importing, Exporting, and Using Files
-------------------------------------

Command	| Description
--------|------------
\copy table-name FROM 'file-path' WITH (FORMAT CSV, HEADER)     | import data from csv file with header
\copy table-name TO 'file-path' WITH (FORMAT CSV, HEADER)       | export data to csv file with header
\o [path-to-file]                     | send all query results to file or pipe
\o                                    | resume outputting to the screen
psql -d database -U user -f sql-file    | run the query saved in external file



psql: Formatting the Results Grid
---------------------------------

\pset border int        results grid has no border (0), between columns (1), or around all cells (2)

\pset null 'NULL'       display null values as NULL

\x                  toggle expanded output (in vertical blocks separated by record numbers)


psql configuration file
-----------------------

.psqlrc     on Linux


PostgreSQL configuration file
-----------------------------
```
SHOW config_file;
```


Change password
---------------

sudo -u postgres psql postgres

``` 
\password postgres
Enter new password: _____
```

Add a Database in command line
------------------------------

```
createdb -U postgres -e new-database-name
```

Option      |   Description
------------|---------------
-U          | user account
-e          | echo - print the sql statements to the screen


Using pg_dump to Back Up a Database or Table
--------------------------------------------

The command line tool `pg_dump` creates an output file that contain all the data from you database, SQL commands for re-creating tables, and other database objects, as well as loading the data into tables.

```
pg_dump -d database-name -U user-name -Fc > path-to-output-file

pg_dump -t 'table-name' -d database-name -U user-name -Fc > path-to-output-file
```

Option	| Description
--------|------------
-Fc     | generate backup in PostgreSQL custom format
-t 'table-name'   | name of the table to export


Restoring a Database Backup with pg_restore
-------------------------------------------

```
pg_restore -C -d database-name -U user-name 'path-to-input-file'
```

Option	| Description
--------|------------
-C      | create the database on the server

