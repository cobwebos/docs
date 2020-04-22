---
title: Azure 数据湖存储查询加速（预览）
description: 查询加速（预览）是 Azure 数据湖存储的新功能，它使应用程序和分析框架能够通过仅检索处理操作所需的数据来显著优化数据处理。
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 191a3280075403c8c5b57c5ffca1c7707d1ddb11
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771815"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Azure 数据湖存储查询加速（预览）

查询加速（预览）是 Azure 数据湖存储的新功能，它使应用程序和分析框架能够通过仅检索执行给定操作所需的数据来显著优化数据处理。 这减少了获得对存储数据的关键洞察所需的时间和处理能力。

> [!NOTE]
> 查询加速功能处于公共预览版中，并且在加拿大中部和法国中部区域可用。 要查看限制，请参阅[已知问题](data-lake-storage-known-issues.md)一文。 要在预览版中注册，请参阅[此窗体](https://aka.ms/adls/qa-preview-signup)。  

## <a name="overview"></a>概述

查询加速度接受筛选*谓词*和*列投影*，使应用程序能够在从磁盘读取数据时筛选行和列。 只有满足谓词条件的数据通过网络传输到应用程序。 这降低了网络延迟和计算成本。  

可以使用 SQL 在查询加速请求中指定行筛选器谓词和列投影。 请求只处理一个文件。 因此，不支持 SQL 的高级关系功能，如按聚合联接和分组。 查询加速支持 CSV 和 JSON 格式化的数据作为输入每个请求。

查询加速功能不仅限于数据湖存储（在其中启用了分层命名空间的存储帐户）。 查询加速与存储帐户中的 Blob 完全兼容，这些 blob**未**启用分层命名空间。 这意味着，当您处理已存储为存储帐户中的 blob 的数据时，可以同样降低网络延迟和计算成本。

有关如何在客户端应用程序中使用查询加速的示例，请参阅[使用 Azure 数据湖存储查询加速筛选数据](data-lake-storage-query-acceleration-how-to.md)。

## <a name="data-flow"></a>数据流

下图说明了典型应用程序如何使用查询加速来处理数据。

> [!div class="mx-imgBorder"]
> ![查询加速概述](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. 客户端应用程序通过指定谓词和列投影来请求文件数据。

2. 查询加速分析指定的 SQL 查询，并分发用于分析和筛选数据的工作。

3. 处理器从磁盘读取数据，使用适当的格式分析数据，然后通过应用指定的谓词和列投影筛选数据。

4. 查询加速将响应分片合并到客户端应用程序。

5. 客户端应用程序接收并分析流式响应。 应用程序不需要筛选任何其他数据，可以直接应用所需的计算或转换。

## <a name="better-performance-at-a-lower-cost"></a>以更低的成本提高性能

查询加速通过减少应用程序传输和处理的数据量来优化性能。

若要计算聚合值，应用程序通常从文件中检索**所有数据**，然后在本地处理和筛选数据。 对分析工作负载的输入/输出模式的分析表明，应用程序通常只需要 20% 的数据，他们读取来执行任何给定的计算。 即使应用[了分区修剪](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning)等技术，此统计信息也是如此。 这意味着 80% 的数据通过网络不必要地传输、解析和筛选。 此模式（本质上是用于删除不需要的数据）会产生巨大的计算成本。  

尽管 Azure 具有业界领先的网络，但在吞吐量和延迟方面，在网络中不必要地传输数据对于应用程序性能来说仍然成本高昂。 通过在存储请求期间筛选出不需要的数据，查询加速消除了此成本。

此外，分析和筛选不需要的数据所需的 CPU 负载需要应用程序预配更多数量和更大的 VM 才能完成工作。 通过将计算负载转移到查询加速，应用程序可以显著节省成本。

## <a name="applications-that-can-benefit-from-query-acceleration"></a>受益于查询加速的应用程序

查询加速专为分布式分析框架和数据处理应用程序而设计。 

分布式分析框架（如 Apache Spark 和 Apache Hive）在框架中包括存储抽象层。 这些引擎还包括查询优化器，这些优化器可以在确定用户查询的最佳查询计划时整合基础 I/O 服务功能的知识。 这些框架开始集成查询加速。 因此，这些框架的用户将看到查询延迟的改善和较低的总拥有成本，而无需对查询进行任何更改。 

查询加速也专为数据处理应用程序而设计。 这些类型的应用程序通常执行大规模数据转换，这些转换可能不会直接导致分析见解，因此它们并不总是使用已建立的分布式分析框架。 这些应用程序通常与基础存储服务有更直接的关系，因此可以直接受益于查询加速等功能。 

有关应用程序如何集成查询加速的示例，请参阅使用 Azure[数据湖存储查询加速筛选数据](data-lake-storage-query-acceleration-how-to.md)。

## <a name="pricing"></a>定价

由于 Azure 数据湖存储服务中的计算负载增加，使用查询加速的定价模型不同于正常的 Azure 数据湖存储事务模型。 查询加速对扫描的数据量以及返回给调用方的数据量的成本收费。

尽管计费模型进行了更改，但查询加速的定价模型旨在降低工作负载的总拥有成本，因为 VM 成本的成本更低。

## <a name="next-steps"></a>后续步骤

- [查询加速注册表单](https://aka.ms/adls/qa-preview-signup)    
- [使用 Azure 数据湖存储查询加速筛选数据](data-lake-storage-query-acceleration-how-to.md)
- [查询加速 SQL 语言参考（预览）](query-acceleration-sql-reference.md)
- 查询加速 REST API 参考



