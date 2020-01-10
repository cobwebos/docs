---
title: 使用重新分区优化 Azure 流分析作业
description: 本文介绍如何使用重新分区优化无法并行化的 Azure 流分析作业。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732376"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>使用 Azure 流分析优化处理

本文介绍如何使用重新分区来缩放 Azure 流分析查询，了解无法完全[并行](stream-analytics-scale-jobs.md)化的情况。

如果以下情况，可能无法使用[并行](stream-analytics-parallelization.md)化：

* 不控制输入流的分区键。
* 在以后需要合并的多个分区中的源 "sprays" 输入。

当处理未根据自然输入方案分片的流上的数据（如事件中心的**PartitionId** ）时，需要重新分区或重新组织。 重新分区时，可以单独处理每个分片，这允许你以线性方式横向扩展流式处理管道。

## <a name="how-to-repartition"></a>如何重新分区

若要重新分区，请**在查询**中的**PARTITION BY**语句后使用关键字。 下面的示例按**DeviceID**将数据分区为10的分区计数。 使用**DeviceID**进行哈希运算可确定哪个分区应接受哪个子流。 如果输出支持分区写入，则为每个分区流单独刷新数据，其中包含10个分区。

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

下面的示例查询联接两个分区的数据。 联接两个重新分区数据流时，流必须具有相同的分区键和计数。 结果是具有相同分区方案的流。

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

输出方案应匹配流方案密钥和计数，以便可以单独刷新每个子流。 在刷新之前，流还可以通过不同的方案再次合并和重新分区，但应避免使用此方法，因为它会将处理的一般延迟添加到处理中并增加资源利用率。

## <a name="streaming-units-for-repartitions"></a>对的流式处理单元

试验并观察作业的资源使用情况，以确定所需的分区数。 必须根据每个分区所需的物理资源调整[流式处理单位（SU）](stream-analytics-streaming-unit-consumption.md)数。 通常，每个分区需要六个 SUs。 如果没有足够的资源分配给该作业，系统将仅应用重新分区，因为它可使作业受益。

## <a name="repartitions-for-sql-output"></a>对 for SQL 输出

如果作业使用 SQL 数据库进行输出，请使用显式重新分区来匹配最佳分区计数，以最大限度地提高吞吐量。 由于 SQL 最适用于8个写入器，因此在刷新之前将流重新分区为八个，或在上游之后重新分区，可能会提高作业性能。 

如果输入分区超过 8 个，则继承输入分区方案可能不是适当的选择。 请考虑在您的查询中使用[INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) ，以显式指定输出写入器的数目。 

下面的示例从输入中读取内容，无论它是自然分区，还是根据 DeviceID 维度对流十倍并刷新要输出的数据。 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

有关详细信息，请参阅从 [Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)。


## <a name="next-steps"></a>后续步骤

* [Azure 流分析入门](stream-analytics-introduction.md)
* [利用 Azure 流分析中的查询并行化](stream-analytics-parallelization.md)
