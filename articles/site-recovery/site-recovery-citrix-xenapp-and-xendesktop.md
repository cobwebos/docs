---
title: "使用 Azure Site Recovery 复制多层 Citrix XenDesktop 和 XenApp 部署 | Microsoft Docs"
description: "本文介绍如何使用 Azure Site Recovery 保护与恢复 Citrix XenDesktop 和 XenApp 部署。"
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: ponatara
ms.openlocfilehash: b117525a4851dee5366aeda77c8aaefd1fdde375
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="replicate-a-multi-tier-citrix-xenapp-and-xendesktop-deployment-using-azure-site-recovery"></a>使用 Azure Site Recovery 复制多层 Citrix XenApp 和 XenDesktop 部署

## <a name="overview"></a>概述

Citrix XenDesktop 是一个桌面虚拟化解决方案，可以将桌面和应用程序作为按需服务传递给任何位置的任何用户。 利用 FlexCast 传递技术，XenDesktop 可以快速、安全地将应用程序和桌面传递给用户。
Citrix XenApp 暂不提供任何灾难恢复功能。

合理的灾难恢复解决方案应该允许围绕上述复杂应用程序体系结构为恢复计划建模，同时，可以添加自定义的步骤来处理各层之间的应用程序映射，以便在发生导致 RTO 降低的灾难时，只需按一下鼠标就能彻底解决问题。

本文档逐步介绍了如何为 Hyper-V 和 VMware vSphere 平台上的本地 Citrix XenApp 部署生成灾难恢复解决方案。 此外还介绍了如何利用恢复计划、支持的配置和先决条件，执行到 Azure 的测试故障转移（灾难恢复演练）和计划外故障转移。


## <a name="prerequisites"></a>先决条件

在开始之前，请确保了解以下知识：

1. [将虚拟机复制到 Azure](site-recovery-vmware-to-azure.md)
1. 如何[设计恢复网络](site-recovery-network-design.md)
1. [执行到 Azure 的测试故障转移](site-recovery-test-failover-to-azure.md)
1. [执行到 Azure 的故障转移](site-recovery-failover.md)
1. 如何[复制域控制器](site-recovery-active-directory.md)
1. 如何[复制 SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>部署模式

Citrix XenApp 和 XenDesktop 场通常具有以下部署模式：

**部署模式**

包含 AD DNS 服务器、SQL 数据库服务器、Citrix 传递控制器、StoreFront 服务器、XenApp Master (VDA) 和 Citrix XenApp 许可证服务器的 Citrix XenApp 和 XenDesktop 部署

![部署模式 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery 支持

本文旨在使用由 vSphere 6.0/System Center VMM 2012 R2 管理的 VMware 虚拟机上的 Citrix 部署来设置 DR。

### <a name="source-and-target"></a>源和目标

**方案** | **到辅助站点** | **到 Azure**
--- | --- | ---
**Hyper-V** | 不在范围内 | 是
**VMware** | 不在范围内 | 是
**物理服务器** | 不在范围内 | 是

### <a name="versions"></a>版本
客户可以部署 XenApp 组件，作为在 Hyper-V 或 VMware 上运行的虚拟机或作为物理服务器。 Azure Site Recovery 可以保护 Azure 中的物理部署与虚拟部署。
由于 Azure 支持 XenApp 7.7 或更高版本，因此只能将包含这些版本的部署故障转移到 Azure，用于灾难恢复或迁移。

### <a name="things-to-keep-in-mind"></a>要点

1. 支持通过使用服务器 OS 计算机传递 XenApp 发布的应用和 XenApp 发布的桌面，来保护和恢复本地部署。

2. 不支持通过使用桌面 OS 计算机传递客户端虚拟桌面（包括 Windows 10）的桌面 VDI 来保护和恢复本地部署。 这是因为 ASR 不支持恢复具有桌面 OS 的计算机。  此外，Azure 尚不支持对某些客户端虚拟桌面操作系统（如 Windows 7）授权。 [了解](https://azure.microsoft.com/pricing/licensing-faq/) Azure 中的客户端/服务器桌面授权。

3.  Azure Site Recovery 无法复制和保护现有的本地 MCS 或 PVS 克隆。
需要使用从传递控制器预配的 Azure RM 重新创建这些克隆。

4. 无法使用 Azure Site Recovery 保护 NetScaler，因为 NetScaler 基于 FreeBSD，而 Azure Site Recovery 不支持保护 FreeBSD OS。 故障转移到 Azure 之后，你需要部署并配置来自 Azure 应用商店的新 NetScaler 设备。


## <a name="replicating-virtual-machines"></a>复制虚拟机

需要保护 Citrix XenApp 部署的以下组件，才能启用复制和恢复。

* 保护 AD DNS 服务器
* 保护 SQL 数据库服务器
* 保护 Citrix 传递控制器
* 保护 StoreFront 服务器。
* 保护 XenApp Master (VDA)
* 保护 Citrix XenApp 许可证服务器


**AD DNS 服务器复制**

有关在 Azure 中复制和配置域控制器的指南，请参阅[使用 Azure Site Recovery 保护 Active Directory 和 DNS](site-recovery-active-directory.md)。

**SQL 数据库服务器复制**

有关用于保护 SQL 服务器的推荐选项的详细技术指南，请参阅[使用 SQL Server 灾难恢复和 Azure Site Recovery 来保护 SQL Server](site-recovery-sql.md)。

遵循[此指南](site-recovery-vmware-to-azure.md)，开始将其他组件虚拟机复制到 Azure。

![保护 XenApp 组件](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**“计算和网络”设置**

计算机受保护后（“复制的项”下的状态显示为“受保护”），需要配置“计算和网络”设置。
在“计算和网络”>“计算属性”中，可以指定 Azure VM 名称和目标大小。
根据需要修改名称，使其符合 Azure 要求。 还可以查看和添加目标网络、子网的相关信息，以及要分配到 Azure VM 的 IP 地址。

注意以下事项：

* 可以设置目标 IP 地址。 如果未提供地址，故障转移的计算机将使用 DHCP。 如果设置了无法用于故障转移的地址，故障转移将不会正常工作。 如果地址可用于测试故障转移网络，则同一个目标 IP 地址可用于测试故障转移。

* 对于 AD/DNS 服务器，如果保留本地地址，则可以为 Azure 虚拟网络指定与 DNS 服务器相同的地址。

网络适配器数目根据你为目标虚拟机指定的大小来确定，如下所述：

*   如果源计算机上的网络适配器数小于或等于目标计算机大小允许的适配器数，则目标的适配器数将与源相同。
*   如果源虚拟机的适配器数大于目标大小允许的数目，则使用目标大小允许的最大数目。
* 例如，如果源计算机有两个网络适配器，而目标计算机大小支持四个，则目标计算机将有两个适配器。 如果源计算机有两个适配器，但支持的目标大小只支持一个，则目标计算机只有一个适配器。
*   如果虚拟机有多个网络适配器，它们将全部连接到同一个网络。
*   如果虚拟机有多个网络适配器，列表中显示的第一个适配器将成为 Azure 虚拟机中的默认网络适配器。


## <a name="creating-a-recovery-plan"></a>创建恢复计划

为 XenApp 组件 VM 启用复制后，下一步是创建恢复计划。
恢复计划将故障转移和恢复要求相似的虚拟机集合在一起。  

**创建恢复计划的步骤**

1. 在恢复计划中添加 XenApp 组件虚拟机。
2. 单击“恢复计划”->“+ 恢复计划”。 为恢复计划提供一个直观的名称。
3. 对于 VMware 虚拟机：选择 VMware 进程服务器作为源，Microsoft Azure 作为目标，Resource Manager 作为部署模型，然后单击“选择项”。
4. 对于 Hyper-V 虚拟机：选择 VMM 服务器作为源，Microsoft Azure 作为目标，Resource Manager 作为部署模型，单击“选择项”，然后选择 XenApp 部署 VM。

### <a name="adding-virtual-machines-to-failover-groups"></a>将虚拟机添加到故障转移组

可以自定义恢复计划，以便按特定启动顺序添加故障转移组、添加脚本或手动操作。 需要向恢复计划添加以下组。

1. 故障转移组1：AD DNS
2. 故障转移组2：SQL Server VM
2. 故障转移组3：VDA 主映像 VM
3. 故障转移组4：传递控制器和 StoreFront 服务器 VM


### <a name="adding-scripts-to-the-recovery-plan"></a>将脚本添加到恢复计划

脚本可以在恢复计划中的特定组之前或之后运行。 在故障转移期间，还可以添加和执行手动操作。

自定义的恢复计划如下所示：

1. 故障转移组1：AD DNS
2. 故障转移组2：SQL Server VM
3. 故障转移组3：VDA 主映像 VM

   >[!NOTE]     
   >包含手动操作或脚本操作的步骤 4、6 和 7 仅适用于具有 MCS/PVS 目录的本地 XenApp 环境。

4. 组 3 手动操作或脚本操作：关闭 Master VDA VM。Master VDA VM 在故障转移到 Azure 时将处于运行状态。 若要使用 Azure ARM 宿主创建新的 MCS 目录，Master VDA VM 需处于“已停止”（已解除分配）状态。 从 Azure 门户关闭 VM。

5. 故障转移组4：传递控制器和 StoreFront 服务器 VM
6. 组3 手动操作或脚本操作 1：

    ***添加 Azure RM 主机连接***

    在传递控制器计算机中创建 Azure ARM 主机连接，以便在 Azure 中预配新的 MCS 目录。 按此[文](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/)所述的步骤进行操作。

7. 组3 手动操作或脚本操作 2：

    ***在 Azure 中重新创建 MCS 目录***

    主站点上现有的 MCS 或 PVS 克隆不会复制到 Azure。 需要使用从传递控制器预配的复制 Master VDA 和 Azure ARM 重新创建这些克隆。按此[文](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/)所述的步骤操作，在 Azure 中创建 MCS 目录。

![XenApp 组件的恢复计划](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >可以使用[此处](https://github.com/Azure/azure-quickstart-templates/blob/>master/asr-automation-recovery/scripts)的脚本，根据需要使用已故障转移虚拟机的新 IP 更新 DNS 或在已故障转移虚拟机上附加负载均衡器。


## <a name="doing-a-test-failover"></a>执行测试故障转移

遵循[此指南](site-recovery-test-failover-to-azure.md)执行测试故障转移。

![恢复计划](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>执行故障转移

执行故障转移时，请遵循[此指南](site-recovery-failover.md)。

## <a name="next-steps"></a>后续步骤

可以从本白皮书中[详细了解](https://aka.ms/citrix-xenapp-xendesktop-with-asr)如何复制 Citrix XenApp 和 XenDesktop 部署。 请查看相关指南，使用 Site Recovery [复制其他应用程序](site-recovery-workload.md)。
