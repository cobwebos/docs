
1. 单击“应用服务”> 选择你的移动应用后端 > 单击“快速启动”> 你的客户端平台（iOS、Android、Xamarin、Cordova）。

![突出显示移动应用快速启动的 Azure 门户][quickstart]

2. 如果未配置数据库的连接，则必须创建一个数据连接。

![Azure 门户：移动应用：连接到 BD][connect]

  * 新建 SQL 数据库和服务器。

  ![Azure 门户：移动应用：新建 BD 和服务器][server]

  * 等到成功创建数据连接。

  ![Azure 门户：移动应用：有关创建数据连接的通知][notification]

  * 数据连接必须成功。

  ![Azure 门户：移动应用：有关创建数据连接的通知][already-connection]

3. 在“2.创建表 API”下，为“后端语言”选择“Node.js”。 接受确认，单击“创建 TodoItem 表” 。 这会在数据库中创建一个新的 *TodoItem* 表。 请注意，将现有后端切换到 Node.js 会覆盖所有内容！ 若要创建 .NET 后端，请[遵循这些说明][instructions]。

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
