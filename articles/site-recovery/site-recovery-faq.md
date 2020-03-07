---
title: 有关 Azure Site Recovery 服务的一般问题
description: 本文讨论有关 Azure Site Recovery 的常见一般问题。
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: a9d0ae4a6e60a72bbb1148aca1a75c44506b2e9e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362552"
---
# <a name="general-questions-about-azure-site-recovery"></a>有关 Azure Site Recovery 的一般问题

本文汇总了有关 Azure Site Recovery 的常见问题。 有关具体方案，请查看以下文章

- [有关 Azure VM 灾难恢复到 Azure 的问题](azure-to-azure-common-questions.md)
- [有关 VMware VM 灾难恢复到 Azure 的问题](vmware-azure-common-questions.md)
- [有关将 Hyper-v VM 灾难恢复到 Azure 的问题](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>常规

### <a name="what-does-site-recovery-do"></a>站点恢复的功能是什么？
通过协调和自动化 Azure VM 复制（本地区域虚拟机和物理服务器到 Azure；本地计算机到辅助数据中心），Site Recovery 可帮助实现业务连续性与灾难恢复 (BCDR) 策略。 [了解详细信息](site-recovery-overview.md)。

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>能否保护具有 Docker 磁盘的虚拟机？

否，此方案不受支持。

## <a name="service-providers"></a>服务提供商

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>我是服务提供商。 站点恢复适用于专用和共享的基础结构模型吗？
是的，站点恢复同时支持专用与共享的基础结构模型。

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>对于服务提供商而言，我的租户标识是否与 Site Recovery 服务共享？
不是。 租户标识是匿名的。 租户不需要访问 Site Recovery 门户。 只有服务提供商管理员才能与门户交互。

### <a name="will-tenant-application-data-ever-go-to-azure"></a>租户应用程序数据是否会发往 Azure？
在服务提供商拥有的站点之间进行复制时，永远不会将应用程序数据发送到 Azure。 数据进行传输中加密并直接在服务提供商站点之间复制。

如果是复制到 Azure，应用程序数据将发送到 Azure 存储而不是站点恢复服务。 数据进行传输中加密并在 Azure 中保持加密状态。

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>我的租户会收到来自 Azure 服务的帐单吗？
不是。 Azure 直接与服务提供商保持计费关系。 服务提供商责任为其租户生成特定的帐单。

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>如果我复制到 Azure，需要在 Azure 中随时运行虚拟机吗？
不可以，将数据复制到订阅中的 Azure 存储。 执行测试故障转移（灾难恢复演练）或实际的故障转移时，站点恢复会在订阅中自动创建虚拟机。

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>当我复制到 Azure 时，们确保提供租户级的隔离吗？
是的。

### <a name="what-platforms-do-you-currently-support"></a>目前支持哪些平台？
我们支持 Azure Pack、云平台系统和基于 System Center 的（2012 和更高版本）的部署。 [了解更多](https://technet.microsoft.com/library/dn850370.aspx)有关 Azure Pack 和 Site Recovery 集成的信息。

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>是否支持单一 Azure Pack 和单一 VMM 服务器部署？
是，可以复制 Hyper-V 虚拟机到 Azure，或者在服务提供商站点之间复制。  请注意，如果在服务提供商站点之间复制，将无法使用 Azure Runbook 集成。

## <a name="pricing"></a>定价

### <a name="where-can-i-find-pricing-information"></a>在哪里可以找到定价信息？
查看[Site Recovery 定价](https://azure.microsoft.com/pricing/details/site-recovery/)详细信息。


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>在使用 Site Recovery 的过程中，如何计算大致的费用？

使用 Site Recovery 时，可以使用[定价计算器](https://aka.ms/asr_pricing_calculator)估算成本。

有关成本的详细估计，请运行适用于[VMware](https://aka.ms/siterecovery_deployment_planner)或[hyper-v](https://aka.ms/asr-deployment-planner)的部署规划器工具，并使用[成本估算报表](https://aka.ms/asr_DP_costreport)。


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>托管磁盘现在用于复制 VMware Vm 和物理服务器。 是否对具有托管磁盘的缓存存储帐户产生额外的费用？

不，缓存没有额外的费用。 复制到标准存储帐户时，此缓存存储属于同一目标存储帐户。

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>我已成为 Azure Site Recovery 用户一个多月。 对于每个受保护的实例，是否仍享受前 31 天免费？

是的。 在前 31 天内，每个受保护的实例不会产生任何 Azure Site Recovery 费用。 例如，如果你在过去6个月内保护了10个实例，并且你将第11个实例连接到 Azure Site Recovery，则在前31天内，第11个实例不会产生任何费用。 前10个实例将继续产生 Azure Site Recovery 费用，因为它们受到保护的时间已超过31天。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在前 31 天的期限内，会产生其他 Azure 费用吗？

是的，即使在受保护实例的前31天内 Site Recovery 免费，你也可能会产生 Azure 存储、存储事务和数据传输的费用。 恢复后的虚拟机也可能会产生 Azure 计算费用。


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>执行灾难恢复演练/测试故障转移是否需要付费？

DR 演练没有单独的费用。 在测试故障转移后创建 VM 后，将会产生计算费用。



## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>复制数据是否会发送到 Site Recovery 服务？
不会。站点恢复不拦截复制的数据，也不拥有虚拟机或物理服务器上运行哪些项目的任何相关信息。
复制数据在本地 Hyper-V 主机、VMware 虚拟机监控程序或物理服务器和 Azure 存储或辅助站点之间交换。 站点恢复并不具有拦截该数据的能力。 只有协调复制与故障转移所需的元数据将发送到站点恢复服务。  

站点恢复已通过 ISO 27001:2013、27018、HIPAA、DPA 认证，目前正在接受 SOC2 和 FedRAMP JAB 评估。

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>为了遵从法规，即使是本地元数据也必须保留在同一个地理区域。 站点恢复可以帮助我们吗？
是的。 在某个区域中创建站点恢复保管库时，我们确保启用和协调复制与故障转移时所需的一切元数据都保留在该区域的地理边界范围内。

### <a name="does-site-recovery-encrypt-replication"></a>站点恢复是否将复制数据加密？
在本地站点之间复制虚拟机和物理服务器时，支持传输中加密。 将虚拟机和物理服务器复制到 Azure 时，同时支持传输中加密和[静态加密（Azure 中）](https://docs.microsoft.com/azure/storage/storage-service-encryption)。

### <a name="how-can-i-enforce-tls-12-on-all-on-premises-azure-site-recovery-components"></a>如何在所有本地 Azure Site Recovery 组件上强制实施 TLS 1.2？
在复制的项上安装的移动代理仅在 TLS 1.2 上与进程服务器通信。 但是，从配置服务器到 Azure 和从进程服务器到 Azure 的通信可能位于 TLS 1.1 或1.0 上。 请按照[指南](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)操作，在所有配置服务器上强制实施 TLS 1.2，并为其设置处理服务器。


## <a name="disaster-recovery"></a>灾难恢复

### <a name="what-can-site-recovery-protect"></a>站点恢复可以保护哪些计算机？
* Azure VM：Site Recovery 可复制受支持 Azure VM 上运行的任何工作负荷
* **Hyper-V 虚拟机**：站点恢复可以保护 Hyper-V VM 上运行的任何工作负荷。
* **物理服务器**：站点恢复可以保护运行 Windows 或 Linux 的物理服务器。
* **VMware 虚拟机**：站点恢复可以保护 VMware VM 上运行的任何工作负荷。

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>我可以使用站点恢复来保护哪些工作负荷？
可以使用站点恢复来保护在支持的 VM 或物理服务器上运行的大多数工作负荷。 站点恢复为应用程序感知型复制提供支持，因此，应用可以恢复为智能状态。 它除了与 Microsoft 应用程序（例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory）集成之外，还能与行业领先的供应商（包括 Oracle、SAP、IBM 及 Red Hat）紧密配合。 [详细了解](site-recovery-workload.md)工作负荷保护。

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>我可以使用站点恢复来管理分支机构的灾难恢复吗？
是的。 使用站点恢复来协调分支机构的复制与故障转移时，可以在一个中心位置获得所有分支机构工作负载的统一视图。 不需要前往分支机构，就可以从总部轻松对所有分支机构运行故障转移和管理灾难恢复。


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Azure Vm 是否支持灾难恢复？

是的，Site Recovery 支持 azure 区域之间 Azure Vm 的灾难。 [查看](azure-to-azure-common-questions.md)有关 Azure VM 灾难恢复的常见问题。

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>VMware Vm 是否支持灾难恢复？

是的，Site Recovery 支持本地 VMware Vm 的灾难恢复。 [查看](vmware-azure-common-questions.md)VMware vm 灾难恢复的常见问题。

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Hyper-v Vm 是否支持灾难恢复？
是的，Site Recovery 支持本地 Hyper-v Vm 的灾难恢复。 [查看](hyper-v-azure-common-questions.md)有关 hyper-v vm 的灾难恢复的常见问题。

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>物理服务器是否支持灾难恢复？
是的，Site Recovery 支持运行 Windows 和 Linux 的本地物理服务器到 Azure 或辅助站点的灾难恢复。 了解灾难恢复到[Azure](vmware-physical-azure-support-matrix.md#replicated-machines)和[辅助站点](vmware-physical-secondary-support-matrix.md#replicated-vm-support)的要求。
请注意，在故障转移后，物理服务器将作为 Vm 在 Azure 中运行。 当前不支持从 Azure 故障回复到本地物理服务器。 只能故障回复到 VMware 虚拟机。





## <a name="replication"></a>复制

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>能否通过站点到站点 VPN 复制到 Azure？
Azure Site Recovery 通过公共终结点将数据复制到 Azure 存储帐户或托管磁盘。 复制不是通过站点到站点 VPN 进行。 

### <a name="why-cant-i-replicate-over-vpn"></a>为何不能通过 VPN 复制？

复制到 Azure 时，复制流量到达 Azure 存储的公共终结点。 因此，只能通过公共 internet 或 ExpressRoute （Microsoft 对等互连或现有的公共对等互连）进行复制。

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>是否可以使用 Riverbed SteelHeads 进行复制？

我们的合作伙伴 Riverbed 提供有关使用 Azure Site Recovery 的详细指南。 查看其[解决方案指南](https://community.riverbed.com/s/article/DOC-4627)。

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>能否使用 ExpressRoute 将虚拟机复制到 Azure？
能，[可以使用 ExpressRoute](concepts-expressroute-with-site-recovery.md) 将本地虚拟机复制到 Azure。

- Azure Site Recovery 通过公共终结点将数据复制到 Azure 存储。 你需要设置 Microsoft 对[等互连](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)或使用现有的[公共对等互连](../expressroute/about-public-peering.md)（不推荐用于新线路）来使用 ExpressRoute 进行 Site Recovery 复制。
- 在复制时，建议使用 Microsoft 对等互连作为路由域。
- 私有对等互连不支持复制。
- 如果要保护 VMware 计算机或物理计算机，请确保同时满足配置服务器的[网络要求](vmware-azure-configuration-server-requirements.md#network-requirements)。 配置服务器需要连接到特定 Url 才能进行 Site Recovery 复制的业务流程。 ExpressRoute 不能用于此连接。
- 将虚拟机故障转移到 Azure 虚拟网络以后，即可使用通过 Azure 虚拟网络设置的[专用对等互连](../expressroute/expressroute-circuit-peerings.md#privatepeering)对其进行访问。


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>如果复制到 Azure，则需要哪种类型的存储帐户或托管磁盘？

需要 LRS 或 GRS 存储。 建议使用 GRS，以便在发生区域性故障或无法恢复主要区域时，能够复原数据。 该帐户必须位于与恢复服务保管库相同的区域中。 在 Azure 门户中部署 Site Recovery 时，支持将高级存储用于 VMware VM、Hyper-V VM 和物理服务器复制。 托管磁盘仅支持 LRS。

### <a name="how-often-can-i-replicate-data"></a>我可以多久复制数据一次？
* **Hyper-v：** 可以每隔30秒（高级存储除外）、5分钟或15分钟复制一次 hyper-v Vm。
* **Azure vm、VMware vm、物理服务器：** 复制频率与此无关。 复制是连续的。

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>我可以将复制从现有的恢复站点扩展到其他站点吗？
不支持扩展或链式复制。 请在[反馈论坛](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中请求此功能。

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>在首次复制到 Azure 时可以进行脱机复制吗？
不支持此操作。 请在[反馈论坛](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中请求此功能。

### <a name="can-i-exclude-specific-disks-from-replication"></a>可以从复制中排除特定的磁盘吗？
使用 Azure 门户将 VMware VM 和 Hyper-V VM 复制到 Azure 时支持此操作。

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>可以使用动态磁盘来复制虚拟机吗？
复制 Hyper-v 虚拟机时，以及将 VMware Vm 和物理机复制到 Azure 时，支持动态磁盘。 操作系统磁盘必须为基本磁盘。


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>能否限制为复制流量分配的带宽？
是的。 有关限制的详细信息，请参阅以下文章：

* [复制 VMware VM 和物理服务器的容量规划](site-recovery-plan-capacity-vmware.md)
* [将 Hyper-V VM 复制到 Azure 的容量规划](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>故障转移
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>如果要故障转移到 Azure，如何在故障转移后访问 Azure Vm？

可以通过安全的 Internet 连接或者站点到站点 VPN 或 Azure ExpressRoute 访问 Azure VM。 若要进行连接，您需要准备一些东西。 [了解详细信息](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>如果我故障转移到 Azure，Azure 如何确保我的数据可恢复？
Azure 具有复原能力。 Site Recovery 已根据 Azure SLA 设计为故障转移到辅助 Azure 数据中心。 发生此情况时，我们确保元数据和保管库都保留在为保管库选择的相同地理区域。  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>如果我在两个数据中心之间进行复制，当我的主数据中心发生意外的服务中断时，会出现什么情况？
可以从辅助站点触发非计划的故障转移。 站点恢复不需要来自主站点的连接即可执行故障转移。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？
故障转移不是自动的。 可以在门户中单击一下来启动故障转移，或者使用[站点恢复 PowerShell](/powershell/module/az.recoveryservices) 来触发故障转移。 在站点恢复门户中可以轻松进行故障回复。

要自动化，可以使用本地 Orchestrator 或 Operations Manager 来检测虚拟机故障，并使用 SDK 来触发故障转移。

* [详细了解](site-recovery-create-recovery-plans.md)恢复计划。
* [详细了解](site-recovery-failover.md)故障转移。
* [详细了解](site-recovery-failback-azure-to-vmware.md) VMware VM 和物理服务器故障回复的信息

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>如果我的本地主机未响应或崩溃，我是否可以故障回复到另一个主机？
是，可以使用备用位置恢复从 Azure 故障回复到另一个主机。

* [对于 VMware 虚拟机](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [针对 Hyper-V 虚拟机](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>自动化

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>是否可以使用 SDK 自动化 Site Recovery 方案？
是的。 可以使用 Rest API、PowerShell 或 Azure SDK 将站点恢复工作流自动化。 当前支持的使用 PowerShell 部署站点恢复的方案：

* [将 VMM 云中的 Hyper-V VM 复制到 Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [将 Hyper-V VM 复制（不使用 VMM）到 Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [使用 PowerShell 资源管理器将 VMware 复制到 Azure](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>组件/提供程序升级

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>在哪里可以找到 Site Recovery 升级的发行说明/更新汇总

[了解](site-recovery-whats-new.md)新的更新，并[获取汇总信息](service-updates-how-to.md)。

## <a name="next-steps"></a>后续步骤
* 阅读 [站点恢复概述](site-recovery-overview.md)

