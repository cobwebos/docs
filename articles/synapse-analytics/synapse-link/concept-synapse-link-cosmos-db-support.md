---
title: Synapse Link for Azure Cosmos DB 支持的功能
description: 了解 Synapse Link for Azure Cosmos DB 支持的当前操作列表
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 1b94d7677026f3695d07be4d83a5059373078c2e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599028"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Synapse Link for Azure Cosmos DB 支持的功能

本文介绍 Synapse Link for Azure Cosmos DB 当前支持的功能。 

## <a name="azure-synapse-support"></a>Azure Synapse 支持

Azure Cosmos DB 中有两种类型的容器：
* HTAP 容器 - 启用了 Synapse Link 的容器。 此容器具有事务存储和分析存储。 
* OLTP 容器 - 仅具有事务存储的容器，且未启用 Synapse Link。 

可以在不启用 Synapse Link 的情况下连接到 Azure Cosmos DB 容器，在这种情况下，只能读取/写入事务存储。 以下是 Synapse Link for Azure Cosmos DB 当前支持功能的列表。 

| 类别              | 说明 |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL 无服务器](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| 运行时支持 |支持通过 Azure Synapse 运行时读取或写入| ✓ | [联系我们](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| Azure Cosmos DB API 支持 |API 支持作为 Synapse Link| SQL / MongoDB | SQL / MongoDB |
| **Object**  |对象，例如可以创建一个表，直接指向 Azure Cosmos DB 容器| 查看, 表 | 查看 |
| **读取**    |读取 Azure Cosmos DB 容器中的数据| OLTP / HTAP | HTAP  |
| **写入**   |将数据从运行时写入 Azure Cosmos DB 容器| OLTP | 不适用 |

* 如果从 Spark 将数据写入 Azure Cosmos DB 容器中，此过程会通过 Azure Cosmos DB 的事务存储进行，并会通过消耗请求单位来影响 Azure Cosmos DB 的事务性能。
* 目前不支持通过外部表进行 SQL 池集成。

## <a name="supported-code-generated-actions-for-spark"></a>支持的 Spark 代码生成的操作

| 手势              | 说明 |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| 加载到数据帧 |加载数据并将其读取到 Spark 数据帧 |X| ✓ |
| 创建 Spark 表 |创建指向 Azure Cosmos DB 容器的表|X| ✓ |
| 将数据帧写入容器 |将数据写入容器|✓| ✓ |
| 加载容器中的流式处理数据帧 |使用 Azure Cosmos DB 更改源流式处理数据|✓| ✓ |
| 将流式处理数据帧写入容器 |使用 Azure Cosmos DB 更改源流式处理数据|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>SQL 无服务器支持的代码生成操作

| 手势              | 说明 |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| 选择前 100 项 |预览容器中的前 100 项|X| ✓ |
| 创建视图 |创建视图以通过 Synapse SQL 在容器中直接具有 BI 访问权限|X| ✓ |

## <a name="next-steps"></a>后续步骤

* 请参阅如何[连接到 Synapse Link for Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [了解如何使用 Spark 查询分析存储](how-to-query-analytical-store-spark.md)