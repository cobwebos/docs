---
title: Azure Cosmos DB 中自动缩放预配吞吐量的常见问题解答
description: 有关 Azure Cosmos DB 数据库和容器的自动缩放预置吞吐量的常见问题解答
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/10/2020
ms.openlocfilehash: ca4e79977132586c619f323015f9d915e04707f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84449509"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Azure Cosmos DB 中自动缩放预配吞吐量的常见问题解答

使用自动缩放预配吞吐量，Azure Cosmos DB 将根据使用情况自动管理和缩放数据库或容器的 RU/s。 本文解答了有关自动缩放的常见问题。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中，“Autopilot”和“自动缩放”有什么区别？
“自动缩放”或“自动缩放预配吞吐量”是该功能的更新名称，以前称为“Autopilot”。 在自动缩放的当前版本中，我们添加了新功能，包括设置自定义最大 RU/s 的功能和编程支持。 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>在之前的 Autopilot 层模型中创建的数据库或容器会发生什么情况？
新的自动缩放自定义最大 RU/s 模型自动支持使用之前的层模型创建的资源。 该层的上限成为新的最大 RU/s，这产生相同的缩放范围。 

例如，如果你之前选择了缩放比例为 400 到 4,000 RU/s 的层，则数据库或容器现在将显示具有最大为 4,000 RU/s 的吞吐量，缩放比例为 400 到 4,000 RU/s。 在此处，你可以将最大 RU/s 更改为自定义值以适应你的工作负载。 

### <a name="how-quickly-will-autoscale-scale-up-and-down-based-on-spikes-in-traffic"></a>自动缩放根据流量高峰进行增减的速度有多快？
使用自动缩放，系统根据传入的流量在 `0.1 * Tmax` 和 `Tmax` 范围内上下缩放吞吐量 (RU/s) `T`。 因为缩放是自动且即时的，所以可在任何时间点无延迟地使用吞吐量（最多使用预配的 `Tmax`）。 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>如何确定系统当前缩放到的吞吐量 (RU/s)？
使用 [Azure Monitor 指标](how-to-choose-offer.md#measure-and-monitor-your-usage)监视已预配的自动缩放最大 RU/s 和系统缩放到的当前吞吐量 (RU/s)。 

### <a name="what-is-the-pricing-for-autoscale"></a>自动缩放采用何种定价方式？
每小时将向你收取系统在该小时内扩展到的最高吞吐量 `T` 的费用。 如果你的资源在这个小时内没有请求，或者缩放的范围未超出 `0.1 * Tmax`，则将向你收取最低 `0.1 * Tmax` 的费用。 请参考 Azure Cosmos DB [定价页面](https://azure.microsoft.com/pricing/details/cosmos-db/)了解详细信息。 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>自动缩放如何显示在我的帐单上？
在单主数据库帐户中，每 100 RU/s 的自动缩放速率是标准（手动）预配吞吐量速率的 1.5 倍。 在账单上，你可看到现有的标准预配吞吐量计量。 此计量的数量乘以 1.5。 例如，如果系统在一小时内缩放到的最高 RU/s 为 6,000 RU/s，则该小时的计费是 60 * 1.5 = 90 个计量单位。

在多主数据库帐户中，每 100 RU/s 的自动缩放速率与标准（手动）预配的多主数据库吞吐量速率相同。 在帐单上，你可看到现有的多主数据库计量。 由于速率相同，因此如果你使用自动缩放，你可看到与标准吞吐量相同的数量。

### <a name="does-autoscale-work-with-reserved-capacity"></a>自动缩放是否可用于预留容量？
是的。 购买单主帐户预留容量时，对计量的使用量应用自动缩放资源的预留折扣，即 1.5 * [特定区域的比率](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region)。 

多主帐户预留容量同样适用于自动缩放和标准（手动）预配吞吐量。 请参阅 [Azure Cosmos DB 预留容量](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>自动缩放是否可用于免费层？
是的。 在免费层，可以对容器使用自动缩放吞吐量。 尚不支持具有自定义最大 RU/s 的自动缩放共享吞吐量数据库。 查看[免费层计费与自动缩放一起工作](understand-your-bill.md#billing-examples-with-free-tier-accounts)的方式。

### <a name="is-autoscale-supported-for-all-apis"></a>是否所有 API 都支持自动缩放？
是的，所有 API 都支持自动缩放：Core (SQL)、Gremlin、Table、Cassandra 以及 MongoDB API。

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>多主数据库帐户是否支持自动缩放？
是的。 添加到 Azure Cosmos DB 帐户的每个区域都支持最大 RU/s。 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>如何对新数据库或容器启用自动缩放？
请参阅此文章[如何启用自动缩放](how-to-provision-autoscale-throughput.md)。

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>是否可对现有数据库或容器启用自动缩放？
是的。 还可以根据需要在自动缩放和标准（手动）预配的吞吐量之间切换。 目前，对于所有 API，只能使用 [Azure 门户](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container)来执行这些操作。

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>自动缩放和标准（手动）预配的吞吐量之间的迁移是如何工作的？
从概念上讲，更改吞吐量类型是一个两阶段的过程。 首先，发送请求，更改吞吐量设置以使用自动缩放或手动预配的吞吐量。 在这两种情况下，系统将根据当前吞吐量设置和存储自动确定和设置初始 RU/s 值。 在此步骤中，不接受用户提供的 RU/s 值。 然后，在更新完成后，可以[更改 RU/s](#can-i-change-the-max-rus-on-the-database-or-container) 以适应你的工作负载。 

从标准（手动）预配的吞吐量迁移到自动缩放

对于容器，请使用以下公式估计初始自动缩放最大 RU/s：``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)``，四舍五入到最接近的 1000 RU/s。 实际的初始自动缩放最大 RU/s 可能因帐户配置而异。

示例 #1：假设你有一个具有 10,000 RU/s 手动预配吞吐量和 25 GB 存储空间的容器。 启用自动缩放时，初始自动缩放最大 RU/s 为：10,000 RU/s，缩放范围为 1,000 - 10,000 RU/s。 

示例 #2：假设你有一个具有 50,000 RU/s 手动预配吞吐量和 2,500 GB 存储的容器。 启用自动缩放时，初始自动缩放最大 RU/s 为：250,000 RU/s，缩放范围为 25,000 - 250,000 RU/s。 

从自动缩放迁移到标准（手动）预配吞吐量

初始手动预配吞吐量等于当前自动缩放最大 RU/s。 

示例：假设你有一个自动缩放数据库或容器，最大 RU/s 为 20,000 RU/s（缩放范围为 2,000 - 20,000 RU/s）。 当你更新为使用手动预配吞吐量时，初始吞吐量将为 20,000 RU/s。 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>是否支持使用自动缩放通过 Azure CLI 或 PowerShell 来管理数据库或容器？
目前，只能使用自动缩放从 Azure 门户、.NET V3 SDK、Java V4 SDK 和 Azure 资源管理器创建和管理资源。 目前尚不提供对 Azure CLI、PowerShell 和其他 SDK 的支持。

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>共享吞吐量数据库是否支持自动缩放？
是的，共享吞吐量数据库支持自动缩放。 若要启用此功能，请在创建数据库时选择自动缩放和“预配吞吐量”选项。 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>启用自动缩放后，每个共享吞吐量数据库允许的容器数是多少？
Azure Cosmos DB 在一个共享吞吐量数据库中最多可实施 25 个容器，这适用于具有自动缩放或标准（手动）吞吐量的数据库。 

### <a name="what-is-the-impact-of-autoscale-on-database-consistency-level"></a>自动缩放对数据库一致性级别有何影响？
自动缩放不影响数据库的一致性级别。
有关可用一致性级别的详细信息，请参阅[一致性级别](consistency-levels.md)一文。

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>与每个最大 RU/s 选项关联的存储限制是多少？  
每个最大 RU/s 的存储限制（以 GB 为单位）为：数据库或容器的最大 RU/s / 100。 例如，如果最大 RU/秒为 20,000 RU/秒，则该资源可以支持 200 GB 的存储空间。 请参阅[自动缩放限制](provision-throughput-autoscale.md#autoscale-limits)一文，了解可用的最大 RU/s 和存储选项。 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>如果超过了与最大吞吐量关联的存储空间上限，会发生什么情况？
如果超出了与数据库或容器的最大吞吐量关联的存储限制，Azure Cosmos DB 会自动将最大吞吐量提高到可以支持该级别存储的下一个最大 RU/s。

例如，如果开始时以 50,000 RU/s 为最大值（在 5000-50,000 RU/s 之间缩放），则最多可以存储 500 GB 的数据。 如果超过 500 GB，例如存储现在为 600 GB，则新的最大 RU/s 为 60,000 RU/s（在 6000-60,000 RU/s 之间缩放）。

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>能否更改数据库或容器的最大 RU/s？ 
是的。 请参阅[本文](how-to-provision-autoscale-throughput.md)，了解关于如何更改最大 RU/s。 根据请求的值更改最大 RU/s 时，此异步操作可能需要一些时间才能完成（根据所选的 RU/s，可能需要长达 4-6 小时）

#### <a name="increasing-the-max-rus"></a>增加最大 RU/s
当你发送请求以增加最大 RU/s `Tmax` 时，根据所选的最大 RU/s，服务将预配更多资源来支持更高的最大 RU/s。 当这种情况发生时，你现有的工作负载和操作不会受到影响。 系统将继续在以前的 `0.1*Tmax` 到 `Tmax` 之间缩放数据库或容器，直到 `0.1*Tmax_new` 到 `Tmax_new` 的新缩放范围就绪。

#### <a name="lowering-the-max-rus"></a>降低最大 RU/s
降低最大 RU/s 时，可设置的最小值为：`MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`，四舍五入到最接近的 1,000 RU/s。 

示例 #1：假设你有一个自动缩放容器，最大 RU/s 为 20,000 RU/s（缩放范围为 2,000 - 20,000 RU/s）且具有 50 GB 存储。 对于最大 RU/s，可设置的最低且最小的值为：MAX(4,000, 20,000 / 10, 50 * 100) = 5,000 RU/s（缩放范围为 500 - 5,000 RU/s）。

示例 #2：假设你有一个自动缩放容器，最大 RU/s 为 100,000 RU/s 且具有 100 GB 存储。 现在，可以将最大 RU/s 缩放到 150,000 RU/s（缩放范围为 15,000 - 150,000 RU/s）。 对于最大 RU/s，现可设置的最低且最小的值为：MAX(4,000, 150,000 / 10, 100 * 100) = 15,000 RU/s（缩放范围为 1,500 - 15,000 RU/s）。 

对于共享的吞吐量数据库，在降低最大 RU/s 时，可设置的最小值为：`MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`，四舍五入到最接近的 1,000 RU/s。  

上述公式和示例与可设置的最小自动缩放最大 RU/s 相关，并且不同于系统自动缩放范围（`0.1 * Tmax` 到 `Tmax`）。 不管最大 RU/s 是多少，系统总是在 `0.1 * Tmax` 到 `Tmax` 之间缩放。 

### <a name="how-does-ttl-work-with-autoscale"></a>TTL 如何与自动缩放一起工作？
使用自动缩放，TTL 操作不影响 RU/s 的缩放。 由于 TTL 而使用的任何 RU 都不属于自动缩放容器的计费 RU/s。 

例如，假设有一个缩放范围在 400 – 4000 RU/秒的自动缩放容器：
- 1 小时：T=0：容器未执行操作（没有 TTL 或工作负载请求）。 计费 RU/s 为 400 RU/s。
- 1 小时：T=1：已启用 TTL。
- 1 小时：T=2：容器开始接收请求，在 1 秒内使用 1,000 RU。 此外，还有需使用 200 RU 的 TTL 要实现。 计费 RU/s 仍然为 1,000 RU/s。 无论何时发生 TTL，它们都不会影响自动缩放逻辑。

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>最大 RU/秒和物理分区之间的映射是什么？
首次选择最大 RU/s 时，Azure Cosmos DB 将进行以下预配：最大 RU/秒/10000 RU/秒 = 物理分区的数量。 每个[物理分区](partition-data.md#physical-partitions)最多可支持 10,000 RU/s 和 50 GB 的存储。 随着存储大小的增长，Azure Cosmos DB 将自动拆分分区，以添加更多物理分区来应对存储的增长；如果存储[超过相关限制](#what-is-the-storage-limit-associated-with-each-max-rus-option)，Azure Cosmos DB 将增加最大 RU/s。 

数据库或容器的最大 RU/s 在所有物理分区之间平均分配。 因此，任何单个物理分区可扩展到的总吞吐量为：数据库或容器的最大 RU/s / 物理分区数。 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>如果传入请求超过数据库或容器的最大 RU/秒，会发生什么情况？
如果 RU/s 总使用量超过数据库或容器的最大 RU/s，则超过最大 RU/s 的请求将受到限制并返回 429 状态代码。 标准化利用率超过 100% 的请求也将受到限制。 标准化利用率定义为所有物理分区中 RU/s 利用率的最大值。 例如，假设最大吞吐量为 20,000 RU/s，并且有两个物理分区 P_1 和 P_2，每个分区都可以扩展到 10,000 RU/s。 在给定的秒数内，如果 P_1 已使用 6000 RU，而 P_2 已使用 8000 RU，则标准化利用率为 MAX(6000 RU / 10,000 RU, 8000 RU / 10,000 RU) = 0.8。

> [!NOTE]
> Azure Cosmos DB 客户端 SDK 和数据导入工具（Azure 数据工厂、批量执行程序库）会在出现 429 错误时自动重试，因此，偶尔出现 429 错误是正常的。 持续大量的 429s 可能表明需要增加最大 RU/秒，或查看针对[热分区](#autoscale-rate-limiting)的分区策略。

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> 启用自动缩放后，是否仍可看到 429s（限制/速率限制）？ 
是的。 在两个情况下可能出现 429s。 第一种，当 RU/s 总使用量超过数据库或容器的最大 RU/s 时，服务将相应地限制请求。 

第二种情况是，如果有一个热分区（即逻辑分区键值，该键值的请求数与其他分区键值的请求数相比过高），则基础物理分区可能超出其 RU/秒预算。 为避免出现热分区，最佳做法是[选择良好的分区键](partitioning-overview.md#choose-partitionkey)，从而实现存储和吞吐量的均匀分布。 

例如，如果选择 20,000 RU/s 的最大吞吐量选项，并具有 200 GB 的存储和四个物理分区，则每个物理分区最多可以自动扩展至 5000 RU/s。 如果特定逻辑分区键上有一个热分区，则当它所在的基础物理分区超过 5000 RU/s（即超过 100% 的标准化利用率）时，将显示 429 错误。


## <a name="next-steps"></a>后续步骤

* 了解如何[对 Azure Cosmos DB 数据库或容器启用自动缩放](how-to-provision-autoscale-throughput.md)。
* 了解[使用自动缩放预配吞吐量的优势](provision-throughput-autoscale.md#benefits-of-autoscale)。
* 详细了解[逻辑和物理分区](partition-data.md)。
                        
