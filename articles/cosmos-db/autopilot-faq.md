---
title: Azure Cosmos DB autopilot 模式下的吞吐量常见问题
description: 有关 Azure Cosmos DB 数据库和容器的 autopilot 模式的常见问题
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483305"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>有关预配吞吐量的常见问题 Azure Cosmos DB autopilot mode （预览版）
使用 autopilot 模式时，Azure Cosmos DB 会根据使用情况自动管理和缩放容器或数据库的 RU/秒。 本文解答了有关 autopilot 模式的常见问题。 

## <a name="frequently-asked-questions"></a>常见问题

### <a name="is-autopilot-mode-supported-for-all-apis"></a>所有 Api 是否支持 autopilot 模式？
是的，所有 Api 都支持 autopilot 模式：核心（SQL）、Gremlin、表、Cassandra 和适用于 MongoDB 的 API。

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>多主机帐户是否支持 autopilot 模式？
是的，多主机帐户支持 autopilot 模式。 添加到 Cosmos 帐户的每个区域中都提供最大 RU/秒。 

### <a name="what-is-the-pricing-for-autopilot"></a>Autopilot 的定价是什么？
有关详细信息，请参阅 Azure Cosmos DB[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>如何实现为容器或数据库启用 autopilot？
可以在新的容器和使用 Azure 门户创建的数据库上启用 Autopilot 模式。 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>是否存在用于创建 autopilot 模式的容器或数据库的 CLI 或 SDK 支持？
目前，在预览版中，只能通过 Azure 门户创建 autopilot 模式的资源。 对 CLI 和 SDK 的支持尚不可用。

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>能否对现有容器或数据库启用 autopilot？
目前，创建 autopilot 时，可以在新的容器和数据库上启用它。 目前尚不支持在现有容器和数据库上启用 autopilot 模式。 可以使用[Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)或[更改源](change-feed.md)将现有容器迁移到新的容器。 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>能否在容器或数据库上关闭 autopilot 模式？
是的，可以通过切换到预配的吞吐量的 "手动" 选项来关闭 autopilot。 在预览版本中，从 autopilot 模式切换到手动模式后，不能再次为同一资源启用 autopilot。 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>共享吞吐量数据库是否支持 autopilot 模式？
是的，共享吞吐量数据库支持 autopilot 模式。 若要启用此功能，请在创建数据库时选择 autopilot 模式和 "**设置吞吐量**" 选项。 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>启用 autopilot 时，每个共享吞吐量数据库允许的集合数是多少？
对于启用了 autopilot 模式的共享吞吐量数据库，允许的集合数为：最小值（25，最大 RU/秒，即 database/1000）。 例如，如果数据库的最大吞吐量为 20000 RU/s，则数据库可以有 MIN （25，20000 RU/s/1000） = 20 个集合。 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>每个最大 RU/秒选项关联的存储限制是多少？  
每个最大 RU/秒的存储限制（以 GB 为单位）为：数据库或容器/100 的最大 RU/秒。 例如，如果最大 RU/秒为 20000 RU/s，则该资源可以支持 200 GB 的存储。 请参阅[autopilot 限制](provision-throughput-autopilot.md#autopilot-limits)一文，了解可用的最大 RU/秒和存储选项。 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>如果我超过了与最大吞吐量关联的存储限制，会发生什么情况？
如果超过了与数据库或容器的最大吞吐量关联的存储限制，则 Azure Cosmos DB 会自动将最大吞吐量增加到可支持该级别存储的下一个最高层。 例如，假设已使用 4000 RU/s "最大吞吐量" 选项设置数据库或容器，该选项的存储限制为 50 GB。 如果资源的存储增加到 100 GB，则数据库或容器的最大 RU/秒将自动增加到 20000 RU/秒，最大可支持 200 GB。 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Autopilot 会根据流量高峰增加和减少速度？
Autopilot 将根据传入流量，在最小和最大 RU/s 范围内即时增加和减少 RU/秒。 计费以1小时为间隔完成，在这种情况下，将按特定时间的最大 RU 数收费。 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>是否可以为 autopilot 模式指定自定义的最大吞吐量（RU/s）？
目前，在预览版本中，你可以在[四个选项](provision-throughput-autopilot.md#autopilot-limits)之间进行选择，以实现最大吞吐量（RU/s）。

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>是否可以增加数据库或容器上的最大 RU/秒（移到更高的层）？ 
可以。 在容器的 "**缩放 & 设置**" 选项或数据库的 "**缩放**" 选项中，可以为 "autopilot" 选择更高的最大 RU/秒。 这是一个异步向上扩展操作，可能需要一段时间才能完成（通常为4-6 小时，具体取决于所选的 RU/s），因为该服务预配更多资源以支持更高的规模。 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>是否可以减少数据库或容器上的最大 RU/秒（移动到较低的层）？
可以。 只要数据库或容器的当前存储小于与要缩减到的最大 RU/s 层关联的[存储限制](#what-is-the-storage-limit-associated-with-each-max-rus-option)，你就可以将最大 ru/秒减少到该层。 例如，如果你选择了 20000 RU/s 作为最大 RU/s，则如果你的存储小于 50 GB （与 4000 RU/s 关联的存储限制），可以将最大 RU/秒缩小到 4000 RU/秒。

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>最大 RU/秒和物理分区之间的映射是什么？
首次选择最大 RU/秒时，Azure Cosmos DB 将预配：最大 RU/秒/10000 RU/秒 = # 个物理分区。 每个[物理分区](partition-data.md#physical-partitions)最多可以支持 10000 RU/s 和 50 GB 存储。 随着存储大小的增长，Azure Cosmos DB 会自动拆分分区以添加更多物理分区来处理存储增加，如果存储[超出关联的限制](#what-is-the-storage-limit-associated-with-each-max-rus-option)，则增加最大 RU/秒层。 

数据库或容器的最大 RU/秒在所有物理分区之间平均划分。 因此，任何单个物理分区可扩展到的总吞吐量为：数据库或容器/# 物理分区的最大 RU/秒。 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>如果传入请求超出了数据库或容器的最大 RU/秒，会发生什么情况？
如果整体消耗的 RU/s 超过了容器或数据库的最大 RU/秒，将限制超出最大 RU/秒的请求，并返回429状态代码。 导致超过100% 标准化利用率的请求也将受到限制。 规范化利用率定义为所有物理分区的 RU/s 利用率的最大值。 例如，假设最大吞吐量为 20000 RU/s，并且有两个物理分区 P_1 和 P_2，每个分区都可以扩展到 10000 RU/秒。 在给定的秒内，如果 P_1 使用了 6000 ru，而 P_2 8000 ru，则标准化利用率为 MAX （6000 RU/10000 RU，8000 RU/10000 RU） = 0.8。

> [!NOTE]
> Azure Cosmos DB 客户端 Sdk 和数据导入工具（Azure 数据工厂、大容量执行程序库）在429s 上自动重试，因此偶尔的429s 是正常的。 持续大量的429s 可能表明你需要增加最大 RU/秒或查看[热分区](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled)的分区策略。

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>启用 autopilot 后，是否仍可查看429s （限制/速率限制）？ 
可以。 在两个方案中，可以看到429s。 第一种情况是，当全部使用的 RU/秒超过容器或数据库的最大 RU/秒时，服务将相应地限制请求。 

第二种情况下，如果有一个热分区，即一个逻辑分区键值，该键值的请求数比其他分区键值还多了，则基础物理分区可能超出其 RU/秒的预算。 为避免热分区，最佳做法是[选择一个适当的分区键](partitioning-overview.md#choose-partitionkey)，以便实现存储和吞吐量的均匀分布。 

例如，如果选择 "20000 RU/秒最大吞吐量" 选项，并且200具有4个物理分区，则每个物理分区最多可以自动缩放到 5000 RU/秒。 如果某个特定逻辑分区键上存在热分区，则当它所在的底层物理分区超过 5000 RU/s 时，将会看到429s，即超出了100% 的规范化利用率。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure Cosmos 容器或数据库上启用 autopilot](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode)。
* 了解 autopilot 的[优势](provision-throughput-autopilot.md#benefits-of-autopilot-mode)。
* 了解有关[逻辑分区和物理分区](partition-data.md)的详细信息。
