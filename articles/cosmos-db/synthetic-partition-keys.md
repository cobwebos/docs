---
title: 在 Azure Cosmos DB 中创建合成分区键，均匀分配数据和工作负载。
description: 了解如何使用 Azure Cosmos 容器中的合成分区键
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 6b3499c36ae7abd03c4a1f1ca3a3a46e2c315120
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792092"
---
# <a name="create-a-synthetic-partition-key"></a>创建合成分区键

它是最佳做法具有大量非重复值，例如数百或数千个分区键。 目标是在与这些分区键值关联的项之间均匀分配数据和工作负荷。 如果数据中不存在这样的属性，可以构造*综合的分区键*。 本文档介绍生成 Cosmos 容器的综合的分区键的几种基本方法。

## <a name="concatenate-multiple-properties-of-an-item"></a>连接某个项的多个属性

将多个属性值连接成单个人工 `partitionKey` 属性可以构成一个分区键。 这些键称为合成键。 例如，考虑以下示例文档：

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

对于上面的文档，一种做法是将 /deviceId 或 /date 设为分区键。 使用此选项时，如果你想要进行分区容器基于设备 ID 或日期。 另一种做法是将这两个值连接成用作分区键的合成 `partitionKey` 属性。

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

在实时情况下，可以有数千个数据库中的项。 无需手动添加的综合键，定义客户端的逻辑以将值连接起来并综合密钥插入 Cosmos 容器中的项。

## <a name="use-a-partition-key-with-a-random-suffix"></a>使用具有随机后缀的分区键

另一种更均匀地分配工作负荷的可行策略是，在分区键值的末尾追加一个随机数。 以这种方式分配项时，可以跨分区执行并行写入操作。

例如，如果某个分区键表示日期， 可以选择介于 1 和 400 之间的随机数字，并将它作为日期后缀相连接。 此方法会导致分区键值等 `2018-08-09.1`，`2018-08-09.2`，依此类推，通过 `2018-08-09.400`。 由于随机化了分区键，每日针对容器执行的写入操作将均匀分散在多个分区之间。 此方法可以提高并行度和总体吞吐量。

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>使用的分区键具有预先计算的后缀 

随机后缀策略可以极大地提高写入吞吐量，但很难读取的特定项。 你不知道编写了该项时使用的后缀值。 若要使其更易读取各个项，使用预先计算的后缀策略。 而不是使用随机数字分发在分区之间的项，请使用计算一个数字均根据您想要查询的内容。

请考虑前面的示例，其中一个容器使用日期作为分区键。 现在假设每个项有 `Vehicle-Identification-Number`(`VIN`) 我们想要访问的属性。 此外，假设通常运行查询来查找项由`VIN`，除了日期。 在应用程序将项写入容器之前，它可以基于 VIN 计算哈希后缀，并将其追加到分区键日期。 计算可能会生成 1 到 400 之间的均匀分布的数字。 此结果将类似于随机后缀策略方法所产生的结果。 分区键值将是与计算结果相连接的日期。

使用此策略可在分区键值之间以及分区之间均匀分散写入。 您可以轻松地读取的特定项和日期，因为可以计算出特定的分区键值`Vehicle-Identification-Number`。 此方法的好处是可以避免创建单个热分区键，即，将所有工作负荷的分区键。 

## <a name="next-steps"></a>后续步骤

可通过以下文章详细了解分区的概念：

* 详细了解[逻辑分区](partition-data.md)。
* 详细了解如何[对 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
