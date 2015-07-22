title: Connection Timeout Issue for MSSQL
author: chen chao
tags: mssql
---

This post is to address connection fast timeout issue for MSSQL when using default connection timeout.

## Content
The default value for MSSQL timeout of connection is 15sec. However, sometimes application connecting to a SQL Server Database may timeout within milliseconds 
```
System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.
    at System.Data.ProviderBase.DbConnectionPool.GetConnection(DbConnection owningObject)
    at System.Data.ProviderBase.DbConnectionFactory.GetConnection(DbConnection owningConnection)
    at System.Data.ProviderBase.DbConnectionPool.GetConnection(DbConnection owningObject)
    at System.Data.ProviderBase.DbConnectionFactory.GetConnection(DbConnection owningConnection)
    at   System.Data.ProviderBase.DbConnectionClosed.OpenConnection(DbConnection outerConnection, DbConnectionFactory connectionFactory)
    at System.Data.SqlClient.SqlConnection.Open()
```

The issue happens, because, SQLClient uses the connection retry algorithm (for TCP/IP Connections), and sets the initial connection timeout value as 15*0.08 = 1.2s and first tries to connect to the principal SQL server and if fails, then, tries to connect to mirrored SQL Server (the fact that the database is mirrored, may come from the connection string or SQLClient may collect this information, the first time it connects to the database). If this connection attempt also fails due to timeout (because of slow response from SQL Server or due to network delays), the SQLClient incorrectly sets the connection to doomed state, giving the connection timeout exception, without further connection retry attempt to principal.

Microsoft has confirmed that this is a problem in the current release of ADO.NET. This issue will be fixed in ADO.NET version, ships with Visual Studio 2011.

In the meantime, we request to use the following workarounds:
1. Increase the connection string timeout to 150 sec. This will give the first attempt enough time to connect( 150* .08=12 sec)
2. Add MinPool Size=20 in the connection string. This will always maintain a minimum of 20 connections in the pool and there will be less chances of creating new connection, thus reducing the chance of this error.
3. Improve the network performance. Update your NIC drivers to the latest firmware version. We have seen network latency when your NIC card is not compatible with certain Scalable Networking Pack settings. If you are on Windows Vista SP1 or above you may also consider disabling Receive Window Auto-Tuning. If you have NIC teaming enabled, disabling it would be a good option.