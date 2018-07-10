---
title: Azure Cosmos DB：使用 .NET 和 MongoDB API 生成 Xamarin.Forms 应用 | Microsoft Docs
description: 演示一个可以用来连接和查询 Azure Cosmos DB MongoDB API 的 Xamarin 代码示例
services: cosmos-db
author: codemillmatt
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quickstart, xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: masoucou
ms.openlocfilehash: 42e2a7f57cbe40dbef8469e4e9ca020f4346a897
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114947"
---
# <a name="quickstart-build-a-mongodb-api-xamarinforms-app-with-net-and-the-azure-portal"></a>快速入门：使用 .NET 和 Azure 门户生成 MongoDB API Xamarin.Forms 应用

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，所有这些都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。

本快速入门教程演示如何使用 Azure 门户创建 Azure Cosmos DB [MongoDB API](mongodb-introduction.md) 帐户、文档数据库和集合。 然后，使用 [MongoDB.NET 驱动程序](https://docs.mongodb.com/ecosystem/drivers/csharp/)生成 Xamarin.Forms 待办事项应用。

## <a name="prerequisites-to-run-the-sample-app"></a>运行示例应用的先决条件

若要运行该示例，需要 [Visual Studio](https://www.visualstudio.com/downloads/) 或 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) 和有效的 Azure CosmosDB 帐户。

如果没有 Visual Studio，请在使用安装程序安装**具有 .NET 的移动开发**后，下载 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。

如果想要使用 Mac，请下载 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) 并运行安装程序。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

本文中所述的示例与 MongoDB.Driver 版本 2.6.1 兼容。

## <a name="clone-the-sample-app"></a>克隆示例应用

首先，从 GitHub 下载示例 MongoDB API 应用。 这会实施一个包含 MongoDB 文档存储模型的待办事项应用。

1. 打开命令提示符，新建一个名为“git-samples”的文件夹，然后关闭命令提示符。

    ```bash
    md "C:\git-samples"
    ```

2. 打开诸如 git bash 之类的 git 终端窗口，并使用 `cd` 命令更改为要安装示例应用的新文件夹。

    ```bash
    cd "C:\git-samples"
    ```

3. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

如果不想使用 git，也可以[下载 ZIP 文件格式的项目](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何使用代码创建数据库资源，可以查看以下代码片段。 否则，可以直接跳转到[更新连接字符串](#update-your-connection-string)。

以下代码片段全部摘自 `MongoService` 类，可在以下路径找到：src/TaskList.Core/Services/MongoService.cs。

* 初始化 Mongo 客户端。
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    MongoClient mongoClient = new MongoClient(settings);
    ```

* 检索对数据库和集合的引用。 如果它们尚不存在，MongoDB.NET SDK 会自动创建该数据库和集合。
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* 以列表形式检索所有文档。
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* 查询特定文档。
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* 创建任务并将其插入到 MongoDB 集合。
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* 更新 MongoDB 集合中的任务。
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* 删除 MongoDB 集合中的任务。
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 在 [Azure 门户](http://portal.azure.com/)的 Azure Cosmos DB 帐户的左侧导航栏中，单击“连接字符串”，并单击“读写密钥”。 在后续步骤中，将使用屏幕右侧的复制按钮复制“主连接字符串”。

2. 打开 **TaskList.Core** 项目的 **Helpers** 目录中的 **APIKeys.cs** 文件。

3. 从门户复制“主连接字符串”值（使用复制按钮），并将其设置为 **APIKeys.cs** 文件中 **ConnectionString** 字段的值。

现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。

## <a name="run-the-app"></a>运行应用

### <a name="visual-studio-2017"></a>Visual Studio 2017

1. 在 Visual Studio 中，右键单击解决方案资源管理器中的每个项目，并单击“管理 NuGet 包”。
2. 单击“还原所有 NuGet 包”。
3. 右键单击“TaskList.Android”，并选择“设为启动项目”。
4. 按 F5 开始调试应用程序。
5. 如果想要在 iOS 上运行，首先请将计算机连接到 Mac（参阅操作[说明](https://docs.microsoft.com/en-us/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio)）。
6. 右键单击“TaskList.iOS”项目，并选择“设为启动项目”。
7. 单击 F5 开始调试应用程序。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 在平台下拉列表中，根据使用的平台选择“TaskList.iOS”或“TaskList.Android”。
2. 按 cmd+Enter 开始调试应用程序。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何创建 Azure Cosmos DB 帐户和使用 API for MongoDB 运行 Xamarin.Forms 应用。 现在可以将其他数据导入 Cosmos DB 帐户。

> [!div class="nextstepaction"]
> [将 MongoDB API 的数据导入 Azure Cosmos DB](mongodb-migrate.md)