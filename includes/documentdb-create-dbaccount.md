1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在跳转栏中，依次单击“新建”、“数据库”、“NoSQL (DocumentDB)”。
   
   ![Azure 门户的屏幕截图，突出显示更多服务和 DocumentDB (NoSQL)](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  
3. 在“新建帐户”  边栏选项卡中，为 DocumentDB 帐户指定所需的配置。
   
    ![“新建 DocumentDB”边栏选项卡的屏幕截图](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)
   
   * 在“ID”框中输入一个名称，用于标识 DocumentDB 帐户。  对“ID”进行验证后，“ID”框中会出现一个绿色的复选标记。  该“ID”值将成为 URI 中的主机名。  “ID”只能包含小写字母、数字及“-”字符，且长度必须为 3 到 50 个字符。 请注意，*documents.azure.com* 会追加到所选终结点名称后面，其结果将成为 DocumentDB 帐户终结点。
   * 在“NoSQL API”框中，选择“DocumentDB”。  
   * 对于“订阅”，请选择要用于 DocumentDB 帐户的 Azure 订阅 。 如果帐户只有一个订阅，则默认为选择该帐户。
   * 在“资源组” 中，为 DocumentDB 帐户选择或创建资源组。  默认创建新的资源组。 有关详细信息，请参阅 [使用 Azure 门户管理 Azure 资源](../articles/azure-portal/resource-group-portal.md)。
   * 使用“位置”指定在其中托管 DocumentDB 帐户的地理位置。 
4. 配置了新的 DocumentDB 帐户后，单击“创建” 。 若要检查部署状态，请检查通知中心。  
   
   ![快速创建数据库 — 通知中心的屏幕截图，其中显示正在创建 DocumentDB 帐户](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  
   
   ![通知中心的屏幕截图，其中显示 DocumentDB 帐户已成功创建并且部署到资源组 — 在线数据库创建者通知](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)
5. 在创建 DocumentDB 帐户后，可随时将其与默认设置配合使用。 若要查看默认设置，请在跳转栏中单击“NoSQL (DocumentDB)”图标，单击你的新帐户，然后在资源菜单中单击“默认一致性”。

   ![显示如何在 Azure 门户中打开 Azure DocumentDB 数据库帐户的屏幕截图](./media/documentdb-create-dbaccount/azure-documentdb-database-open-account-portal.png)  

   DocumentDB 帐户的默认一致性设置为“会话” 。  可单击资源菜单中的“默认一致性”  调整默认一致性。 若要了解有关 DocumentDB 提供的一致性级别的详细信息，请参阅 [DocumentDB 中的一致性级别](../articles/documentdb/documentdb-consistency-levels.md)。

[如何：创建 DocumentDB 帐户]: #Howto
[后续步骤]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md


<!--HONumber=Nov16_HO2-->


