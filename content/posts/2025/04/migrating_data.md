---
layout: post
title: "The limitations of TRIM() in T‑SQL when cleaning/migrating data"
date: 2025-02-22 20:33:00 
categories: [blog, tech]
excerpt: "A brief description or summary of my article."
---

**In T-SQL**, the **TRIM()** function only removes spaces (ASCII 32) and cannot remove hidden characters (such as newline characters, carriage returns, tabs, or some emojis). These characters might cause issues during data cleaning (or migration), for example:

- Data imported may contain carriage return (CHAR(13)) or newline (CHAR(10)).

- Data copied and pasted may contain tab characters (CHAR(9)).

- Specially formatted strings may contain invisible Unicode control characters, such as certain emojis.

Common hidden characters include:
| Type       | Description               | CHAR Code     |
|:----------------|:---------------------------|:---------------|
| Space          | Standard Space            | CHAR(32)      |
| Tab            | Tab                       | CHAR(9)       |
| Line Feed      | LF (Line Feed)            | CHAR(10)      |
| Carriage Return| CR (Carriage Return)      | CHAR(13)      |
| No-Break Space | No-Break Space            | CHAR(160)     |

***How*** to solve those problems ?
##### 1. Use Replace().
```
SELECT REPLACE(REPLACE(REPLACE(ColumnName, CHAR(13), ''), CHAR(10), ''), CHAR(9), '') AS CleanedColumn
FROM TableName;
```
##### 2. Use Translate()
This requires SQL Server 2017 or later, and allows replacing multiple characters at once.
```
SELECT TRANSLATE(ColumnName, CHAR(9) + CHAR(10) + CHAR(13), '   ') AS CleanedColumn
FROM TableName;
```
Combine with Replace function could be better.
##### 3. Use Patindex()
```
SELECT ColumnName
FROM TableName
WHERE PATINDEX('%[' + CHAR(9) + CHAR(10) + CHAR(13) + ']%', ColumnName) > 0;
```
If return result, then it contains Tab, Enter or LF.

##### 4. Use Like.
```
SELECT ColumnName 
FROM TableName 
WHERE ColumnName LIKE '%'+CHAR(13)+'%' OR ColumnName LIKE '%'+CHAR(10)+'%';
```
Or, if your source file comes from a UNIX-like system, opening it in Notepad will show the following symbols (in contrast to text files from Windows):
![Win](/Windows.webp)
![Unix](/Unix.webp)

If you often need to handle these hidden characters, the best approach is to write a function.

***In summary***, when performing data migration or data cleaning—or even during application debugging—hidden characters are one of the issues you need to consider.