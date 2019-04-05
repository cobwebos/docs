---
title: Azure Cosmos DB 中的请求单位和吞吐量
description: 了解如何指定和评估 Azure Cosmos DB 中的请求单位要求。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 72ec8332a3363d5336fb84b3390d5e44ced0b2e5
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047308"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB 中的请求单位数

使用 Azure Cosmos DB 时，需要支付预配的吞吐量和每小时消耗的存储的费用。 必须预配吞吐量，以确保足够的系统资源在任何时候都可用于 Azure Cosmos 数据库。 你需要足够的资源来达到或超过[Azure Cosmos DB Sla](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)。

Azure Cosmos DB 支持多种 API，例如 SQL、MongoDB、Cassandra、Gremlin 和表。 每个 API 具有自身的数据库操作集。 这些操作包括简单的点读取和写入，以及复杂的查询等等。 每个数据库操作根据其复杂性消耗系统资源。 

所有数据库操作的成本的规范化方式为 Azure Cosmos DB 和表示由*请求单位*（或 Ru，简称）。 可将每秒 RU 数视为吞吐量的货币。 每秒 RU 数是基于费率的货币。 它抽象化了执行 Azure Cosmos DB 支持的数据库操作所需的系统资源，例如 CPU、IOPS 和内存。 

读取 1 KB 项是 1 个请求单位的成本 (或 1 RU)。 所有其他数据库操作同样分配以使用保留单位的成本。 不管使用哪个 API 来与 Azure Cosmos 容器和数据库操作交互，都始终以 RU 来计量成本。 无论数据库操作是写入、读取还是查询，都始终以 RU 来计量成本。

下图显示的 Ru 大致的了解：

![数据库操作消耗请求单位](./media/request-units/request-units.png)

为了方便管理和规划容量，Azure Cosmos DB 可确保针对给定数据集执行的给定数据库操作的 RU 数是确定性的。 您可以检查响应标头，若要跟踪的任何数据库操作使用的 Ru 数量。 当您了解[因素会影响 RU 费用](request-units.md#request-unit-considerations)和应用程序的吞吐量要求，可以运行经济高效的方式应用程序。

可以按秒来预配应用程序的 RU 数，增量为每秒 100 RU。 若要缩放应用程序的预配吞吐量，随时可以增加或减少 RU 数。 可缩放中递增或递减的 100 个 Ru。 可以编程方式或使用 Azure 门户进行更改。 按每小时计费。

可在两个不同的粒度级别预配吞吐量： 

* **容器**：有关详细信息，请参阅[对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* **数据库**:有关详细信息，请参阅[对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。

## <a name="request-unit-considerations"></a>请求单位注意事项

估算要预配的每秒 RU 数量时，请考虑以下因素：

* **项大小**：随着项的增大，读取或写入该项所要消耗的 RU 数也会增加。

* **项索引**：默认情况下会自动为每个项创建索引。 如果选择不为容器中的某些项创建索引，则消耗的 RU 数将会减少。

* **项属性计数**：假设默认索引对所有属性，使用编写随着项属性计数的增加而增加，某项的 Ru 数量。

* **带索引的属性**：每个容器的索引策略都可确定默认情况下要进行索引的属性类别。 若要减少写入操作的 RU 消耗，请限制带索引的属性数目。

* **数据一致性**：在执行读取操作时，非常一致性和有限过期一致性级别消耗的 RU 数大约比其他宽松一致性级别要多两倍。

* **查询模式**：查询的复杂性会影响操作使用的 RU 数。 影响查询操作成本的因素： 
    
    - 查询结果数
    - 谓词数
    - 谓词的性质
    - 用户定义函数的数目
    - 源数据的大小
    - 结果集的大小
    - 投影

  Azure Cosmos DB 保证针对相同数据重复执行的相同查询所消耗的 RU 数相同。

* **脚本的使用**：与查询一样，存储过程和触发器也是根据所执行的操作的复杂性来消耗 RU。 开发应用程序时，检查[请求费用标头](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query)以更好地了解每个操作消耗的 RU 容量。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[对 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)。
* 详细了解[逻辑分区](partition-data.md)。
* 详细了解如何[全局缩放预配的吞吐量](scaling-throughput.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
* 了解如何[操作找到的请求单位费用](find-request-unit-charge.md)。
* 了解如何[优化了 Azure Cosmos DB 中的预配的吞吐量成本](optimize-cost-throughput.md)。
* 了解如何[优化读取和写入 Azure Cosmos DB 中的成本](optimize-cost-reads-writes.md)。
* 了解如何[优化 Azure Cosmos DB 中的查询开销](optimize-cost-queries.md)。
