---
title: "图形 API 的 Azure Cosmos DB 全局分发教程 | Microsoft Docs"
description: "了解如何使用图形 API 设置 Azure Cosmos DB 全局分发。"
services: cosmos-db
keywords: "全局分发, 图形, gremlin"
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.openlocfilehash: d05295f6b952209e6df88d9015153893fbbb9903
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>如何使用图形 API 设置 Azure Cosmos DB 全局分发

本文介绍了如何使用 Azure 门户设置 Azure Cosmos DB 全局分发，并使用图形 API 进行连接（预览版）。

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 Azure 门户配置全局分发
> * 使用[图形 API](graph-introduction.md) 配置全局分发（预览版）

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>通过 .NET SDK 使用图形 API 连接到首选区域

图形 API 在 DocumentDB SDK 上作为扩展库公开。

为了利用[全局分发](distribute-data-globally.md)，客户端应用程序可以指定要用于执行文档操作的区域优先顺序列表。 可通过设置连接策略来实现此目的。 SDK 会根据 Azure Cosmos DB 帐户配置、当前区域可用性和指定的优先顺序列表，选择最佳的终结点来执行写入和读取操作。

此优先顺序列表是在使用 SDK 初始化连接时指定的。 SDK 接受可选参数“PreferredLocations”，这是 Azure 区域的顺序列表。

* 写入：SDK 会自动将所有写入请求发送到当前写入区域。
* 读取：所有读取请求将发送到 PreferredLocations 列表中的第一个可用区域。 如果请求失败，客户端会将请求转发到列表中的下一个区域，依此类推。 SDK 只会尝试读取 PreferredLocations 中指定的区域。 例如，如果 Cosmos DB 帐户在三个区域中可用，但客户端只为 PreferredLocations 指定了两个非写入区域，那么，即使是在故障转移时，也不会从写入区域为读取提供服务。

应用程序可以通过检查 SDK 1.8 和更高版本中提供的两个属性 - WriteEndpoint 和 ReadEndpoint - 来验证 SDK 选择的当前写入终结点和读取终结点。 如果未设置 PreferredLocations 属性，则会从当前写入区域为所有请求提供服务。

### <a name="using-the-sdk"></a>使用 SDK

例如，在 .NET SDK 中，`DocumentClient` 构造函数的 `ConnectionPolicy` 参数包括一个名为 `PreferredLocations` 的属性。 可以将此属性设置为区域名称的列表。 可以将 [Azure 区域][regions]的显示名称指定为 `PreferredLocations` 的一部分。

> [!NOTE]
> 不应将终结点 URL 视为长期不变的常量。 服务随时会更新这些 URL。 SDK 会自动处理这种更改。
>
>

```cs
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

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

本教程到此结束。 阅读 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)，了解如何管理全局复制帐户的一致性。 若要深入了解 Azure Cosmos DB 中全局数据库复制的工作原理，请参阅[使用 Azure Cosmos DB 全局分发数据](distribute-data-globally.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 使用 Azure 门户配置全局分发
> * 使用 DocumentDB API 配置全局分发

现在可以继续学习下一个教程，了解如何使用 Azure Cosmos DB 本地模拟器在本地开发。

> [!div class="nextstepaction"]
> [通过模拟器在本地开发](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

