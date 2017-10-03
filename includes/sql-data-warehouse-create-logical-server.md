### <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>在 Azure 门户中创建新的 SQL 逻辑服务器

1. 单击“新建”，搜索“逻辑服务器”，并按 **ENTER**。

    ![搜索逻辑服务器](./media/sql-data-warehouse-create-logical-server/search-logical-server.png)
2. 选择“SQL 服务器(逻辑服务器)”。 

    ![选择逻辑服务器](./media/sql-data-warehouse-create-logical-server/select-logical-server.png)
  
3. 单击“创建”打开“新建 SQL 服务器(逻辑服务器)”边栏选项卡。

   <kbd>![打开逻辑服务器边栏选项卡](./media/sql-data-warehouse-create-logical-server/open-logical-server-blade.png)</kbd><kbd>![逻辑服务器边栏选项卡](./media/sql-data-warehouse-create-logical-server/logical-server-blade.png)</kbd>
  
3. 在“SQL 服务器(逻辑服务器)”边栏选项卡的服务器名称文本框中，提供新逻辑服务器的有效名称。 绿色的对勾表示已提供有效名称。
    
    ![新服务器名称](./media/sql-data-warehouse-create-logical-server/new-name-logical-server.png)

    > [!IMPORTANT]
    > 新服务器的完全限定名称为 <服务器名称>.database.windows.net。
    >
    
4. 在“服务器管理员登录名”文本框中，提供此服务器的 SQL 身份验证登录名的用户名。 此登录名称为服务器主体登录名。 绿色的对勾表示已提供有效名称。
    
    ![SQL 管理员登录名](./media/sql-data-warehouse-create-logical-server/sql-admin-login.png)
5. 在“密码”和“确认密码”文本框中，提供服务器主体登录帐户的密码。 绿色复选标记表示提供的密码有效。
    
    ![SQL 管理员密码](./media/sql-data-warehouse-create-logical-server/sql-admin-password.png)
6. 选择一个有权在其中创建对象的订阅。

    ![订阅](./media/sql-data-warehouse-create-logical-server/subscription.png)
7. 在“资源组”文本框中选择“新建”，并在“资源组”文本框中提供新资源组的有效名称（如果已自行创建了一个资源组，则也可以使用该资源组）。 绿色的对勾表示已提供有效名称。

    ![新建资源组](./media/sql-data-warehouse-create-logical-server/new-resource-group.png)

8. 在“位置”文本框中，选择适合所在位置的数据中心，如“澳大利亚东部”。
    
    ![服务器位置](./media/sql-data-warehouse-create-logical-server/server-location.png)
    
    > [!TIP]
    > 无法在此边栏选项卡中更改“允许 Azure 服务访问服务器”复选框的状态。 可以在服务器防火墙边栏选项卡中更改此设置。 有关详细信息，请参阅 [Get started with security](../articles/sql-database/sql-database-manage-servers-portal.md)（安全性入门）。
    >
    
9. 单击“创建” 。

    ![创建按钮](./media/sql-data-warehouse-create-logical-server/create.png)

