---
title: "准备 Site Recovery 部署 | Microsoft Azure"
description: "本文介绍如何准备使用 Azure Site Recovery 部署复制。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5ed46a6bd8931d3b8829a053ace12fd219f108c4
ms.openlocfilehash: 6430ae9a97888ef993cd3dd35eea765fe95bb43f

---

# <a name="prepare-for-azure-site-recovery-deployment"></a>准备 Azure Site Recovery 部署

组织需要制定 BCDR 策略来确定应用、工作负荷和数据如何在计划和非计划停机期间保持运行和可用，并尽快恢复正常运行情况。 Site Recovery 是一项 Azure 服务，可以通过协调从本地物理服务器和虚拟机到云 (Azure) 或辅助数据中心的的复制，来为 BCDR 策略提供辅助。 当主要位置发生故障时，你可以故障转移到辅助位置，使应用和工作负荷保持可用。 当主要位置恢复正常时，你可以故障转移回到主要位置。 有关详细信息，请参阅 [什么是 Site Recovery？](site-recovery-overview.md)

本文汇总了每种 Site Recovery 复制方案的概括性要求，并提供了详细部署演练的链接。  

欢迎在本文底部发表任何看法，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上咨询技术问题。



## <a name="azure-deployment-models"></a>Azure 部署模型

Azure 提供两种不同的[部署模型](../azure-resource-manager/resource-manager-deployment-model.md)用于创建和处理资源：Azure Resource Manager 模型和经典模型。 Azure 还有两个门户 – 支持经典部署模型的 [Azure 经典门户](https://manage.windowsazure.com/)，以及支持两种部署模型的 [Azure 门户](https://ms.portal.azure.com/)。

应在 [Azure 门户](https://portal.azure.com)中部署新的 Site Recovery 方案。 此门户提供新的功能以及改进的部署体验。 在经典门户中可以维护保管库，但无法创建新保管库。


## <a name="deployment-scenarios"></a>部署方案

下面是可以使用 Site Recovery 复制的内容：

| **部署** | **详细信息** | **Azure 门户** | **经典门户** | **PowerShell 部署** |
| --- | --- | --- | --- | --- |
| [VMware 到 Azure](site-recovery-vmware-to-azure.md) | 将本地 VMware VM 复制到 Azure 存储 | 使用 Resource Manager 或经典存储和网络。 故障转移到基于 Resource Manager 或经典模型的 VM。 | 只能使用维护模式。 无法创建新保管库。 | 目前不支持。 |
| [物理服务器到 Azure](site-recovery-vmware-to-vmware.md) | 将物理 Windows/Linux 服务器复制到 Azure 存储 | 使用 Resource Manager 或经典存储和网络。 故障转移到基于 Resource Manager 或经典模型的 VM。 | 只能使用维护模式。 无法创建新保管库。  |
| [将 VMM 云中的 Hyper-V VM 复制到 Azure](site-recovery-vmm-to-azure.md) | 将 VMM 云中的本地 Hyper-V VM 复制到 Azure 存储。<br/><br/> | 使用 Resource Manager 或经典存储和网络。 故障转移到基于 Resource Manager 或经典模型的 VM。   | 只能使用维护模式。 无法创建新保管库。 | 支持 |
| [Hyper-V VM 到 Azure（不包含 VMM）](site-recovery-hyper-v-site-to-azure.md) | 将本地 Hyper-V VM 复制到 Azure 存储。 | 使用 Resource Manager 或经典存储和网络。 故障转移到基于 Resource Manager 或经典模型的 VM。 | 只能使用维护模式。 无法创建新保管库。 | 支持 |
| 将 VMware VM/物理服务器复制到辅助站点 (site-recovery-vmware-to-vmware.md) | 将 VMware VM 或物理 Windows/Linux 服务器复制到辅助站点。<br/><br/> [下载帮助指南](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)和 InMage Scout 用户指南。 | Azure 门户中未提供 | 从 Site Recovery 保管库下载 InMage Scout。 | 不支持 |
| [Hyper-V VM 到辅助站点](site-recovery-vmm-to-vmm.md) | 将 VMM 云中的 Hyper-V VM 复制到辅助云  | 复制使用标准 Hyper-V 副本。 不支持 SAN。 | 使用 Hyper-V 副本或 [SAN 复制](site-recovery-vmm-san.md)功能进行复制 | 支持 |

## <a name="requirements-for-replication-to-azure"></a>复制到 Azure 的要求

| **要求** | **详细信息** |
| --- | --- |
| **Azure 帐户** | 一个 [Microsoft Azure](http://azure.microsoft.com/) 帐户。<br/><br/> 你可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。 [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 定价。 |
| **Azure 存储空间** | 复制的数据存储在 Azure 存储中，发生故障转移时将创建 Azure VM。 需要一个 [Azure 存储帐户](../storage/storage-introduction.md)。<br/><br/>在 Azure 门户使用 [GRS](../storage/storage-redundancy.md#geo-redundant-storage)或 LRS 存储。 经典门户仅支持 GRS。<br/><br/> 如果在 Azure 门户中复制 VMware VM 或物理服务器，支持使用高级存储。<br/><br/>  |
| **Azure 网络** | 需要设置一个与 Azure VM 连接的 Azure 网络 <br/><br/> 在 Azure 门户中，可以使用经典网络或 Resource Manager 网络。<br/><br/> 该网络必须位于与保管库相同的区域中。<br/><br/> 如果从 VMM 复制到 Azure，需在 VMM VM 网络与 Azure 网络之间设置[网络映射](site-recovery-network-mapping.md)，确保 Azure VM 在故障转移后连接到适当的网络。 |
| **本地** |**VMware VM**：需要一台运行 Site Recovery 组件的本地计算机。 此外，还需要 VMware vSphere 主机/vCenter 服务器和要复制的 VM。 [了解详细信息](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites)。<br/><br/> **物理服务器**：需要一台运行 Site Recovery 组件的本地计算机，以及要复制的物理服务器。 [了解详细信息](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites)。 如果要从 Azure [故障回复](site-recovery-failback-azure-to-vmware.md)物理服务器，需要设置 VMware 基础结构。<br/><br/> **Hyper-V VM**：一台 VMM 服务器，以及一台包含所要复制的 VM 的 Hyper-V 主机。 [了解详细信息](site-recovery-vmm-to-azure.md#on-premises-prerequisites)。<br/><br/> 若要复制不包含 VMM 的 Hyper-V VM，则只需 Hyper-V 主机。 [了解详细信息](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites)。 |
| **受保护的计算机** | 将要复制到 Azure 的受保护计算机必须符合下述 [Azure 先决条件](#azure-virtual-machine-requirements)。 |

## <a name="requirements-for-replication-to-a-secondary-site"></a>复制到辅助站点的要求

| **要求** | **详细信息** |
| --- | --- |
| **Azure** | 一个 [Microsoft Azure](http://azure.microsoft.com/) 帐户。<br/><br/> 你可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。 [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 定价。 |
| **本地** |**VMware VM**：在主站点中，需要设置一台进程服务器用于缓存、压缩和加密复制数据。 在辅助站点中，需要设置一台配置服务器用于管理和监视部署服务器与主目标服务器。 为了方便管理，建议同时安装一台 vContinuum 服务器。 此外，需要一台或多台 VMware vSphere 主机，并选择性地部署一台 vCenter 服务器。 [了解详细信息](site-recovery-vmware-to-vmware.md)<br/><br/> **VMM 云中的 Hyper-V VM**：设置 VMM 服务器，以及包含所要复制的 VM 的 Hyper-V 主机。 [了解详细信息](site-recovery-vmm-to-vmm.md#on-premises-prerequisites)。 |
| **网络（VMM 到 VMM）** | 如果要从 VMM 复制到 VMM，则可设置[网络映射](site-recovery-network-mapping.md)，确保在故障转移之后，副本 VM 连接到适当的网络，并以最佳方式放置在 Hyper-V 主机上。 |

## <a name="url-access"></a>URL 访问

应该可从 VMware、VMM 和 Hyper-V 主机服务器访问以下 URL。

**URL** | **VMM 到 VMM** | **VMM 到 Azure** | **Hyper-V 到 Azure** | **VMware 到 Azure** |
|--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | ALLOW | 允许 | 允许 | ALLOW
``*.backup.windowsazure.com`` | 不是必需 | 允许 | 允许 | 允许
``*.hypervrecoverymanager.windowsazure.com`` | 允许 | 允许 | 允许 | 允许
``*.store.core.windows.net`` | 允许 | 允许 | 允许 | ALLOW
``*.blob.core.windows.net`` | 不是必需 | 允许 | 允许 | 允许
``https://www.msftncsi.com/ncsi.txt`` | 允许 | 允许 | 允许 | ALLOW
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | 不是必需 | 不是必需 | 不是必需 | 允许 SQL 下载
``time.windows.com`` | ALLOW | 允许 | 允许 | 允许
``time.nist.gov`` | 允许 | 允许 | 允许 | ALLOW



## <a name="azure-virtual-machine-requirements"></a>Azure 虚拟机要求

可以部署 Site Recovery 以复制运行受 Azure 支持的任何操作系统的虚拟机和物理服务器。 这包括大多数的 Windows 和 Linux 版本。 要复制的本地虚拟机必须符合 Azure 要求。

**功能** | **要求** | **详细信息**
--- | --- | ---
**Hyper-V 主机** | 应运行 Windows Server 2012 R2 或更高版本 | 如果操作系统不受支持，先决条件检查将会失败
**VMware 虚拟机监控程序** | 支持的操作系统 | [检查要求](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**来宾操作系统** | 对于从 Hyper-V 到 Azure 的复制，Site Recovery 支持 [Azure 支持](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)的所有操作系统。 <br/><br/> 对于 VMware 和物理服务器复制，请检查 Windows 和 Linux [先决条件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | 如果不支持，先决条件检查将会失败。
**来宾操作系统体系结构** | 64 位 | 如果不支持，先决条件检查将会失败
**操作系统磁盘大小** | 最大 1023 GB | 如果不支持，先决条件检查将会失败
**操作系统磁盘计数** | 1 | 如果不支持，先决条件检查将会失败。
**数据磁盘计数** | 16 或更少（最大值取决于所创建的虚拟机大小。 16 = XL） | 如果不支持，先决条件检查将会失败
**数据磁盘 VHD 大小** | 最大 1023 GB | 如果不支持，先决条件检查将会失败
**网络适配器** | 支持多个适配器 |
**静态 IP 地址** | 支持 | 如果主虚拟机使用的是静态 IP 地址，则你可为要在 Azure 中创建的虚拟机指定静态 IP 地址。<br/><br/> **在 Hyper-v 上运行的 Linux VM** 不支持静态 IP 地址。
**iSCSI 磁盘** | 不支持 | 如果不支持，先决条件检查将会失败
**共享 VHD** | 不支持 | 如果不支持，先决条件检查将会失败
**FC 磁盘** | 不支持 | 如果不支持，先决条件检查将会失败
**硬盘格式** | VHD <br/><br/> VHDX | 尽管 Azure 当前不支持 VHDX，但当你故障转移到 Azure 时，站点恢复会自动将 VHDX 转换为 VHD。 当你故障回复到本地时，虚拟机将继续使用 VHDX 格式。
**Bitlocker** | 不支持 | 保护虚拟机之前，必须先禁用 Bitlocker。
**VM 名称** | 介于 1 和 63 个字符之间。 限制为字母、数字和连字符。 应以字母或数字开头和结尾 | 在 Site Recovery 中更新虚拟机属性中的值
**VM 类型** | 第 1 代<br/><br/> 第 2 代 - Windows | OS 磁盘类型为“基本”的第 2 代 VM，其中包括一个或两个格式化为 VHDX 的数据卷，并且支持的大小小于 300 GB。<br/><br/>。 不支持 Linux 第 2 代 VM。 [了解详细信息](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |

## <a name="deployment-optimization"></a>部署优化

使用以下提示来优化和缩放部署。

- **操作系统卷大小**：将虚拟机复制到 Azure 时，操作系统卷必须小于 1TB。 如果你的卷容量超过此值，可以在开始部署之前，手动将卷容量转移到另一个磁盘。
- **数据磁盘大小**：如果要复制到 Azure，一个虚拟机中最多可以包含 64 个数据磁盘，每个磁盘的最大大小为 1 TB。 可以有效地复制和故障转移约 64 TB 的虚拟机。
- **恢复计划限制**：Site Recovery 可以扩展到数千个虚拟机。 恢复计划旨在用作应一起故障转移的应用程序的模型，因此，我们可以将一个恢复计划中的计算机数目限制为 50。
- **Azure 服务限制**：每个 Azure 订阅在核心、云服务等方面附带了一组默认限制。建议运行测试故障转移，验证订阅中资源的可用性。 可以通过 Azure 支持人员修改这些限制。
- **容量规划**：阅读 Site Recovery 的[容量规划](site-recovery-capacity-planner.md)相关信息。
- **复制带宽**：如果你的复制带宽不足，请注意：
- **ExpressRoute**：Site Recovery 可以与 Azure ExpressRoute 和 WAN 优化器（如 Riverbed）配合使用。 [阅读更多](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx)有关 ExpressRoute 的信息。
- **复制流量**：Site Recovery 用户只能使用数据块（而不是整个 VHD）执行智能初始复制。 在复制过程中，只会复制更改。
- **网络流量**：可以通过使用基于目标 IP 地址和端口的策略设置 [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx)，来控制用于复制的网络流量。  此外，如果要使用 Azure 备份代理复制到 Azure Site Recovery，可以为该代理配置限制。 [了解详细信息](https://support.microsoft.com/kb/3056159)。
- **RTO**：若要度量使用 Site Recovery 时预期的恢复时间目标 (RTO)，我们建议运行测试故障转移并查看 Site Recovery 作业，分析完成操作所花费的时间。 如果你要故障转移到 Azure，为实现最佳 RTO，我们建议你通过与 Azure 自动化和恢复计划集成来自动化所有手动操作。
- **RPO**：复制到 Azure 时，Site Recovery 支持近乎同步的恢复点目标 (RPO)。 这假设数据中心和 Azure 之间有足够的带宽。



## <a name="next-steps"></a>后续步骤
查看常规部署要求之后，请阅读详细的先决条件并部署方案。

* [将 VMware 虚拟机复制到 Azure](site-recovery-vmware-to-azure.md)
* [将物理服务器复制到 Azure](site-recovery-vmware-to-azure.md)
* [将 VMM 云中的 Hyper-V 服务器复制到 Azure](site-recovery-vmm-to-azure.md)
* [将 Hyper-V 虚拟机（不在 VMM 中）复制到 Azure](site-recovery-hyper-v-site-to-azure.md)
* [将 Hyper-V VM 复制到辅助站点](site-recovery-vmm-to-vmm.md)
* [使用单个 VMM 服务器复制 Hyper-V VM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


