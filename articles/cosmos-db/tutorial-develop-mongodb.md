---
title: "使用 Azure Cosmos DB 的适用于 MongoDB 的 API 生成 Web 应用 | Microsoft Docs"
description: "使用适用于 MongoDB 的 API 创建联机数据库 Web 应用的 Azure Cosmos DB 教程。"
keywords: "mongodb 示例"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: anhoh
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 47ec9998c68455a90b505c917156d9b6b0fe0f24
ms.contentlocale: zh-cn
ms.lasthandoff: 06/03/2017

---
# <a name="azure-cosmos-db-connect-to-a-mongodb-app-using-net"></a>Azure Cosmos DB：使用 .NET 连接到 MongoDB 应用

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值，及图形数据库，它们均受益于 Azure Cosmos DB 最核心的全球分布和水平缩放功能。 

本教程演示如何使用 Azure 门户创建 Azure Cosmos DB 帐户，以及如何使用 [MongoDB API](mongodb-introduction.md) 创建用于存储数据的数据库和集合。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 创建 Azure Cosmos DB 帐户 
> * 更新连接字符串
> * 在虚拟机上创建 MongoDB 应用 


## <a name="create-a-database-account"></a>创建数据库帐户

现在首先在 Azure 门户中创建 Azure Cosmos DB 帐户。  

> [!TIP]
> * 已有一个 Azure Cosmos DB 帐户？ 如果有，请跳到[设置 Visual Studio 解决方案](#SetupVS)
> * 是否具有 Azure DocumentDB 帐户？ 如果有，则该帐户现为 Azure Cosmos DB 帐户，你可以直接跳到[设置 Visual Studio 解决方案](#SetupVS)。  
> * 如果使用 Azure Cosmos DB Emulator，请遵循 [Azure Cosmos DB Emulator](local-emulator.md) 中的步骤设置该模拟器，然后直接跳到[设置 Visual Studio 解决方案](#SetupVS)。 
>
>

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>更新连接字符串

1. 在 Azure 门户的“Azure Cosmos DB”页面中，选择用于 MongoDB 帐户的 API。 
2. 在“帐户”边栏选项卡的左侧，单击“快速启动”。 
3. 选择平台（*.NET 驱动程序*、*Node.js 驱动程序*、*MongoDB Shell*、*Java 驱动程序*、*Python 驱动程序*）。 如果未列出驱动程序或工具，别担心，我们持续记录了更多连接代码片段。 
4. 将代码片段复制粘贴到 MongoDB 应用，一切准备就绪。

## <a name="set-up-your-mongodb-app"></a>设置 MongoDB 应用

可以参考[在 Azure 中创建连接到虚拟机上运行的 MongoDB 的 Web 应用](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md)教程（需进行少量修改），快速设置一个连接到 MongoDB 帐户的 API 的 MongoDB 应用程序（在本地或发布到 Azure Web 应用）。  

1. 请遵循该教程，不过需要做出一项修改。  将 Dal.cs 代码替换为以下内容：

    ```csharp   
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    using System.Security.Authentication;
   
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            //private MongoServer mongoServer = null;
            private bool disposed = false;
   
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net
            private string connectionString = "mongodb://localhost:27017";
            private string userName = "<your user name>";
            private string host = "<your host>";
            private string password = "<your password>";
   
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
   
            // Default constructor.        
            public Dal()
            {
            }
   
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
   
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
   
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
   
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            # region IDisposable
   
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
   
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                    }
                }
   
                this.disposed = true;
            }
   
            # endregion
        }
    }
    ```

2. 从 Azure 门户的“密钥”页中，根据帐户设置在 Dal.cs 文件中修改以下变量：

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. 应用可供使用！

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请使用以下步骤删除本教程在 Azure 门户中创建的所有资源。 

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 创建 Azure Cosmos DB 帐户 
> * 更新连接字符串
> * 在虚拟机上创建 MongoDB 应用

可以继续学习下一教程，将 MongoDB 数据导入到 Azure Cosmos DB。  

> [!div class="nextstepaction"]
> [将 MongoDB 数据导入 Azure Cosmos DB](mongodb-migrate.md)


