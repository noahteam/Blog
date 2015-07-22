title: Session State for IIS 7
date: 2015-07-22 09:20:22
tags: IIS
thumbnailImage: https://lh3.googleusercontent.com/EdNref_ZKMhWHqME0SK4vn5fnyeW3nmpMamCfv6MxvU=w479-h274-no
---

##About
For IIS 7 there are four modes of Session State.
1. **InProc mode**, which stores session state in memory on the web server. This is the default.
2. **StateServer mode**, which stores session state in a separate process called the ASP.NET state service. This ensures that session state is preserved if the Web application is restarted and also makes session state available to multiple Web servers in a Web farm.
3. **SQLServer mode** stores session state in a SQL Server database. This ensures that session state is preserved if the Web application is restarted and also makes session state available to multiple Web servers in a Web farm.
4. **Custom mode**, which enables you to specify a custom storage provider.
5. **Off mode**, which disables session state.

This article will be focusing on the first three modes.

## InProc mode
This mode is the default session state mode when you first set up an website in IIS and it is the only mode which support Session_OnEnd event. From the previous prargraph, we know it strores the session state in memory on the web server. To be more specific, it stored the session in the worker process (w3wp.exe) which runs the site. So it has the same life cycle of AppDomain of web application. Several factors below will cause AppDomain to be restarted.
1. Many attributes (for example, the idleTimeout attribute) have particular settings in the <processModel> section of the configuration file.
2. Modification of Global.asax or the Web.config file.(Some antivirus software will mark these two files as modified which may also cause recycle)
3. Modification of the bin directory of web application.

In many situations, this may not be the best way to manage user sessions. 

## StateServer mode
StateServer mode store session in a process which is seperate from w3wp.exe worker process or application pool. It ensures the session preserved if the web application restarted and also makse session state availiable to multiple web servers in a web farm.

To use StateServer mode, the first thing we need to ensure is that state service is running on the server used to store session. ASP.NET state service is installed in systemroot\Microsoft.NET\Framework\versionNumber\aspnet_state.exe, or you could use UI to start the service.
1. Press Win + R to call out the Run dialog box. Type services.msc and click OK.
![run services.msc](https://lh3.googleusercontent.com/xn0So5qvosJ2f_EaI7bZJpwZVzxJ4BKPPiPGljXTsp4=w472-h278-no)

2. Find the service named **ASP.NET State Service** and check the status make sure it is **started**
![ASP.NET State Service](https://lh3.googleusercontent.com/jn2J9aFLU81QwgveEzatZSOoSEmWMD-ovWo8Gmsidus=w761-h225-no)

3. If not double click the service and change startup type to **Automatic** and click **Start**
![Start State Service](https://lh3.googleusercontent.com/O8yd78jqnTR5TO3NK4aEs05Bps2sneQdvAW43FlOsy4=w554-h578-no)

After make sure ASP.NET State Service is started, add following configuration to web.config
```xml
<configuration>
  <system.web>
    <sessionState mode="StateServer"
      stateConnectionString="tcpip=SampleStateServer:42424"
      cookieless="false"
      timeout="20"/>
  </system.web>
</configuration>
```
Change the SampleStateServer to the ip of your state server.

##SQL Server Mode
SQLServer mode stores session state in SQL Server daatabase. 

To install the session state database on SQL Server, run the Aspnet_regsql.exe tool located in the systemroot\Microsoft.NET\Framework\versionNumber folder on your Web server. Supply the following information with the command:
1. The name of the SQL Server instance, using the -S option.
2. The logon credentials for an account that has permission to create a database on SQL Server. Use the -E option to use the currently logged-on user, or use the -U option to specify a user ID along with the -P option to specify a password.
3. The -ssadd command-line option to add the session state database.

By default, you cannot use the Aspnet_regsql.exe tool to install the session state database on SQL Server Express. In order to run the Aspnet_regsql.exe tool to install a SQL Server Express database, you must first enable the Agent XPs SQL Server option using Transact-SQL commands like the following:
```sql
EXECUTE sp_configure 'show advanced options', 1
RECONFIGURE WITH OVERRIDE
GO

EXECUTE sp_configure 'Agent XPs', 1
RECONFIGURE WITH OVERRIDE
GO

EXECUTE sp_configure 'show advanced options', 0
RECONFIGURE WITH OVERRIDE
GO
```
You must run these Transact-SQL commands for any instance of SQL Server Express where the Agent XPs option is disabled.
By default, the Aspnet_regsql.exe tool will create a database named ASPState containing stored procedures that support SQLServer mode. Session data itself is stored in the tempdb database by default. You can optionally use the -sstype option to change the storage location of session data. The following table specifies the possible values for the -sstype option:

|Option | Description|
|:-----:|:----------:|
|t      |   Stores session data in the SQL Server tempdb database. This is the default. If you store session data in the tempdb database, the session data is lost if SQL Server is restarted.|
|p      |   Stores session data in the ASPState database instead of in the tempdb database.|
|c      |   Stores session data in a custom database. If you specify the c option, you must also include the name of the custom database using the -d option.|

For example, the following command creates a database named ASPState on a SQL Server instance named "SampleSqlServer" and specifies that session data is also stored in the ASPState database:
```sql
aspnet_regsql.exe -S SampleSqlServer -E -ssadd -sstype p
```

##Tips
One thing need to be careful is that Objects stored in session must be serializable if using this mode. The simple way is to add SerializableAttribute to the class like below.
```java
[Serializable()]
public class ObjectSaveToSession{
	/*
	some attribute or method here
	*/
}
```