---
title: "使用 Azure Site Recovery 复制多层 Dynamics AX 部署 | Microsoft Docs"
description: "本文介绍如何使用 Azure Site Recovery 复制和保护 Dynamics AX。"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: asgang
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: cf568d20f60709dbb64774bcbcc1b4aa6c43d8d3
ms.contentlocale: zh-cn
ms.lasthandoff: 06/16/2017


---
# <a name="replicate-a-multi-tier-dynamics-ax-application-using-azure-site-recovery"></a>使用 Azure Site Recovery 复制多层 Dynamics AX 应用程序

## <a name="overview"></a>概述


Microsoft Dynamics AX 是在企业中最广泛使用的 ERP 解决方案之一，可用于标准化不同位置的流程，管理资源及简化符合性。 假设应用程序是组织的业务关键型应用程序，应确保在发生任何灾难时，该应用程序都能尽快恢复正常运行。

目前，Microsoft Dynamics AX 不提供任何现成的灾难恢复功能。 Microsoft Dynamics AX 包括许多服务器组件，例如应用程序对象服务器、Active Directory (AD)、SQL 数据库服务器、SharePoint Server、Reporting Server等。手动管理其中每个组件的灾难恢复不仅费用高昂，而且还容易出错。

本文详细介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 为 Dynamics AX 应用程序创建灾难恢复解决方案。 此外，还介绍使用一键式恢复计划的计划内/计划外/测试故障转移，以及支持的配置和先决条件。
Microsoft Dynamics AX 已全面测试、认证并推荐基于 Azure Site Recovery 的灾难恢复解决方案。



## <a name="prerequisites"></a>先决条件

使用 Azure Site Recovery 为 Dynamics AX 应用程序实现灾难恢复需要满足以下先决条件。

•   已设置本地 Dynamics AX 部署

•   已在 Microsoft Azure 订阅中创建 Azure Site Recovery 服务保管库

•   如果 Azure 是恢复站点，则可以在 VM 上运行 Azure 虚拟机准备情况评估工具，确保这些 VM 与 Azure VM 和 Azure Site Recovery 服务兼容


## <a name="site-recovery-support"></a>Site Recovery 支持

制作本文时，在 Windows Server 2012 R2 Enterprise 中使用了装有 Dynamics AX 2012R3 的 VMware 虚拟机。 由于 Site Recovery 复制不区分应用程序，因此本文提供的建议应该也适用于后续方案。

### <a name="source-and-target"></a>源和目标

**方案** | **到辅助站点** | **到 Azure**
--- | --- | ---
**Hyper-V** | 是 | 是
**VMware** | 是 | 是
**物理服务器** | 是 | 是

## <a name="enable-dr-of-dynamics-ax-application-using-asr"></a>使用 ASR 为 Dynamics AX 应用程序启用 DR
### <a name="protect-your-dynamics-ax-application"></a>保护 Dynamics AX 应用程序
若要实现完整应用程序复制与恢复，Dynamics AX 的每个组件都需要受到保护。 本部分的内容：

**1.保护 Active Directory**

**2.保护 SQL 层**

**3.保护应用层和 Web 层**

**4.网络配置**

**5.恢复计划**

### <a name="1-setup-ad-and-dns-replication"></a>1.设置 AD 和 DNS 复制

要使 Dynamics AX 应用程序正常运行，需要在 DR 站点上设置 Active Directory。 根据客户本地环境的复杂性，可以使用两个建议的选项。

**选项 1**

如果客户的整个本地站点中只有少量的应用程序和单个域控制器，并且需要故障转移整个站点，则我们建议使用 ASR 复制将 DC 计算机复制到辅助站点（适用于站点到站点以及站点到 Azure 方案）。

**选项 2**

如果客户有大量应用程序，正在运行 Active Directory 林，并且每次只故障转移少量的应用程序，我们建议在 DR 站点（辅助站点或 Azure 中）另外设置一个域控制器。

请参阅[有关在 DR 站点上提供域控制器的随附指南](site-recovery-active-directory.md)。 本文档余下内容假设 DR 站点上提供了 DC。

### <a name="2-setup-sql-server-replication"></a>2.设置 SQL Server 复制
请参阅随附的指南，了解用于保护 [SQL 层](site-recovery-sql.md)的建议选项的详细技术指导。

### <a name="3-enable-protection-for-dynamics-ax-client-and-aos-vms"></a>3.为 Dynamics AX 客户端和 AOS VM 启用保护
根据 VM 是在 [Hyper-V](site-recovery-hyper-v-site-to-azure.md) 还是 [VMware](site-recovery-vmware-to-azure.md) 上部署的，执行相关的 Azure Site Recovery 配置。

> [!TIP]
> 建议将“崩溃一致性频率”配置为 15 分钟。
>

以下快照显示“VMware 站点到 Azure”保护方案中 Dynamics 组件 VM 的保护状态。
![受保护的项](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4.配置网络
配置 VM 计算和网络设置

对于 AX 客户端和 AOS VM，请在 ASR 中配置网络设置，以便在故障转移后将 VM 网络附加到适当的 DR 网络。 确保可将这些层的 DR 网络路由到 SQL 层。

可以在已复制的项中选择要配置网络设置的 VM，如以下快照中所示。

* 对于 AOS 服务器，请选择适当的可用性集。

* 如果使用静态 IP，请在“网络设置”的“目标 IP”字段中指定希望虚拟机采用的 IP![](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)



### <a name="5-creating-a-recovery-plan"></a>5.创建恢复计划

可以在 ASR 中创建恢复计划，将故障转移过程自动化。 在恢复计划中添加应用层和 Web 层。 在不同的组中将它们排序，以便先关闭前端，再关闭应用层。

1)  在订阅中选择 ASR 保管库，然后单击“恢复计划”磁贴。

2)  单击“+ 恢复计划”并指定名称。

3)  选择“源”和“目标”。 目标可以是 Azure 或辅助站点。 如果选择 Azure，必须指定部署模型

![创建恢复计划](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4)  选择在包含在恢复计划中的 AOS 和客户端 VM，然后单击 ✓。
![创建恢复计划](./media/site-recovery-dynamics-ax/selectvms.png)


![恢复计划](./media/site-recovery-dynamics-ax/recoveryplan.png)

可根据下面的详述添加各种步骤来自定义 Dynamics AX 应用程序的恢复计划。 上面的快照显示添加所有步骤后的完整恢复计划。

*步骤：*

*1.SQL Server 故障转移步骤*

请参阅 [SQL Server DR 解决方案](site-recovery-sql.md)随附指南，了解有关特定于 SQL Server 的恢复步骤的详细信息。

*2.故障转移组 1：故障转移 AOS VM*

确保选择的恢复点尽量靠近数据库 PIT，但不能在它的前面。

*3.脚本：添加负载均衡器（仅限 E-A）*AOS VM 组启动后，请添加一个脚本（通过 Azure 自动化），以便在其中添加负载均衡器。 可以使用脚本完成此任务。 请参阅[如何为多层应用程序 DR 添加负载均衡器](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)一文

*4.故障转移组 2：故障转移 AX 客户端 VM*
在执行恢复计划的过程中故障转移 Web 层 VM。


### <a name="doing-a-test-failover"></a>执行测试故障转移

请参阅“AD DR 解决方案”和“SQL Server DR 解决方案”随附指南，分别了解在测试故障转移期间特定于 AD 和 SQL Server 的注意事项。

1.  转到 Azure 门户并选择 Site Recovery 保管库。
2.  单击针对 Dynamics AX 创建的恢复计划。
3.  单击“测试故障转移”。
4.  选择虚拟网络开始测试故障转移过程。
5.  辅助环境启动后，可以执行验证。
6.  完成验证后，可以选择“验证完成”，随后将清理测试故障转移环境。

遵循[此指南](site-recovery-test-failover-to-azure.md)执行测试故障转移。

### <a name="doing-a-failover"></a>执行故障转移

1.  转到 Azure 门户并选择 Site Recovery 保管库。
2.  单击针对 Dynamics AX 创建的恢复计划。
3.  单击“故障转移”并选择“故障转移”。
4.  选择目标网络，然后单击 ✓ 开始故障转移过程。

执行故障转移时，请遵循[此指南](site-recovery-failover.md)。

### <a name="perform-a-failback"></a>执行故障回复

请参阅“SQL Server DR 解决方案”随附指南，了解故障回复期间特定于 SQL Server 的注意事项。

1.  转到 Azure 门户并选择 Site Recovery 保管库。
2.  单击针对 Dynamics AX 创建的恢复计划。
3.  单击“故障转移”并选择“故障转移”。
4.  单击“更改方向”。
5.  选择适当的选项 - 数据同步和 VM 创建选项
6.  单击 ✓ 开始执行“故障回复”过程。


执行故障回复时，请遵循[此指南](site-recovery-failback-azure-to-vmware.md)。

##<a name="summary"></a>摘要
使用 Azure Site Recovery 可为 Dynamics AX 应用程序创建一个完整的自动化灾难恢复计划。 发生服务中断时，可在数秒内从任何位置启动故障转移，在数分钟内启动和运行应用程序。

## <a name="next-steps"></a>后续步骤
阅读[我可以保护哪些工作负荷？](site-recovery-workload.md)详细了解如何使用 Azure Site Recovery 保护企业工作负荷。

