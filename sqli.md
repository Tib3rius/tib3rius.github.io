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
<td rowspan="2" style="vertical-align:top">MySQL</td>
<td>'foo' 'bar'</td>
</tr>
<tr>
<td>CONCAT('foo', 'bar')</td>
</tr>
<tr>
<td rowspan="2" style="vertical-align:top">PostgreSQL</td>
<td>'foo'||'bar'</td>
</tr>
<tr>
<td>CONCAT('foo', 'bar')</td>
</tr>
<tr>
<td rowspan="2" style="vertical-align:top">MSSQL</td>
<td>'foo'+'bar'</td>
</tr>
<tr>
<td>CONCAT('foo', 'bar')</td>
</tr>
<tr>
<td rowspan="2" style="vertical-align:top">Oracle</td>
<td>'foo'||'bar'</td>
</tr>
<tr>
<td>CONCAT('foo', 'bar')</td>
</tr>
<tr>
<td rowspan="2" style="vertical-align:top">SQLite</td>
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
<td rowspan="2" style="vertical-align:top">MySQL</td>
<td>SUBSTRING('foobar', START, LENGTH)</td>
</tr>
<tr>
<td>SUBSTR('foobar', START, LENGTH)</td>
</tr>
<tr>
<td rowspan="2" style="vertical-align:top">PostgreSQL</td>
<td>SUBSTRING('foobar', START, LENGTH)</td>
</tr>
<tr>
<td>SUBSTR('foobar', START, LENGTH)</td>
</tr>
<tr>
<td style="vertical-align:top">MSSQL</td>
<td>SUBSTRING('foobar', START, LENGTH)</td>
</tr>
<tr>
<td style="vertical-align:top">Oracle</td>
<td>SUBSTR('foobar', START, LENGTH)</td>
</tr>
<tr>
<td rowspan="2" style="vertical-align:top">SQLite</td>
<td>SUBSTRING('foobar', START, LENGTH)</td>
</tr>
<tr>
<td>SUBSTR('foobar', START, LENGTH)</td>
</tr>
</tbody>
</table>