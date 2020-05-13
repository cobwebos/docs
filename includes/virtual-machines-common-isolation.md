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
ms.openlocfilehash: 033ec0f4011cae657fc464849aac5c5d9a850907
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343314"
---
Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。  这些虚拟机大小非常适合于与其他客户的工作负载（涉及符合性和法规要求等元素）高度隔离的工作负载。  客户还可以选择利用[对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，对这些独立的虚拟机资源进一步细分。

使用独立大小可保证你的虚拟机将是在特定服务器实例上唯一运行的虚拟机。  当前的独立虚拟机产品/服务包括：
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2<sup>*</sup>
* Standard_D15_v2<sup>*</sup>
* Standard_F72s_v2

<sup>*</sup>隔离保证将在5月15日2020

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>在 2020 5 月15日停用 D15_v2/DS15_v2 隔离
**2020年2月10日更新： "隔离" 停用时间线已延长到5月15日，2020**

Azure 专用主机现已正式发布，使你能够在单租户物理服务器上运行你的组织的 Linux 和 Windows 虚拟机。 我们计划将隔离的 Azure Vm 完全替换为 Azure 专用主机。 **2020 到5月15日之后，** D15_v2/DS15_v2 Azure vm 将不再与硬件隔离。

## <a name="how-does-this-affect-me"></a>这对我有何影响？
2020 5 月15日之后，将不再为你的 D15_v2/DS15_v2 Azure 虚拟机提供隔离保障。 

## <a name="what-actions-should-i-take"></a>我应该采取什么措施？
如果不需要硬件隔离，则无需执行任何操作。 

如果需要隔离，请在5月 15 2020 日之前，你需要执行以下操作之一：

•将工作负荷[迁移](https://azure.microsoft.com/blog/introducing-azure-dedicated-host)到 Azure 专用主机。

•[请求访问](https://aka.ms/D15iRequestAccess)D15i_v2 和 DS15I_V2 Azure VM，以获得相同的性价比。 此选项仅适用于即用即付和一年保留实例方案。    

•将工作负荷[迁移](https://azure.microsoft.com/blog/resize-virtual-machines/)到另一台 Azure 隔离的虚拟机。 

有关详细信息，请参阅以下内容：

## <a name="timeline"></a>时间线
| Date | 操作 | 
| --- | --- |
| 2019 年 11 月 18 日 | D/DS15i_v2 （PAYG，1年 RI）的可用性 |
| 5月14日2020  | 第一天购买 D/DS15i_v2 1 年 RI | 
| 5月15日2020   | 删除了 D/DS15_v2 隔离保障 | 
| 5月15日2021  | 停用 D/DS15i_v2 （在2019年11月18日之前购买了 D/DS15_v2 的3年 RI 除外的所有客户）| 
| 2022年11月17日  | 当完成3年的 RIs （对于在2019年11月18日之前购买 DS15_v2 了3年 RI 的客户），停用 D/DS15i_v2 | 

## <a name="faq"></a>常见问题解答
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>问：是否要停用 D/DS15_v2 大小？
**答**：不能，仅 "隔离" 功能将被停用。 如果不需要隔离，则无需执行任何操作。

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>问：是否要停用 D/DS15i_v2 大小？
**答**：是的，大小仅在 2021 5 月15日之前可用。 对于在11月18日之前购买了3年以上 DS15_v2 的 RIs 的客户，2019到年11月 2022 17 日之前将有权访问 D/DS15i_v2。

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>问：为什么我在门户中看不到新的 D/DS15i_v2 大小？
**答**：如果你是当前的 d/DS15_v2 客户，并且想要使用新的 d/DS15i_v2 大小，请填写此[窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>问：为何我看不到新的 D/DS15i_v2 大小的配额？
**答**：如果你是当前的 d/DS15_v2 客户，并且想要使用新的 d/DS15i_v2 大小，请填写此[窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>问：是否要停用其他隔离的大小？
**答**：我们将在官方取消使用之前12个月内提供提醒。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>问：如果 vm 落在非隔离硬件上，是否会出现停机？
**答**：如果不需要隔离，则无需执行任何操作，也不会出现任何停机时间。

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>问：移动到非隔离虚拟机是否有任何成本变化？
**答**：否 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>问：我已经为 D15_v2 或 Ds15_v2 购买了1年或3年的预订实例。 如何将折扣应用于 VM 使用情况？
**答**：在2019年11月18日之前购买的 RIs 会自动将覆盖范围扩展到新的隔离 VM 系列。 

| RI |  实例大小灵活性 | 权益资格 |   
| --- | --- | --- |
|   D15_v2  |   关闭     |   D15_v2 和 D15i_v2 |    
|   D15_v2  |   启用  |   D15_v2 系列和 D15i_v2 将获得 RI 权益。 |    
|   D14_v2  |   启用  |   D15_v2 系列和 D15i_v2 将获得 RI 权益。 |    
 
同样，对于 Dsv2 系列也是如此。
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>问：我想要为 Dv2 购买额外的预订实例。 应选择哪一种？
**答**：在2019年11月18日之后购买的所有 RIs 都具有以下行为。 

| RI |  实例大小灵活性 | 权益资格 |   
| --- | --- | --- |
| D15_v2 |  关闭 |   仅 D15_v2  
| D15_v2 |  启用 |    D15_v2 系列将获得 RI 权益。 新的 D15i_v2 将不能从此 RI 类型获得 RI 权益。 | 
| D15i_v2 |     关闭 | 仅 D15i_v2 |  
| D15i_v2 |     启用  | 仅 D15i_v2 | 
 
实例大小灵活性不能应用于任何其他大小，如 D2_v2、D4_v2 或 D15_v2。 同样，对于 Dsv2 系列也是如此。  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>问：是否可以购买新的3年 RI 用于 D15i_v2 和 DS15i_v2？
**答**：遗憾的是，只有1年的 RI 可用于新购买。
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>问：我是否可以将现有 D15_v2/DS15_v2 Reserve 实例移到隔离大小的保留实例？
**答**：此操作并不是必需的，因为此权益将同时适用于隔离大小和非隔离大小。 但 Azure 将支持更改现有 D15_v2/DS15_v2 保留实例 D15i_v2/DS15i_v2。 对于所有其他 Dv2/Dsv2 保留实例，使用现有的保留实例或为隔离大小购买新的保留实例。

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>问：我是一种 Azure Service Fabric 客户，它依赖于银色或金牌。 此更改是否会影响我？
**答**：否。 即使在此更改后，Service Fabric 的[持久性层](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)提供的保证仍将继续工作。 如果出于其他原因需要物理硬件隔离，可能仍需执行上述操作之一。 
