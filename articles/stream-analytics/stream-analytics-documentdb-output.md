---
title: "流分析的 JSON 输出 | Microsoft Docs"
description: "了解流分析如何针对 Azure Cosmos DB 进行 JSON 输出，以实现对非结构化 JSON 数据进行数据存档和低延迟查询。"
keywords: "JSON 输出"
documentationcenter: 
services: stream-analytics,documentdb
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 29be0f5100aabe8374a26e6548effe20ccb9ac86
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="target-azure-cosmos-db-for-json-output-from-stream-analytics"></a>从流分析针对 Azure Cosmos DB 进行 JSON 输出
流分析可以针对 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 进行 JSON 输出，从而支持对非结构化 JSON 数据进行数据存档和低延迟查询。 本文档介绍有关实现此配置的一些最佳做法。

不熟悉 Cosmos DB 的人员可以查看 [Azure Cosmos DB 的学习路径](https://azure.microsoft.com/documentation/learning-paths/documentdb/)开始上手。 

> [!Note]
> 目前，Azure 流分析仅支持使用 SQL API 连接到 CosmosDB。
> 尚不支持使用其他 Azure Cosmos DB API。 如果使用其他 API 将 Azure 流分析指向 创建的 Azure Cosmos DB 帐户，则可能无法正确存储数据。 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>作为输出目标的 Cosmos DB 基础知识
使用流分析中的 Azure Cosmos DB 输出可以将流处理结果作为 JSON 输出写入到 Cosmos DB 集合中。 流分析不会在数据库中创建集合，而需要你预先创建这些集合。 这是为了让用户清楚地了解 Cosmos DB 集合的计费成本，以便可直接使用 [Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 调整集合的性能、一致性和容量。 建议对每个流式处理作业使用一个 Cosmos DB 数据库，以便在逻辑上为流式处理作业划分集合。

下面详细介绍一些 Cosmos DB 集合选项。

## <a name="tune-consistency-availability-and-latency"></a>调整一致性、可用性和延迟
为了满足应用程序的要求，Cosmos DB 允许微调数据库和集合，并在一致性、可用性和延迟之间进行权衡。 根据方案针对读写延迟需要什么级别的读取一致性，可以在数据库帐户上选择一致性级别。 另外，默认情况下，Cosmos DB 对集合的每个 CRUD 操作启用了同步索引。 这是另一个有用选项，可控制 Cosmos DB 中的读/写性能。 有关本主题的更多信息，请参阅[更改数据库和查询的一致性级别](../cosmos-db/consistency-levels.md)文章。

## <a name="upserts-from-stream-analytics"></a>从流分析进行 Upsert 操作
由于流分析与 Cosmos DB 集成，因此可以在 Cosmos DB 集合中基于给定的文档 ID 列插入或更新记录。 这也称为 *Upsert*。

流分析利用了乐观 Upsert 方法，即仅当由于文档 ID 冲突而插入失败时才进行更新。 此更新由流分析以修补程序的形式执行，因此它使得对文档的部分更新（即添加新属性或替换现有属性）以增量方式执行。 请注意，JSON 文档中数组属性的值的更改会导致整个数组被覆盖，即不会合并数组。

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB 中的数据分区
建议使用 Cosmos DB [分区集合](../cosmos-db/partition-data.md)对数据进行分区。 

对于单一 Cosmos DB 集合，用户仍然可以使用流分析根据应用程序的查询模式和性能需求对数据进行分区。 每个集合最多可包含 10GB 的数据（最大值），并且当前无法扩展（或溢出）集合。 要向外扩展，流分析允许写入到具有给定前缀的多个集合（请参见下面的用法详细信息）。 流分析根据用户提供的 PartitionKey 列，使用一致的[哈希分区解析程序](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)对其输出记录进行分区。 流式处理作业开始时，具有给定前缀的集合数将用作输出分区计数，作业将并行写入到这些集合（Cosmos DB 集合数 = 输出分区数）。 对于延迟索引仅执行插入的单个集合，可以预期大约 0.4 MB/秒的写入吞吐量。 使用多个集合，可以允许实现更高的吞吐量和更大的容量。

如果想要在将来增加分区计数，则可能需要停止作业，将现有集合中的数据重新划分为新的集合，然后重新启动流分析作业。 有关使用 PartitionResolver 和重新分区的更多详细信息以及示例代码会在后续帖子中提供。 [Cosmos DB 中的分区和缩放](../cosmos-db/sql-api-partition-data.md)一文中也提供了有关此内容的详细信息。

## <a name="cosmos-db-settings-for-json-output"></a>JSON 输出的 Cosmos DB 设置
创建 Cosmos DB 作为流分析中的输出时，会生成如下所示的信息提示。 本部分提供属性定义的说明。

已分区集合 | 多个“单一分区”集合
---|---
![documentdb 流分析输出屏幕](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![documentdb 流分析输出屏幕](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> **多个“单一分区”集合**方案需要一个分区键，是一种受支持的配置。 

* **输出别名** - 用于在 ASA 查询中引用此输出的别名  
* 帐户名 - Cosmos DB 帐户的名称或终结点 URI。  
* 帐户密钥 - Cosmos DB 帐户的共享访问密钥。  
* 数据库 - Cosmos DB 数据库名称。  
* **集合名称模式** - 要使用的集合的集合名称或其模式。 可以使用可选的 {partition} 令牌（其中分区从 0 开始）构造集合名称格式。 以下是有效输入示例：  
  1\) MyCollection - 必须存在一个名为“MyCollection”的集合。  
  2\) MyCollection{partition} – 此类集合必须存在 –“MyCollection0”、“MyCollection1”、“MyCollection2”等。  
* **分区键** - 可选。 仅当你在集合名称模式中使用 {partition} 令牌时，才需要此项。 输出事件中的字段的名称，该字段用于指定跨集合分区输出的键。 对于单个集合输出，可使用任何任意输出列（例如 PartitionId）。  
* **文档 ID** - 可选。 输出事件中的字段的名称，该字段用于指定插入或更新操作所基于的主键。  
