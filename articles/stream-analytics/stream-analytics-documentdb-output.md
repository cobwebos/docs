---
title: "流分析的 JSON 输出 | Microsoft 文档"
description: "了解流分析如何针对 Azure DocumentDB 进行 JSON 输出，以实现对非结构化 JSON 数据进行数据存档和低延迟查询。"
keywords: "JSON 输出"
documentationcenter: 
services: stream-analytics,documentdb
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: dcbce2327bbd04847bc388e2e4d432ba8b190284
ms.openlocfilehash: ecfe38081d0a64509c94dfba0f5a98c5f45025c9


---
# <a name="target-azure-documentdb-for-json-output-from-stream-analytics"></a>从流分析针对 Azure DocumentDB 进行 JSON 输出
流分析可以针对 [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 进行 JSON 输出，从而支持对非结构化 JSON 数据进行数据存档和低延迟查询。 了解如何最有效地实施这种集成。

不熟悉 DocumentDB 的人员可以看一下 [DocumentDB 的学习路径](https://azure.microsoft.com/documentation/learning-paths/documentdb/)开始入门。

## <a name="basics-of-documentdb-as-an-output-target"></a>作为输出目标的 DocumentDB 的基础知识
使用流分析中的 Azure DocumentDB 输出可以将流处理结果作为 JSON 输出写入到 DocumentDB 集合中。 流分析不会在数据库中创建集合，而需要你预先创建这些集合。 这是为了让你清除了解 DocumentDB 集合的计费成本，以便可以直接使用 [DocumentDB API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 调整集合的性能、一致性和容量。 我们建议对每个流式处理作业使用一个 DocumentDB 数据库，以便在逻辑上为流式处理作业划分集合。

下面详细介绍一些 DocumentDB 集合选项。

## <a name="tune-consistency-availability-and-latency"></a>调整一致性、可用性和延迟
为了满足应用程序的要求，DocumentDB 允许你微调数据库和集合并在一致性、可用性和延迟之间进行权衡。 根据你的方案针对读写延迟需要什么级别的读取一致性，你可以在数据库帐户上选择一致性级别。 另外，默认情况下，DocumentDB 对集合的每个 CRUD 操作启用了同步索引。 这是另一个有用选项，可以控制 DocumentDB 中的读/写性能。 有关本主题的更多信息，请参阅[更改数据库和查询的一致性级别](../documentdb/documentdb-consistency-levels.md)文章。

## <a name="choose-a-performance-level"></a>选择性能级别
可以以 3 种不同性能级别（S1、S2 或 S3）创建 DocumentDB 集合，这可确定可用于该集合的 CRUD 的吞吐量。 此外，性能会受到集合的索引/一致性级别的影响。 请参考[此文](../documentdb/documentdb-performance-levels.md)，详细了解这些性能级别。

## <a name="upserts-from-stream-analytics"></a>从流分析进行 Upsert 操作
由于流分析与 DocumentDB 集成，你可以在 DocumentDB 集合中基于给定的文档 ID 列插入或更新记录。 这也称为 *Upsert*。

流分析利用了乐观 Upsert 方法，即仅当由于文档 ID 冲突而插入失败时才进行更新。 此更新由流分析以修补程序的形式执行，因此它使得对文档的部分更新（即添加新属性或替换现有属性）以增量方式执行。 请注意，JSON 文档中数组属性的值的更改会导致整个数组被覆盖，即不会合并数组。

## <a name="data-partitioning-in-documentdb"></a>DocumentDB 中的数据分区
DocumentDB 集合允许你根据应用程序的查询模式和性能需求来对数据进行分区。 每个集合最多可包含 10GB 的数据（最大值），并且当前无法向上扩展（或溢出）集合。 若要向外扩展，流分析允许你写入到具有给定前缀的多个集合（请参见下面的用法详细信息）。 流分析根据用户提供的 PartitionKey 列，使用一致的[哈希分区解析程序](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)对其输出记录进行分区。 流式处理作业开始时具有给定前缀的集合数将用作输出分区计数，作业将并行写入到这些集合（DocumentDB 集合数 = 输出分区数）。 对于延迟索引仅执行插入的单个 S3 集合，可以预期大约 0.4 MB/秒写吞吐量。 使用多个集合，可以允许你实现更高的吞吐量和更大的容量。

如果你想要在将来增加分区计数，则可能需要停止作业，将现有集合中的数据重新划分为新的集合，然后重新启动流分析作业。 有关使用 PartitionResolver 和重新分区的更多详细信息以及示例代码将在后续帖子中提供。 [DocumentDB 中的分区和缩放](../documentdb/documentdb-partition-data.md)一文中也提供了有关此内容的详细信息。

## <a name="documentdb-settings-for-json-output"></a>JSON 输出的 DocumentDB 设置
创建 DocumentDB 作为流分析中的输出时，会生成如下所示的信息提示。 本部分提供属性定义的说明。

![documentdb 流分析输出屏幕](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)  

* **输出别名** - 用于在 ASA 查询中引用此输出的别名  
* **帐户名** - DocumentDB 帐户的名称或终结点 URI。  
* **帐户密钥** - DocumentDB 帐户的共享访问密钥。  
* **数据库** - DocumentDB 数据库名称。  
* **集合名称模式** - 要使用的集合的集合名称模式。 可以使用可选的 {partition} 令牌（其中分区从 0 开始）构造集合名称格式。 以下是有效输入示例：  
  1\) MyCollection - 必须存在一个名为“MyCollection”的集合。  
  2\) MyCollection{partition} – 此类集合必须存在 –“MyCollection0”、“MyCollection1”、“MyCollection2”等。  
* **分区键** - 输出事件中字段的名称，该字段用于指定跨集合分区输出的键。 对于单个集合输出，可使用任何任意输出列（例如 PartitionId）。  
* **文档 ID** - 可选。 输出事件中的字段的名称，该字段用于指定插入或更新操作所基于的主键。  



<!--HONumber=Nov16_HO3-->


