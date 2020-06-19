---
title: 有关 Azure Cosmos DB 中自动缩放预配吞吐量的常见问题解答
description: 有关 Azure Cosmos DB 数据库和容器中自动缩放预配吞吐量的常见问题解答
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/10/2020
ms.openlocfilehash: b398f739189232f39a2fee06fc6e6ff0d53348f0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656614"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>有关 Azure Cosmos DB 中自动缩放预配吞吐量的常见问题解答

使用自动缩放预配吞吐量，Azure Cosmos DB 会根据使用情况自动管理并缩放数据库或容器的 RU/秒。 本文解答了和自动缩放有关的常见问题。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Azure Cosmos DB 中的“autopilot”和“自动缩放”之间有何区别？
“自动缩放”或“自动缩放预配吞吐量”是更新的功能名称，以前称为“autopilot”。 在最新版本的自动缩放中，我们添加了新功能，包括设置自定义最大 RU/秒和编程支持的功能。 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>在以前的 autopilot 层模型中创建的数据库或容器将发生什么情况？
新的自动缩放自定义最大 RU/s 模型自动支持通过以前的层模型创建的资源。 该层的上限将成为新的最大 RU/秒，它们会产生相同的缩放范围。 

例如，如果之前选择了缩放范围在 400 RU/秒到 4000 RU/秒的层，那么数据库或容器现在将显示最大 RU/秒为 4000 RU/秒，其缩放范围是 400 RU/秒到 4000 RU/秒。 在这里，可以将最大 RU/秒更改为自定义值，以适合自己的工作负荷。 

### <a name="how-quickly-will-autoscale-scale-up-and-down-based-on-spikes-in-traffic"></a>自动缩放根据流量峰值增加和减少的速度有多快？
使用自动缩放，系统会根据传入流量在 `0.1 * Tmax` 和 `Tmax` 范围内增加或减少吞吐量（RU/秒）`T`。 由于缩放是自动且即时的，因此任何时间都可以无延迟地消耗预配的`Tmax`。 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>如何确定系统当前扩展到的 RU/秒？
使用 [Azure Monitor 指标](how-to-choose-offer.md#measure-and-monitor-your-usage)监视预配的自动缩放最大 RU/秒和系统缩放到的当前吞吐量（RU/秒）。 

### <a name="what-is-the-pricing-for-autoscale"></a>自动缩放采用何种定价方式？
每小时都会按系统在该小时内缩放到的最高吞吐量 `T` 进行计费。 如果你的资源在该小时内没有请求，或者缩放范围未超出 `0.1 * Tmax`，则将按照 `0.1 * Tmax` 的最低费用收费。 请参考 Azure Cosmos DB [定价页面](https://azure.microsoft.com/pricing/details/cosmos-db/)了解详细信息。 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>帐单上的自动缩放速率是如何计算的？
在单主帐户中，每 100 RU/秒的自动缩放速率为标准（手动）预配吞吐量速率的 1.5 倍。 帐单上显示的是现有的标准预配吞吐量的计数。 用该计数乘以 1.5 就是自动缩放速率。 例如，如果系统在一小时内扩展到的最高 RU/秒为 6000 RU/秒，则你在该小时应付的费用为：60 * 1.5 = 90 个计量单位。

在多主帐户中，每 100 RU/秒的自动缩放速率与标准（手动）预配的多主帐户吞吐量的速率相同。 帐单上显示的是现有的多主帐户计数。 由于速率是相同的，因此，如果使用自动缩放，帐单上将显示与标准吞吐量相同的数量。

### <a name="does-autoscale-work-with-reserved-capacity"></a>自动缩放是否可用于预留容量？
是的。 购买单主帐户预留容量时，对计量的使用量应用自动缩放资源的预留折扣，即 1.5 * [特定区域的比率](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region)。 

多主帐户预留容量同样适用于自动缩放和标准（手动）预配吞吐量。 请参阅 [Azure Cosmos DB 预留容量](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>自动缩放是否可用于免费层？
是的。 在免费层，可以使用容器上的自动缩放吞吐量。 尚不支持具有自定义最大 RU/秒的自动缩放共享吞吐量数据库。 请参阅[如何对自动缩放使用免费层计费模式](understand-your-bill.md#billing-examples-with-free-tier-accounts)。

### <a name="is-autoscale-supported-for-all-apis"></a>是否所有 API 都支持自动缩放？
是的，所有 API 都支持自动缩放：Core (SQL)、Gremlin、Table、Cassandra 以及 MongoDB API。

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>多主帐户是否支持自动缩放？
是的。 添加到 Azure Cosmos DB 帐户的每个区域中都提供最大 RU/秒。 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>如何在新数据库或容器上启用自动缩放？
请参阅这篇有关[如何启用自动缩放](how-to-provision-autoscale-throughput.md)的文章。

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>是否能在现有的数据库或容器上启用自动缩放？
是的。 还可以根据需要在自动缩放与标准（手动）预配吞吐量之间切换。 目前，对于所有 API，只能使用 [Azure 门户](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container)执行这些操作。

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>如何在自动缩放与标准（手动）预配吞吐量之间迁移？
从概念上讲，更改吞吐量类型这一过程包含两个阶段。 首先，发送请求，将吞吐量设置更改为使用自动缩放或手动预配吞吐量。 在这两种情况下，系统将根据当前吞吐量设置和存储来自动确定和设置初始 RU/秒值。 在此步骤中，不接受用户提供的 RU/秒值。 然后，在更新完成后，可以[更改 RU/秒](#can-i-change-the-max-rus-on-the-database-or-container)以适合自己的工作负荷。 

**从标准（手动）预配吞吐量迁移到自动缩放**

对于容器，请使用以下公式估计初始自动缩放最大 RU/秒：``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)``，舍入为最接近的 1000 RU/秒。 实际初始自动缩放最大 RU/秒可能因帐户配置而异。

示例 #1：假设有一个容器，其中包含 10,000 RU/秒的手动预配吞吐量和 25 GB 的存储空间。 启用自动缩放后，初始自动缩放最大 RU/秒将为：10,000 RU/秒，将在 1000 - 10,000 /秒之间缩放。 

示例 #2：假设有一个容器，其中包含 50,000 RU/秒的手动预配吞吐量和 2500 GB 的存储空间。 启用自动缩放后，初始自动缩放最大 RU/秒将为：250,000 RU/秒，将在 25,000 - 250,000/秒之间缩放。 

**从自动缩放迁移到标准（手动）预配吞吐量**

初始手动预配吞吐量将等于当前的自动缩放最大 RU/秒。 

示例：假设有一个自动缩放数据库或容器，最大 RU/秒为 20,000 RU/秒（在 2000 - 20,000 RU/秒之间缩放）。 更新后以使用手动预配吞吐量时，初始吞吐量将为 20,000 RU/秒。 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>是否支持 Azure CLI 或 PowerShell 通过自动缩放功能管理数据库或容器？
目前，只能通过 Azure 门户、.NET V3 SDK、Java V4 SDK 和 Azure 资源管理器中的自动缩放功能来创建和管理资源。 目前尚不提供对 Azure CLI、PowerShell 和其他 SDK 的支持。

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>共享吞吐量数据库是否支持自动缩放？
是的，共享吞吐量数据库支持自动缩放。 若要启用此功能，请在创建数据库时选择“自动缩放”和“预配吞吐量”选项。 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>启用自动缩放后，每个共享吞吐量数据库允许的容器数量是多少？
Azure Cosmos DB 在共享吞吐量数据库中最多强制执行 25 个容器，适用于具有自动缩放或标准（手动）吞吐量的数据库。 

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>与每个最大 RU/秒选项关联的存储空间上限是多少？  
每个最大 RU/秒的存储空间上限 (GB) 为：数据库或容器的最大 RU/秒/100。 例如，如果最大 RU/秒为 20,000 RU/秒，则该资源可以支持 200 GB 的存储空间。 有关可用的最大 RU/秒和存储选项，请参阅[自动缩放限制](provision-throughput-autoscale.md#autoscale-limits)文章。 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>如果超过了与最大吞吐量关联的存储空间上限，会发生什么情况？
如果超过了与数据库或容器的最大吞吐量关联的存储空间上限，则 Azure Cosmos DB 会自动将最大吞吐量增加到可支持该级别存储的下一个最大 RU/秒。

例如，如果从最大 RU/秒 50,000 RU/秒（在 5000 - 50,000 RU/秒之间缩放）开始，则最多可存储 500 GB 的数据。 如果超过 500 GB（例如，存储现在为 600 GB），则新的最大 RU/秒将是 60,000 RU/秒（在 6000 - 60,000 RU/秒之间缩放）。

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>是否能更改数据库或容器的最大 RU/秒？ 
是的。 请参阅这篇有关如何更改最大 RU/秒的[文章](how-to-provision-autoscale-throughput.md)。 更改最大 RU/秒时，根据请求的值，这可能是一个需要一些时间才能完成的异步操作（可能最长为 4-6 小时，具体取决于所选的 RU/秒）

#### <a name="increasing-the-max-rus"></a>增加最大 RU/秒
当你发送请求以增加最大 RU/秒 `Tmax` 时，该服务将预配更多的资源来支持更高的最大 RU/秒，具体取决于所选的最大 RU/秒。 发生这种情况时，现有工作负荷和操作不会受到影响。 系统将继续在以前 `0.1*Tmax` 和 `Tmax` 之间缩放数据库或容器，直到新的缩放范围（`0.1*Tmax_new` 到 `Tmax_new`）准备就绪。

#### <a name="lowering-the-max-rus"></a>减少最大 RU/秒
减少最大 RU/秒时，最大 RU/秒的最低值可以设置为：`MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`，舍入为最接近的 1000 RU/秒。 

示例 #1：假设有一个自动缩放数据库或容器，最大 RU/秒为 20,000 RU/秒（缩放范围：2000 - 20,000 RU/秒），存储空间为 50 GB。 最大 RU/秒设置的最低值可以设置为：MAX(4000, 20,000 / 10, **50 * 100**) = 5000 RU/秒（缩放范围：500 - 5000 RU/秒）。

示例 #2：假设有一个自动缩放数据库或容器，最大 RU/秒为 100,000 RU/秒，存储空间为 100 GB。 现在，最多可将最大 RU/秒增加到 150,000 RU/秒（缩放范围：15,000 - 150,000 RU/秒）。 现在最大 RU/秒的最低值可以设置为：MAX(4000, **150,000 / 10**, 100 * 100) = 15,000 RU/秒（缩放范围：1500 - 15,000 RU/秒）。 

对于共享吞吐量数据库，减少最大 RU/秒时，最大 RU/秒的最低值可以设置为：`MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`，舍入为最接近的 1000 RU/秒。  

以上公式和示例与可设置的自动缩放最大 RU/秒的最小值相关，与系统自动缩放的范围（`0.1 * Tmax` 到 `Tmax`）不同。 无论最大 RU/秒是什么，系统都将始终在 `0.1 * Tmax` 和 `Tmax` 之间缩放数据库或容器。 

### <a name="how-does-ttl-work-with-autoscale"></a>TTL 如何与自动缩放配合使用？
使用自动缩放时，TTL 操作不会影响 RU/秒的缩放。 由于 TTL 操作而使用的任何 RU 都不属于自动缩放容器的计费 RU/秒。 

例如，假设有一个缩放范围在 400 – 4000 RU/秒的自动缩放容器：
- 1 小时：T = 0：容器没有使用量（没有 TTL 或工作负荷请求）。 计费 RU/秒为 400 RU/秒。
- 1 小时：T = 1：已启用 TTL。
- 1 小时：T = 2：容器开始获取请求，这会在 1 秒内使用 1000 RU。 TTL 操作还需要 200 RU。 计费 RU/秒仍为1000 RU/秒。 无论 TTL 何时发生，它们都不会影响自动缩放的缩放逻辑。

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>最大 RU/秒和物理分区之间的映射是什么？
首次选择最大 RU/秒时，Azure Cosmos DB 将预配：最大 RU/秒/10000 RU/秒 = 物理分区的数量。 每个[物理分区](partition-data.md#physical-partitions)最多可支持 10,000 RU/秒和 50 GB 的存储空间。 随着存储大小的增长，Azure Cosmos DB 会自动拆分分区，以便添加更多物理分区来处理存储增加情况，如果存储[超过关联的存储上限](#what-is-the-storage-limit-associated-with-each-max-rus-option)，则增加最大 RU/秒值。 

数据库或容器的最大 RU/秒在所有物理分区之间平均划分。 因此，任何单个物理分区可扩展到的总吞吐量为：数据库或容器的最大 RU/秒/物理分区的数量。 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>如果传入请求超过数据库或容器的最大 RU/秒，会发生什么情况？
如果整体使用的 RU/秒超过了数据库或容器的最大 RU/秒，超出最大 RU/秒的请求将受到限制，并返回 429 状态代码。 标准化利用率超过 100% 的请求也将受到限制。 标准化利用率为所有物理分区的 RU/秒利用率的最大值。 例如，假设最大吞吐量为 20,000 RU/秒，并且有两个物理分区 P_1 和 P_2，每个分区都可以扩展到 10,000 RU/秒。 在给定的秒内，如果 P_1 使用了 6000 RU，而 P_2 使用了 8000 RU，则标准化利用率为 MAX(6000 RU / 10,000 RU, 8000 RU / 10,000 RU)，即 0.8。

> [!NOTE]
> Azure Cosmos DB 客户端 SDK 和数据导入工具（Azure 数据工厂、批量执行工具库）在 429s 上自动重试，因此偶尔的 429s 是正常的。 持续大量的 429s 可能表明需要增加最大 RU/秒，或查看针对[热分区](#autoscale-rate-limiting)的分区策略。

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> 启用自动缩放后，是否仍可看到 429s（限制/速率限制）？ 
是的。 在两个情况下可能出现 429s。 第一种情况是，当总体消耗的 RU/秒超过数据库或容器的最大 RU/秒时，服务将相应地限制请求。 

第二种情况是，如果有一个热分区（即逻辑分区键值，该键值的请求数与其他分区键值的请求数相比过高），则基础物理分区可能超出其 RU/秒预算。 为避免出现热分区，最佳做法是[选择良好的分区键](partitioning-overview.md#choose-partitionkey)，从而实现存储和吞吐量的均匀分布。 

例如，如果选择“20,000 RU/秒最大吞吐量”选项，并且 200 GB 的存储空间包含 4 个物理分区，则每个物理分区最多可以自动扩展至 5000 RU/秒。 如果某个特定逻辑分区键上存在热分区，则当它所在的基础物理分区超过 5000 RU/秒（即超出了 100% 的标准化利用率）时，将会出现 429s。


## <a name="next-steps"></a>后续步骤

* 了解如何[对 Azure Cosmos DB 数据库或容器启用自动缩放](how-to-provision-autoscale-throughput.md)。
* 了解[自动缩放预配吞吐量的优势](provision-throughput-autoscale.md#benefits-of-autoscale)。
* 详细了解[逻辑和物理分区](partition-data.md)。
                        