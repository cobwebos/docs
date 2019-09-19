---
title: 通过重新分区优化使用 Azure 流分析进行的处理
description: 本文介绍如何使用重新分区优化无法并行化的 Azure 流分析作业。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 82e4a225d26bac04ed4754169cc4a79e0a8f9b32
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101514"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>通过重新分区优化使用 Azure 流分析进行的处理

本文介绍如何使用重新分区针对无法完全[并行化](stream-analytics-scale-jobs.md)的方案缩放 Azure 流分析查询。

如果出现以下情况，则可能无法使用[并行化](stream-analytics-parallelization.md)：

* 你无法控制输入流的分区键。
* 你的源“散布”的输入跨多个以后需要合并的分区。 

## <a name="how-to-repartition"></a>如何重新分区

当你处理的数据所在的流不是按照自然的输入方案（例如事件中心的 **PartitionId**）分片时，需要重新分区或重新组织。 如果重新分区，则每个分片都可以独立处理，这样就可以通过线性方式横向扩展流式处理管道。

若要重新分区，请在查询中的 **PARTITION BY** 语句后使用关键字 **INTO**。 以下示例按 **DeviceID** 将数据分区，分区数目为 10。 使用 **DeviceID** 的哈希来确定哪个分区应该接受哪个子流。 将针对每个分区的流独立刷新数据，假定输出支持分区的写入并有 10 个分区。

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

以下示例查询联接两个其数据已重新分区的流。 联接两个其数据已重新分区的流时，这两个流必须有相同的分区键和计数。 结果就是一个具有相同分区方案的流。

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

输出方案应该与流方案键和计数匹配，这样就可以单独刷新每个子流。 流也可以在刷新之前通过另一方案再次进行合并和重新分区，但是应避免该方法，因为它会导致常规处理延迟增加，以及资源使用率增加。

## <a name="streaming-units-for-repartitions"></a>重新分区的流单元

试验并观察作业的资源使用情况，确定所需的具体分区数。 [流单元 (SU)](stream-analytics-streaming-unit-consumption.md) 的数目必须按照每个分区所需的物理资源进行调整。 通常情况下，每个分区需要六个 SU。 如果分配给作业的资源不足，系统只会在有益于作业的情况下应用重新分区。

## <a name="repartitions-for-sql-output"></a>针对 SQL 输出的重新分区

当作业使用 SQL 数据库进行输出时，请使用显式重新分区来匹配优化的分区计数，以便将吞吐量最大化。 由于 SQL 在使用八个写入器时效果最好，因此在刷新之前将流重新分区为八个（或在上游的某个位置进一步进行分区）可能会有益于作业性能。 

如果输入分区超过 8 个，则继承输入分区方案可能不是适当的选择。 请考虑在您的查询中使用[INTO](/stream-analytics-query/into-azure-stream-analytics.md#into-shard-count) ，以显式指定输出写入器的数目。 

下面的示例从输入中读取内容，无论它是自然分区，还是根据 DeviceID 维度对流十倍并刷新要输出的数据。 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

有关详细信息，请参阅从 [Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)。


## <a name="next-steps"></a>后续步骤

* [Azure 流分析入门](stream-analytics-introduction.md)
* [利用 Azure 流分析中的查询并行化](stream-analytics-parallelization.md)
