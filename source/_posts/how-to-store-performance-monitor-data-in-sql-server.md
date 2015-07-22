title: How to store performance monitor data in SQL server database
date: 2015-07-21 00:37:17
author: Toh Leng Wee
tags: [sql, performance monitor, database]
---

## About
![Performance Monitor](https://lh3.googleusercontent.com/OX_1xPFlXWJt-QYiB9FuAN7AhQy0QT4-EceNIUf-W8g=w1024-h764-no "Performance Monitor")
Performance monitor is a very good tool for us to monitor real time SQL Server performance data, such as **CPU usage**, **memory usage**, **batch request/sec** and so on. But the question is, could we store these information into database to do offline analysis ? The answer is yes ! And we are going to show you how.

## Create system Data Source Name (DSN)
First of all, you have to create an empty database, in this case, my database is created in my local laptop called **PerformanceMonitor**
![PerformanceMonitorDatabase](https://lh3.googleusercontent.com/pOusKB28E6pKMKHiAba7uzw-w9UiTf79kmyOqa8Mdd0=w345-h225-no "PerformanceMonitor Database")

Run **odbcad32.exe** in Start > Run. To open **ODBC Data Source Administrator**.
![odbcad32.exe](https://lh3.googleusercontent.com/1kJHjdUUWXKZMygiIy9umChB_hY5M00-Di3wffMxRtE=w413-h212-no "odbcad32.exe")

Then goes to **System DSN** and select **Add**.
![System DSN](https://lh3.googleusercontent.com/daDlscLuV_9OJZH2bFY4O7cIKVtFwTk607gK249LydA=w471-h390-no "System DSN")

Select a driver
![Select a driver for which you want to set up a data source](https://lh3.googleusercontent.com/SXkrQxSDo3_jNmpbcDlG_M-xIZlWKtKvOEJzmrpmbjk=w478-h358-no "Select a driver for which you want to set up a data source")

Enter any name, and your database location (in this case it's localhost ".\")
![Create a new data source to SQL Server](https://lh3.googleusercontent.com/5ujQBMr8xntptoQ_JMzK_2E6PiQRcGRi_-TF9f7bVtY=w520-h368-no "Create a new data source to SQL Server")

Set up authentication
![Set up authentication](https://lh3.googleusercontent.com/aMv0WnBlC2XsHrrfeAOXrfy4C1aFSBw37r3tVibqeN4=w520-h368-no "Set up authentication")

Change the default database to **PerformanceMonitor**
![Change the default database to PerformanceMonitor](https://lh3.googleusercontent.com/ChzZ60EhEmMyBpFejVyK9ns2qvR8icfO8qjN5yjgwqI=w520-h368-no "Change the default database to PerformanceMonitor")

Next and click Finish
![Next and click Finish](https://lh3.googleusercontent.com/wOtmF3V_dEeFG8xrbjAoRtex2FJnhmrG6bCthsuxy4A=w520-h368-no "Next and click Finish")

You could test data source connection
![Test data source](https://lh3.googleusercontent.com/BRuclBri51QPYh3p3owc1mex-NPqw0OC4HZaiq2YUZU=w378-h392-no "Test data source")

## Reliability and Performance Monitor
Run **perfmon.exe** in Start > Run. To open **Performance Monitor**.
![perfmon.exe](https://lh3.googleusercontent.com/jULpc3x-fKikOBdzjCv6qsQFLXoP4iY2_ky_AcYsLnc=w413-h212-no "perfmon.exe")

Create a new data collector set
![Create a new data collector set](https://lh3.googleusercontent.com/1hWNNB0SXoi5670gX9U6YGP0l69GIoWx97sCxPooUas=w547-h355-no "Create a new data collector set")

Create manually (Advanced)
![Create manually (Advanced)](https://lh3.googleusercontent.com/oTlYRpSiK5dMGeCrGqpJKJCZfEkvIQXzN7u4-52Igvc=w549-h424-no "Create manually (Advanced)")

Create data logs, select Performance counter
![Select performance counter](https://lh3.googleusercontent.com/1aU5q1SKtPX0QsYcWD9w3r18NJfaOIO0dQw4EZyrGJU=w549-h424-no "Select performance counter")

Add performance counters and choose Data Source Name (DSN)
![Add performance counters](https://lh3.googleusercontent.com/Mpzd1rV4C6GLrAHfhV3We2fk-vDAGgrTN1fQr9uTtT0=w414-h461-no "Add performance counters")

**Start**
![Start by hitting green arrow](https://lh3.googleusercontent.com/krl2Xz3gLzselFD_dQAJyXHtJnRfPFbIQceaL4yWwlA=w534-h274-no "Start by hitting green arrow")

You Shall see there are some tables being created in **PerformanceMonitor** database.
![Database](https://lh3.googleusercontent.com/YHS3Jc257-MvYGlq10Ps-zcK27vApaLcQf3inpfOVC4=w345-h438-no "Database")


## Trouble Shooting
You might get errors such as "Call to SQLExecDirect failed with %1", this is most likely since the "PerformanceMonitor" is set to run as System or any
other account that doesn't have access to the SQL Server. 
If this is the case, then right click "PerformanceMonitor" and select properties.

This will give you the option to change the Run As account, do this and use an account that has the proper rights, for example your windows account.

## Reference

[How to store performance monitor data into SQL Server using Reliability and Performance Monitor](http://blogs.msdn.com/b/spike/archive/2009/08/27/how-to-store-performance-monitor-data-into-sql-server-using-reliability-and-performance-monitor.aspx)