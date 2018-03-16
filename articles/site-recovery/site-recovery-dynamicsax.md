---
title: "使用 Azure Site Recovery 复制多层 Dynamics AX 部署 | Microsoft Docs"
description: "本文介绍如何使用 Azure Site Recovery 复制和保护 Dynamics AX"
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
ms.date: 03/09/2018
ms.author: asgang
ms.openlocfilehash: b390f6c62a6ddf8c800f79b42a36dac2c4f4c908
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="replicate-a-multitier-dynamics-ax-application-by-using-azure-site-recovery"></a>使用 Azure Site Recovery 复制多层 Dynamics AX 应用程序

## <a name="overview"></a>概述


 Dynamics AX 是企业使用最广泛的 ERP 解决方案之一，可用于标准化不同位置的流程，管理资源及简化符合性。 应用程序对组织至关重要，因此发生灾难时，应用程序应在最短的时间内启动并运行。

目前，Dynamics AX 不提供任何现成的灾难恢复功能。 Dynamics AX 包括许多服务器组件，例如 Windows 应用程序对象服务器、Azure Active Directory、Azure SQL 数据库、SharePoint Server 和 Reporting Services 等。 手动管理其中每个组件的灾难恢复不仅费用高昂，而且还容易出错。

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 为 Dynamics AX 应用程序创建灾难恢复解决方案。 此外，还介绍了使用一键式恢复计划的计划内/计划外测试故障转移，以及支持的配置和先决条件。



## <a name="prerequisites"></a>先决条件

使用 Site Recovery 为 Dynamics AX 应用程序实现灾难恢复需以下先决条件：

• 设置本地 Dynamics AX 部署。

• 在 Azure 订阅中创建 Site Recovery 保管库。

• 如果 Azure 是恢复站点，则在 VM 上运行 Azure 虚拟机就绪评估工具。 它们必须与 Azure 虚拟机和 Site Recovery 服务兼容。

## <a name="site-recovery-support"></a>Site Recovery 支持

为创作本文，我们在 Windows Server 2012 R2 Enterprise 上装载了 Dynamics AX 2012 R3，并使用了 VMware 虚拟机。 由于 Site Recovery 复制不区分应用程序，因此预计此处提供的建议也适用于后续方案。

### <a name="source-and-target"></a>源和目标

**方案** | **到辅助站点** | **到 Azure**
--- | --- | ---
**Hyper-V** | 是 | 是
**VMware** | 是 | 是
**物理服务器** | 是 | 是

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>使用 Site Recovery 启用 Dynamics AX 应用程序的灾难恢复
### <a name="protect-your-dynamics-ax-application"></a>保护 Dynamics AX 应用程序
若要实现完整应用程序复制与恢复，Dynamics AX 的每个组件都必须受到保护。

### <a name="1-set-up-active-directory-and-dns-replication"></a>1.设置 Active Directory 和 DNS 复制

要使 Dynamics AX 应用程序正常运行，需要在灾难恢复站点上设置 Active Directory。 根据客户本地环境的复杂性，建议使用以下两个选项。

**选项 1**

客户的整个本地站点有少量应用程序和一个域控制器，计划同时故障转移整个站点。 建议使用 Site Recovery 复制，将域控制器计算机复制到辅助站点（适用于站点到站点和站点到 Azure 方案）。

**选项 2**

客户拥有大量应用程序且在运行 Active Directory 林，计划一次故障转移几个应用程序。 建议在灾难恢复站点（辅助站点或 Azure 中）设置另一域控制器。

 有关详细信息，请参阅[在灾难恢复站点启用域控制器](site-recovery-active-directory.md)。 本文档余下内容假设灾难恢复站点上提供了域控制器。

### <a name="2-set-up-sql-server-replication"></a>2.设置 SQL Server 复制
有关保护 SQL 层的建议选项的技术指导，请参阅[使用 SQL Server 和 Azure Site Recovery 复制应用程序](site-recovery-sql.md)。

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3.为 Dynamics AX 客户端和应用程序对象服务器 VM 启用保护
根据 VM 是在 [Hyper-V](site-recovery-hyper-v-site-to-azure.md) 还是在 [VMware](site-recovery-vmware-to-azure.md) 上部署的，执行相关的 Site Recovery 配置。

> [!TIP]
> 建议将“崩溃时一致”频率配置为 15 分钟。
>

以下快照显示 VMware 站点到 Azure 保护方案中 Dynamics 组件 VM 的保护状态。

![受保护的项](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4.配置网络
**配置 VM 计算和网络设置**

对于 Dynamics AX 客户端和应用程序对象服务器 VM，请在 Site Recovery 中配置网络设置，以便在故障转移后将 VM 网络附加到适当的灾难恢复网络。 确保可将这些层的灾难恢复网络路由到 SQL 层。

可以在已复制的项中选择要配置网络设置的 VM，如以下快照中所示：

* 对于应用程序对象服务器，选择正确的可用性集。

* 如果使用静态 IP，请在“目标 IP”文本框中指定希望 VM 采用的 IP。

    ![网络设置 ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)。


### <a name="5-create-a-recovery-plan"></a>5.创建恢复计划

可以在 Site Recovery 中创建恢复计划，将故障转移过程自动化。 在恢复计划中添加应用层和 Web 层。 在不同的组中将它们排序，以便先关闭前端，再关闭应用层。

1. 在订阅中选择 Site Recovery 保管库，并选择“恢复计划”磁贴。

2. 选择“+ 恢复计划”并指定名称。

3. 选择“源”和“目标”。 目标可以是 Azure 或辅助站点。 如果选择 Azure，则必须指定部署模型。

    ![创建恢复计划](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. 为恢复计划选择应用程序对象服务器和客户端 VM，然后选择 ✓。

    ![选择项](./media/site-recovery-dynamics-ax/selectvms.png)

    恢复计划示例：

    ![恢复计划详细信息](./media/site-recovery-dynamics-ax/recoveryplan.png)

可通过添加以下步骤来自定义 Dynamics AX 应用程序的恢复计划。 以上快照显示添加所有步骤后的完整恢复计划。


* SQL Server 故障转移步骤：有关 SQL Server 特定恢复步骤的信息，请参阅[使用 SQL Server 和 Azure Site Recovery 复制应用程序](site-recovery-sql.md)。

* 故障转移组 1：对应用程序对象服务器 VM 进行故障转移。
确保选择的恢复点尽量靠近数据库 PIT，但不能在它的前面。

* 脚本：添加负载均衡器（仅限 E-A）。
应用程序对象服务器 VM 组启动后，请添加一个脚本（通过 Azure 自动化），以便向其添加负载均衡器。 可以使用脚本完成此任务。 有关详细信息，请参阅[如何为多层应用程序灾难恢复添加负载均衡器](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)。

* 故障转移组 2：对 Dynamics AX 客户端 VM 进行故障转移。 在执行恢复计划的过程中故障转移 Web 层 VM。


### <a name="perform-a-test-failover"></a>执行测试故障转移

有关在测试故障转移期间的 Active Directory 具体信息，请参阅“Active Directory 灾难恢复解决方案”随附的指南。

有关测试故障转移期间 SQL Server 的特定信息，请参阅[使用 SQL Server 和 Azure Site Recovery 复制应用程序](site-recovery-sql.md)。

1. 转到 Azure 门户，选择 Site Recovery 保管库。

2. 选择针对 Dynamics AX 创建的恢复计划。

3. 选择“测试故障转移”。

4. 选择虚拟网络开始测试故障转移过程。

5. 辅助环境启动后，可以执行验证。

6. 完成验证后，选择“验证完成”，随后将清理测试故障转移环境。

有关执行测试故障转移的详细信息，请参阅[在 Site Recovery 中执行到 Azure 的测试故障转移](site-recovery-test-failover-to-azure.md)。

### <a name="perform-a-failover"></a>执行故障转移

1. 转到 Azure 门户，选择 Site Recovery 保管库。

2. 选择针对 Dynamics AX 创建的恢复计划。

3. 选择“故障转移”，然后选择“故障转移”。

4. 选择目标网络，并选择 ✓ 开始故障转移过程。

有关执行故障转移的详细信息，请参阅 [Site Recovery 中的故障转移](site-recovery-failover.md)。

### <a name="perform-a-failback"></a>执行故障回复

有关在故障回复期间的 SQL Server 具体注意事项，请参阅[使用 SQL Server 和 Azure Site Recovery 复制应用程序](site-recovery-sql.md)。

1. 转到 Azure 门户，选择 Site Recovery 保管库。

2. 选择针对 Dynamics AX 创建的恢复计划。

3. 选择“故障转移”，然后选择“故障转移”。

4. 选择“更改方向”。

5. 选择适当的选项：数据同步和 VM 创建。

6. 选择 ✓ 开始执行故障回复过程。


有关执行故障回复的详细信息，请参阅[将 VMware VM 从 Azure 故障回复到本地](site-recovery-failback-azure-to-vmware.md)。

## <a name="summary"></a>摘要
使用 Site Recovery 可为 Dynamics AX 应用程序创建一个完整的自动化灾难恢复计划。 发生服务中断时，可在数秒内从任何位置启动故障转移，在数分钟内启动和运行应用程序。

## <a name="next-steps"></a>后续步骤
若要详细了解如何使用 Site Recovery 保护企业工作负荷，请参阅[我可以保护哪些工作负荷？](site-recovery-workload.md)。
