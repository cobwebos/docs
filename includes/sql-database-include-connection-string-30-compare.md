
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>比较连接字符串
下表针对你的 C# 程序连接到本地 SQL Server 所需的连接字符串，与连接到云中的 Azure SQL 数据库所需的连接字符串进行了比较。 差异以粗体显示。

| 连接字符串 -<br/>Azure SQL 数据库 | 连接字符串 -<br/>Microsoft SQL Server |
|:--- |:--- |
| Server=**tcp:**{your_serverName_here}**.database.windows.net,1433**;<br/>User ID={your_loginName_here}**@{your_serverName_here}**;<br/>Password={your_password_here};<br/>**Database={your_databaseName_here};**<br/>**Connection Timeout=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; |Server={your_serverName_here};<br/>User ID={your_loginName_here};<br/>Password={your_password_here}; |

**Database=** 对于 SQL Server 是可选的，但对于 SQL 数据库是必需的。

[.NET ADO SqlConnectionStringBuilder 属性](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) - 详细讨论所有参数。

<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Jan17_HO3-->


