
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>从 Azure 门户中获取连接字符串
使用[Azure 门户](https://portal.azure.com/)来获得客户端程序与 Azure SQL 数据库交互所需的连接字符串： 

1. 单击**浏览** > **SQL 数据库**。
2. 在左上角附近的筛选器文本框中输入你的数据库的名称**SQL 数据库**边栏选项卡。
3. 单击你的数据库的行。
4. 边栏选项卡显示有关你的数据库后，为了观看方便你可以单击标准的最小化控件，以便折叠用于浏览和数据库筛选边栏选项卡。 
   
    ![筛选器以隔离你的数据库][10-FilterDatabase]
5. 在你的数据库的边栏选项卡，单击**显示数据库连接字符串**。
6. 如果你想要使用 ADO.NET 连接库，将复制的字符串标记为**ADO**。 
   
    ![复制你的数据库的 ADO 连接字符串][20-CopyAdoConnectionString]
7. 在一种格式或另一个中，将粘贴到客户端程序代码中的连接字符串信息。

有关详情，请参阅：<br/>[连接字符串和配置文件](http://msdn.microsoft.com/library/ms254494.aspx)。

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
