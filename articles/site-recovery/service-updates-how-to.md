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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362710"
---
# <a name="service-updates-in-site-recovery"></a>Site Recovery 中的服务更新

本文概述了[Azure Site Recovery](site-recovery-overview.md)更新，并介绍了如何升级 Site Recovery 组件。

Site Recovery 定期发布服务更新。 更新包括新功能、支持改进、组件更新和 bug 修复。 为了充分利用最新的功能和修补程序，我们建议运行 Site Recovery 组件的最新版本。 
 
 
## <a name="updates-support"></a>更新支持

### <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery 的支持说明

建议始终升级到最新的组件版本：

**对于发布的 Azure Site Recovery 组件的每个新版本 "n"，所有低于 "n-1" 的版本都被视为不受支持**。 

> [!IMPORTANT]
> 官方支持用于从 > N-4 版本升级到 N 版本。 例如，如果你正在运行，则需要先升级到 n-4，然后再升级到 N。


### <a name="links-to-currently-supported-update-rollups"></a>当前受支持更新汇总的链接

 查看[本文](site-recovery-whats-new.md)中的最新更新汇总（版本 N）。 请记住，Site Recovery 为 N-4 版本提供支持。



## <a name="component-expiry"></a>组件过期

Site Recovery 通过电子邮件（如果订阅了电子邮件通知）或在门户的保管库仪表板上通知你过期的组件（或即将到期）。

- 此外，当有更新时，可以在门户中的方案的基础结构视图中显示 "**更新可用**" 按钮。 此按钮可将您重定向到用于下载最新组件版本的链接。
-  如果要复制 Hyper-v Vm，保管库仪表板通知不可用。 

发送电子邮件通知，如下所示。

**时间** | **频率**
--- | ---
超过60天后，组件到期 | 每周一次
未来53天 | 一周一次
过去 7 天 | 一天一次
过期后 | 每周一次


### <a name="upgrading-outside-official-support"></a>升级外部支持

如果组件版本与最新版本之间的差异超过四个，则将其视为不支持。 在这种情况下，升级如下： 

1. 将当前安装的组件升级到当前版本和四个版本。 例如，如果版本为9.16，则升级到9.20。
2. 然后，升级到下一个兼容的版本。 因此，在我们的示例中，将9.16 升级到9.20 后，升级到9.24。 

对于所有相关组件，请遵循相同的过程。

### <a name="support-for-latest-operating-systemskernels"></a>支持最新的操作系统/内核

> [!NOTE]
> 如果你计划了维护时段，并在其中包括了重新启动，则建议你首先升级 Site Recovery 组件，然后在维护时段中继续执行计划的活动。

1. 升级操作系统/内核版本之前，请验证 Site Recovery 是否支持目标版本。 

    - [AZURE VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)支持。
    - [VMware/物理服务器](vmware-physical-azure-support-matrix.md#replicated-machines)支持
    - [Hyper-v](hyper-v-azure-support-matrix.md#replicated-vms)支持。
2. 查看[可用的更新](site-recovery-whats-new.md)，了解你要升级的内容。
3. 升级到最新的 Site Recovery 版本。
4. 将操作系统/内核升级到所需的版本。
5. 要求.


此过程可确保计算机操作系统/内核升级到最新版本，并确保在计算机上加载支持新版本所需的最新 Site Recovery 更改。

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure VM 灾难恢复到 Azure

在此方案中，我们强烈建议你[启用自动更新](azure-to-azure-autoupdate.md)。 你可以允许 Site Recovery 管理更新，如下所示：

- 在启用复制过程期间。
- 通过设置保管库中的扩展更新设置。

如果要手动管理更新，请执行以下操作：

1. 在保管库中 > "**复制的项**"，单击屏幕顶部的 "通知"： 
    
    **新 Site Recovery 复制代理更新可用。单击以安装->**

4. 选择要应用更新的 Vm，然后单击 **"确定"** 。


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware VM/物理服务器到 Azure 的灾难恢复

1. 根据当前版本和[支持声明](#support-statement-for-azure-site-recovery)，请使用[这些说明](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)在本地配置服务器上首先安装更新。 
2. 如果有横向扩展进程服务器，请在下一步使用[这些说明](vmware-azure-manage-process-server.md#upgrade-a-process-server)进行更新。
3. 若要更新每台受保护计算机上的移动代理，请参阅[此](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)文。

### <a name="reboot-after-mobility-service-upgrade"></a>移动服务升级后重新启动

建议在每次升级移动服务后重新启动，以确保在源计算机上加载所有最新的更改。

重新启动并不是必需的，除非上一次重新启动时代理版本和当前版本之间的差异大于四。

下表中的示例显示了其工作原理。

|**代理版本（上次重新启动）** | **升级到** | **是否需要重新启动？**|
|---------|---------|---------|
|9.16 |  9.18 | 不是必需的|
|9.16 | 9.19 | 不是必需的|
| 9.16 | 9.20 | 不是必需的
 | 9.16 | 9.21 | Mandatory。<br/><br/> 升级到9.20，然后在升级到9.21 之前重新启动。

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Hyper-V VM 灾难恢复到 Azure

### <a name="between-a-hyper-v-site-and-azure"></a>在 Hyper-v 站点与 Azure 之间

1. 下载 Microsoft Azure Site Recovery 提供程序的更新。
2. 在 Site Recovery 中注册的每个 Hyper-v 服务器上安装提供程序。 如果正在运行群集，请在所有群集节点上升级。


## <a name="between-an-on-premises-vmm-site-and-azure"></a>在本地 VMM 站点与 Azure 之间
1. 下载 Microsoft Azure Site Recovery 提供程序的更新。
2. 在 VMM 服务器上安装提供程序。 如果 VMM 部署在群集中，则在所有群集节点上安装该提供程序。
3. 在所有 Hyper-v 主机或群集节点上安装最新的 Microsoft Azure 恢复服务代理。


## <a name="between-two-on-premises-vmm-sites"></a>在两个本地 VMM 站点之间
1. 下载 Microsoft Azure Site Recovery 提供程序的最新更新。
2. 在管理辅助恢复站点的 VMM 服务器上安装最新的提供程序。 如果 VMM 部署在群集中，则在所有群集节点上安装该提供程序。
3. 在恢复站点更新之后，在管理主站点的 VMM 服务器上安装提供程序。

## <a name="next-steps"></a>后续步骤

按照 " [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)" 页跟踪新的更新和发布。
