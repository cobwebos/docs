---
title: include 文件
description: include 文件
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 4f6a6dd28662c98c89dad39da3fd5c967f227dc3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87084037"
---
Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。 独立大小在特定的硬件生成上有效并运行，当硬件生成失效时，将弃用。

独立的虚拟机大小最适合于由于满足符合性和法规要求等原因而需要与其他客户的工作负载高度隔离的工作负载。  使用独立大小可保证你的虚拟机将是在特定服务器实例上唯一运行的虚拟机。 


另外，由于独立大小的 VM 很大，客户可以选择使用 [对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)来细分这些 VM 的资源。

当前的独立虚拟机产品/服务包括：
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_F72s_v2

> [!NOTE]
> 独立的 VM 大小具有有限的硬件寿命。 详情请参阅下文

## <a name="deprecation-of-isolated-vm-sizes"></a>弃用独立的 VM 大小
由于独立的 VM 大小是硬件绑定的大小，Azure 将在正式弃用这些大小之前 12 个月提供提醒。  Azure 还将为我们的下一个硬件版本提供已更新的独立大小，客户可以考虑将其工作负载转移到该版本上。

| 大小 | 隔离停用日期 | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 2020 年 5 月 15 日 |
| Standard_D15_v2<sup>1</sup>  | 2020 年 5 月 15 日 |

<sup>1</sup> 有关 Standard_DS15_v2 和 Standard_D15_v2 隔离停用计划的详细信息，请参阅常见问题解答


## <a name="faq"></a>常见问题解答
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>问：是要停用大小还是只停用“隔离”功能？
**答**：如果虚拟机大小没有“i”下标，则只有“隔离”功能将失效。 如果不需要隔离，则不需要执行任何操作，VM 将继续按预期工作。 例如 Standard_DS15_v2、Standard_D15_v2、Standard_M128ms 等。如果虚拟机大小包括“i”下标，那么该大小将被停用。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>问：当我的虚拟机落脚于非隔离的硬件上时，是否会出现停机？
**答**：如果不需要隔离，就不需要采取任何行动，也不会有停机时间。

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>问：迁移到非独立的虚拟机是否有成本增量？
**答**：否

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>问：其他独立大小将于何时停用？
**答**：我们将提前 12 个月进行提醒，以防官方弃用孤立的大小。

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>问：我是依赖于白银或黄金耐久性层级的 Azure Service Fabric 客户。 此更改是否会影响我？
**答**：否。 Service Fabric 的[耐久性层级](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)提供的保证即使在此更改发生后也将继续履行。 如果你出于其他原因而需要物理硬件隔离，可能仍需采取上述措施之一。 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>问： D15_v2 或 DS15_v2 隔离停用的里程碑有哪些？ 
**A**： 
| Date | 操作 | 
| --- | --- |
| 2019 年 11 月 18 日 |  (PAYG，1年 RI 的 DS15i_v2 可用性)  |
| 5月14日2020  | 第一天购买 D/DS15i_v2 1 年 RI | 
| 2020 年 5 月 15 日   | 删除了 D/DS15_v2 隔离保障 | 
| 5月15日2021  |  (所有客户（在) 2019 年11月18日之前购买了第3年 DS15_v2 的第3年 DS15i_v2| 
| 2022年11月17日  | 当在2019年11月18日之前购买 DS15_v2 了3年版的第3年 RI 的客户 (，在完成了3年的 RIs 后，停用了 D/DS15i_v2)  | 
