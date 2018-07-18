---
title: 重新生成 Azure 搜索索引或刷新可搜索内容 | Microsoft Docs
description: 添加新元素、更新现有元素或文档，或删除完整重新生成或部分增量索引中的过时文档，以刷新 Azure 搜索索引。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: cb99096c1217fca1527b17946dc12390ddf3f62c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659164"
---
# <a name="how-to-scale-out-indexing-in-azure-seearch"></a>如何在 Azure 搜索中横向扩展索引

随着数据量的增长或处理需求的变化，你可能会发现，简单的[重新生成和重建索引作业](search-howto-reindex.md)并不足以满足需要。 

作为满足更高需求的第一步，我们建议在现有服务的限制范围内提高[规模和容量](search-capacity-planning.md)。 

在第二个步骤中，如果可以使用[索引器](search-indexer-overview.md)，将向可扩展索引添加机制。 索引器附带内置的计划程序，用于定期打包索引编制作业，或者将处理能力扩展到 24 小时时限以外。 此外，与数据源定义搭配使用时，索引器可以通过数据分区并使用计划来并行执行，帮助实现某种形式的并行度。

### <a name="scheduled-indexing-for-large-data-sets"></a>针对大型数据集的按计划索引编制

计划是用于处理大型数据集和缓慢运行的分析（例如，认知搜索管道中的图像分析）的重要机制。 索引器处理需在 24 小时时限内完成运行。 如果处理未能在 24 小时内完成，则索引器计划的行为能够提供优势。 

根据设计，计划的索引按特定的间隔启动，作业通常会按下一个计划间隔在恢复之前完成。 但是，如果处理在该间隔内未完成，则索引器会停止（因为已超时）。 在下一个间隔，处理将上次中断的位置恢复，同时，系统会跟踪该位置。 

实际上，对于跨越好几天的索引负载，可按 24 小时计划放置索引器。 在下一个 24 小时定时时间恢复索引编制时，该作业会从已知正常的文档重新开始。 这样，索引器便可以处理很多天的文档积压工作，直到处理完所有未处理的文档。 有关此方法的详细信息，请参阅[为大型数据集编制索引](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>并行索引

第二个选项是设置并行索引策略。 对于非例行性的计算密集型索引要求（例如，在认知搜索管道中针对扫描的文档执行 OCR），并行索引策略可能是实现该特定目标的适当方法。 在认知搜索扩充管道中，图像分析和自然语言处理会长时间运行。 在操作慢速处理内容的大段正文时，针对不会同时处理查询请求的服务执行并行索引编制可能是可行的选项。 

用于并行处理的策略包含以下元素：

+ 在在多个容器之间分割源数据，或者分割同一容器中的多个虚拟文件夹。 
+ 将每个微型数据集映射到与其自身[索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer)配对的[数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)。
+ 对于认知搜索，请在每个索引器定义中引用相同的[技能集](https://docs.microsoft.com/rest/api/searchservice/create-skillset)。
+ 写入相同的目标搜索索引。 
+ 将所有索引器计划为在同一时间运行。

> [!Note]
> Azure 搜索不支持将副本或分区专用于特定的工作负荷。 重度并发索引的风险是使系统过载，导致查询性能下降。 如果创建了测试环境，请先在该环境中实施并行索引，以了解利弊。

## <a name="configure-parallel-indexing"></a>配置并行索引

对于索引器，处理能力并不严格依赖于搜索服务所用每个服务单位 (SU) 的一个索引器子系统。 可以在基本或标准层上预配的、至少包含两个副本的 Azure 搜索服务中创建多个并发索引器。 

1. 在 [Azure 门户](https://portal.azure.com)中，在搜索服务仪表板的“概述”页上，选中“定价层”以确认它能够适应并行索引。 基本和标准层提供多个副本。

2. 在“设置” > “缩放”中，为并行处理[增加副本](search-capacity-planning.md)：为每个索引器工作负荷额外添加一个副本。 保留足够数量的现有查询卷。 为索引牺牲查询工作负荷并不是一个很好的折衷方法。

3. 在 Azure 搜索索引器可以访问的级别，将数据分配到多个容器。 这可能是 Azure SQL 数据库中的多个表、Azure Blob 存储中的多个容器，或多个集合。 为每个表或容器定义一个数据源对象。

4. 创建并计划要并行运行的多个索引器：

   + 假设某个服务包含六个副本。 配置六个索引器（每个索引器映射到包含 1/6 数据集的数据源），以便对整个数据集进行 6 向拆分。 

   + 将每个索引器指向相同的索引。 对于认知搜索工作负荷，请将每个索引器指向相同的技能集。

   + 在每个索引器定义中，计划相同的运行时执行模式。 例如，`"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` 在 2018-05-15 针对所有索引器创建计划，这些计划每隔八小时运行。

在计划的时间，所有索引器将开始执行、加载数据、应用扩充（如果配置了认知搜索管道），并写入索引。 Azure 搜索不会为更新锁定索引。 如果特定的写入在首次尝试时不成功，则会使用重试对并发写入进行管理。

> [!Note]
> 增加副本时，如果索引大小预计会显著增加，请考虑增加分区计数。 分区存储索引内容的切片；分区数越多，每个分区要存储的切片就越小。

## <a name="see-also"></a>另请参阅

+ [索引器概述](search-indexer-overview.md)
+ [在门户中编制索引](search-import-data-portal.md)
+ [Azure SQL 数据库索引器](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 索引器](search-howto-index-cosmosdb.md)
+ [Azure Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)
+ [Azure 表存储索引器](search-howto-indexing-azure-tables.md)
+ [Azure 搜索中的安全性](search-security-overview.md)