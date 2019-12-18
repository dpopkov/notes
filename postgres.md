PostgreSQL
==========


Export/Import
-------------

### Export data to a CSV file
```sql
COPY table-name TO 'absolute-path' WITH (FORMAT CSV, HEADER, DELIMITER '|');

COPY table-name TO 'absolute-path' WITH (FORMAT CSV, HEADER, DELIMITER '|', QUOTE 'quote-character');

COPY table-name (column1, column2, column3) TO 'absolute-path' 
WITH (FORMAT CSV, HEADER, DELIMITER '|', QUOTE 'quote-character');
```
### Exporting Query Results
```sql
COPY (select-statement) TO 'absolute-path' WITH (FORMAT CSV, HEADER, DELIMITER '|');
```

### Import data from a CSV file
```sql
COPY table-name FROM 'absolute-path' WITH (FORMAT CSV, HEADER);

COPY table-name (column1, column2) FROM 'absolute-path' WITH (FORMAT CSV, HEADER);
```

### Create and use Temporary Table
```sql
CREATE TEMPORARY TABLE temp-table-name (LIKE source-table-name);

COPY temp-table-name (column1, column3) 
FROM 'absolute-path-to-csv-input-file' WITH (FORMAT CSV, HEADER);

INSERT INTO real-table-name (column1, column2, column3) 
SELECT column1, 'column2-default-value', column3
FROM temp-table-name;

DROP TABLE temp-table-name;
```


Math and Stats
--------------

### Basic Math Operators

Operator | Description
---------|------------
"%"	| Modulo
"^"	| Exponentiation
"\|/"	| Square root
"\|\|/"	| Cube root
"!"	| Factorial


