# various scripts for sql server
1. [sp_interpolate_message](https://github.com/iscenigmax/scripts-sqlserver/blob/master/sp_interpolate_message.sql): 
   * __description:__ script to interpolate a text message with a database query
   * __result:__ Today 2020-05-31 a query interpolation process was carried out with a text message on the server MYSERVER with version Microsoft SQL Server 2017

```sql
DECLARE @MESSAGE VARCHAR(MAX), @MESSAGE_QUERY VARCHAR(MAX)

SET @MESSAGE = N'Today {1} a query interpolation process was carried out with a text message on the server {2} with version {3}'
SET @MESSAGE_QUERY = N'SELECT CONVERT(VARCHAR(10),GETDATE(),121) [1], @@SERVERNAME [2], @@VERSION [3] INTO ##T_INTERPOLATE'

EXEC sp_interpolate_message @MESSAGE OUTPUT, @MESSAGE_QUERY
```
