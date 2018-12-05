---
title: Azure Cosmos DB 中的请求单位和吞吐量
description: 了解如何指定和评估 Azure Cosmos DB 中的请求单位要求。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: c43b718002267cd33135c130c6c41cabd8ee9c3b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310026"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB 中的请求单位数

使用 Azure Cosmos DB 时，需要支付预配的吞吐量和每小时消耗的存储的费用。 必须预配吞吐量才能确保随时为 Azure Cosmos 数据库提供足够的系统资源，并达到或超过 Azure Cosmos DB SLA 的要求。

Azure Cosmos DB 支持各种 API（SQL、MongoDB、Cassandra、Gremlin 和表）。 每个 API 具有自身的数据库操作集，从简单的点读取和写入，到复杂的查询。 每个数据库操作根据其复杂性消耗系统资源。  所有数据库操作的成本将由 Azure Cosmos DB 规范化，并以“请求单位”(RU) 表示。 读取 1 KB 项的成本是 1 个请求单位 (1 RU)，并且使用 1 GB 存储所需的最小 RU 数为 40。 以类似方式为其他所有数据库操作分配 RU 成本。 不管使用哪个 API 来与 Azure Cosmos 容器和数据库操作（写入、读取、查询）交互，都始终以 RU 来计量成本。

可将“RU/秒”视为吞吐量的货币。 RU/秒是基于费率的货币，它抽象化了执行 Azure Cosmos DB 支持的数据库操作所需的系统资源，例如 CPU、IOPS 和内存。 下图显示了不同数据库操作消耗的请求单位：

![数据库操作消耗请求单位](./media/request-units/request-units.png)

为了方便管理和规划容量，Azure Cosmos DB 可确保针对给定数据集执行的给定数据库操作的 RU 数是确定性的。 可以通过检查响应标头，来跟踪任一数据库操作消耗的 RU 数。 了解影响请求单位费用的因素以及应用程序吞吐量要求后，可以经济高效地运行应用程序。

尽管是按每小时计费，但你可以按秒来预配应用程序的 RU 数，增量为 100 RU/秒。 若要缩放应用程序的预配吞吐量，随时可以编程方式或使用 Azure 门户增加或减少 RU 数（增量或减量为 100 RU）。

可在两个不同的粒度级别预配吞吐量： 

* **容器**。 有关详细信息，请参阅[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* **数据库**。 有关详细信息，请参阅[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。

## <a name="request-unit-considerations"></a>请求单位注意事项

估算要预配的“RU/秒”数量时，必须考虑以下因素：

* **项大小** - 随着项的增大，读取或写入该项所要消耗的 RU 数也会增加。

* **项索引** - 默认会自动为每个项创建索引。 如果选择不为容器中的某些项创建索引，则消耗的请求单位数将会减少。

* **项属性计数** - 假设所有属性采用默认索引，写入某个项所要消耗的 RU 数会随着项属性计数的增加而增加。

* **带索引的属性** - 每个容器的索引策略都可确定默认情况下要进行索引的属性类别。 可以通过限制已创建索引的属性数目，来减少写入操作的请求单位消耗。

* **数据一致性** - 在执行读取操作时，非常一致性和有限过期一致性级别消耗的 RU 数大约比其他宽松一致性级别要多 2 倍。

* **查询模式** - 查询的复杂性会影响操作消耗的请求单位数。 查询结果数、谓词数、谓词性质、用户定义的函数数目、源数据的大小、结果集的大小和投影数都会影响查询操作的成本。 Azure Cosmos DB 保证针对相同数据重复执行的相同查询所消耗的 RU 数相同。

* **脚本的使用** - 与查询一样，存储过程和触发器也是根据所执行的操作的复杂性来消耗 RU。 开发应用程序时，请检查请求费用标头，以更好地了解每个操作消耗的请求单位容量。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[为 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)
* 详细了解[逻辑分区](partition-data.md)
* 详细了解[全局缩放预配的吞吐量](scaling-throughput.md)
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)
