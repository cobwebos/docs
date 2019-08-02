---
title: Azure Cosmos DB 表 API .NET Standard SDK 和资源
description: 了解有关 Azure Cosmos DB 表 API 和 .NET Standard SDK 的所有信息，包括发布日期、停用日期和各版本之间所做的更改。
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: b3ebd8b6f65ec86d338932579f5d4ccec6a90be3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704734"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB 表 .NET Standard API：下载和发行说明
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET 标准](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 下载**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**示例**|[Cosmos DB 表 API .NET 示例](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**快速入门**|[快速入门](create-table-dotnet.md)|
|**教程**|[教程](tutorial-develop-table-dotnet.md)|
|**当前受支持的框架**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>发行说明

### <a name="a-name104-preview104-preview"></a><a name="1.0.4-preview"/>1.0.4-预览
* Bug 修复
* 提供 RestExecutorConfiguration 的 HttpClientTimeout 选项。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Bug 修复

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 正式发布版

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-preview
* 对 CloudTableClient 的配置方式进行了更改。 它现在会在构造过程中使用 TableClientConfiguration 对象。 TableClientConfiguration 提供不同的属性来配置客户端行为，具体取决于目标终结点是 Cosmos DB 表 API 还是 Azure 存储表 API。
* 增加了对 TableQuery 的支持，可以在自定义列中按排序顺序返回结果。 只有 Cosmos DB 表终结点支持此功能。
* 增加了相关支持，可以在不同的结果类型上公开 RequestCharge。 只有 Cosmos DB 表终结点支持此功能。

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1 预览版
* 针对 Azure 存储表终结点添加了对 SAS 令牌以及 TablePermissions、ServiceProperties 和 ServiceStats 的操作的支持。 
   > [!NOTE]
   > 尚不支持以前的 Azure 存储表 SDK 中的某些功能，例如客户端加密。

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0 预览版
* 针对 Azure 存储表终结点添加了对核心 CRUD、批处理和查询操作的支持。 
   > [!NOTE]
   > 尚不支持以前的 Azure 存储表 SDK 中的某些功能，例如客户端加密。

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1 预览版
* Azure Cosmos DB 表 .NET Standard SDK 是一个跨平台 .NET 库，可高效访问 Cosmos DB 上的表数据模型。 此初始版本支持完整的表和实体 CRUD + 查询功能集，其中 API 与[用于 .NET Framework 的 Cosmos DB 表 SDK](table-sdk-dotnet.md) 类似。 
   > [!NOTE]
   >  0\.9.1 预览版尚不支持 Azure 存储表终结点。

## <a name="release-and-retirement-dates"></a>发布日期和停用日期
Microsoft 至少会在停用 SDK 前提前 12 个月发出通知，以便顺利转换为更高版本/受支持版本。

这种跨平台 .NET Standard 库将替换 .NET Framework 库[Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) 。) 将替换[CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)。

| Version | 发布日期 | 停用日期 |
| --- | --- | --- |
| [1.0.4-预览](#1.0.4-preview) |2019年7月26日 |--- |
| 1.0.2-预览版 |5月2日, 2019 |--- |
| [1.0.1](#1.0.1) |2019年4月19日 |--- |
| [1.0.0](#1.0.0) |2019 年 3 月 13 日 |--- |
| [0.11.0-preview](#0.11.0-preview) |2019 年 3 月 5 日 |--- |
| [0.10.1 预览版](#0.10.1-preview) |2019 年 1 月 22 日 |--- |
| [0.10.0 预览版](#0.10.0-preview) |2018 年 12 月 18 日 |--- |
| [0.9.1 预览版](#0.9.1-preview) |2018 年 10 月 18 日 |--- |


## <a name="faq"></a>常见问题

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>请参阅
若要了解有关 Azure Cosmos DB 表 API 的详细信息，请参阅 [Azure Cosmos DB 表 API 简介](table-introduction.md)。 
