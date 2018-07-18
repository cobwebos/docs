---
title: SQL API 的 Azure Cosmos DB 全局分发教程 | Microsoft Docs
description: 了解如何使用 SQL API 设置 Azure Cosmos DB 全局分发。
services: cosmos-db
keywords: 全局分发
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: 1d0a660845052f51af84b78584787760e01f16a6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38542640"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>使用 SQL API 设置 Azure Cosmos DB 全局分发

本文介绍了如何使用 Azure 门户设置 Azure Cosmos DB 全局分发，并使用 SQL API 进行连接。

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 Azure 门户配置全局分发
> * 使用 [SQL API](sql-api-introduction.md) 配置全局分发

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>使用 SQL API 连接到首选区域

为了利用[全局分发](distribute-data-globally.md)，客户端应用程序可以指定要用于执行文档操作的区域优先顺序列表。 可通过设置连接策略来实现此目的。 SQL SDK 会根据 Azure Cosmos DB 帐户配置、当前区域可用性和指定的优先顺序列表，选择最佳的终结点来执行写入和读取操作。

此优先顺序列表是在使用 SQL SDK 初始化连接时指定的。 SDK 接受可选参数“PreferredLocations”，这是 Azure 区域的顺序列表。

SDK 会自动将所有写入请求发送到当前写入区域。

所有读取请求将发送到 PreferredLocations 列表中的第一个可用区域。 如果请求失败，客户端会将请求转发到列表中的下一个区域，依此类推。

SDK 只会尝试读取 PreferredLocations 中指定的区域。 因此，例如，如果数据库帐户在四个区域中可用，但客户端只为 PreferredLocations 指定了两个读取（非写入）区域，那么将不会从 PreferredLocations 中未指定的读取区域为读取提供服务。 如果 PreferredLocations 中指定的读取区域不可用，将从写入区域为读取提供服务。

应用程序可以通过检查 SDK 1.8 和更高版本中提供的两个属性 - WriteEndpoint 和 ReadEndpoint - 来验证 SDK 选择的当前写入终结点和读取终结点。

如果未设置 PreferredLocations 属性，则会从当前写入区域为所有请求提供服务。

## <a name="net-sdk"></a>.NET SDK
无需进行任何代码更改即可使用该 SDK。 在此情况下，SDK 会自动将读取和写入请求定向到当前写入区域。

在 .NET SDK 1.8 和更高版本中，DocumentClient 构造函数的 ConnectionPolicy 参数有一个名为 Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations 的属性。 此属性的类型为 Collection `<string>`，应包含区域名称的列表。 字符串值已根据 [Azure 区域][regions]页上的“区域名称”列设置格式，其第一个字符的前面和最后一个字符的后面均没有空格。

当前写入终结点和读取终结点分别在 DocumentClient.WriteEndpoint 和 DocumentClient.ReadEndpoint 中提供。

> [!NOTE]
> 不应将终结点 URL 视为长期不变的常量。 服务随时会更新这些 URL。 SDK 会自动处理这种更改。
>
>

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS、JavaScript 和 Python SDK
无需进行任何代码更改即可使用该 SDK。 在此情况下，SDK 会自动将读取和写入请求定向到当前写入区域。

在每个 SDK 的 1.8 和更高版本中，DocumentClient 构造函数的 ConnectionPolicy 参数有一个名为 DocumentClient.ConnectionPolicy.PreferredLocations 的新属性。 此参数是采用区域名称列表的字符串数组。 名称已根据 [Azure 区域][regions]页中的“区域名称”列设置格式。 也可以在便捷对象 AzureDocuments.Regions 中使用预定义的常量

当前写入终结点和读取终结点分别在 DocumentClient.getWriteEndpoint 和 DocumentClient.getReadEndpoint 中提供。

> [!NOTE]
> 不应将终结点 URL 视为长期不变的常量。 服务随时会更新这些 URL。 SDK 会自动处理这种更改。
>
>

下面是 NodeJS/Javascript 的代码示例。 Python 和 Java 将遵循相同的模式。

```java
// Creating a ConnectionPolicy object
var connectionPolicy = new DocumentBase.ConnectionPolicy();

// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);
```

## <a name="rest"></a>REST
数据库帐户在多个区域中可用后，客户端可以通过对以下 URI 执行 GET 请求来查询该帐户的可用性。

    https://{databaseaccount}.documents.azure.com/

服务将返回副本的区域及其对应 Azure Cosmos DB 终结点 URI 的列表。 当前写入区域会在响应中指示。 然后，客户端可为所有其他 REST API 请求选择适当的终结点，如下所示。

示例响应

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* 所有 PUT、POST 和 DELETE 请求必须转到指示的写入 URI
* 所有 GET 和其他只读请求（例如查询）可以转到客户端选择的任何终结点

向只读区域发出的写入请求会失败并出现 HTTP 错误代码 403（“禁止”）。

如果在客户端初始发现阶段过后写入区域发生更改，则向先前写入区域进行的后续写入会失败并出现 HTTP 错误代码 403（“禁止”）。 然后，客户端应再次对区域列表执行 GET 以获取更新的写入区域。

本教程到此结束。 阅读 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)，了解如何管理全局复制帐户的一致性。 若要深入了解 Azure Cosmos DB 中全局数据库复制的工作原理，请参阅[使用 Azure Cosmos DB 全局分发数据](distribute-data-globally.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 使用 Azure 门户配置全局分发
> * 使用 SQL API 配置全局分发

现在可以继续学习下一个教程，了解如何使用 Azure Cosmos DB 本地模拟器在本地开发。

> [!div class="nextstepaction"]
> [通过模拟器在本地开发](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

