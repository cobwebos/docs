### <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>在 Azure 门户中创建新的逻辑 SQL 服务器

1. 单击**新建**，搜索**逻辑服务器**，然后按**ENTER**。

    ![搜索逻辑服务器](./media/sql-data-warehouse-create-logical-server/search-logical-server.png)
2. 选择**SQL server （逻辑服务器）** 

    ![选择逻辑服务器](./media/sql-data-warehouse-create-logical-server/select-logical-server.png)
  
3. 单击**创建**以打开新的 SQL Server （逻辑服务器） 边栏选项卡。

   <kbd>![打开逻辑服务器边栏选项卡](./media/sql-data-warehouse-create-logical-server/open-logical-server-blade.png) </kbd> <kbd>![逻辑服务器边栏选项卡](./media/sql-data-warehouse-create-logical-server/logical-server-blade.png)</kbd>
  
3. 在 SQL Server （逻辑服务器） 边栏选项卡的服务器名称文本框中，提供新的逻辑服务器的有效名称。 绿色的选中标记指示你提供有效的名称。
    
    ![新的服务器名称](./media/sql-data-warehouse-create-logical-server/new-name-logical-server.png)

    > [!IMPORTANT]
    > 为新服务器的完全限定的名称将为 < your_server_name >。 database.windows.net。
    >
    
4. 在服务器管理员的登录名文本框中，为此服务器提供 SQL 身份验证登录名的用户名称。 此登录名被称为服务器主体登录名。 绿色的选中标记指示你提供有效的名称。
    
    ![SQL 管理员登录名](./media/sql-data-warehouse-create-logical-server/sql-admin-login.png)
5. 在**密码**和**确认密码**文本框中，提供密码的服务器主体的登录帐户。 绿色的选中标记指示你提供一个有效的密码。
    
    ![SQL 管理员密码](./media/sql-data-warehouse-create-logical-server/sql-admin-password.png)
6. 选择在其中您有权创建对象的订阅。

    ![订阅](./media/sql-data-warehouse-create-logical-server/subscription.png)
7. 在资源组文本框中，选择**新建**然后，在资源组文本框中，提供新的资源组 （你也可以使用现有资源组如果你已创建您自己） 的有效名称。 绿色的选中标记指示你提供有效的名称。

    ![新的资源组](./media/sql-data-warehouse-create-logical-server/new-resource-group.png)

8. 在**位置**文本框中，选择一个数据中心适用于你的位置-例如"澳大利亚东部"。
    
    ![服务器位置](./media/sql-data-warehouse-create-logical-server/server-location.png)
    
    > [!TIP]
    > 复选框**允许 azure 服务访问服务器**无法在此边栏选项卡上更改。 你可以更改此设置在服务器防火墙边栏选项卡。 有关详细信息，请参阅[入门安全](../articles/sql-database/sql-database-manage-servers-portal.md)。
    >
    
9. 单击“创建”。

    ![创建按钮](./media/sql-data-warehouse-create-logical-server/create.png)

