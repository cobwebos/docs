---
title: 启用区域以对 Azure 虚拟机进行灾难恢复
description: 本文介绍何时以及如何使用区域对 Azure 虚拟机进行灾难恢复。
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 27b433ba33d287a183d8ce6cc05d87649b2d0b00
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891502"
---
# <a name="enable-zone-to-zone-disaster-recovery-for-azure-virtual-machines"></a>启用区域以对 Azure 虚拟机进行灾难恢复

本文介绍如何将 Azure 虚拟机从一个可用性区域复制、故障转移和故障回复到同一 Azure 区域中的另一个。

>[!NOTE]
>
>- Site Recovery 当前不支持区域到区域灾难恢复的恢复计划。 
>- 对区域到区域灾难恢复的支持当前限制为两个区域：东南亚和英国南部。  

Site Recovery 服务通过在计划内和计划外的停机期间保持业务应用程序的运行，来为业务连续性和灾难恢复策略提供贡献。 建议使用灾难恢复选项，使应用程序在发生区域性服务中断时能够正常运行。

可用性区域是 Azure 区域中独特的物理位置。 每个区域都有一个或多个数据中心。 

## <a name="using-availability-zones-for-disaster-recovery"></a>使用可用性区域进行灾难恢复 

通常，可用性区域用于在高可用性配置中部署 Vm。 在发生自然灾害时，它们可能过于接近于灾难恢复解决方案。

但是，在某些情况下，可以利用可用性区域进行灾难恢复：

- 如果许多客户在本地托管应用程序时拥有 metro 灾难恢复策略，有时会在将应用程序迁移到 Azure 后，模拟这一策略。 这些客户承认，大都市性灾难恢复策略可能无法在大规模物理灾难发生时使用，并接受此风险。 对于此类客户，可以使用区域到区域灾难恢复作为灾难恢复选项。

- 许多其他客户都有复杂的网络基础结构，并且不希望因为相关的成本和复杂性而在次要区域中重新创建它。 区域到灾难恢复的区域降低了复杂性，因为它利用了跨可用性区域进行配置的更简单的冗余网络概念。 此类客户更愿意简单，还可以使用可用性区域进行灾难恢复。

- 在某些区域中，如果在同一法律管辖区域内没有配对区域（例如，东南亚），区域到区域的灾难恢复可以作为实际的灾难恢复解决方案，因为它有助于确保合法遵从性，因为你的应用程序和数据不跨越全国边界。 

- 与 Azure 到 Azure 灾难恢复相比，区域到区域的灾难恢复意味着在较短的距离内复制数据，因此，可能会出现延迟下降，从而降低 RPO。

尽管这些功能非常强大，但在发生区域范围的自然灾难时，可能会有可能导致区域灾难恢复的区域不是复原要求。

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>区域到区域灾难恢复的网络

如上所述，区域到灾难恢复的区域可降低复杂性，因为它利用了跨可用性区域进行配置的更简单的冗余网络概念。 下面概述了区域中用于区域灾难恢复方案的网络组件的行为： 

- 虚拟网络：可以使用与源网络相同的虚拟网络进行实际故障转移。 使用不同的虚拟网络到源虚拟网络进行测试性故障转移。

- 子网：支持故障转移到同一子网。

- 专用 IP 地址：如果你要使用静态 IP 地址，则可以在目标区域中使用相同的 ip，前提是你选择以这种方式对其进行配置。

- 加速网络：类似于 Azure 到 Azure 的灾难恢复，如果 VM SKU 支持，则可以启用加速网络。

- 公共 IP 地址：可将以前创建的标准公共 IP 地址附加到目标 VM 的同一区域中。 基本公共 IP 地址不支持可用性区域相关的方案。

- 负载均衡器：标准负载均衡器是一个区域资源，因此目标 VM 可以附加到同一个负载均衡器的后端池。 不需要新的负载均衡器。

- 网络安全组：可以使用与源 VM 相同的网络安全组。

## <a name="pre-requisites"></a>先决条件

在将区域部署到 Vm 的区域灾难恢复之前，请务必确保 VM 上启用的其他功能可与区域进行互操作，以便进行灾难恢复。

|Feature  | 支持声明  |
|---------|---------|
|经典 VM   |     不支持    |
|ARM Vm    |    支持    |
|Azure 磁盘加密 v1 （双重 pass，带有 AAD）     |     支持 |
|Azure 磁盘加密 v2 （单一传递，无 AAD）    |    支持    |
|非托管磁盘    |    不支持    |
|托管磁盘    |    支持    |
|客户管理的密钥    |    支持    |
|邻近放置组    |    支持    |
|备份互操作性    |    支持文件级别备份和还原。 磁盘和 VM 级别备份与还原，并且不受支持。    |
|热添加/删除    |    启用区域到区域复制后，可以添加磁盘。 不支持在启用区域到区域复制之后删除磁盘。    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>设置 Site Recovery 区域以进行灾难恢复

### <a name="log-in"></a>登录

登录到 Azure 门户。

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>为区域性 Azure 虚拟机启用复制

1. 在“Azure 门户”菜单中，选择“虚拟机”，或在任何页面上搜索并选择“虚拟机”。 选择要复制的 VM。 对于区域到灾难恢复的区域，此 VM 必须已在可用性区域中。

2. 在 "操作" 中，选择 "灾难恢复"。

3. 如下所示，在 "基本信息" 选项卡中，选择 "是" 作为 "可用性区域之间的灾难恢复"

    ![基本设置页](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. 如果接受所有默认值，请单击 "复查 + 开始复制"，然后单击 "开始复制"。

5. 如果要对复制设置进行更改，请单击 "下一步：高级设置"。

6. 在适当的位置将设置远离默认值。 对于 Azure 到 Azure 灾难恢复的用户，此页可能很熟悉。 有关此边栏选项卡上显示的选项的更多详细信息，请参阅[此处](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)

    ![高级设置页面](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. 单击 "下一步：审核 + 开始复制"，然后单击 "开始复制"。

## <a name="faqs"></a>常见问题解答

**1. 区域到灾难恢复的区域的定价如何？**
区域到区域灾难恢复的定价与 Azure 到 Azure 灾难恢复的定价相同。 可在[此处](https://azure.microsoft.com/pricing/details/site-recovery/)和[此处](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)找到有关定价页面的更多详细信息。 请注意，要在区域灾难恢复中看到的出口费用将低于区域进行灾难恢复。

**2. RTO 和 RPO 的 SLA 是什么？**
RTO SLA 与 Site Recovery 整体相同。 我们承诺最多2小时的 RTO。 没有针对 RPO 定义的 SLA。

**3. 是否可在辅助区域中保证容量？**
Site Recovery 团队和 Azure 容量管理团队规划了足够的基础结构容量。 启动故障转移时，团队还有助于确保受 Site Recovery 保护的 VM 实例将部署到目标区域。

**4. 支持哪些操作系统？**
区域到区域的灾难恢复支持与 Azure 到 Azure 灾难恢复相同的操作系统。 请参阅[此处](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)的支持矩阵。

## <a name="next-steps"></a>后续步骤

运行灾难恢复演练、故障转移、重新保护和故障回复时需要遵循的步骤与 Azure 到 Azure 灾难恢复方案中的步骤相同。

若要执行灾难恢复演练，请执行[此处](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-dr-drill)概述的步骤。

若要执行故障转移并重新保护辅助区域中的 Vm，请按照[此处](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)概述的步骤进行操作。

若要故障回复到主要区域，请执行[此处](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failback)概述的步骤。