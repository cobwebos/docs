---
title: 快速入门 - 通过 Azure Cosmos DB 使用 Java 创建文档数据库
description: 本快速入门演示一个可以用来连接到 Azure Cosmos DB SQL API 并进行查询的 Java 代码示例
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: a4a8990b3da534acb39ff87c9f7665fb3b08ef06
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708163"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>快速入门：生成 Java 应用以管理 Azure Cosmos DB SQL API 数据


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

本快速入门介绍如何使用 Java 应用程序创建和管理 Azure Cosmos DB SQL API 帐户的文档数据库。 首先，请使用 Azure 门户创建 Azure Cosmos DB SQL API 帐户，使用 SQL Java SDK 创建 Java 应用，然后使用 Java 应用程序将资源添加到 Cosmos DB 帐户。 本快速入门中的说明适用于任何能够运行 Java 的操作系统。 完成本快速入门以后，你就会熟悉如何通过 UI 或编程方式（以首选方式为准）创建和修改 Cosmos DB 数据库和容器。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

此外： 

* [Java 开发工具包 (JDK) 版本 8](https://aka.ms/azure-jdks)
    * 请确保设置 JAVA_HOME 环境变量，使之指向在其中安装了 JDK 的文件夹。
* [下载](https://maven.apache.org/download.cgi)和[安装](https://maven.apache.org/install.html) [Maven](https://maven.apache.org/) 二进制存档
    * 在 Ubuntu 上，可以通过运行 `apt-get install maven` 来安装 Maven。
* [Git](https://www.git-scm.com/)
    * 在 Ubuntu 上，可以通过运行 `sudo apt-get install git` 来安装 Git。

## <a name="create-a-database-account"></a>创建数据库帐户

在创建文档数据库之前，需通过 Azure Cosmos DB 创建 SQL API 帐户。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>添加容器

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>添加示例数据

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>查询数据

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，让我们转到如何使用代码上来。 接下来，克隆 GitHub 中的 SQL API 应用程序，设置连接字符串，并运行应用程序。 会看到以编程方式处理数据是多么容易。 

1. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
    ```

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何使用代码创建数据库资源，可以查看以下代码片段。 否则，可以跳到[运行应用](#run-the-app)。 

* `CosmosClient` 初始化。 `CosmosClient` 为 Azure Cosmos 数据库服务提供客户端逻辑表示形式。 此客户端用于对服务配置和执行请求。
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* 创建 CosmosDatabase。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* 创建 CosmosContainer。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* 使用 `createItem` 方法创建项。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* 使用 `getItem` 和 `read` 方法执行点读取

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* 使用 `queryItems` 方法对 JSON 执行 SQL 查询。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>运行应用

现在返回到 Azure 门户，获取连接字符串信息，并使用终结点信息启动应用。 这样，应用程序就可以与托管的数据库进行通信。


1. 在 git 终端窗口中，通过 `cd` 转至示例代码文件夹。

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. 在 git 终端窗口中，使用以下命令安装所需的 Java 包。

    ```bash
    mvn package
    ```

3. 在 git 终端窗口中，使用以下命令启动 Java 应用程序（将 YOUR_COSMOS_DB_HOSTNAME 替换为门户中带引号的 URI 值，将 YOUR_COSMOS_DB_MASTER_KEY 替换为门户中带引号的主键）

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    此时，终端窗口显示通知，提示 FamilyDB 数据库已创建。 
    
4. 该应用创建名为 `AzureSampleFamilyDB` 的数据库
5. 该应用创建名为 `FamilyContainer` 的容器
6. 该应用使用对象 ID 和分区键值（在本示例中为 lastName）执行点读取。 
7. 该应用将查询项，以检索姓氏中包含 ('Andersen', 'Wakefield', 'Johnson') 的所有家庭

7. 应用不删除创建的资源。 切换回门户，以便在帐户中[清理资源](#clean-up-resources)，  以免产生费用。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用数据资源管理器创建 Azure Cosmos 帐户、文档数据库和容器，以及如何通过运行应用以编程方式执行同一操作。 现在可以将其他数据导入 Azure Cosmos 容器。 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)
