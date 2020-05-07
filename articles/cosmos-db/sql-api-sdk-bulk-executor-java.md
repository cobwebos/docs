---
title: Azure Cosmos DB：批量执行器 Java API、SDK & 资源
description: 了解有关批量执行程序 Java API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB 批量执行程序 Java SDK 各版本之间所做的更改。
author: milismsft
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/21/2018
ms.author: adrianmi
ms.openlocfilehash: 0030f974a36dc80dc8c4112000aa5934126a2482
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836438"
---
# <a name="java-bulk-executor-library-download-information"></a>Java 批量执行程序库：下载信息

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
> * [SQL](sql-api-query-reference.md)
> * [批量执行程序 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**说明**|批量执行程序库允许客户端应用程序在 Azure Cosmos DB 帐户中执行批量操作。 批量执行程序库提供 BulkImport 和 BulkUpdate 命名空间。 BulkImport 模块可以批量以优化方式引入文档，以便最大程度地使用为集合配置的吞吐量。 BulkUpdate 模块可以作为修补程序批量更新 Azure Cosmos 容器中的现有数据。|
|**SDK 下载**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub 中的批量执行程序库**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API 文档**| [Java API 参考文档](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**入门**|[批量执行程序库 Java SDK 入门](bulk-executor-java.md)|
|受支持的最小运行时 |[Java 开发工具包 (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>发行说明

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* 修复 DocumentAnalyzer 以从 json 正确提取嵌套分区键值。

### <a name="294"></a><a name="2.9.4"/>2.9.4

* 在 BulkDelete 操作中添加功能，以便在特定故障后重试，还会向用户返回可重试的失败列表。

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Cosmos SDK 版本2.4.7 的更新。

### <a name="292"></a><a name="2.9.2"/>2.9.2

* 修复了 "mergeAll" 以继续执行 "id" 和分区键值，以便将 "id" 和 "分区键值" 下放置的所有已修补的文档属性添加到更新的项列表。

### <a name="291"></a><a name="2.9.1"/>2.9.1

* 将并发启动度更新为1并添加 minibatch 的调试日志。


