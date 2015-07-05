title: How to filter a IP from a IP range
author: Toh Leng Wee
date: 2015-06-29 14:25:47
tags: tsql
---
## About
In this post, we will show you how to filter a IP from a IP range in tsql.

## Example
We would like to know whether a IP is between **64.233.160.0** and **64.233.191.255**.
We could use <a href="http://www.sqlteam.com/article/using-the-parsename-function-to-split-delimited-data" target="_blank" >PARSENAME function to split delimited data</a> such as IP. After that we add them together by multiplying each part of the IP with 2<sup>0</sup>, 2<sup>8</sup>, 2<sup>16</sup> and 2<sup>32</sup>.

```sql
DECLARE @beginInt bigint
DECLARE @endInt bigint

SET @beginInt = CONVERT(bigint, PARSENAME('64.233.160.0', 4)) * 16777216
              + CONVERT(bigint, PARSENAME('64.233.160.0', 3)) * 65536
              + CONVERT(bigint, PARSENAME('64.233.160.0', 2)) * 256
              + CONVERT(bigint, PARSENAME('64.233.160.0', 1))
SET @endInt = CONVERT(bigint, PARSENAME('64.233.191.255', 4)) * 16777216
            + CONVERT(bigint, PARSENAME('64.233.191.255', 3)) * 65536
            + CONVERT(bigint, PARSENAME('64.233.191.255', 2)) * 256
            + CONVERT(bigint, PARSENAME('64.233.191.255', 1))

SELECT [IP]
FROM [YourTableName]
WHERE CONVERT(bigint, PARSENAME(t.ip, 4)) * 16777216
    + CONVERT(bigint, PARSENAME(t.ip, 3)) * 65536
    + CONVERT(bigint, PARSENAME(t.ip, 2)) * 256
    + CONVERT(bigint, PARSENAME(t.ip, 1)) between @beginInt and @endInt
```