<properties 
	pageTitle="使用非关系数据存储构建服务 - Azure 移动服务" 
	description="了解如何使用非关系数据存储（如 MongoDB 或 Azure 表存储）用于基于 .NET 的移动服务" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.date="04/24/2015" 
	wacn.date=""/>

# 使用作为数据存储的 MongoDB 和 .NET 后端构建服务

本主题说明如何将非关系数据存储用于移动服务。在本教程中，你将要修改移动服务快速入门项目，以使用 MongoDB 而不是 SQL 作为数据存储。

只有在完成[移动服务入门]或[将移动服务添加到现有应用程序]教程之后，才可以完成本教程。

## <a name="create-store"></a>创建非关系存储

1. 在 [Azure 管理门户]中，单击“新建”，然后选择“应用商店”。

2. 选择“MongoLab”外接程序，然后在向导中导航以注册一个帐户。有关 MongoLab 的详细信息，请参阅 [MongoLab 外接程序页]。

2. 设置帐户后，选择“连接信息”并复制连接字符串。

3. 导航到门户的“移动服务”部分并选择“配置”选项卡。

4. 在“应用程序设置”下，输入包含键 `MongoConnectionString` 的连接字符串，然后单击“保存”。

    ![][1]

2. 将以下代码添加到 `TodoItemController`：

        static bool connectionStringInitialized = false;

        private void InitializeConnectionString(string connectionStringName)
        {
            if (!connectionStringInitialized)
            {
                connectionStringInitialized = true;
                if (!this.Services.Settings.Connections.ContainsKey(connectionStringName))
                {
                    var connectionString = this.Services.Settings[connectionStringName];
                    var connectionSetting = new ConnectionSettings(connectionStringName, connectionString);
                    this.Services.Settings.Connections.Add(connectionStringName, connectionSetting);
                }
            }
        }
    
    此代码将加载应用程序设置，并告知移动服务要将此设置将为可由 `TableController` 使用的连接。稍后，你将在调用 `TodoItemController` 时调用此方法。



## <a name="modify-service"></a>修改数据和控制器

1. 安装 **WindowsAzure.MobileServices.Backend.Mongo** NuGet 包。

2. 将 `TodoItem` 修改为从 `DocumentData` 而不是 `EntityData` 派生。

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. 在 `TodoItemController` 中，将 `Initialize` 方法替换为以下内容：

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4. 在上述 `Initialize` 方法的代码中，将 **YOUR-DATABASE-NAME** 替换为你在设置 MongoLab 外接程序时选择的名称。


## <a name="test-application"></a>测试应用程序

1. 重新发布移动服务后端项目。

2. 运行客户端应用程序。请注意，你将看不到前面从快速入门教程存储在 SQL 数据库中的任何项。

3. 创建新项。应用程序应该会像以前一样工作，不过，现在你的数据会存储到非关系存储中。


<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[移动服务入门]: mobile-services-dotnet-backend-windows-store-dotnet-get-started
[将移动服务添加到现有应用程序]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Azure 管理门户]: https://manage.windowsazure.cn/
[What is the Table Service]: storage-dotnet-how-to-use-tables.md#what-is
[MongoLab 外接程序页]: /zh-cn/gallery/store/mongolab/mongolab

<!---HONumber=61-->