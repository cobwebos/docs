---
title: 在 Azure Cosmos DB 中创建合成分区键，均匀分配数据和工作负载。
description: 了解如何使用 Azure Cosmos 容器中的合成分区键
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: mjbrown
ms.openlocfilehash: 8becfe375f2e887348729cf1d76820fc41156d2a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997094"
---
# <a name="create-a-synthetic-partition-key"></a>创建合成分区键

采用具有大量（例如，几百甚至几千个）非重复性值的分区键是最佳做法。 目标是在与这些分区键值关联的项之间均匀分配数据和工作负荷。 如果数据中不存在此类属性，可以构造一个合成分区键。 以下部分介绍为容器生成合成分区键的几种基本方法。

## <a name="concatenate-multiple-properties-of-an-item"></a>连接某个项的多个属性

将多个属性值连接成单个人工 `partitionKey` 属性可以构成一个分区键。 这些键称为合成键。 例如，考虑以下示例文档：

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

对于上面的文档，一种做法是将 /deviceId 或 /date 设为分区键。 若要根据设备 ID 或日期将容器分区，可使用这种做法。 另一种做法是将这两个值连接成用作分区键的合成 `partitionKey` 属性。

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

在实时方案中，数据库可能包含数千个文档。 请不要手动添加合成键，而应该定义客户端逻辑来连接值，并将合成键插入文档中。

## <a name="use-a-partition-key-with-a-random-suffix"></a>使用具有随机后缀的分区键

另一种更均匀地分配工作负荷的可行策略是，在分区键值的末尾追加一个随机数。 以这种方式分配项时，可以跨分区执行并行写入操作。

例如，如果某个分区键表示日期， 则你可以选择介于 1 和 400 之间的随机数，并将其作为后缀连接到该日期。 此方法会生成如下所示的分区键值：2018-08-09.1、2018-08-09.2 ... 2018-08-09.400。 由于随机化了分区键，每日针对容器执行的写入操作将均匀分散在多个分区之间。 此方法可以提高并行度和总体吞吐量。

## <a name="use-a-partition-key-with-precalculated-suffixes"></a>使用具有预先计算的后缀的分区键 

尽管随机化策略能够大幅提高写入吞吐量，但却难以读取特定的项。 你不知道写入该项时使用的后缀值。 若要更轻松地读取单个项，请使用预先计算的后缀策略。 此策略不是使用随机数在分区之间分配项，而是使用根据想要查询的内容计算的某个数字。

沿用前面的示例，其中的某个容器使用分区键中的某个日期。 现在，假设每个项具有可访问的车辆标识号 (VIN) 属性。 此外，假设你经常运行查询来按 VIN 和日期查找项。 在应用程序将项写入容器之前，它可以基于 VIN 计算哈希后缀，并将其追加到分区键日期。 这种计算可以生成介于 1 和 400 之间的均匀分布数字。 此结果类似于随机策略方法生成的结果。 分区键值将是与计算结果相连接的日期。

使用此策略可在分区键值之间以及分区之间均匀分散写入。 由于可以计算特定车辆标识号的分区键值，因此可以轻松读取特定的项和日期。 此方法的好处是可以避免创建单个热分区键。 热分区键是取所有工作负荷值的分区键。 

## <a name="next-steps"></a>后续步骤

可通过以下文章详细了解分区的概念：

* 详细了解[逻辑分区](partition-data.md)。
* 详细了解如何[对 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
