---
title: Cosmos DB 的 Azure 流分析输出
description: 本文介绍如何使用 Azure 流分析保存 Azure Cosmos DB 的输出以进行 JSON 输出，从而实现对非结构化 JSON 数据进行数据存档和低延迟查询。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: ff2071a703b0b5e94cd68122a878b51e9d97669a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112745"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB 的 Azure 流分析输出  
流分析可以针对 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 进行 JSON 输出，从而支持对非结构化 JSON 数据进行数据存档和低延迟查询。 本文档介绍有关实现此配置的一些最佳做法。

不熟悉 Cosmos DB 的人员可以查看 [Azure Cosmos DB 的学习路径](https://azure.microsoft.com/documentation/learning-paths/documentdb/)开始上手。 

> [!Note]
> 目前，Azure 流分析仅支持使用 **SQL API** 连接到 Azure Cosmos DB。
> 尚不支持使用其他 Azure Cosmos DB API。 如果使用其他 API 将 Azure 流分析指向 创建的 Azure Cosmos DB 帐户，则可能无法正确存储数据。 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>作为输出目标的 Cosmos DB 基础知识
使用流分析中的 Azure Cosmos DB 输出可以将流处理结果作为 JSON 输出写入到 Cosmos DB 集合中。 流分析不会在数据库中创建集合，而需要你预先创建这些集合。 这是为了让你控制 Cosmos DB 集合的计费成本，以便可直接使用 [Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 调整集合的性能、一致性和容量。 

下面详细介绍一些 Cosmos DB 集合选项。

## <a name="tune-consistency-availability-and-latency"></a>调整一致性、可用性和延迟
为了满足应用程序的要求，Azure Cosmos DB 允许微调数据库和集合，并在一致性、可用性和延迟之间进行权衡。 根据方案针对读写延迟需要什么级别的读取一致性，可以在数据库帐户上选择一致性级别。 另外，默认情况下，Azure Cosmos DB 对集合的每个 CRUD 操作启用了同步索引。 这是另一个有用选项，可控制 Azure Cosmos DB 中的读/写性能。 有关详细信息，请参阅[更改数据库和查询的一致性级别](../cosmos-db/consistency-levels.md)一文。

## <a name="upserts-from-stream-analytics"></a>从流分析进行 Upsert 操作
由于流分析与 Azure Cosmos DB 集成，因此可以在集合中基于给定的文档 ID 列插入或更新记录。 这也称为 *Upsert*。

流分析使用乐观 Upsert 方法，即仅当由于文档 ID 冲突而插入失败时才进行更新。 此更新以修补程序的形式执行，因此它使得对文档的部分更新（即添加新属性或替换现有属性）以增量方式执行。 但是，JSON 文档中数组属性的值的更改会导致整个数组被覆盖，即不会合并数组。

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB 中的数据分区
建议使用 Azure Cosmos DB [unlimited](../cosmos-db/partition-data.md) 将数据分区，因为 Azure Cosmos DB 可根据工作负荷自动缩放分区。 写入到无限制的容器时，流分析会使用先前查询步骤或输入分区方案中一样多的并行写入器。

对于固定的 Azure Cosmos DB 集合，在这些集合已满后，流分析不允许通过任何方式进行纵向或横向扩展。 这些集合的大小上限为 10 GB，吞吐量上限为 10,000 RU/秒。  若要将数据从固定的容器迁移到无限制容器（例如，吞吐量至少为 1,000 RU/秒，且具有分区键），则需使用[数据迁移工具](../cosmos-db/import-data.md)或[更改源库](../cosmos-db/change-feed.md)。

写入多个固定容器的做法已过时，不建议使用此方法来横向扩展流分析作业。 [Cosmos DB 中的分区和缩放](../cosmos-db/sql-api-partition-data.md)一文提供了更多详细信息。

## <a name="cosmos-db-settings-for-json-output"></a>JSON 输出的 Cosmos DB 设置
创建 Cosmos DB 作为流分析中的输出时，会生成如下所示的信息提示。 本部分提供属性定义的说明。


![documentdb 流分析输出屏幕](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

字段           | 说明 
-------------   | -------------
输出别名    | 用于在 ASA 查询中引用此输出的别名   
帐户名    | Azure Cosmos DB 帐户的名称或终结点 URI 
帐户密钥     | Azure Cosmos DB 帐户的共享访问密钥
数据库        | Azure Cosmos DB 数据库名称
集合名称 | 要使用的集合的集合名称。 `MyCollection` 是有效的输入示例 - 必须存在一个名为 `MyCollection` 的集合。  
文档 ID     | 可选。 输出事件中用作唯一键的列名称，插入或更新操作必须基于该键。 如果留空，则插入所有事件，但不使用更新选项。
