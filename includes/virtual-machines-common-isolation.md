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
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084037"
---
Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。 在硬件生成停用时，隔离大小并对特定硬件生成进行操作并将被弃用。

隔离的虚拟机大小最适用于需要与其他客户的工作负荷高度隔离的工作负荷，原因包括满足符合性和法规要求。  使用独立大小可保证你的虚拟机将是在特定服务器实例上唯一运行的虚拟机。 


此外，随着虚拟机的大小较大，客户可以选择使用[Azure 支持嵌套虚拟机](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)来细分这些 vm 的资源。

当前的独立虚拟机产品/服务包括：
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_F72s_v2

> [!NOTE]
> 独立 VM 大小具有硬件有限生命周期。 请参阅下面的详细信息

## <a name="deprecation-of-isolated-vm-sizes"></a>弃用隔离的 VM 大小
由于隔离 VM 大小为硬件绑定大小，Azure 将在官方否决大小之前的12个月内提供提醒。  此外，Azure 还会在我们的下一个硬件版本上提供一个已更新的隔离大小，客户可以考虑将工作负荷转移到该版本。

| 大小 | 隔离停用日期 | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 5月15日2020 |
| Standard_D15_v2<sup>1</sup>  | 5月15日2020 |

<sup>1</sup>有关 Standard_DS15_v2 和 Standard_D15_v2 隔离停用计划的详细信息，请参阅 faq


## <a name="faq"></a>常见问题
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>问：是要停用的大小，还是只有 "隔离" 功能？
**答**：如果虚拟机大小不包含 "i" 下标，则只会停用 "隔离" 功能。 如果不需要隔离，则没有要执行的操作，VM 将继续按预期方式工作。 例如 Standard_DS15_v2、Standard_D15_v2、Standard_M128ms 等。如果虚拟机大小包含 "i" 下标，则其大小将变为 "已停用"。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>问：当我的虚拟机落脚于非隔离的硬件上时，是否会出现停机？
**答**：如果无需隔离，则无需执行任何操作，也不会造成停机。

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>问：移动到非隔离虚拟机是否有任何成本增量？
**答**：否

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>问：其他独立大小将于何时停用？
**答**：我们将在官方弃用的隔离大小提前12个月后提供提醒。

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>问：我是依赖于白银或黄金耐久性层级的 Azure Service Fabric 客户。 此更改是否会影响我？
**答**：否。 Service Fabric 的[耐久性层级](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)提供的保证即使在此更改发生后也将继续履行。 如果你出于其他原因而需要物理硬件隔离，可能仍需采取上述措施之一。 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>问： D15_v2 或 DS15_v2 隔离停用的里程碑有哪些？ 
**A**： 
| 日期 | 操作 | 
| --- | --- |
| 2019 年 11 月 18 日 | D/DS15i_v2 （PAYG，1年 RI）的可用性 |
| 5月14日2020  | 第一天购买 D/DS15i_v2 1 年 RI | 
| 5月15日2020   | 删除了 D/DS15_v2 隔离保障 | 
| 5月15日2021  | 停用 D/DS15i_v2 （在2019年11月18日之前购买了 D/DS15_v2 的3年 RI 除外的所有客户）| 
| 2022年11月17日  | 当完成3年的 RIs （对于在2019年11月18日之前购买 DS15_v2 了3年 RI 的客户），停用 D/DS15i_v2 | 
