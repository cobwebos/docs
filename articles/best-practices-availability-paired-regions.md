---
title: 使用 Azure 配对区域确保业务连续性和灾难恢复
description: 使用 Azure 区域配对确保应用程序复原能力
author: barbkess
manager: barbkess
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: barbkess
ms.custom: references_regions
ms.openlocfilehash: b720d9dd824018d885ccc9860ee9fd8a90a46051
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84194314"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>业务连续性和灾难恢复 (BCDR)：Azure 配对区域

## <a name="what-are-paired-regions"></a>什么是配对区域？

Azure 区域由一组数据中心组成，这些数据中心部署在定义了延迟的外围内，并通过专用的低延迟网络进行连接。  这可确保 Azure 区域中的 Azure 服务提供尽可能最佳的性能和安全性。  

Azure 地理位置定义了至少包含一个 Azure 区域的世界区域。 地理位置定义了一种离散市场，通常包含两个或更多区域，这些区域会保留数据驻留和符合性边界。  [在此处](https://azure.microsoft.com/global-infrastructure/regions/)查找有关 Azure 全球基础结构的详细信息

区域对由同一地理位置中的两个区域组成。 Azure 会跨区域对序列化平台更新（计划内维护），以确保一次仅更新每对中的一个区域。 如果中断影响多个区域，则每对中的至少一个区域将优先进行恢复。

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

某些 Azure 服务会进一步利用配对区域，以确保业务连续性并防止数据丢失。  Azure 提供了几种[存储解决方案](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region)，这些解决方案利用配对区域来确保数据可用性。 例如，[Azure 异地冗余存储](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) 会自动将数据复制到次要区域，确保即使在主要区域不可恢复的情况下，数据也是持久的。 

请注意，并不是所有 Azure 服务都会自动复制数据，也不是所有 Azure 服务都会自动从故障区域回退到其配对区域。  在这种情况下，客户必须配置恢复和复制。

## <a name="can-i-select-my-regional-pairs"></a>能否选择自己的区域对？

否。 某些 Azure 服务依赖于区域对，例如 Azure [冗余存储](./storage/common/storage-redundancy.md)。 这些服务不允许你创建新的区域配对。  同样，由于 Azure 控制区域对的计划内维护和恢复优先级，因此你不能定义自己的区域对以利用这些服务。 但是，你可以通过在任意数量的区域中生成服务并利用 Azure 服务对这些区域进行配对来创建自己的灾难恢复解决方案。 

例如，可以使用 Azure 服务（如 [AzCopy](./storage/common/storage-use-azcopy-v10.md)）对数据备份进行计划，将数据备份到其他区域中的存储帐户。  使用 [Azure DNS 和 Azure 流量管理器](./networking/disaster-recovery-dns-traffic-manager.md)，客户可以为其应用程序设计一个在主要区域丢失后会继续存在的弹性体系结构。

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>我是否被限制在我的区域对中使用服务？

否。 虽然给定的 Azure 服务可能依赖于区域对，但你可以在满足业务需求的任何区域中托管其他服务。  使用美国东部的计算资源，Azure GRS 存储解决方案可以将加拿大中部的数据与加拿大东部的对等互连。  

## <a name="must-i-use-azure-regional-pairs"></a>是否必须使用 Azure 区域对？

否。 客户可以利用 Azure 服务来构建可复原的服务，无需依赖 Azure 的区域对。  但是，我们建议跨区域对配置业务连续性灾难恢复（BCDR），以便从[隔离](./security/fundamentals/isolation-choices.md)和提高[可用性](./availability-zones/az-overview.md)中获益。 对于支持多个活动区域的应用程序，我们建议尽可能使用区域对中的这两个区域。 这可确保应用程序的最佳可用性，并在发生灾难时最大限度地缩短恢复时间。 尽可能设计你的应用程序以实现[最大](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)的恢复能力和简化[灾难恢复](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)。

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
下图显示了一个使用区域对进行灾难恢复的虚构应用程序。 绿色数字突出显示了三个 Azure 服务（Azure 计算、存储和数据库）的跨区域活动，以及这些服务如何配置为跨区域复制。 橙色数字突出显示了跨配对区域部署的独特优势。

![配对区域的优势概览](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

图 2 – 虚构的 Azure 区域对

## <a name="cross-region-activities"></a>跨区域活动
如图 2 所示。

1. **Azure 计算 (IaaS)** - 必须提前预配附加的计算资源，以确保在发生灾难期间另一个区域可以提供资源。 有关详细信息，请参阅 [Azure 复原技术指南](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md)。 

2. **Azure 存储**-如果使用托管磁盘，请使用 Azure 备份了解[跨区域备份](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines)，并使用 Azure Site Recovery 将[vm](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)从一个区域复制到另一个区域。 如果使用的是存储帐户，则在创建 Azure 存储帐户时默认配置异地冗余存储 (GRS)。 使用 GRS 时，数据在主要区域自动复制三次，并在配对区域复制三次。 有关详细信息，请参阅 [Azure 存储冗余选项](storage/common/storage-redundancy.md)。

3. **Azure SQL 数据库** - 使用 Azure SQL 数据库异地复制，可以将事务的异步复制配置到全球任何区域；但是，我们建议在配对区域中为大多数灾难恢复方案部署这些资源。 有关详细信息，请参阅 [Azure SQL 数据库中的异地复制](sql-database/sql-database-geo-replication-overview.md)

4. **Azure 资源管理器** - 资源管理器原本就能提供对跨区域的组件进行逻辑隔离的功能。 这意味着某个区域发生逻辑故障不太可能会影响另一个区域。

## <a name="benefits-of-paired-regions"></a>配对区域的优势

5. **物理隔离** - Azure 希望区域对中数据中心之间的距离尽量至少保持 300 英里，但要在所有地理位置满足这个要求并不实际，也不可能。 物理数据中心隔离能够降低自然灾害、社会动乱、电力中断或物理网络中断同时影响两个区域的可能性。 隔离受限于地理位置的约束（地理位置大小、电源/网络基础结构可用性、法规，等等）。  

6. **平台提供的复制** - 某些服务（例如异地冗余存储）提供自动复制到配对区域的功能。

7. **区域恢复顺序** - 如果发生广泛的中断，会优先恢复每个对中的一个区域。 可以保证跨配对区域部署的应用程序在其中一个区域中优先恢复。 如果应用程序部署在未配对的区域中，则可能会发生恢复延迟；最坏的情况是，选中的两个区域可能最后才会得到恢复。

8. **依序更新** - 计划的 Azure 系统更新将按顺序发布到配对区域（不是同时发布），以便在出现罕见的更新失败时，将停机时间、Bug 影响和逻辑故障的可能性降到最低。

9. **数据驻留** - 一个区域驻留在与其配对区域相同的地理位置（巴西南部除外），以符合税务和执法管辖范围方面的数据驻留要求。
