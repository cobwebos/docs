---
title: 在 Azure Cosmos DB 中创建合成分区键，均匀分配数据和工作负载。
description: 了解如何使用 Azure Cosmos 容器中的合成分区键
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 8b4e2b8abac39f3268e0da7838acd566f40fdccc
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754819"
---
# <a name="create-a-synthetic-partition-key"></a>创建合成分区键

最佳做法是使用包含多个非重复值的分区键，如几百或千位。 目标是在与这些分区键值关联的项之间均匀分配数据和工作负荷。 如果数据中不存在此类属性，则可以构造*合成分区键*。 本文档介绍了几种为 Cosmos 容器生成综合分区键的基本技术。

## <a name="concatenate-multiple-properties-of-an-item"></a>连接某个项的多个属性

将多个属性值连接成单个人工 `partitionKey` 属性可以构成一个分区键。 这些键称为合成键。 例如，考虑以下示例文档：

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

对于上面的文档，一种做法是将 /deviceId 或 /date 设为分区键。 如果要根据设备 ID 或日期对容器进行分区，请使用此选项。 另一种做法是将这两个值连接成用作分区键的合成 `partitionKey` 属性。

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

在实时方案中，您可以在数据库中拥有数千个项。 定义客户端逻辑来连接值并将合成密钥插入到 Cosmos 容器中的项，而不是手动添加合成密钥。

## <a name="use-a-partition-key-with-a-random-suffix"></a>使用具有随机后缀的分区键

另一种更均匀地分配工作负荷的可行策略是，在分区键值的末尾追加一个随机数。 以这种方式分配项时，可以跨分区执行并行写入操作。

例如，如果某个分区键表示日期， 您可以选择1到400之间的一个随机数，并将其作为后缀连接到该日期。 此方法通过  `2018-08-09.400` 生成分区键值，如  `2018-08-09.1`、`2018-08-09.2` 等。 由于随机化了分区键，每日针对容器执行的写入操作将均匀分散在多个分区之间。 此方法可以提高并行度和总体吞吐量。

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>使用带有预计算后缀的分区键 

随机后缀策略可以极大地提高写入吞吐量，但难以读取特定的项目。 您不知道在写入项时使用的后缀值。 若要更轻松地读取各个项，请使用预先计算的后缀策略。 请使用根据要查询的内容计算的数字，而不是使用随机数字在分区之间分发项。

请考虑前面的示例，其中容器使用日期作为分区键。 现在假设每个项都有一个要访问的  `Vehicle-Identification-Number` （`VIN`）特性。 此外，假设你经常运行查询以查找 `VIN` 中的项以及日期。 在应用程序将项写入容器之前，它可以基于 VIN 计算哈希后缀，并将其追加到分区键日期。 计算可能会生成一个介于1和400之间的数字，该数字是均匀分布的。 此结果与随机后缀策略方法生成的结果类似。 分区键值将是与计算结果相连接的日期。

使用此策略可在分区键值之间以及分区之间均匀分散写入。 您可以轻松读取特定的项目和日期，因为您可以为特定的 `Vehicle-Identification-Number` 计算分区键值。 此方法的优点是，你可以避免创建单个热分区键，例如，使用所有工作负荷的分区键。 

## <a name="next-steps"></a>后续步骤

可通过以下文章详细了解分区的概念：

* 详细了解[逻辑分区](partition-data.md)。
* 详细了解如何[对 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
