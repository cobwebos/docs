---
title: 常见问题 - 使用 Azure Site Recovery 进行 Hyper-V 到 Azure 的灾难恢复 | Microsoft Docs
description: 本文汇总了有关使用 Azure Site Recovery 站点设置本地 Hyper-V VM 到 Azure 的灾难恢复的常见问题。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 03/18/2018
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 4888d019065e557cb49574e2268515323b3fd005
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310697"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>常见问题 - Hyper-V 到 Azure 的灾难恢复

本文提供将本地 Hyper-V VM 复制到 Azure 时可能遇到的常见问题的解答。 


## <a name="general"></a>常规

### <a name="how-is-site-recovery-priced"></a>Site Recovery 如何计费？
请查看 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)。

### <a name="how-do-i-pay-for-azure-vms"></a>Azure VM 如何计费？
在复制期间，数据将复制到 Azure 存储，因此，VM 不会产生任何费用。 故障转移到 Azure 时，Site Recovery 会自动创建 Azure IaaS 虚拟机。 然后，在 Azure 中使用的计算资源会产生费用。

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-azure"></a>需要在 Azure 中做好哪些准备？
需要一个 Azure 订阅、一个恢复服务保管库、一个存储帐户和一个虚拟网络。 保管库、存储帐户和网络必须位于同一区域。

### <a name="what-azure-storage-account-do-i-need"></a>需要哪个 Azure 存储帐户？
需要 LRS 或 GRS 存储帐户。 建议使用 GRS，以便在发生区域性故障或无法恢复主要区域时，能够复原数据。 支持高级存储。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>我的 Azure 帐户是否需要拥有创建 VM 的权限？
如果你是订阅管理员，则已经获得了所需的复制权限。 否则，需要有权在配置 Site Recovery 时指定的资源组和虚拟网络中创建 Azure VM，并有权写入选定的存储帐户。 [了解详细信息](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。

### <a name="is-replication-data-sent-to-site-recovery"></a>复制数据是否会发送到 Site Recovery？
不会。Site Recovery 不会拦截复制的数据，也不包含 VM 上运行的组件的任何相关信息。 复制数据在 Hyper-V 主机与 Azure 存储之间交换。 站点恢复并不具有拦截该数据的能力。 只有协调复制与故障转移所需的元数据将发送到站点恢复服务。  

站点恢复已通过 ISO 27001:2013、27018、HIPAA、DPA 认证，目前正在接受 SOC2 和 FedRAMP JAB 评估。

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>是否可将本地元数据保留在某个地理区域中？
是的。 当你在某个区域中创建保管库时，我们会确保 Site Recovery 使用的所有元数据保留在该区域的地理边界内。

### <a name="does-site-recovery-encrypt-replication"></a>站点恢复是否将复制数据加密？
是的，传输中加密和 [Azure 中加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)均受支持。


## <a name="deployment"></a>部署

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>可通过哪些方法实现 Hyper-V 到 Azure 的复制？

- **灾难恢复**：可以设置完整的灾难恢复。 在此场景中，可将本地 Hyper-V VM 复制到 Azure 存储：
    - 可将 VM 复制到 Azure。 如果本地基础结构不可用，则可以故障转移到 Azure。
    - 在故障转移时，将使用复制的数据创建 Azure VM。 可以访问 Azure VM 上的应用和工作负载。
    - 本地数据中心重新可用时，可从 Azure 故障回复到本地站点。
- **迁移**：可以使用 Site Recovery 将本地 Hyper-V VM 迁移到 Azure 存储。 然后从本地故障转移到 Azure。 故障转移后，应用和工作负荷可供使用并在 Azure VM 上运行。


### <a name="what-do-i-need-on-premises"></a>需要在本地做好哪些准备？

需要一个或多个在一个或多个独立或群集 Hyper-V 主机上运行的 VM。 还可以复制 System Center Virtual Machine Manager (VMM) 托管的主机上运行的 VM。
- 如果不运行 VMM，则在 Site Recovery 部署期间，将 Hyper-V 主机和群集收集到 Hyper-V 站点中。 在每个 Hyper-V 主机上安装 Site Recovery 代理（Azure Site Recovery 提供程序和 Recovery Services 代理）。
- 如果 Hyper-V 主机位于 VMM 云中，则在 VMM 中协调复制。 在 VMM 服务器上安装 Site Recovery 提供程序，在每个 Hyper-V 主机上安装恢复服务代理。 在 VMM 逻辑/VM 网络和 Azure VNet 之间进行映射。
- [详细了解](hyper-v-azure-architecture.md) Hyper-V 到 Azure 的体系结构。

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>是否可以复制 Hyper-V 群集上的 VM？

可以，Site Recovery 支持群集 Hyper-V 主机。 请注意：

- 群集的所有节点应注册到同一保管库。
- 如果不使用 VMM，则应将群集中的所有 Hyper-V 主机添加到同一 Hyper-V 站点。
- 在群集中每个 Hyper-V 主机上安装 Azure Site Recovery 提供程序和恢复服务代理，并将每个主机添加到 Hyper-V 站点。
- 无需在群集上执行任何特定步骤。
- 如果对 Hyper-V 运行部署规划器工具，此工具会从正在运行且其中运行此 VM 的节点收集配置文件数据。 该工具不会从已关闭的节点中收集任何数据，但会跟踪该节点。 该节点启用和运行后，此工具开始从其中收集 VM 配置文件数据（如果此 VM 是配置文件 VM 列表的一部分且在该节点上运行）。
- 如果 Site Recovery 保管库中的 Hyper-V 主机上的某个 VM 迁移到同一群集中的其他 Hyper-V 主机或迁移到独立主机，则该 VM 的复制不受影响。 Hyper-V 主机必须满足[先决条件](hyper-v-azure-support-matrix.md#on-premises-servers)，且必须在 Site Recovery 保管库中进行配置。 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>当 Hyper-V 在客户端操作系统上运行时，我可以保护 VM 吗？
不可以。VM 必须位于在受支持的 Windows 服务器计算机上运行的 Hyper-V 主机服务器上。 如果需要保护客户端计算机，可以[将其作为物理计算机复制](physical-azure-disaster-recovery.md)到 Azure。

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>我可以将 Hyper-V 第 2 代虚拟机复制到 Azure 吗？
是的。 站点恢复在故障转移过程中将从第 2 代转换成第 1 代。 在故障回复时，计算机将转换回到第 2 代。

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>是否可以使用 SDK 自动化 Site Recovery 方案？
是的。 可以使用 Rest API、PowerShell 或 Azure SDK 将站点恢复工作流自动化。 以下为通过 PowerShell 将 Hyper-V 复制到 Azure 的当前支持方案：

- [使用 PowerShell 在无 VMM 的情况下复制 Hyper-V](hyper-v-azure-powershell-resource-manager.md)
- [使用 PowerShell 在有 VMM 的情况下复制 Hyper-V](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>复制

### <a name="where-do-on-premises-vms-replicate-to"></a>本地 VM 将复制到哪个位置？
数据将复制到 Azure 存储。 运行故障转移时，Site Recovery 会自动从存储帐户创建 Azure VM。

### <a name="what-apps-can-i-replicate"></a>可以复制哪些应用？
可以复制任何运行符合[复制要求](hyper-v-azure-support-matrix.md#replicated-vms)的 Hyper-V VM 的应用或工作负荷均。 Site Recovery 支持应用程序感知型复制，因此，应用可以故障转移或故障回复到智能状态。 Site Recovery 除了与 Microsoft 应用程序（例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory）集成之外，还能与行业领先的供应商（包括 Oracle、SAP、IBM 及 Red Hat）紧密配合。 [详细了解](site-recovery-workload.md)工作负荷保护。

### <a name="whats-the-replication-process"></a>复制过程是什么？

1. 当触发初始复制时，系统会拍摄 Hyper-V VM 快照。
2. VM 上的虚拟硬盘是逐一复制的，直至全部复制到 Azure 为止。 该过程可能需要一些时间，具体取决于 VM 大小和网络带宽。 了解如何增加网络带宽。
3. 如果在初始复制期间发生磁盘更改，Hyper-V 副本复制跟踪器将跟踪这些更改，并将其记录在 Hyper-V 复制日志 (.hrl) 中。 这些日志文件位于与磁盘相同的文件夹中。 每个磁盘都有一个关联的 .hrl 文件，该文件将发送到辅助存储器。 当初始复制正在进行时，快照和日志将占用磁盘资源。
4. 当初始复制完成时，将删除 VM 快照。
5. 日志中的任何磁盘更改会进行同步，并合并到父磁盘中。
6. 初始复制完成后，“在虚拟机上完成保护”作业将运行。 该作业会配置网络和其他复制后设置以便保护 VM。
7. 在此阶段，可以检查 VM 设置以确保它已为故障转移做好准备。 可针对 VM 运行灾难恢复钻取（测试故障转移）来检查它是否按预期进行故障转移。
8. 在完成初始复制后，根据复制策略开始增量复制同步。
9. 更改是所记录的 .hrl 文件。 为复制配置的每个磁盘都有一个关联的 .hrl 文件。
10. 此日志会发送到客户的存储帐户。 当日志正处于传输到 Azure 的过程中时，主磁盘中的变更会记录到同一文件夹的另一日志文件中。
11. 在初始复制和增量复制过程中，均可以在 Azure 门户中监视 VM。

[详细了解](hyper-v-azure-architecture.md#replication-process)复制过程。

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>是否可以使用站点到站点 VPN 复制到 Azure？

Site Recovery 通过公共终结点或使用 ExpressRoute 公共对等互连将数据从本地复制到 Azure 存储。 不支持通过站点到站点 VPN 网络进行的复制。

### <a name="can-i-replicate-to-azure-with-expressroute"></a>是否可以使用 ExpressRoute 复制到 Azure？

可以使用 ExpressRoute 将 VM 复制到 Azure。 Site Recovery 通过公共终结点将数据复制到 Azure 存储帐户。需要设置[公共对等互连](../expressroute/expressroute-circuit-peerings.md#publicpeering)才能进行 Site Recovery 复制。 将 VM 故障转移到 Azure 虚拟网络后，可以使用[专用对等互连](../expressroute/expressroute-circuit-peerings.md#privatepeering)访问这些 VM。


### <a name="why-cant-i-replicate-over-vpn"></a>为何不能通过 VPN 复制？

复制到 Azure 时，复制流量会到达 Azure 存储帐户的公共终结点，因此，只能使用 ExpressRoute（公共对等互连）通过公共 Internet 进行复制，而不能使用 VPN。 

### <a name="what-are-the-replicated-vm-requirements"></a>复制的 VM 要满足哪些要求？

若要复制某个 Hyper-V VM，该 VM 必须运行受支持的操作系统。 此外，该 VM 必须满足 Azure VM 的要求。 在支持矩阵中[了解详细信息](hyper-v-azure-support-matrix.md#replicated-vms)。

### <a name="how-often-can-i-replicate-to-azure"></a>可以多久复制到 Azure 一次？

可以每隔 30 秒（高级存储除外）、5 分钟或 15 分钟复制一次 Hyper-V VM。

### <a name="can-i-extend-replication"></a>是否可以扩展复制？
不支持扩展扩展或链式复制。 请在[反馈论坛](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中请求此功能。

### <a name="can-i-do-an-offline-initial-replication"></a>是否可以执行脱机初始复制？
不支持此操作。 请在[反馈论坛](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中请求此功能。

### <a name="can-i-exclude-disks"></a>是否可以排除磁盘？
可以从复制中排除磁盘。 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>是否可以复制包含动态磁盘的 VM？
可以复制动态磁盘。 操作系统磁盘必须为基本磁盘。



## <a name="security"></a>安全

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Site Recovery 需要对 Hyper-V 主机拥有哪些访问权限？

Site Recovery 需要对 Hyper-V 主机具备访问权限才能复制所选的 VM。 Site Recovery 在 Hyper-V 主机上安装以下代理：

- 如果不运行 VMM，则在每个主机上安装 Azure Site Recovery 提供程序和恢复服务代理。
- 如果运行 VMM，则在每个主机上安装恢复服务代理。 此提供程序在 VMM 服务器上运行。


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Site Recovery 在 Hyper-V VM 上安装什么内容？

Site Recovery 不会在启用复制的 Hyper-V VM 上显式安装任何内容。




## <a name="failover-and-failback"></a>故障转移和故障回复


### <a name="how-do-i-fail-over-to-azure"></a>如何故障转移到 Azure？

可以运行从本地 Hyper-V VM 到 Azure 的计划内或计划外故障转移。
    - 如果运行计划的故障转移，源 VM 将关闭以确保不会丢失数据。
    - 如果无法访问主站点，可以运行计划外故障转移。
    - 可以故障转移单个虚拟机，或者创建恢复计划来协调多个虚拟机的故障转移。
    - 运行故障转移。 故障转移的第一阶段完成后，应该会在 Azure 中看到创建的副本 VM。 如果需要，可向 VM 分配公共 IP 地址。 然后，提交故障转移以开始从副本 Azure VM 访问工作负载。
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>故障转移后如何访问 Azure VM？
故障转移后，可以通过安全的 Internet 连接、站点到站点 VPN 或 Azure ExpressRoute 来访问 Azure VM。 在连接之前需要做许多准备。 [了解详细信息](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>故障转移的数据是否有复原能力？
Azure 具有复原能力。 Site Recovery 能够根据 Azure SLA 故障转移到辅助 Azure 数据中心。 发生故障转移时，我们会确保元数据和保管库保留在为保管库选择的同一地理区域中。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？
[故障转移](site-recovery-failover.md)不是自动的。 启动在门户中，单击一下故障转移，也可以使用[PowerShell](/powershell/module/azurerm.siterecovery)触发故障转移。

### <a name="how-do-i-fail-back"></a>如何故障回复？

在本地基础结构启动并再次运行后，即可进行故障回复。 故障回复会出现在以下三个阶段：

1. 可以使用以下不同选项启动从 Azure 到本地站点的计划内故障转移：

    - 最大限度减少停机时间：如果使用此选项，Site Recovery 将在故障转移之前同步数据。 它会检查更改的数据块并将它们下载到本地站点，同时让 Azure VM 保持运行并最大限度减少停机时间。 当手动指定故障转移应完成时，Azure VM 会关闭，任何最终增量更改会被复制，而故障转移将启动。
    - 完整下载：使用此选项可在故障转移期间同步数据。 此选项会下载整个磁盘。 该操作更快，因为不计算校验和，但停机时间会增加。 如果运行副本 Azure VM 已有一段时间，或者如果本地 VM 已删除，请使用此选项。

2. 可选择故障回复到同一 VM 或备用 VM。 如果 VM 尚不存在，可指定 Site Recovery 应创建 VM。
3. 初始数据同步完成后，选择完成故障转移。 该操作完成后，可以登录到本地 VM 验证一切是否按预期运行。 在 Azure 门户中，可以看到 Azure VM 均已停止。
4. 完成故障转移的提交，并重新开始从本地 VM 访问工作负载。
5. 在工作负载进行故障回复后，启用反向复制，以便本地 VM 重新复制到 Azure。

### <a name="can-i-fail-back-to-a-different-location"></a>是否可以故障回复到不同位置？
可以。故障转移到 Azure 后，如果原始位置不可用，可以故障回复到不同的位置。 [了解详细信息](hyper-v-azure-failback.md#failback-to-an-alternate-location-in-hyper-v-environment)。
