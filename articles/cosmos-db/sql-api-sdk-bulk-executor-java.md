---
title: Azure Cosmos DB：批量执行程序 Java API、SDK 和资源 | Microsoft Docs
description: 了解有关批量执行程序 Java API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB 批量执行程序 Java SDK 各版本之间所做的更改。
services: cosmos-db
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: f7784883bca4773d69d94b04e7ec24a89ecd88c1
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300690"
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
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [批量执行程序 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**说明**</td><td>批量执行程序库允许客户端应用程序在 Azure Cosmos DB 帐户中执行批量操作。 批量执行程序库提供 BulkImport 和 BulkUpdate 命名空间。 BulkImport 模块可以批量以优化方式引入文档，以便最大程度地使用为集合配置的吞吐量。 BulkUpdate 模块可以作为修补程序批量更新 Azure Cosmos DB 容器中的现有数据。</td></tr>

<tr><td>**SDK 下载**</td><td>[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)</td></tr>

<tr><td>**GitHub 中的 BulkExecutor 库**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)</td></tr>

<tr><td>**API 文档**</td><td>[.Net API 参考文档](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)</td></tr>

<tr><td>**入门**</td><td>[批量执行程序库 Java SDK 入门](bulk-executor-java.md)</td></tr>

<tr><td>受支持的最小运行时</td><td>JDK 7</td></tr>
</table></br>

