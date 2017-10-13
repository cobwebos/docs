### <a name="prerequisites"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 一个 [Azure SQL 数据库](../articles/sql-database/sql-database-get-started.md)及其连接信息（其中包括服务器名称、数据库名称和用户名/密码）。 该信息将包含在 SQL 数据库连接字符串中：
  
    Server=tcp:*yoursqlservername*.database.windows.net,1433;Initial Catalog=*yourqldbname*;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  
    详细了解 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database)。

> [!NOTE]
> 在创建 Azure SQL 数据库时，还可以使用 SQL 创建包含的示例数据库。 
> 
> 

在逻辑应用中使用 Azure SQL 数据库之前，请先连接到 SQL 数据库。 可以在 Azure 门户中的逻辑应用内轻松地执行此操作。  

按照以下步骤操作，即可连接到 Azure SQL 数据库：  

1. 创建逻辑应用。 在逻辑应用设计器中，添加触发器，并添加操作。 在下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“sql”。 选择其中一个操作：  
   
    ![SQL Azure 连接创建步骤](./media/connectors-create-api-sqlazure/sql-actions.png)
2. 如果以前尚未创建任何与 SQL 数据库的连接，系统会提示输入连接详细信息：  
   
    ![SQL Azure 连接创建步骤](./media/connectors-create-api-sqlazure/connection-details.png) 
3. 输入 SQL 数据库详细信息。 带有星号的属性必填。
   
   | 属性 | 详细信息 |
   | --- | --- |
   | 通过网关连接 |将该属性保留未选中状态。 在连接到本地 SQL Server 时，会使用该属性。 |
   | 连接名称 * |为连接输入任何名称。 |
   | SQL Server 名称 * |输入服务器名称；这有点类似于 *servername.database.windows.net*。 该服务器名称会显示在 Azure 门户中的 SQL 数据库属性中，也会显示在连接字符串中。 |
   | SQL 数据库名称 * |输入 SQL 数据库的名称。 该名称将列在连接字符串中的 SQL 数据库属性中：Initial Catalog=*yoursqldbname*。 |
   | 用户名 * |输入在创建 SQL 数据库时创建的用户名。 该用户名将列在 Azure 门户中的 SQL 数据库属性中。 |
   | 密码 * |输入在创建 SQL 数据库时创建的密码。 |
   
    这些凭据用于授权逻辑应用进行连接，然后访问 SQL 数据。 完成之后，连接详细信息会类似于下面这样：  
   
    ![SQL Azure 连接创建步骤](./media/connectors-create-api-sqlazure/sample-connection.png) 
4. 选择“创建” 。 
5. 请注意，连接已创建。 现在，继续在逻辑应用中执行其他步骤： 
   
    ![SQL Azure 连接创建步骤](./media/connectors-create-api-sqlazure/table.png)

