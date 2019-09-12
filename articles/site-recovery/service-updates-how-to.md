---
title: Azure Site Recovery 中的更新和组件升级
description: 提供 Azure Site Recovery 服务更新和组件升级的概述。
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910478"
---
# <a name="service-updates-in-site-recovery"></a>Site Recovery 中的服务更新

本文概述 [Azure Site Recovery](site-recovery-overview.md) 更新，并介绍如何升级 Site Recovery 组件。

Site Recovery 定期发布服务更新。 更新包括新功能、支持改进、组件更新和 bug 修复。 为了利用最新的功能和修复，我们建议运行最新版本的 Site Recovery 组件。 
 
 
## <a name="updates-support"></a>更新支持

### <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery 的支持说明

我们建议始终升级到最新的组件版本：

**每次发布 Azure Site Recovery 组件的新版本“N”，“N-4”以下的所有版本都被视为终止支持**。 

> [!IMPORTANT]
> 官方的升级支持适用于 N-4 以上的版本到 N 版本。 例如，如果你正在运行 N-6，则需要先升级到 N-4，然后升级到 N。


### <a name="links-to-currently-supported-update-rollups"></a>当前受支持更新汇总的链接

 查看[此文](site-recovery-whats-new.md)中的最新更新汇总（版本 N）。 请记住，Site Recovery 为 N-4 版本提供支持。



## <a name="component-expiry"></a>组件过期

组件已过期（或即将到期）时，Site Recovery 将通过电子邮件（如果你已订阅电子邮件通知）或门户中的保管库仪表板向你发出通知。

- 此外，有可用的更新时，门户中方案基础结构视图中的相应组件旁边会显示“有可用的更新”按钮。 单击此按钮会重定向到某个链接，可通过该链接下载最新组件版本。
-  如果复制 Hyper-V VM，则不会显示保管库仪表板通知， 

而是发送电子邮件通知，如下所示。

**时间** | **频率**
--- | ---
组件过期前的 60 天 | 每两周一次
后续 53 天 | 每周一次
最近 7 天 | 每天 1 次
过期后 | 每两周一次


### <a name="upgrading-outside-official-support"></a>在不接受官方支持的情况下升级

如果组件版本与最新版本之间的差大于 4，则此组件将被视为已终止支持。 在这种情况下，请按如下所述进行升级： 

1. 将当前安装的组件升级到当前版本加 4。 例如，如果当前版本为 9.16，则升级到 9.20。
2. 然后升级到下一个兼容的版本。 沿用上面的示例，将 9.16 升级到 9.20 后，再升级到 9.24。 

对所有相关组件遵循相同的过程。

### <a name="support-for-latest-operating-systemskernels"></a>最新操作系统/内核的支持

> [!NOTE]
> 如果你已计划维护时段，并且维护后需要重新启动，则我们建议先升级 Site Recovery 组件，然后在维护时段内继续执行剩余的计划活动。

1. 在升级操作系统/内核版本之前，请验证 Site Recovery 是否支持目标版本。 

    - [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) 支持。
    - [VMware/物理服务器](vmware-physical-azure-support-matrix.md#replicated-machines)支持。
    - [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) 支持。
2. 查看[可用的更新](site-recovery-whats-new.md)来了解需要升级的组件。
3. 升级到最新的 Site Recovery 版本。
4. 将操作系统/内核升级到所需版本。
5. 重新启动。


此过程可确保将计算机操作系统/内核升级到最新版本，并将支持新版本所需的最新 Site Recovery 更改加载到计算机。

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure VM 灾难恢复到 Azure

在此方案中，我们强烈建议你[启用自动更新](azure-to-azure-autoupdate.md)。 你可以允许 Site Recovery 管理更新，如下所示：

- 在启用复制过程期间。
- 在保管库中指定扩展更新设置。

若要手动管理更新，请执行以下操作：

1. 在保管库 >“复制的项”中，单击屏幕顶部的此通知： 
    
    **新 Site Recovery 复制代理更新可用。单击以安装->**

4. 选择要将更新应用到的 VM，然后单击“确定”。


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>将 VMware VM/物理服务器灾难恢复到 Azure

1. 根据当前版本和[支持声明](#support-statement-for-azure-site-recovery)，遵照[这些说明](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)先在本地配置服务器上安装更新。 
2. 如果有横向扩展进程服务器，接下来请遵照[这些说明](vmware-azure-manage-process-server.md#upgrade-a-process-server)更新这些服务器。
3. 若要更新每台受保护计算机上的移动代理，请参阅[此](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)文。

### <a name="reboot-after-mobility-service-upgrade"></a>升级移动服务后重新启动

建议在每次升级移动服务后重新启动，以确保在源计算机上加载所有最新更改。

除非上次重新启动时的代理版本与当前版本之差大于 4，否则不一定要重新启动。

下表中的示例对此做了解释。

|**代理版本（上次重新启动时）** | **升级到** | **是否需要重新启动？**|
|---------|---------|---------|
|9.16 |  9.18 | 不是必需的|
|9.16 | 9.19 | 不是必需的|
| 9.16 | 9.20 | 不是必需的
 | 9.16 | 9.21 | 必需。<br/><br/> 升级到 9.20，然后在升级到 9.21 之前重新启动。

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Hyper-V VM 灾难恢复到 Azure

### <a name="between-a-hyper-v-site-and-azure"></a>在 Hyper-V 站点与 Azure 之间

1. 下载 Microsoft Azure Site Recovery 提供程序的更新。
2. 在 Site Recovery 中注册的每个 Hyper-V 服务器上安装该提供程序。 如果你正在运行群集，请在所有群集节点上升级。


## <a name="between-an-on-premises-vmm-site-and-azure"></a>在本地 VMM 站点与 Azure 之间
1. 下载 Microsoft Azure Site Recovery 提供程序的更新。
2. 在 VMM 服务器上安装该提供程序。 如果 VMM 部署在群集中，请在所有群集节点上安装该提供程序。
3. 在所有 Hyper-v 主机或群集节点上安装最新的 Microsoft Azure 恢复服务代理。


## <a name="between-two-on-premises-vmm-sites"></a>在两个本地 VMM 站点之间
1. 下载 Microsoft Azure Site Recovery 提供程序的最新更新。
2. 在管理辅助恢复站点的 VMM 服务器上安装最新的提供程序。 如果 VMM 部署在群集中，请在所有群集节点上安装该提供程序。
3. 更新恢复站点后，在管理主站点的 VMM 服务器上安装该提供程序。

## <a name="next-steps"></a>后续步骤

请关注我们的 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)页来了解新的更新和版本。
