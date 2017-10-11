1. 在登录[Azure 门户]。
2. 单击**+ 新建** > **Web + 移动** > **移动应用程序**，然后提供你的移动应用程序后端的名称。
3. 有关**资源组**，选择现有资源组，或创建一个新 （作为您的应用程序使用相同的名称）。 
   
    你可以选择另一个 App Service 计划，或创建一个新。 有关应用程序服务的详细计划以及如何创建一个新的计划不同的定价层，在所需的位置，请参阅[Azure App Service 计划深入概述](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。
4. 有关**App Service 计划**，默认的计划 (在[标准层](https://azure.microsoft.com/pricing/details/app-service/)) 选择。 你还可以选择另一个计划，或[创建一个新](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan)。 App Service 计划的设置将确定[位置，功能，成本和计算资源](https://azure.microsoft.com/pricing/details/app-service/)与您的应用程序相关联。 
   
    在决定了计划后，单击**创建**。 这将创建移动应用后端。 
5. 在**设置**边栏选项卡中为新的移动应用程序后端，单击**快速入门**> 你的客户端应用程序平台 >**连接数据库**。 
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
6. 在**添加数据连接**边栏选项卡，单击**SQL 数据库** > **创建新的数据库**，键入数据库**名称**，请选择定价层，然后单击**服务器**。  你可以重用此新数据库。 如果你已经有了数据库，在同一位置，则可以选择**使用现有数据库**。 由于带宽成本和更高的延迟，则不建议使用的不同位置中的数据库。
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
7. 在**新服务器**边栏选项卡，键入唯一的服务器名称中**服务器名称**字段中，提供登录名和密码，则检查**允许 azure 服务访问服务器**，单击**确定**。 这将创建新的数据库。
8. 返回**添加数据连接**边栏选项卡，单击**连接字符串**，键入你的数据库的登录名和密码值，单击**确定**。 等待几分钟才能在继续之前已成功部署该数据库。

<!-- URLs. -->
[Azure 门户]: https://portal.azure.com/
