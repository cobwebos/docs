---
title: 使用 Azure 配对区域确保业务连续性 & 灾难恢复
description: 使用 Azure 区域配对确保应用程序复原
author: barbkess
manager: barbkess
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: barbkess
ms.openlocfilehash: be6d2fb590dfefb170b3f23378c507b4d6b2295d
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980042"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>业务连续性和灾难恢复 (BCDR)：Azure 配对区域

## <a name="what-are-paired-regions"></a>什么是配对区域？

Azure 区域包含一组在延迟定义的外围部署的数据中心，并通过专用的低延迟网络进行连接。  这可确保 Azure 区域中的 Azure 服务提供最佳的性能和安全性。  

Azure 地理位置定义了一个世界区域，其中包含至少一个 Azure 区域。 地理位置定义了一种离散市场，通常包含两个或多个区域，这些区域保留数据驻留和符合性边界。  [在此处](https://azure.microsoft.com/global-infrastructure/regions/)查找有关 Azure 全球基础结构的详细信息

区域对由同一地理区域中的两个区域组成。 Azure 跨区域对序列化平台更新（计划内维护），确保每个对中的一个区域一次只能更新一次。 如果中断影响多个区域，则每个对中的至少一个区域将优先进行恢复。

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

某些 Azure 服务会进一步利用配对区域，以确保业务连续性并防范数据丢失。  Azure 提供了多个[存储解决方案](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region)，这些解决方案利用配对的区域来确保数据的可用性。 例如， [Azure 异地冗余存储](./storage/common/storage-redundancy.md#geo-redundant-storage)（GRS）会自动将数据复制到次要区域，确保即使在主要区域无法恢复的情况下，数据也是持久的。 

请注意，并非所有 Azure 服务都会自动复制数据，也不会将所有 Azure 服务自动从失败的区域回滚到其对。  在这种情况下，客户必须配置恢复和复制。

## <a name="can-i-select-my-regional-pairs"></a>能否选择区域对？

不是。 某些 Azure 服务依赖于区域对（如 Azure[冗余存储](./storage/common/storage-redundancy.md)）。 这些服务不允许你创建新的区域配对。  同样，由于 Azure 控制区域对的计划维护和恢复优先级，因此你不能定义自己的区域对以利用这些服务。 但是，你可以创建自己的灾难恢复解决方案，方法是在任意数量的区域中生成服务，并利用 Azure 服务对其进行配对。 

例如，你可以使用 Azure 服务（如[AzCopy](./storage/common/storage-use-azcopy-v10.md) ）将数据备份到其他区域中的存储帐户。  使用[Azure DNS 和 Azure 流量管理器](./networking/disaster-recovery-dns-traffic-manager.md)，客户可以为其应用程序设计可在主要区域丢失的情况下提供弹性体系结构。

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>我是否限制使用区域对中的服务？

不是。 虽然给定的 Azure 服务可能依赖于区域对，但你可以在满足业务需求的任何区域中托管其他服务。  使用美国东部的计算资源，Azure GRS 存储解决方案可以将加拿大中部的数据与加拿大东部的对等互连。  

## <a name="must-i-use-azure-regional-pairs"></a>是否必须使用 Azure 区域对？

不是。 客户可以利用 Azure 服务来构建可复原的服务，而无需依赖 Azure 的区域对。  但是，我们建议跨区域对配置业务连续性灾难恢复（BCDR），以便从[隔离](./security/fundamentals/isolation-choices.md)和提高[可用性](./availability-zones/az-overview.md)中获益。 对于支持多个活动区域的应用程序，建议尽可能使用区域对中的这两个区域。 这可确保应用程序的最佳可用性，并确保在发生灾难时最短的恢复时间。 尽可能设计你的应用程序以实现[最大](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)的恢复能力和简化[灾难恢复](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)。

## <a name="azure-regional-pairs"></a>Azure 区域对

| 地理位置 | 区域对 A | 区域对 B  |
|:--- |:--- |:--- |
| 亚太地区 |东亚（香港特别行政区） | 东南亚（新加坡） |
| 澳大利亚 |澳大利亚东部 |澳大利亚东南部 |
| 澳大利亚 |澳大利亚中部 |澳大利亚中部 2 |
| 巴西 |巴西南部 |美国中南部 |
| Canada |加拿大中部 |加拿大东部 |
| 中国 |中国北部 |中国东部|
| 中国 |中国北部 2 |中国东部 2|
| 欧洲 |北欧（爱尔兰） |西欧（荷兰） |
| 法国 |法国中部|法国南部|
| 德国 |德国中部 |德国东北部 |
| 印度 |印度中部 |印度南部 |
| 印度 |印度西部 |印度南部 |
| 日本 |日本东部 |日本西部 |
| 韩国 |韩国中部 |韩国南部 |
| 北美 |美国东部 |美国西部 |
| 北美 |美国东部 2 |美国中部 |
| 北美 |美国中北部 |美国中南部 |
| 北美 |美国西部 2 |美国中西部 |
| 挪威 | 挪威东部 | 挪威西部 |
| 南非 | 南非北部 |南非西部 |
| 瑞士 | 瑞士北部 |瑞士西部 |
| 英国 |英国西部 |英国南部 |
| 阿拉伯联合酋长国 | 阿拉伯联合酋长国北部 | 阿联酋中部
| 美国国防部 |US DoD 东部 |US DoD 中部 |
| 美国政府 |US Gov 亚利桑那州 |US Gov 德克萨斯州 |
| 美国政府 |US Gov 爱荷华州 |US Gov 弗吉尼亚州 |
| 美国政府 |US Gov 弗吉尼亚州 |US Gov 德克萨斯州 |

> [!Important]
> - 印度西部仅成对一个方向配对。 印度西部的次要区域是印度南部，而印度南部的次要区域是印度中部。
> - 巴西南部是唯一的，因为它与地理位置外部的区域配对。 巴西南部的次要区域是美国中南部。 美国中南部的次要区域不是巴西南部。


## <a name="an-example-of-paired-regions"></a>配对区域的示例
下图显示了使用区域对进行灾难恢复的虚构应用程序。 绿色数字突出显示了三个 Azure 服务（Azure 计算、存储和数据库）的跨区域活动，以及如何将它们配置为跨区域复制。 橙色数字突出显示了跨配对区域部署的独特优势。

![配对区域的优势概览](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

图 2 – 虚构的 Azure 区域对

## <a name="cross-region-activities"></a>跨区域活动
如图 2 所示。

1. **Azure 计算（IaaS）** –必须事先预配额外的计算资源，以确保在发生灾难时其他区域中的资源可用。 有关详细信息，请参阅 [Azure resiliency technical guidance](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md)（Azure 复原技术指南）。 

2. **Azure 存储**-如果使用托管磁盘，请使用 Azure 备份了解[跨区域备份](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines)，并使用 Azure Site Recovery 将[vm](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)从一个区域复制到另一个区域。 如果使用的是存储帐户，则在创建 Azure 存储帐户时，将默认配置异地冗余存储（GRS）。 使用 GRS 时，数据会在主要区域自动复制三次，并在配对区域复制三次。 有关详细信息，请参阅 [Azure 存储冗余选项](storage/common/storage-redundancy.md)。

3. **AZURE Sql 数据库**-利用 Azure Sql 数据库异地复制，可以配置将事务异步复制到世界上任何区域的情况;但是，对于大多数灾难恢复方案，我们建议在配对区域中部署这些资源。 有关详细信息，请参阅 [Azure SQL 数据库中的异地复制](sql-database/sql-database-geo-replication-overview.md)。

4. **Azure 资源管理器** - 资源管理器原本就能跨区域提供组件的逻辑隔离。 这意味着某个区域发生逻辑故障不太可能会影响另一个区域。

## <a name="benefits-of-paired-regions"></a>配对区域的优势

5. **物理隔离**-如果可能，Azure 首选区域对中的数据中心之间至少300英里的距离，但在所有地理区域中，这并不是可行的或可能的。 物理数据中心隔离能够降低自然灾害、社会动乱、电力中断或物理网络中断同时影响两个区域的可能性。 隔离受限于地理位置的约束（地理位置大小、电源/网络基础结构可用性、法规，等等）。  

6. **平台提供的复制**-某些服务（例如异地冗余存储）提供自动复制到配对区域。

7. **区域恢复顺序**–如果发生很多中断，则从每个对中恢复一个区域的优先级。 可以保证跨配对区域部署的应用程序在其中一个区域中优先恢复。 如果应用程序部署在未配对的区域中，则可能会发生恢复延迟；最坏的情况是，选中的两个区域可能最后才会得到恢复。

8. **顺序更新**–计划的 Azure 系统更新将按顺序（而不是同时）向配对区域推出，以最大程度地减少停机时间、bug 影响和逻辑故障，这种情况极少导致错误更新。

9. **数据驻留**–一个区域驻留在与其对相同的地理位置（巴西南部除外），以满足税务和执法管辖目的的数据驻留要求。
