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
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123223"
---
Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。  这些虚拟机大小非常适合于与其他客户的工作负载（涉及符合性和法规要求等元素）高度隔离的工作负载。  客户还可以选择利用[对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，对这些独立的虚拟机资源进一步细分。

使用独立大小可保证你的虚拟机将是在特定服务器实例上唯一运行的虚拟机。  当前的独立虚拟机产品/服务包括：
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

你可以[在这里](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)了解更多关于每个可用的独立大小。

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>2020年5月15日退休D15_v2/DS15_v2隔离
**2020 年 2 月 10 日更新："隔离"退休时间表已延长至 2020 年 5 月 15 日"**

Azure 专用主机现在是 GA，它允许您在单租户物理服务器上运行组织的 Linux 和 Windows 虚拟机。 我们计划用 Azure 专用主机完全替换隔离的 Azure VM。 **2020 年 5 月 15**日之后，D15_v2/DS15_v2 Azure VM 将不再与硬件隔离。

## <a name="how-does-this-affect-me"></a>这对我有何影响？
2020 年 5 月 15 日之后，我们将不再为D15_v2/DS15_v2 Azure 虚拟机提供隔离保证。 

## <a name="what-actions-should-i-take"></a>我应该采取什么行动？
如果不需要硬件隔离，则无需执行任何操作。 

如果您在 2020 年 5 月 15 日之前需要隔离，则需要：

• 将工作负荷[迁移到](https://azure.microsoft.com/blog/introducing-azure-dedicated-host)Azure 专用主机。

•[请求访问](https://aka.ms/D15iRequestAccess)D15i_v2并DS15i_v2 Azure VM，以获得相同的性价比。 此选项仅适用于即用即付和一年预留实例方案。    

• 将工作负荷[迁移到](https://azure.microsoft.com/blog/resize-virtual-machines/)另一个 Azure 隔离虚拟机。 

有关详细信息，请参阅以下内容：

## <a name="timeline"></a>时间线
| Date | 操作 | 
| --- | --- |
| 2019 年 11 月 18 日 | D/DS15i_v2（PAYG，1 年 RI） |
| 2020年5月14日  | 最后一天购买D/DS15i_v21年RI | 
| 2020年5月15日   | D/DS15_v2隔离保证已移除 | 
| 2021年5月15日  | 停用 D/DS15i_v2（除在 2019 年 11 月 18 日之前购买 3 年 D/DS15_v2 RI 的客户外）| 
| 2022年11月17日  | 3 年期 RI 完成后停用 D/DS15i_v2（适用于在 2019 年 11 月 18 日之前购买 3 年 D/DS15_v2 RI 的客户） | 

## <a name="faq"></a>FAQ
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>问：D/DS15_v2号是否即将停用？
**答**：不，只有"隔离"功能才会停用。 如果不需要隔离，则不需要执行任何操作。

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>问：D/DS15i_v2号会退休吗？
**答**：是的，尺寸有效期至2021年5月15日。 对于在 2019 年 11 月 18 日之前在 D/DS15_v2 上购买 3 年期 RIs 的客户，在 2022 年 11 月 17 日之前可以访问 D/DS15i_v2。

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>问：为什么我在门户中看不到新的 D/DS15i_v2 大小？
**A**： 如果您是当前 D/DS15_v2 客户，并且想要使用新的 D/DS15i_v2 大小，请填写此[表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>问：为什么我看不到任何新的 D/DS15i_v2 尺寸配额？
**A**： 如果您是当前 D/DS15_v2 客户，并且想要使用新的 D/DS15i_v2 大小，请填写此[表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>问：其他孤立的尺寸何时会停用？
**答**：我们将在正式退役前12个月提供提醒。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>问：当我的 vm 落在非隔离硬件上时，是否有停机时间？
**答**：如果您不需要隔离，则不需要执行任何操作，也不会看到任何停机时间。

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>问：迁移到非隔离虚拟机是否有任何成本变化？
**A**： 否 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>问：我已经购买了 1 年或 3 年的预留实例，用于D15_v2或Ds15_v2。 折扣将如何应用于我的 VM 使用情况？
**答**：2019 年 11 月 18 日之前购买的 RI 将自动扩展到新的隔离 VM 系列。 

| RI |  实例大小灵活性 | 福利资格 |   
| --- | --- | --- |
|   D15_v2  |   关闭     |   D15_v2和D15i_v2 |    
|   D15_v2  |   启用  |   D15_v2系列和D15i_v2都将获得 RI 权益。 |    
|   D14_v2  |   启用  |   D15_v2系列和D15i_v2都将获得 RI 权益。 |    
 
同样对于 Dsv2 系列。
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>问：我想为 Dv2 购买其他预留实例。 应选择哪一种？
**答**：2019 年 11 月 18 日之后购买的所有 RI 都有以下行为。 

| RI |  实例大小灵活性 | 福利资格 |   
| --- | --- | --- |
| D15_v2 |  关闭 |   仅D15_v2  
| D15_v2 |  启用 |    D15_v2系列将获得 RI 权益。 新D15i_v2将不符合此 RI 类型的 RI 权益。 | 
| D15i_v2 |     关闭 | 仅D15i_v2 |  
| D15i_v2 |     启用  | 仅D15i_v2 | 
 
实例大小灵活性不能用于应用于任何其他大小，如D2_v2、D4_v2或D15_v2。 同样，对于 Dsv2 系列。  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>问：我可以为D15i_v2购买新的 3 年 RI 并DS15i_v2吗？
**答**：不幸的是，没有，只有1年RI可用于新购买。
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>问：我可以将现有D15_v2/DS15_v2预留实例移动到隔离大小预留实例吗？
**答**：此操作是不必要的，因为该好处将同时适用于隔离大小和非隔离大小。 但是，Azure 将支持将现有D15_v2/DS15_v2保留实例更改为D15i_v2/DS15i_v2。 对于所有其他 Dv2/Dsv2 预留实例，请使用现有的预留实例或购买隔离大小的新预留实例。

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>问：我是依靠银或金耐久等级的 Azure 服务结构客户。 这种变化对我有影响吗？
**答**：不需要。 即使在此更改之后，Service Fabric[的耐用性层](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)提供的保证仍将继续发挥作用。 如果出于其他原因需要物理硬件隔离，则可能仍需要执行上述操作之一。 
