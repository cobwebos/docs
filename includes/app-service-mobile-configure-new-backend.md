
1. 单击“应用服务”按钮，然后依次选择移动应用后端、“快速入门”、客户端平台（iOS、Android、Xamarin、Cordova）。

    ![突出显示移动应用快速启动的 Azure 门户][quickstart]

2. 如果未配置数据库连接，请创建一个，方法是执行以下操作：

    ![Azure 门户：移动应用：连接到数据库][connect]

    a. 新建 SQL 数据库和服务器。

    ![Azure 门户：移动应用：新建数据库和服务器][server]

    b. 等到成功创建数据连接。

    ![成功创建数据连接的 Azure 门户通知][notification]

    c. 数据连接必须成功。

    ![Azure 门户通知“你已经有一个数据连接”][already-connection]

3. 在“2.创建表 API”下，为“后端语言”选择“Node.js”。 
 
4. 接受确认，然后选择“创建 TodoItem 表”。  
    此操作会在数据库中创建一个新的代办事项表。 

    >[!IMPORTANT]
    > 将现有的后端切换到 Node.js 会覆盖所有内容。 若改为创建 .NET 后端，请参阅[使用适用于移动应用的 .NET 后端服务器][instructions]。

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
