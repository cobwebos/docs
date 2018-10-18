---
title: Azure Cosmos DB：批量执行程序 .NET API、SDK 和资源 | Microsoft Docs
description: 了解有关批量执行程序 .NET API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB 批量执行程序 .NET SDK 各版本之间所做的更改。
services: cosmos-db
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/14/2018
ms.author: ramkris
ms.openlocfilehash: ffd8f438429cd8769ac0dbff7f489327166e0000
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294452"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET 批量执行程序库：下载信息 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [异步 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [批量执行程序 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**说明**</td><td>批量执行程序库允许客户端应用程序在 Azure Cosmos DB 帐户中执行批量操作。 批量执行程序库提供 BulkImport、BulkUpdate 和 BulkDelete 命名空间。 BulkImport 模块可以批量以优化方式引入文档，以便最大程度地使用为集合配置的吞吐量。 BulkUpdate 模块可以作为修补程序批量更新 Azure Cosmos DB 容器中的现有数据。 BulkDelete 模块可以批量以优化方式删除文档，以便最大程度地使用为集合配置的吞吐量。</td></tr>

<tr><td>**SDK 下载**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**GitHub 中的 BulkExecutor 库**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**API 文档**</td><td>[.Net API 参考文档](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**入门**</td><td>[批量执行程序库 .NET SDK 入门](bulk-executor-dot-net.md)</td></tr>

<tr><td>**当前受支持的框架**</td><td><ul><li>[Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)（版本 >= 2.0.0）</li><li>
[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)（版本 >= 9.0.1）
</li></ul></td></tr>
</table></br>

## <a name="release-notes"></a>发行说明

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* 添加了对 Azure Cosmos DB SQL API 帐户的 BulkDelete 操作的支持。
* 添加了对 Azure Cosmos DB MongoDB API 帐户的 BulkImport 操作的支持。
* 增加了对版本 2.0.0 的 DocumentDB .NET SDK 依赖项。 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* 添加了对 Azure Cosmos DB Gremlin API 帐户的 BulkImport 操作的支持。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* 对 Azure Cosmos DB SQL API 帐户的 BulkImport 操作的小 bug 修复。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* 添加了对 Azure Cosmos DB SQL API 帐户的 BulkImport 和 BulkUpdate 操作的支持。
