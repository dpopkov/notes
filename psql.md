In Command Line
---------------

psql -f [filename]              execute commands from file, then exit

psql -f demo.sql -U postgres    execute sql from demo.sql as user postgres

In psql
-------

Command	| Description
--------|------------
\q        | quit
\?        | help
\h [name] | help on syntax
\i file   | execute commands from file
\l     		| list databases
\d      	| list tables, views, and sequences
\d name 	| descibe table, view, sequence, or index
\dt     	| list tables
\dv     	| list views
\di     	| список индексов
\c [dbname]   	| connect
\password [username]  | change password for a user
\cd [dir]  	| change current directory
\timing [on|off]| включение/выключение таймера, показывающее затраченное командой время
\! [command]	| execute command in shell


Change password
---------------

sudo -u postgres psql postgres

``` 
\password postgres
Enter new password: _____
```

