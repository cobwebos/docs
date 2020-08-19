---
title: 请求单位为 Azure Cosmos DB 中的吞吐量和性能货币
description: 了解如何指定和评估 Azure Cosmos DB 中的请求单位要求。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 6831cb3f39c25eb69d16300156f456980cf57fa0
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604811"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB 中的请求单位

Azure Cosmos DB 支持多种 API，例如 SQL、MongoDB、Cassandra、Gremlin 和表。 每个 API 具有自身的数据库操作集。 这些操作包括简单的点读取和写入，以及复杂的查询等等。 每个数据库操作根据其复杂性消耗系统资源。

所有数据库操作的成本都按 Azure Cosmos DB 进行标准化，并按 (或 ru 的 *请求单位* 表示，这是简短的) 。 您可以将 ru 视为一种性能货币，用于提取执行 Azure Cosmos DB 所支持的数据库操作所需的系统资源，例如 CPU、IOPS 和内存。

执行点读取操作所需的成本 (例如，通过其 ID 和分区键值) 获取 1 KB 项的单个项是1个请求单位 (或 1 RU) 。 以类似方式为其他所有数据库操作分配 RU 成本。 不管使用哪个 API 来与 Azure Cosmos 容器和数据库操作交互，都始终以 RU 来计量成本。 无论数据库操作是写入、点读取还是查询，成本始终以 ru 度量。

下图显示了 RU 的高级理念：

:::image type="content" source="./media/request-units/request-units.png" alt-text="数据库操作消耗请求单位" border="false":::

为了方便管理和规划容量，Azure Cosmos DB 可确保针对给定数据集执行的给定数据库操作的 RU 数是确定性的。 可以检查响应标头来跟踪任一数据库操作消耗的 RU 数。 了解 [影响 RU 费用](request-units.md#request-unit-considerations) 和应用程序吞吐量要求的因素时，可以有效地运行应用程序。

你使用的 Azure Cosmos 帐户的类型决定了使用的 ru 的计费方式：

- 在 [预配的吞吐量](set-throughput.md) 模式下，你可以以每秒 100 ru/秒的增量预配应用程序的 ru 数。 若要缩放应用程序的预配吞吐量，你可以在任何时间以 100 ru 的增量或减量增加或减少 ru 数。 可以编程方式或使用 Azure 门户进行更改。 每小时按你预配的每秒 ru 量为你收费。 可在两个不同的粒度级别预配吞吐量：
  - **容器**：有关详细信息，请参阅 [在 Azure Cosmos 容器上预配吞吐量](how-to-provision-container-throughput.md)。
  - **数据库**：有关详细信息，请参阅 [在 Azure Cosmos 数据库上预配吞吐量](how-to-provision-database-throughput.md)。
- 在 [无服务器](serverless.md) 模式下，在 Azure Cosmos 帐户中创建资源时，无需设置任何吞吐量。 在计费周期结束时，你将为数据库操作使用的请求单位数量收费。

## <a name="request-unit-considerations"></a>请求单位注意事项

在估计工作负荷消耗的 ru 数量时，请考虑以下因素：

* **项大小**：随着项的大小增加，读取或写入项时所用的 RU 数也会增加。

* **项索引**：默认情况下会自动为每个项创建索引。 如果选择不为容器中的某些项编制索引，则使用的 RU 数会减少。

* **项属性计数**：假设所有属性采用默认索引，写入某个项所要消耗的 RU 数会随着项属性计数的增加而增加。

* **带索引的属性**：每个容器的索引策略都可确定默认情况下要进行索引的属性类别。 若要减少写入操作使用的 RU 数，请限制索引属性的数量。

* **数据一致性**：与其他宽松一致性级别相比，在执行读取操作时，强和有限过期的一致性级别消耗大约两倍于 ru 的时间。

* **读取类型**：点读取开销比查询少很多。

* **查询模式**：查询的复杂性会影响操作使用的 RU 数。 影响查询操作成本的因素： 
    
    - 查询结果数
    - 谓词数
    - 谓词性质
    - 用户定义的函数数目
    - 源数据的大小
    - 结果集的大小
    - 投影数

  Azure Cosmos DB 保证针对相同数据重复执行的相同查询所消耗的 RU 数相同。

* **脚本用法**：与查询一样，存储过程和触发器会根据所执行操作的复杂性使用 ru。 开发应用程序时，请检查[请求费用标头](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query)，以更好地了解每个操作消耗的 RU 容量。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[对 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)。
* 详细了解 [Azure Cosmos DB 上的无服务器](serverless.md)。
* 详细了解[逻辑分区](partition-data.md)。
* 详细了解如何[全局缩放预配的吞吐量](scaling-throughput.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
* 了解如何[查找操作所产生的请求单位费用](find-request-unit-charge.md)。
* 了解如何[在 Azure Cosmos DB 中优化预配的吞吐量成本](optimize-cost-throughput.md)。
* 了解如何[优化 Azure Cosmos DB 中的读取和写入成本](optimize-cost-reads-writes.md)。
* 了解如何[优化 Azure Cosmos DB 中的查询成本](optimize-cost-queries.md)。
* 了解如何[使用指标监视吞吐量](use-metrics.md)。
