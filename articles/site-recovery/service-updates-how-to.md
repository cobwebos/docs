---
title: Azure Site Recovery 更新 | Microsoft Docs
description: 概述服务更新，以及如何升级 Azure Site Recovery 中使用的组件。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/25/2019
ms.author: rajanaki
ms.openlocfilehash: bde341063fb6742bbe2a92592981d4a2a437d214
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203435"
---
# <a name="service-updates-in-azure-site-recovery"></a>Azure Site Recovery 中的服务更新
组织需确定在发生计划内和计划外中断时，如何维护数据的安全，以及如何确保应用/工作负荷处于运行状态。 Azure Site Recovery 可以在站点出现故障时让应用始终在 VM 上运行并使物理服务器保持可用，以此帮助实现 BCDR 策略。 Site Recovery 可以复制在 VM 和物理服务器上运行的工作负荷，因此当主站点不可用时，始终可以在次要位置使用这些工作负荷。 当主站点重新启动并运行时，它会将工作负荷恢复到主站点。

Site Recovery 可以为以下计算机管理复制：

- [在 Azure 区域之间进行复制的 Azure VM](azure-to-azure-tutorial-dr-drill.md)。
- 将数据复制到 Azure 或辅助站点的本地虚拟机和物理服务器。
有关详细信息，请参阅[此文档](https://docs.microsoft.com/azure/site-recovery)。

Azure Site Recovery 定期发布服务更新 - 包括添加新功能、改进支持矩阵和修复 bug（如果有）。 为了持续利用所有最新功能、增强功能和 bug 修复（如果有），我们建议用户始终更新到最新版本的 Azure SIte Recovery 组件。 
 
## <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery 的支持说明 

> [!IMPORTANT]
> **每次发布 Azure Site Recovery 组件的版本“N”，“N-4”以下的所有版本都被视为不受支持**。 因此，始终建议升级到最新可用版本。

> [!IMPORTANT]
> 官方的升级支持适用于 N-4 以上的版本到 N 版本（N 是最新版本）。 如果你正在使用 N-6，则需要先升级到 N-4，然后升级到 N。

## <a name="expiry-of-components"></a>组件的过期日期
Site Recovery 通知客户即将到期的组件或 （如果您已经订阅了它们） 通过电子邮件通知已过期或在门户的保管库仪表板上。 如果要保护的大肆宣传 rV VM 目前保管库仪表板通知不可用。 此外，如果你导航到相应的基础结构视图为你的方案，将有的组件，将重定向到下载最新版本的链接旁边的可用更新按钮。

下面是组件即将到期的电子邮件通知的频率。
- 60 天之后该组件即将过期,： 一次每两周
- 下一步 53 天： 每周一次
- 过去 7 天内：每日一次
- 在其过期后： 一次每两周



### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>当前版本与最新发行版之间的差大于 4 时进行升级

1. 首先将当前安装的组件从版本 N 升级到 N+4，然后升级到下一个兼容版本。 假设最新版本是 9.24，你正在使用 9.16，那么，请先升级到 9.20，然后再升级到 9.24。
2. 根据方案针对所有组件遵循相同的过程。

### <a name="support-for-latest-oskernel-versions"></a>最新 OS/内核版本的支持

> [!NOTE]
> 如果你计划了维护时段，并且每次维护后都需要重新启动，则我们建议先升级 Site Recovery 组件，然后继续执行剩余的计划活动。

1. 升级内核/OS 版本之前，请先验证目标版本是否受 Azure Site Recovery 的支持。 可以在 Azure VM、[VMware VM](vmware-physical-azure-support-matrix.md) 和 Hyper-V VM 的文档中找到相关信息
2. 请参阅[服务更新](https://azure.microsoft.com/updates/?product=site-recovery)，确定哪个 Site Recovery 组件版本支持所要升级到的特定版本。
3. 首先，请升级到最新版本的 Site Recovery。
4. 接下来，将 OS/内核升级到所需的版本。
5. 执行重新启动。
6. 这可以确保将计算机上的 OS/内核版本升级到最新版本，同时在源计算机上加载支持新版本所需的最新 Site Recovery 更改。



## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure VM 灾难恢复到 Azure
对于此方案，我们强烈建议[启用](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate)自动更新。 可选择允许 Site Recovery 通过以下方式管理更新：

- 在启用复制步骤的过程中
- 在保管库内切换扩展更新设置

如果已选择手动管理更新，请执行以下步骤：

1. 转到 Azure 门户并导航到“恢复服务保管库”。
2. 在 Azure 门户中转到“恢复服务保管库”对应的“已复制的项”窗格。
3. 单击屏幕顶部的以下通知：
    
    “有新的 Site Recovery 复制代理更新可用。 
    
    请单击此处安装 ->” 

4. 选择要将更新应用到的 VM，然后单击“确定”。 

## <a name="between-two-on-premises-vmm-sites"></a>在两个本地 VMM 站点之间
1. 下载 Microsoft Azure Site Recovery 提供程序的最新更新汇总。
2. 首先在管理站点恢复的本地 VMM 服务器上安装更新汇总。
3. 更新恢复站点后，在管理主站点的 VMM 服务器上安装更新汇总。

> [!NOTE]
> 如果 VMM 是高可用性 VMM（群集 VMM），请确保在安装了 VMM 服务的所有群集节点上安装升级。

## <a name="between-an-on-premises-vmm-site-and-azure"></a>在本地 VMM 站点与 Azure 之间
1. 下载 Microsoft Azure Site Recovery 提供程序的更新汇总。
2. 在本地 VMM 服务器上安装更新汇总。
3. 在所有 Hyper-V 主机上安装最新的 MARS 代理。

> [!NOTE]
> 如果 VMM 是高可用性 VMM（群集 VMM），请确保在安装了 VMM 服务的所有群集节点上安装升级。

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>在本地 Hyper-V 站点与 Azure 之间

1. 下载 Microsoft Azure Site Recovery 提供程序的更新汇总。
2. 在已注册到 Azure Site Recovery 中的每个 Hyper-V 服务器节点上安装该提供程序。

> [!NOTE]
> 如果 Hyper-V 是主机群集 Hyper-V 服务器，请确保在该群集的所有节点上安装升级

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>在本地 VMware 或物理站点与 Azure 之间

进行更新之前，请参阅 [Site Recovery 支持声明](#support-statement-for-azure-site-recovery)以了解升级路径。

1. 根据当前版本和上面提供的支持声明，按照[此处](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)提供的准则，先在本地管理服务器上安装更新。 这是具有配置服务器和进程服务器角色的服务器。
2. 如果横向扩展了进程服务器，接下来请按照[此处](vmware-azure-manage-process-server.md#upgrade-a-process-server)提供的准则更新这些服务器。
3. 接下来，若要对每个受保护的项更新移动代理，请转到 Azure 门户，然后转到“受保护的项”   > “复制的项”  页面。 在此页上选择一个 VM。 选择页面底部显示的、每个 VM 对应的“更新代理”按钮。  这会更新所有受保护 VM 上的移动服务代理。

### <a name="reboot-of-source-machine-after-mobility-agent-upgrade"></a>移动代理升级之后重新启动源计算机

建议在每次升级移动代理后重新启动，以确保在源计算机上加载所有最新更改。 不过，重新启动**不是必需的**操作。 如果上次重新启动时的代理版本与当前版本之间的差大于 4，则必须重新启动。 请参考下表中的详细说明。

|**上次重新启动时的代理版本** | **升级到** | **是否必须重新启动？**|
|---------|---------|---------|
|9.16 |  9.18 | 不是必需的|
|9.16 | 9.19 | 不是必需的|
| 9.16 | 9.20 | 不是必需的
 | 9.16 | 9.21 | 是，应该先升级到 9.20，再重新启动，然后升级到 9.21，因为版本差大于 4（上次执行重新启动时的版本为 9.16，目标版本为 9.21）。

## <a name="links-to-currently-supported-update-rollups"></a>当前受支持更新汇总的链接

|更新汇总  |提供程序  |统一安装程序| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[更新汇总 37](https://support.microsoft.com/help/4508614/update-rollup-37-for-azure-site-recovery)     |   5.1.4300.0  |  9.25.5241.1   |  5.1.4300.0  | 2.0.9163.0
|[更新汇总 36](https://support.microsoft.com/en-in/help/4503156)     |   5.1.4150.0  |  9.24.5211.1   |  5.1.4150.0  | 2.0.9160.0
|[更新汇总 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)     |   5.1.4000.0  |  9.23.5163.1   |  5.1.4000.0  | 2.0.9156.0
|[更新汇总 34](https://support.microsoft.com/en-us/help/4490016/update-rollup-34-for-azure-site-recovery) - 修补程序     |   5.1.3950.0  |  9.22.5142.1   |  5.1.3950.0  | 2.0.9155.0
|[更新汇总 33](https://support.microsoft.com/en-us/help/4489582/update-rollup-33-for-azure-site-recovery)     |   5.1.3900.0  |  9.22.5109.1   |  5.1.3900.0  | 2.0.9155.0
|[更新汇总 32](https://support.microsoft.com/en-us/help/4485985/update-rollup-32-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0

## <a name="previous-update-rollups"></a>以前的更新汇总

- [更新汇总 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)
- [更新汇总 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)
- [更新汇总 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)
- [更新汇总 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)
- [更新汇总 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)
- [更新汇总 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [更新汇总 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [更新汇总 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [更新汇总 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [更新汇总 21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [更新汇总 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [更新汇总 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
