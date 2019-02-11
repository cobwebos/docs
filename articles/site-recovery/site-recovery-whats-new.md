---
title: Azure Site Recovery 新增功能 | Microsoft Docs
description: 汇总 Azure Site Recovery 中引入的新功能
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: raynew
ms.openlocfilehash: 61e66a19b625141c69a9473373d3d5d808e18fde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211111"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery 中的新增功能

[Azure Site Recovery](site-recovery-overview.md) 服务会持续进行更新和改进。 本文介绍最新版本、新功能和新内容，让你始终了解最新动态。 此页会定期更新。

如果有关于 Site Recovery 功能的建议，请[提供反馈](https://feedback.azure.com/forums/256299-site-recovery)。

## <a name="q1-2019"></a>2019 年第 1 季度

### <a name="linux-support"></a>Linux 支持

[更新汇总 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) 提供 Site Recovery 代理和提供程序的更新。 这些更新增加了对 Linux 的支持，如下所示：

- **Azure VM 的灾难恢复**：RedHat 工作站 6/7；适用于 Ubuntu、Debian 和 SUSE 的新内核版本。
- **VMware VM/物理服务器到 Azure 的灾难恢复**：RedHat Enterprise Linux 7.6；RedHat 工作站 6/7；Oracle Linux 6.10/7.6；Ubuntu、Debian 和 SUSE 的新内核版本。



## <a name="q4-2018"></a>2018 年第 4 季度

## <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>针对 Azure VM 灾难恢复的价格计算器

进行 Azure VM 的灾难恢复需支付 VM 许可费用以及网络和存储费用。 Azure 提供[定价计算器](https://aka.ms/a2a-cost-estimator)，用于计算这些费用。 Site Recovery 现在提供[定价估算](https://aka.ms/a2a-cost-estimator)功能，可以根据三层应用的情况对示例部署定价。该应用使用 6 个 VM，其中包含 12 个标准 HDD 磁盘和 6 个高级 SSD 磁盘。 此示例假设标准磁盘的数据更改率为每天 10 GB，高级磁盘的数据更改率为每天 20 GB。 对于特定的部署，可以更改变量来估算费用。 可以指定 VM 的数目、托管磁盘的数目和类型，以及预期的跨 VM 总数据更改率。 另外，可以通过应用一个压缩因素来估算带宽费用。 [阅读](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)公告。

### <a name="support-for-azure-vms-in-zones"></a>在区域中支持 Azure VM

Azure 可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 现在可以为 Azure VM 启用复制功能，并设置一个目标，以便故障转移到单个 VM 实例、可用性集中的 VM 或可用性区域中的 VM。 此设置不影响复制。 [阅读](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)公告。
 
### <a name="disaster-recovery-for-encrypted-vms"></a>已加密 VM 的灾难恢复

Site Recovery 支持使用 Azure AD 应用通过 Azure 磁盘加密 (ADE) 进行加密的 Azure VM。 [了解详细信息](azure-to-azure-how-to-enable-replication-ade-vms.md)。

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>使用加速网络针对 VM 进行的灾难恢复

使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，提升网络性能。 为 Azure VM 启用复制时，Site Recovery 会检测是否启用了加速网络。 如果启用了加速网络，Site Recovery 会在故障转移后自动在目标副本 Azure VM 上针对 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 和 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms) 配置加速网络。 [了解详细信息](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="automatic-updates-for-the-mobility-service-extension"></a>针对移动服务扩展进行的自动更新

Site Recovery 增加了一个选项，可以针对移动服务扩展进行自动更新。 移动服务扩展安装在每个通过 Site Recovery 复制的 Azure VM 上。 启用复制时，请选择是否允许 Site Recovery 管理对扩展的更新。 更新不需 VM 重启，且不影响复制。 [了解详细信息](azure-to-azure-autoupdate.md)。

### <a name="support-for-standard-ssd-disks"></a>支持标准 SSD 磁盘

Azure 引入了[标准固态硬盘 (SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd) 磁盘，可以为特定应用（例如需要一致的性能但磁盘 IOPS 不高的 Web 服务器）提供经济有效的存储解决方案。 它们组合了高级 SSD 和标准 HDD 磁盘的元素。 Site Recovery 为使用标准 SSD 磁盘的 Azure VM 提供灾难恢复。 默认情况下，在故障转移到目标区域以后，磁盘类型会保留。

### <a name="support-for-azure-storage-firewall"></a>支持 Azure 存储防火墙

可以为帐户启用防火墙规则，将 Azure 存储帐户的访问范围限定于特定的一组网络。 请配置存储帐户，使之默认拒绝来自内部网络和 Internet 的流量，然后将访问权限授予来自特定 VNet 的流量。 在启用防火墙的存储帐户上，Site Recovery 支持将非托管磁盘所在 VM 上的内容复制到次要区域。 在目标区域中，对于非托管磁盘，可以选择启用了防火墙的存储帐户。 也可将网络访问局限于源 VM 所在的网络，从而将访问范围局限于缓存存储帐户。 请注意，对于受信任的 Microsoft 服务，必须[允许访问](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)。

## <a name="q3-2018"></a>2018 年第 3 季度 

### <a name="linux-support"></a>Linux 支持

#### <a name="update-rollup-28"></a>更新汇总 28

[更新汇总 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) 提供 Site Recovery 代理和提供程序的更新。 这些更新增加了对 Linux 的支持，如下所示：

- **Azure VM 的灾难恢复**：现在支持 RedHat Enterprise Linux 6.10、CentOS 6.10、基于 Linux 且在旧式 BIOS 兼容模式中使用 GUID 分区表 (GPT) 分区类型的 VM。
- **VMware VM/物理服务器到 Azure 的灾难恢复**：现在支持 RedHat Enterprise Linux 6.10、CentOS 6.10、基于 Linux 且在旧式 BIOS 兼容模式中使用 GUID 分区表 (GPT) 分区类型的 VM。

#### <a name="update-rollup-27"></a>更新汇总 27

[更新汇总 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) 提供 Site Recovery 代理和提供程序的更新。 这些更新增加了对 Linux 的支持，如下所示：

- **Azure VM 的灾难恢复**：Red Hat Enterprise Linux 7.5
- **VMware VM/物理服务器到 Azure 的灾难恢复**：SUSE Linux Enterprise Server 12、Red Hat Enterprise Linux 7.5

### <a name="support-for-azure-vms-running-on-windows-server-2016"></a>支持在 Windows Server 2016 上运行的 Azure VM

在 Windows Server 2016 上运行的 Azure VM 可以跨 Azure Site Recovery 所在的 Azure 区域复制。

### <a name="support-for-azure-vms-running-storage-spaces-direct"></a>支持运行存储空间直通的 Azure VM

[存储空间直通](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)（从 Windows Server 2016 开始提供）将驱动器组合在一起，形成一个存储池，然后使用该池的容量创建存储空间。 存储空间可以在独立 VM 上使用，可以在[包含 Azure VM 的来宾群集](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm)上使用（该 VM 在每个群集节点上使用本地存储），也可以在跨群集的共享存储上使用。

## <a name="next-steps"></a>后续步骤

在 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)页上时刻了解更新。


