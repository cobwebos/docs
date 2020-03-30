---
title: 使用内置索引器为大型数据集编制索引
titleSuffix: Azure Cognitive Search
description: 通过计划、并行和分布式索引的批处理模式、资源处理和技术，为大型数据编制索引或进行计算密集型索引编制的策略。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190960"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中为大型数据集编制索引

随着数据量的增长或处理需求的变化，你可能发现，简单或默认的索引编制策略不再实用。 在 Azure 认知搜索中，可通过多种方法来适应较大的数据集，包括构建数据上传请求、对计划和分布式工作负荷使用特定于源的索引器，等等。

这些相同的技术也适用于长时间运行的进程。 特别是，[并行索引](#parallel-indexing)中概述的步骤有助于计算密集型索引，例如[AI 扩充管道](cognitive-search-concept-intro.md)中的图像分析或自然语言处理。

以下部分探讨的三种技术适用于对大量数据进行索引编制。

## <a name="option-1-pass-multiple-documents"></a>选项 1：传递多个文档

为较大数据集编制索引的最简单机制之一是在单个请求中提交多个文档或记录。 只要整个有效负载小于 16 MB，则请求就可以在一个批量上传操作中最多处理 1000 个文档。 不管你在 .NET SDK 中使用[添加文档 REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 还是[索引方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet)，这些限制均适用。 不管什么 API，你都会在每个请求的正文中打包 1000 个文档。

批处理索引编制是使用 REST 或 .NET 或通过索引器针对单个请求实现的。 有几个索引器根据不同的限制运行。 具体而言，当平均文档大小较大时，Azure Blob 索引编制会将批大小设置为 10 个文档。 对于基于[创建索引器 REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) 的索引器，可以设置 `BatchSize` 参数来自定义此设置，以便与数据特征更相符。 

> [!NOTE]
> 若要保持较小的文档大小，请避免向索引添加不可查询的数据。 图像和其他二进制数据不可直接搜索，不应存储在索引中。 若要将不可查询的数据集成到搜索结果中，应定义用于存储资源的 URL 引用的不可搜索字段。

## <a name="option-2-add-resources"></a>选项 2：添加资源

在某个[标准定价层](search-sku-tier.md)中预配的服务为存储和工作负荷（查询或索引）提供的容量往往利用不足，因此，绝对需要[增加分区和副本计数](search-capacity-planning.md)才能适应较大的数据集。 为获得最佳效果，需要提供存储分区和副本，使数据引入能够正常工作。

增加副本和分区是计费的事件，会增大成本，但是，除非你要在最大负载状态下持续编制索引，否则，可以在编制索引的持续时间内添加缩放功能，然后在完成索引编制后重新下调资源级别。

## <a name="option-3-use-indexers"></a>选项 3：使用索引器

[索引器](search-indexer-overview.md)用于在支持的 Azure 数据源中通过爬网找到可搜索的内容。 有几项索引器功能尽管并非专门用于大规模索引编制，但它们特别能够适应较大的数据集：

+ 使用计划程序可按固定的间隔来分配索引编制工作，以便在不同的时间完成这些工作。
+ 计划的索引编制可在最后一个已知的停止点处恢复。 如果在 24 小时期限内未完全爬网某个数据源，第二天，索引器将在中断位置恢复索引编制。
+ 将数据分区成较小的独立数据源可以实现并行处理。 可以将源数据拆分成较小的组件（例如 Azure Blob 存储中的多个容器），然后在 Azure 认知搜索中创建多个相应的可以通过并行方式编制索引的[数据源对象](https://docs.microsoft.com/rest/api/searchservice/create-data-source)。

> [!NOTE]
> 索引器是特定于数据源的，因此使用索引器方法仅适用于 Azure 上的选定数据源[：SQL 数据库](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)[、Blob 存储](search-howto-indexing-azure-blob-storage.md)、[表存储](search-howto-indexing-azure-tables.md)[、Cosmos DB](search-howto-index-cosmosdb.md)。

### <a name="scheduled-indexing"></a>计划的索引

索引器计划是用于处理大型数据集和缓慢运行的进程（例如，认知搜索管道中的图像分析）的重要机制。 索引器处理需在 24 小时时限内完成运行。 如果处理未能在 24 小时内完成，则索引器计划的行为能够提供优势。 

根据设计，计划的索引按特定的间隔启动，作业通常会按下一个计划间隔在恢复之前完成。 但是，如果处理在该间隔内未完成，则索引器会停止（因为已超时）。 在下一个间隔，处理将上次中断的位置恢复，同时，系统会跟踪该位置。 

实际上，对于跨越好几天的索引负载，可按 24 小时计划放置索引器。 在下一个 24 小时周期恢复索引编制时，该作业会从已知正常的文档重新开始。 这样，索引器便可以处理很多天的文档积压工作，直到处理完所有未处理的文档。 有关此方法的详细信息，请参阅[为 Azure Blob 存储中的大型数据集编制索引](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)。 有关设置计划的一般详细信息，请参阅[创建索引器 REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) 或[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>并行索引

并行索引编制策略基于同时为多个数据源编制索引，其中，每个数据源定义指定数据的子集。 

对于非常规的计算密集型索引编制要求 - 例如，针对认知搜索管道中扫描的文档运行 OCR、图像分析或自然语言处理 - 并行索引编制策略通常是在最短时间内完成长时间运行的进程的适当方法。 如果可以消除或减少查询请求，则在操作慢速处理内容的大段正文时，针对不会同时处理查询的服务使用并行索引编制是最好的策略选项。 

并行处理具有以下要素：

+ 在多个容器之间分割源数据，或者分割同一容器中的多个虚拟文件夹。 
+ 将每个微型数据集映射到与其自身[索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer)配对的自身[数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)。
+ 对于认知搜索，请在每个索引器定义中引用相同的[技能集](https://docs.microsoft.com/rest/api/searchservice/create-skillset)。
+ 写入相同的目标搜索索引。 
+ 将所有索引器计划为在同一时间运行。

> [!NOTE]
> 在 Azure 认知搜索中，不能将单个副本或分区分配给索引或查询处理。 系统确定如何使用资源。 若要了解对查询性能的影响，可以先在测试环境中尝试并行索引，然后再将其投入生产。  

### <a name="how-to-configure-parallel-indexing"></a>如何配置并行索引编制

对于索引器，处理能力并不严格依赖于搜索服务所用每个服务单位 (SU) 的一个索引器子系统。 可以在基本或标准层上预配的、至少包含两个副本的 Azure 认知搜索服务中创建多个并发索引器。 

1. 在 [Azure 门户](https://portal.azure.com)中，在搜索服务仪表板的“概述”页上，选中“定价层”以确认它能够适应并行索引。******** 基本和标准层提供多个副本。

2. 在 **"设置** > **缩放"中**，增加用于并行处理[的副本](search-capacity-planning.md)：每个索引器工作负载的一个附加副本。 保留足够数量的现有查询卷。 为索引牺牲查询工作负荷并不是一个很好的折衷方法。

3. 在 Azure 认知搜索索引器可以访问的级别，将数据分配到多个容器。 这可能是 Azure SQL 数据库中的多个表、Azure Blob 存储中的多个容器，或多个集合。 为每个表或容器定义一个数据源对象。

4. 创建并计划要并行运行的多个索引器：

   + 假设某个服务包含六个副本。 配置六个索引器（每个索引器映射到包含 1/6 数据集的数据源），以便对整个数据集进行 6 向拆分。 

   + 将每个索引器指向相同的索引。 对于认知搜索工作负荷，请将每个索引器指向相同的技能集。

   + 在每个索引器定义中，计划相同的运行时执行模式。 例如，`"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` 在 2018-05-15 针对所有索引器创建计划，这些计划每隔八小时运行。

在计划的时间，所有索引器将开始执行、加载数据、应用扩充（如果配置了认知搜索管道），并写入索引。 Azure 认知搜索不会为更新锁定索引。 如果特定的写入在首次尝试时不成功，则会使用重试对并发写入进行管理。

> [!Note]
> 增加副本时，如果索引大小预计会显著增加，请考虑增加分区计数。 分区存储索引内容的切片；分区数越多，每个分区要存储的切片就越小。

## <a name="see-also"></a>请参阅

+ [索引器概述](search-indexer-overview.md)
+ [在门户中编制索引](search-import-data-portal.md)
+ [Azure SQL 数据库索引器](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 索引器](search-howto-index-cosmosdb.md)
+ [Azure Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)
+ [Azure 表存储索引器](search-howto-indexing-azure-tables.md)
+ [Azure 认知搜索中的安全性](search-security-overview.md)
