---
title: Azure Cosmos DB：批量执行程序 Java API、SDK 和资源
description: 了解有关批量执行程序 Java API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB 批量执行程序 Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: 6fe075d2679ec13217200bc8b0da132e8f4d9b57
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660444"
---
# <a name="java-bulk-executor-library-download-information"></a>Java 批量执行程序库：下载信息

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
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
|受支持的最小运行时|[Java 开发工具包 (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>发行说明

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* 修复了 DocumentAnalyzer.java，以从 JSON 中正确提取嵌套的分区键值。

### <a name="294"></a><a name="2.9.4"/>2.9.4

* 在 BulkDelete 操作中添加了相应的功能，以便在发生特定的失败后重试，并向用户返回可重试的失败列表。

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Cosmos SDK 版本 2.4.7 的更新。

### <a name="292"></a><a name="2.9.2"/>2.9.2

* 修复了“mergeAll”，使之能够在遇到“id”和分区键值时继续运行，以便将“id”和分区键值后面的所有已修补文档属性添加到更新的项列表。

### <a name="291"></a><a name="2.9.1"/>2.9.1

* 已将起始并发度更新为 1，并增加了 minibatch 的调试日志。


