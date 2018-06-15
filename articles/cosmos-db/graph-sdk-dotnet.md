---
title: Azure CosmosDB Graph API .NET SDK 和资源 | Microsoft Docs
description: 了解有关 Azure CosmosDB Graph API 的全部信息，包括发布日期、停用日期和各版本之间进行的更改。
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/17/2017
ms.author: lbosq
ms.openlocfilehash: c5975809b0f196fe88a11504549b046e910fc93c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795918"
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>Azure Cosmos DB Graph .NET API：下载和发行说明

|   |   |
|---|---|
|**SDK 下载**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**API 文档**|[ 参考文档](https://aka.ms/acdbgraphapiref)|
|**快速入门**|[Azure Cosmos DB：使用 .NET 和 Graph API 创建图形应用](create-graph-dotnet.md)|
|**教程**|[Azure CosmosDB：使用图形 API 创建容器](tutorial-develop-graph-dotnet.md)|
|**当前受支持的框架**| [Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>发行说明

我们建议对 .NET、Gremlin.Net 使用开源 Gremlin 驱动程序。 请在 [Azure Cosmos DB：使用 .NET 和图形 API 创建图形应用程序](create-graph-dotnet.md)中了解如何入门。

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-preview

#### <a name="bug-fixes"></a>Bug 修复
* 修复为视需要加载 `appsettings.json` (`netstandard1.6`)

#### <a name="whats-new"></a>新增功能
* 将 Microsoft.Azure.Graphs 切换到目标平台 AnyCPU。
* 从 `net461` 包清单中删除 Mono 程序集。

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-preview

#### <a name="whats-new"></a>新增功能
* 添加了对 `.netstandard 1.6` 的支持
  * 需要 `Microsoft.Azure.DocumentDB.Core >= 1.5.1`
* 添加了一个新的 `gremlin-groovy` 分析器以替换现有分析器。 此分析器支持 Tinkerpop 的 `gremlin-groovy` 语法的子集并包括：
  * 将分析性能提高了 2 倍。
  * 解决了与以下内容相关的许多问题：字符串中转义的字符、不正确地处理文本值以及旧分析器中的其他异常。
* 添加了针对遍历边缘谓词的优化。
  *  使用筛选器遍历跃点时将可以利用此项改进，例如：`g.V('1').outE().has('name', 'marko').inV()`。
* 添加了针对遍历 `limit()` 步骤的优化。

#### <a name="breaking-changes"></a>重大更改
* 删除了对 .NET Framework 4.5.1 的支持

* 新分析器符合 `gremlin-groovy` 语法。 因此，一些以前正常工作的表达式对于新分析器来说是不明确的。 需要注意的事例之一：
  * `in` 和 `as` 是 `gremlin-groovy` 中的保留关键字，因此这些步骤必须用 `.in()` 或 `.as()` 进行限定以避免语法错误。 例如：`g.V().repeat(in()).times(2)` ->  _会引发语法错误_  
 `g.V().repeat(__.in()).times(2)` -> _成功_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview
* 初始预览版本。

## <a name="release--retirement-dates"></a>发布和停用日期
Microsoft 至少会在停用 SDK 的 **12 个月**之前发出通知，以便顺利转换到更新的/受支持的版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。 

使用已停用的 SDK 对 Azure Cosmos DB 发出的任何请求都会被服务拒绝。

<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [0.3.1-preview](#0.3.1-preview) |2017 年 10 月 17 日 |--- |
| [0.3.0-preview](#0.3.0-preview) |2017 年 10 月 2 日 |--- |
| [0.2.4-preview](#0.2.4-preview) |2017 年 8 月 4 日 |--- |
| [0.2.2-preview](#0.2.2-preview) |2017 年 6 月 23 日 |--- |
| [0.2.1-preview](#0.2.2-preview) |2017 年 6 月 8 日 |--- |
| [0.2.0-preview](#0.2.2-preview) |2017 年 5 月 10 日 |--- |
| [0.1.0-preview](#0.1.0-preview) |2017 年 5 月 8 日 |--- |

## <a name="see-also"></a>另请参阅
若要了解有关 Azure Cosmos DB Graph API 的详细信息，请参阅 [Azure Cosmos DB：Graph API 简介](graph-introduction.md)。 
