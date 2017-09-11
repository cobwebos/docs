---
title: "Azure Site Recovery：常见问题解答 | Microsoft 文档"
description: "本文讨论了有关 Azure Site Recovery 的常见问题。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5cdc4bcd-b4fe-48c7-8be1-1db39bd9c078
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/22/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2bdec82891bbd61e3526bd4498f802a0de068f87
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery：常见问题 (FAQ)
本文包含有关 Azure Site Recovery 的常见问题。 如果在阅读本文后有任何问题，请在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)上发布问题。

## <a name="general"></a>常规
### <a name="what-does-site-recovery-do"></a>站点恢复的功能是什么？
通过协调和自动化 Azure VM 复制（本地区域虚拟机和物理服务器到 Azure；本地计算机到辅助数据中心），Site Recovery 可帮助实现业务连续性与灾难恢复 (BCDR) 策略。 [了解详细信息](site-recovery-overview.md)。

### <a name="what-can-site-recovery-protect"></a>站点恢复可以保护哪些计算机？
* Azure VM：Site Recovery 可复制受支持 Azure VM 上运行的任何工作负荷
* **Hyper-V 虚拟机**：站点恢复可以保护 Hyper-V VM 上运行的任何工作负荷。
* **物理服务器**：站点恢复可以保护运行 Windows 或 Linux 的物理服务器。
* **VMware 虚拟机**：站点恢复可以保护 VMware VM 上运行的任何工作负荷。

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>站点恢复是否支持 Azure Resource Manager 模型？
可在 Azure 门户中使用 Site Recovery，并支持 Resource Manager。 Site Recovery 支持 Azure 经典门户中的旧版部署。 不能在经典门户中创建新保管库，且不支持新功能。

### <a name="can-i-replicate-azure-vms"></a>我能复制 Azure VM 吗？
可以，用户可在 Azure 区域间复制受支持的 Azure VM。 [了解详细信息](site-recovery-azure-to-azure.md)。

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>为什么需要在 Hyper-V 中使用站点恢复协调复制？
对于 Hyper-V 主机服务器，所需取决于部署方案。 在以下内容中查看 Hyper-V 先决条件：

* [将 Hyper-V VM 复制（不使用 VMM）到 Azure](site-recovery-hyper-v-site-to-azure.md)
* [将 Hyper-V VM 复制（使用 VMM）到 Azure](site-recovery-vmm-to-azure.md)
* [将 Hyper-V VM 复制到辅助数据中心](site-recovery-vmm-to-vmm.md)
* 如果要复制到辅助数据中心，请阅读 [Hyper-V 虚拟机的受支持的来宾操作系统](https://technet.microsoft.com/library/mt126277.aspx)。
* 如果要复制到 Azure，站点恢复支持 [Azure 支持的](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)所有来宾操作系统。

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>当 Hyper-V 在客户端操作系统上运行时，我可以保护 VM 吗？
不可以。VM 必须位于在受支持的 Windows 服务器计算机上运行的 Hyper-V 主机服务器上。 如果需要保护客户端计算机，可以将其作为物理计算机复制到 [Azure](site-recovery-vmware-to-azure.md) 或[辅助数据中心](site-recovery-vmware-to-vmware.md)。

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>我可以使用站点恢复来保护哪些工作负荷？
可以使用站点恢复来保护在支持的 VM 或物理服务器上运行的大多数工作负荷。 站点恢复为应用程序感知型复制提供支持，因此，应用可以恢复为智能状态。 它除了与 Microsoft 应用程序（例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory）集成之外，还能与行业领先的供应商（包括 Oracle、SAP、IBM 及 Red Hat）紧密配合。 [详细了解](site-recovery-workload.md)工作负荷保护。

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hyper-V 主机是否需要位于 VMM 云中？
如果要复制到辅助数据中心，那么 Hyper-V VM 就必须位于 VMM 云中的 Hyper-V 主机服务器上。 如果想要复制到 Azure，那么可以复制 Hyper-V 主机服务器（无论是否位于 VMM 云中）上的 VM。 [了解详细信息](site-recovery-hyper-v-site-to-azure.md)。

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>如果我只有一个 VMM 服务器，可以部署站点恢复来配合 VMM 吗？

是的。 可以将 VMM 云中 Hyper-V 服务器上的 VM 复制到 Azure，或者在同一台服务器上的 VMM 云之间进行复制。 对于本地到本地复制，我们建议在主站点与辅助站点中都部署一个 VMM 服务器。  

### <a name="what-physical-servers-can-i-protect"></a>我可以保护哪些物理服务器？
可以将运行 Windows 或 Linux 的物理服务器复制到 Azure 或辅助站点。 [了解](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)操作系统要求。  无论是将物理服务器复制到 Azure 还是辅助站点，都需要满足相同的要求。


请注意，如果本地服务器关闭，物理服务器会在 Azure 中以 VM 的形式运行。 当前不支持故障回复到本地物理服务器。 对于作为物理机进行保护的计算机，只能故障回复到 VMware 虚拟机。

### <a name="what-vmware-vms-can-i-protect"></a>我可以保护哪些 VMware VM？

若要保护 VMware VM，则需要 vSphere 虚拟机监控程序和运行 VMware 工具的虚拟机。 我们还建议使用 VMware vCenter 服务器托管虚拟机监控程序。 [了解](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)有关复制 VMware 服务器和 VM 到 Azure 或辅助站点的精确要求。


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>我可以使用站点恢复来管理分支机构的灾难恢复吗？
是的。 使用站点恢复来协调分支机构的复制与故障转移时，可以在一个中心位置获得所有分支机构工作负载的统一视图。 不需要前往分支机构，就可以从总部轻松对所有分支机构运行故障转移和管理灾难恢复。

## <a name="pricing"></a>定价

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>使用 Azure Site Recovery 时，会产生哪些费用？
使用 Site Recovery 时，会产生 Site Recovery 许可证、Azure 存储、存储事务和出站数据传输费用。 [了解详细信息](https://azure.microsoft.com/pricing/details/site-recovery)。

Site Recovery 许可证费用根据受保护的实例收取，实例可以是 VM 或物理服务器。

- 如果将 VM 磁盘复制到标准存储帐户，Azure 存储费用根据存储消耗量收取。 例如，如果源磁盘大小为 1 TB，使用了其中的 400 GB，则 Site Recovery 会在 Azure 中创建 1 TB VHD，但存储费用根据 400 GB（加上复制日志使用的存储空间量）收取。
- 如果将 VM 磁盘复制到高级存储帐户，Azure 存储费用将按照预配的存储大小（已根据最接近的高级存储磁盘选项舍入）收取。 例如，如果源磁盘大小为 50 GB，则 Site Recovery 会在 Azure 中创建 50 GB 磁盘，Azure 会将此大小对应为最接近的高级存储磁盘 (P10)。  费用将按照 P10 计算，而不是按照 50 GB 磁盘大小计算。  [了解详细信息](https://aka.ms/premium-storage-pricing)。  如果使用高级存储，则还需要为复制日志记录使用一个标准存储帐户，用于这些日志的标准存储空间量也会计费。
- 在进行测试故障转移或故障转移前，不创建磁盘。 在复制状态下，根据[存储定价计算器](https://azure.microsoft.com/en-in/pricing/calculator/)在“页 blob 和磁盘”目录下会发生存储费用。 这些费用基于高级/标准存储类型和数据冗余类型 - LRS、GRS、RA-GRS 等。
- 如果选择在故障转移时使用托管磁盘，则在进行故障转移/测试故障转移后，会产生[托管磁盘费用](https://azure.microsoft.com/en-in/pricing/details/managed-disks/)。 复制期间不会产生托管磁盘费用。
- 如果未选择在故障转移时使用托管磁盘，则在进行故障转移后，根据[存储定价计算器](https://azure.microsoft.com/en-in/pricing/calculator/)在“页 blob 和磁盘”目录下会发生存储费用。 这些费用基于高级/标准存储类型和数据冗余类型 - LRS、GRS、RA-GRS 等。
- 在稳定状态复制期间会发生存储交易费用，且在进行故障转移/测试故障转移后，针对常规 VM 操作也会发生存储交易费用。 但这些费用低到可以忽略不计。

测试故障转移期间也会产生 VM、存储、传出流量和存储事务方面的费用。



## <a name="security"></a>“安全”
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>复制数据是否会发送到 Site Recovery 服务？
不会。站点恢复不拦截复制的数据，也不拥有虚拟机或物理服务器上运行哪些项目的任何相关信息。
复制数据在本地 Hyper-V 主机、VMware 虚拟机监控程序或物理服务器和 Azure 存储或辅助站点之间交换。 站点恢复并不具有拦截该数据的能力。 只有协调复制与故障转移所需的元数据将发送到站点恢复服务。  

站点恢复已通过 ISO 27001:2013、27018、HIPAA、DPA 认证，目前正在接受 SOC2 和 FedRAMP JAB 评估。

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>为了遵从法规，即使是本地元数据也必须保留在同一个地理区域。 站点恢复可以帮助我们吗？
是的。 在某个区域中创建站点恢复保管库时，我们确保启用和协调复制与故障转移时所需的一切元数据都保留在该区域的地理边界范围内。

### <a name="does-site-recovery-encrypt-replication"></a>站点恢复是否将复制数据加密？
在本地站点之间复制虚拟机和物理服务器时，支持传输中加密。 将虚拟机和物理服务器复制到 Azure 时，同时支持传输中加密和[静态加密（Azure 中）](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption)。

## <a name="replication"></a>复制

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>能否通过站点到站点 VPN 复制到 Azure？
Azure Site Recovery 通过公共终结点将数据复制到 Azure 存储帐户。 复制不是通过站点到站点 VPN 进行。 可以使用 Azure 虚拟网络创建站点到站点 VPN。 这不会干扰 Site Recovery 复制。

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>能否使用 ExpressRoute 将虚拟机复制到 Azure？
能，可以使用 ExpressRoute 将虚拟机复制到 Azure。 Azure Site Recovery 通过公共终结点将数据复制到 Azure 存储帐户。 需要设置[公共对等互连](../expressroute/expressroute-circuit-peerings.md#public-peering)将 ExpressRoute 用于 Site Recovery 复制。 将虚拟机故障转移到 Azure 虚拟网络以后，即可使用通过 Azure 虚拟网络设置的[专用对等互连](../expressroute/expressroute-circuit-peerings.md#private-peering)对其进行访问。

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>将虚拟机复制到 Azure 需要满足任何先决条件吗？
要复制到 Azure 的虚拟机应符合 [Azure 要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

Azure 用户帐户需要具有某些[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能启用新的虚拟机到 Azure 的复制。

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>我可以将 Hyper-V 第 2 代虚拟机复制到 Azure 吗？
是的。 站点恢复在故障转移过程中将从第 2 代转换成第 1 代。 在故障回复时，计算机将转换回到第 2 代。 [了解详细信息](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>如果复制到 Azure，我要支付哪些 Azure VM 费用？
在常规复制期间，数据将复制到异地冗余的 Azure 存储，不需要支付任何 Azure IaaS 虚拟机费用（一个明显的优势）。 故障转移到 Azure 时，站点恢复会自动创建 Azure IaaS 虚拟机，此后，需要为在 Azure 中使用的计算资源付费。

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>是否可以使用 SDK 自动化 Site Recovery 方案？
是的。 可以使用 Rest API、PowerShell 或 Azure SDK 将站点恢复工作流自动化。 当前支持的使用 PowerShell 部署站点恢复的方案：

* [将 VMM 云中的 Hyper-V VM 复制到 Azure PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [将 Hyper-V VM 复制（不使用 VMM）到 Azure PowerShell Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>如果要复制到 Azure，我需要哪种存储帐户？
* **Azure 经典门户**：如果要在 Azure 经典门户中部署站点恢复，则需要 [标准异地冗余存储帐户](../storage/common/storage-redundancy.md#geo-redundant-storage)。 当前不支持高级存储。 该帐户必须位于与站点恢复保管库相同的区域中。
* **Azure 门户**：如果要在 Azure 门户中部署站点恢复，则需要 LRS 或 GRS 存储帐户。 建议使用 GRS，以便在发生区域性故障或无法恢复主要区域时，能够复原数据。 该帐户必须位于与恢复服务保管库相同的区域中。 在 Azure 门户中部署 Site Recovery 时，现在支持将高级存储用于 VMware VM、Hyper-V VM 和物理服务器复制。

### <a name="how-often-can-i-replicate-data"></a>我可以多久复制数据一次？
* **Hyper-V：**可以每隔 30 秒（高级存储除外）、5 分钟或 15 分钟复制一次 Hyper-V VM。 如果已设置 SAN 复制，则复制将是同步的。
* **VMware 和物理服务器：**复制频率无关紧要。 复制是连续的。

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>我可以将复制从现有的恢复站点扩展到其他站点吗？
不支持扩展扩展或链式复制。 请在[反馈论坛](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)中请求此功能。

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>在首次复制到 Azure 时可以进行脱机复制吗？
不支持此操作。 请在[反馈论坛](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中请求此功能。

### <a name="can-i-exclude-specific-disks-from-replication"></a>可以从复制中排除特定的磁盘吗？
使用 Azure 门户将 [VMware VM 和 Hyper-V VM 复制](site-recovery-exclude-disk.md)到 Azure 时支持此操作。

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>可以使用动态磁盘来复制虚拟机吗？
复制 Hyper-V 虚拟机时，支持使用动态磁盘。 将 VMware VM 和物理计算机复制到 Azure 时也支持使用动态磁盘。 操作系统磁盘必须为基本磁盘。

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>我能否将新计算机添加到现有的复制组中？
支持将新计算机添加到现有的复制组。 要进行此操作，请从“已复制项目”边栏选项卡中，选择复制组并右键单击/选择复制组中的上下文菜单，并选择相应的选项。

![添加复制组](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>可以限制针对 Hyper-V 复制流量分配的带宽吗？
是的。 可以从以下部署文章中阅读更多有关限制带宽的信息：

* [复制 VMware VM 和物理服务器的容量规划](site-recovery-plan-capacity-vmware.md)
* [复制 VMM 云中的 Hyper-V VM 的容量规划](site-recovery-vmm-to-azure.md#capacity-planning)
* [复制 Hyper-V VM（不使用 VMM）的容量规划](site-recovery-hyper-v-site-to-azure.md)

## <a name="failover"></a>故障转移
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>在故障转移到 Azure 之后，如何访问 Azure 虚拟机？
可以通过安全的 Internet 连接或者站点到站点 VPN 或 Azure ExpressRoute 访问 Azure VM。 在连接之前需要做许多准备。 [了解详细信息](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>如果我故障转移到 Azure，Azure 如何确保我的数据可恢复？
Azure 具有复原能力。 站点恢复已经能够根据需要故障转移到符合 Azure SLA 的辅助 Azure 数据中心。 发生此情况时，我们确保元数据和保管库都保留在为保管库选择的相同地理区域。  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>如果我在两个数据中心之间进行复制，当我的主数据中心发生意外的服务中断时，会出现什么情况？
可以从辅助站点触发非计划的故障转移。 站点恢复不需要来自主站点的连接即可执行故障转移。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？
故障转移不是自动的。 可以在门户中单击一下来启动故障转移，或者使用[站点恢复 PowerShell](/powershell/module/azurerm.siterecovery) 来触发故障转移。 在站点恢复门户中可以轻松进行故障回复。

要自动化，可以使用本地 Orchestrator 或 Operations Manager 来检测虚拟机故障，并使用 SDK 来触发故障转移。

* [详细了解](site-recovery-create-recovery-plans.md)恢复计划。
* [详细了解](site-recovery-failover.md)故障转移。
* [详细了解](site-recovery-failback-azure-to-vmware.md) VMware VM 和物理服务器故障回复的信息

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>如果我的本地主机未响应或崩溃，我是否可以故障转移回到另一个主机？
是，可以使用备用位置恢复从 Azure 故障回复到另一个主机。 通过用于 VMware 和 Hyper-v 虚拟机的以下链接详细了解选项。

* [对于 VMware 虚拟机](site-recovery-how-to-failback-azure-to-vmware.md#fail-back-to-the-original-or-alternate-location)
* [对于 Hyper-v 虚拟机](site-recovery-failback-from-azure-to-hyper-v.md#failback-to-an-alternate-location)

## <a name="service-providers"></a>服务提供商
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>我是服务提供商。 站点恢复适用于专用和共享的基础结构模型吗？
是的，站点恢复同时支持专用与共享的基础结构模型。

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>对于服务提供商而言，我的租户标识是否与 Site Recovery 服务共享？
否。 租户标识是匿名的。 租户不需要访问 Site Recovery 门户。 只有服务提供商管理员才能与门户交互。

### <a name="will-tenant-application-data-ever-go-to-azure"></a>租户应用程序数据是否会发往 Azure？
在服务提供商拥有的站点之间进行复制时，永远不会将应用程序数据发送到 Azure。 数据进行传输中加密并直接在服务提供商站点之间复制。

如果是复制到 Azure，应用程序数据将发送到 Azure 存储而不是站点恢复服务。 数据进行传输中加密并在 Azure 中保持加密状态。

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>我的租户会收到来自 Azure 服务的帐单吗？
否。 Azure 直接与服务提供商保持计费关系。 服务提供商责任为其租户生成特定的帐单。

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>如果我复制到 Azure，需要在 Azure 中随时运行虚拟机吗？
不需要。会将数据复制到订阅中的 Azure 存储帐户。 执行测试故障转移（灾难恢复演练）或实际的故障转移时，站点恢复会在订阅中自动创建虚拟机。

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>当我复制到 Azure 时，们确保提供租户级的隔离吗？
是的。

### <a name="what-platforms-do-you-currently-support"></a>目前支持哪些平台？
我们支持 Azure Pack、云平台系统和基于 System Center 的（2012 和更高版本）的部署。 [了解更多](https://technet.microsoft.com/library/dn850370.aspx)有关 Azure Pack 和 Site Recovery 集成的信息。

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>是否支持单一 Azure Pack 和单一 VMM 服务器部署？
是，可以复制 Hyper-V 虚拟机到 Azure，或者在服务提供商站点之间复制。  请注意，如果在服务提供商站点之间复制，将无法使用 Azure Runbook 集成。

## <a name="next-steps"></a>后续步骤
* 阅读 [站点恢复概述](site-recovery-overview.md)
* 了解有关 [站点恢复体系结构](site-recovery-components.md) 的信息  

