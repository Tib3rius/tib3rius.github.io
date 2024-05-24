---
title: "SQL Injection Cheatsheet"
---
# SQL Injection Cheatsheet

## Comments

<table>
<thead>
<tr>
<th>Variant</th>
<th>Example</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan="3" class="span">MySQL</td>
<td># comment</td>
</tr>
<tr>
<td>-- comment</td>
</tr>
<tr>
<td>/*comment*/</td>
</tr>
<tr>
<td rowspan="2" class="span">PostgreSQL</td>
<td>-- comment</td>
</tr>
<tr>
<td>/*comment*/</td>
</tr>
<tr>
<td rowspan="2" class="span">MSSQL</td>
<td>-- comment</td>
</tr>
<tr>
<td>/*comment*/</td>
</tr>
<tr>
<td rowspan="2" class="span">Oracle</td>
<td>-- comment</td>
</tr>
<tr>
<td>/*comment*/</td>
</tr>
<tr>
<td rowspan="2" class="span">SQLite</td>
<td>-- comment</td>
</tr>
<tr>
<td>/*comment*/</td>
</tr>
</tbody>
</table>

## String Concatenation

<table>
<thead>
<tr>
<th>Variant</th>
<th>Function / Syntax</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan="2" class="span">MySQL</td>
<td>'foo' 'bar'</td>
</tr>
<tr>
<td>CONCAT('foo', 'bar')</td>
</tr>
<tr>
<td rowspan="2" class="span">PostgreSQL</td>
<td>'foo'||'bar'</td>
</tr>
<tr>
<td>CONCAT('foo', 'bar')</td>
</tr>
<tr>
<td rowspan="2" class="span">MSSQL</td>
<td>'foo'+'bar'</td>
</tr>
<tr>
<td>CONCAT('foo', 'bar')</td>
</tr>
<tr>
<td rowspan="2" class="span">Oracle</td>
<td>'foo'||'bar'</td>
</tr>
<tr>
<td>CONCAT('foo', 'bar')</td>
</tr>
<tr>
<td rowspan="2" class="span">SQLite</td>
<td>'foo'||'bar'</td>
</tr>
<tr>
<td>CONCAT('foo', 'bar')</td>
</tr>
</tbody>
</table>

## Substring

<table>
<thead>
<tr>
<th>Variant</th>
<th>Function / Syntax</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan="2" class="span">MySQL</td>
<td>SUBSTRING('foobar', START, LENGTH)</td>
</tr>
<tr>
<td>SUBSTR('foobar', START, LENGTH)</td>
</tr>
<tr>
<td rowspan="2" class="span">PostgreSQL</td>
<td>SUBSTRING('foobar', START, LENGTH)</td>
</tr>
<tr>
<td>SUBSTR('foobar', START, LENGTH)</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>SUBSTRING('foobar', START, LENGTH)</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>SUBSTR('foobar', START, LENGTH)</td>
</tr>
<tr>
<td rowspan="2" class="span">SQLite</td>
<td>SUBSTRING('foobar', START, LENGTH)</td>
</tr>
<tr>
<td>SUBSTR('foobar', START, LENGTH)</td>
</tr>
</tbody>
</table>

## Length

<table>
<thead>
<tr>
<th>Variant</th>
<th>Function</th>
<th>Notes</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan="2" class="span">MySQL</td>
<td>LENGTH('foo')</td>
<td>Counts number of bytes</td>
</tr>
<tr>
<td>CHAR_LENGTH('foo')</td>
<td>Counts number of chars (inc. multi-byte chars)</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>LENGTH('foo')</td>
<td>Counts number of chars (inc. multi-byte chars)</td>
</tr>
<tr>
<td rowspan="2" class="span">MSSQL</td>
<td>DATALENGTH('foo')</td>
<td>Counts number of bytes</td>
</tr>
<tr>
<td>LEN('foo')</td>
<td>Counts number of chars (inc. multi-byte chars)</td>
</tr>
<tr>
<td rowspan="2" class="span">Oracle</td>
<td>LENGTHB('foo')</td>
<td>Counts number of bytes</td>
</tr>
<tr>
<td>LENGTH('foo')</td>
<td>Counts number of chars (inc. multi-byte chars)</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>LENGTH('foo')</td>
<td>Counts number of chars (inc. multi-byte chars)</td>
</tr>
</tbody>
</table>

## Group Concatenation

<table>
<thead>
<tr>
<th>Variant</th>
<th>Function</th>
</tr>
</thead>
<tbody>
<tr>
<td class="span">MySQL</td>
<td>GROUP_CONCAT(expression, 'DELIMITER')</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>STRING_AGG(expression, 'DELIMITER')</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>STRING_AGG(expression, 'DELIMITER')</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>LISTAGG(expression, 'DELIMITER')</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>GROUP_CONCAT(expression, 'DELIMITER')</td>
</tr>
</tbody>
</table>

## Limiting & Offsetting Queries

<table>
<thead>
<tr>
<th>Variant</th>
<th>Limit Query Result to 1 Row</th>
<th>Limit to 1 Row, Starting at Row 5</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan="2" class="span">MySQL</td>
<td rowspan="2">SELECT * FROM users LIMIT 1</td>
<td>SELECT * FROM users LIMIT 4, 1</td>
</tr>
<tr>
<td>SELECT * FROM users LIMIT 1 OFFSET 4</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>SELECT * FROM users LIMIT 1</td>
<td>SELECT * FROM users LIMIT 1 OFFSET 4</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>SELECT * FROM users ORDER BY 1 OFFSET 0 ROWS FETCH NEXT 1 ROWS ONLY</td>
<td>SELECT * FROM users ORDER BY 1 OFFSET 4 ROWS FETCH NEXT 1 ROWS ONLY</td>
</tr>
<tr>
<td class="span">Oracle >= v12</td>
<td>SELECT * FROM users FETCH NEXT 1 ROWS ONLY</td>
<td>SELECT * FROM users OFFSET 4 ROWS FETCH NEXT 1 ROWS ONLY</td>
</tr>
<tr>
<td class="span">Oracle <= v11</td>
<td>SELECT * FROM users WHERE ROWNUM = 1</td>
<td>SELECT * FROM users WHERE ROWNUM = 5</td>
</tr>
<tr>
<td rowspan="2" class="span">SQLite</td>
<td rowspan="2">SELECT * FROM users LIMIT 1</td>
<td>SELECT * FROM users LIMIT 4, 1</td>
</tr>
<tr>
<td>SELECT * FROM users LIMIT 1 OFFSET 4</td>
</tr>
</tbody>
</table>

## Current Database / Schema

<table>
<thead>
<tr>
<th>Variant</th>
<th>Query</th>
</tr>
</thead>
<tbody>
<tr>
<td class="span">MySQL</td>
<td>SELECT DATABASE()</td>
</tr>
<tr>
<td rowspan="2" class="span">PostgreSQL</td>
<td>SELECT CURRENT_DATABASE()</td>
</tr>
<tr>
<td>SELECT CURRENT_SCHEMA()</td>
</tr>
<tr>
<td rowspan="2" class="span">MSSQL</td>
<td>SELECT DB_NAME()</td>
</tr>
<tr>
<td>SELECT SCHEMA_NAME()</td>
</tr>
<tr>
<td rowspan="3" class="span">Oracle</td>
<td>SELECT name FROM V$database</td>
</tr>
<tr>
<td>SELECT * FROM global_name</td>
</tr>
<tr>
<td>SELECT sys_context('USERENV', 'CURRENT_SCHEMA') FROM dual;</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>N/A</td>
</tr>
</tbody>
</table>

## List Databases

<table>
<thead>
<tr>
<th>Variant</th>
<th>Query</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan="2" class="span">MySQL</td>
<td>SELECT schema_name FROM INFORMATION_SCHEMA.SCHEMATA</td>
</tr>
<tr>
<td>SELECT db FROM mysql.db</td>
</tr>
<tr>
<td rowspan="2" class="span">PostgreSQL</td>
<td>SELECT datname FROM pg_database</td>
</tr>
<tr>
<td>SELECT DISTINCT(schemaname) FROM pg_tables</td>
</tr>
<tr>
<td rowspan="2" class="span">MSSQL</td>
<td>SELECT name FROM master.sys.databases</td>
</tr>
<tr>
<td>SELECT name FROM master..sysdatabases</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>SELECT OWNER FROM (SELECT DISTINCT(OWNER) FROM SYS.ALL_TABLES)</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>N/A</td>
</tr>
</tbody>
</table>

## List Tables

<table>
<thead>
<tr>
<th>Variant</th>
<th>Query</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan="2" class="span">MySQL</td>
<td>SELECT table_name FROM INFORMATION_SCHEMA.TABLES WHERE table_schema='[DBNAME]'</td>
</tr>
<tr>
<td>SELECT database_name,table_name FROM mysql.innodb_table_stats WHERE database_name='[DBNAME]'</td>
</tr>
<tr>
<td rowspan="2" class="span">PostgreSQL</td>
<td>SELECT tablename FROM pg_tables WHERE schemaname = '[SCHEMA_NAME]'</td>
</tr>
<tr>
<td>SELECT table_name FROM information_schema.tables WHERE table_schema='[SCHEMA_NAME]'</td>
</tr>
<tr>
<td rowspan="2" class="span">MSSQL</td>
<td>SELECT table_name FROM information_schema.tables WHERE table_catalog='[DBNAME]'</td>
</tr>
<tr>
<td>SELECT name FROM [DBNAME]..sysobjects WHERE xtype='U'</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>SELECT OWNER,TABLE_NAME FROM SYS.ALL_TABLES WHERE OWNER='[DBNAME]'</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>SELECT tbl_name FROM sqlite_master WHERE type='table'</td>
</tr>
</tbody>
</table>

## List Columns

<table>
<thead>
<tr>
<th>Variant</th>
<th>Query</th>
</tr>
</thead>
<tbody>
<tr>
<td class="span">MySQL</td>
<td>SELECT column_name,column_type FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name='[TABLE_NAME]' AND table_schema='[DBNAME]'</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>SELECT column_name,data_type FROM information_schema.columns WHERE table_schema='[DBNAME]' AND table_name='[TABLE_NAME]'</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>SELECT COL_NAME(OBJECT_ID('[DBNAME].[TABLE_NAME]'), [INDEX])</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>SELECT COLUMN_NAME,DATA_TYPE FROM SYS.ALL_TAB_COLUMNS WHERE TABLE_NAME='[TABLE_NAME]' AND OWNER='[DBNAME]'</td>
</tr>
<tr>
<td rowspan="2" class="span">SQLite</td>
<td>SELECT MAX(sql) FROM sqlite_master WHERE tbl_name='[TABLE_NAME]'</td>
</tr>
<tr>
<td>SELECT name FROM PRAGMA_TABLE_INFO('[TABLE_NAME]')</td>
</tr>
</tbody>
</table>