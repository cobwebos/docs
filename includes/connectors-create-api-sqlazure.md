### <a name="prerequisites"></a>必备条件
* Azure 帐户;你可以创建[免费帐户](https://azure.microsoft.com/free)
* [Azure SQL 数据库](../articles/sql-database/sql-database-get-started.md)与其连接信息，包括服务器名称、 数据库名称和用户名/密码。 此信息包含在 SQL 数据库连接字符串：
  
    服务器 = tcp:*yoursqlservername*。 database.windows.net,1433;Initial 目录 =*yourqldbname*;持久性安全信息 = False;用户 ID = {your_username};密码 = {your_password};MultipleActiveResultSets = False;加密 = True;TrustServerCertificate = False;连接超时值 = 30;
  
    阅读更多有关[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database)。

> [!NOTE]
> 在创建 Azure SQL 数据库时，你还可以创建包含 SQL 的示例数据库。 
> 
> 

在使用之前你的 Azure SQL 数据库在逻辑应用程序中，连接到你的 SQL 数据库。 逻辑应用在 Azure 门户中可以轻松执行此操作。  

连接到 Azure SQL 数据库使用以下步骤：  

1. 创建一个逻辑应用。 Logic Apps 设计器中，添加了触发器，然后再添加操作。 选择**显示 Microsoft 托管 Api**在下拉列表，然后在搜索框中输入"sql"。 选择其中一项操作：  
   
    ![SQL Azure 连接创建步骤](./media/connectors-create-api-sqlazure/sql-actions.png)
2. 如果你以前尚未创建任何连接到 SQL Database，系统会提示有关连接详细信息：  
   
    ![SQL Azure 连接创建步骤](./media/connectors-create-api-sqlazure/connection-details.png) 
3. 输入 SQL 数据库详细信息。 有一个星号属性都是必需的。
   
   | 属性 | 详细信息 |
   | --- | --- |
   | 通过网关连接 |请取消选中。 它用于连接到本地 SQL Server。 |
   | 连接名称 * |输入任何名称用于你的连接。 |
   | SQL Server 名称 * |输入服务器名称;这是类似*servername.database.windows.net，则*。 服务器名称是显示在 Azure 门户中的 SQL 数据库属性，并且还显示连接字符串中。 |
   | SQL 数据库名称 * |输入为指定你的 SQL 数据库的名称。 这连接字符串中的 SQL 数据库属性中列出： 初始目录 =*yoursqldbname*。 |
   | 用户名 * |输入创建 SQL 数据库时创建的用户名。 这是在 Azure 门户中的 SQL 数据库属性中列出的。 |
   | 密码 * |输入创建 SQL 数据库时创建的密码。 |
   
    这些凭据用于授权逻辑应用程序连接，并访问你的 SQL 数据。 完成后，你连接的详细信息类似于以下内容：  
   
    ![SQL Azure 连接创建步骤](./media/connectors-create-api-sqlazure/sample-connection.png) 
4. 选择“创建”。 
5. 请注意创建连接。 现在，继续执行应用程序逻辑中的其他步骤： 
   
    ![SQL Azure 连接创建步骤](./media/connectors-create-api-sqlazure/table.png)

