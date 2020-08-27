---
title: 有关 Azure Site Recovery 服务的一般问题
description: 本文讨论有关 Azure Site Recovery 的常见问题。
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: d77f62a57a75f13589b11e023f902c1a128a0d95
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950487"
---
# <a name="general-questions-about-azure-site-recovery"></a>有关 Azure Site Recovery 的一般问题

本文总结有关 Azure Site Recovery 的常见问题。 有关具体方案，请查看以下文章

- [关于 Azure VM 灾难恢复到 Azure 的问题](azure-to-azure-common-questions.md)
- [关于 VMware VM 灾难恢复到 Azure 的问题](vmware-azure-common-questions.md)
- [关于 Hyper-V VM 灾难恢复到 Azure 的问题](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>常规

### <a name="what-does-site-recovery-do"></a>站点恢复的功能是什么？

通过协调和自动化 Azure VM 复制（本地区域虚拟机和物理服务器到 Azure；本地计算机到辅助数据中心），Site Recovery 可帮助实现业务连续性与灾难恢复 (BCDR) 策略。 [了解详细信息](site-recovery-overview.md)。

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>是否可以保护具有 Docker 磁盘的虚拟机？

否，此方案不受支持。

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>Site Recovery 如何确保数据完整性？

Site Recovery 采取各种措施来确保数据完整性。 使用 HTTPS 协议在所有服务之间建立安全连接。 这可确保任何恶意软件或外部实体都无法篡改数据。 采用的另一个度量值是使用校验和。 源和目标之间的数据传输通过计算它们之间数据的校验和来执行。 这可确保传输的数据是一致的。

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
不需要，数据会复制到订阅中的 Azure 存储。 执行测试故障转移（灾难恢复演练）或实际的故障转移时，站点恢复会在订阅中自动创建虚拟机。

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>当我复制到 Azure 时，们确保提供租户级的隔离吗？
是的。

### <a name="what-platforms-do-you-currently-support"></a>目前支持哪些平台？
我们支持 Azure Pack、云平台系统和基于 System Center 的（2012 和更高版本）的部署。 [了解更多](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10))有关 Azure Pack 和 Site Recovery 集成的信息。

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>是否支持单一 Azure Pack 和单一 VMM 服务器部署？
是，可以复制 Hyper-V 虚拟机到 Azure，或者在服务提供商站点之间复制。  请注意，如果在服务提供商站点之间复制，将无法使用 Azure Runbook 集成。

## <a name="pricing"></a>定价

### <a name="where-can-i-find-pricing-information"></a>我可以在哪里找到定价信息？
请查看 [Site Recovery 定价](https://azure.microsoft.com/pricing/details/site-recovery/)详细信息。


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>在使用 Site Recovery 的过程中，如何计算大致的费用？

可以使用[定价计算器](https://aka.ms/asr_pricing_calculator)来估算使用 Site Recovery 时的费用。

若要对费用进行详细估算，请运行 [VMware](https://aka.ms/siterecovery_deployment_planner) 或 [Hyper-V](https://aka.ms/asr-deployment-planner) 的部署规划器工具并使用[成本估算报告](https://aka.ms/asr_DP_costreport)。


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>现在，托管磁盘用于复制 VMware VM 和物理服务器。 使用托管磁盘的缓存存储帐户是否会产生其他费用？

不会，缓存不会产生其他费用。 复制到标准存储帐户时，此缓存存储是同一目标存储帐户的一部分。

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>我已成为 Azure Site Recovery 用户一个多月。 对于每个受保护的实例，是否仍享受前 31 天免费？

是的。 在前 31 天内，每个受保护的实例不会产生任何 Azure Site Recovery 费用。 例如，你在过去 6 个月内保护了 10 个实例，现在你将第 11 个实例连接到 Azure Site Recovery，在连接后的前 31 天内，第 11 个实例不会产生任何费用。 而前 10 个实例将继续产生 Azure Site Recovery 费用，因为它们受到保护的时间已超过 31 天。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在前 31 天的期限内，会产生其他 Azure 费用吗？

是，尽管受保护实例的 Site Recovery 在前 31 天内为免费，但可能会产生 Azure 存储、存储事务和数据传输的费用。 恢复后的虚拟机也可能会产生 Azure 计算费用。


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>进行灾难恢复演练/测试故障转移时，是否有与之相关联的费用？

DR 演练没有单独的费用。 如果在测试故障转移后创建 VM，则会有计算费用。



## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>复制数据是否会发送到 Site Recovery 服务？
不会。站点恢复不拦截复制的数据，也不拥有虚拟机或物理服务器上运行哪些项目的任何相关信息。
复制数据在本地 Hyper-V 主机、VMware 虚拟机监控程序或物理服务器和 Azure 存储或辅助站点之间交换。 站点恢复并不具有拦截该数据的能力。 只有协调复制与故障转移所需的元数据将发送到站点恢复服务。  

站点恢复已通过 ISO 27001:2013、27018、HIPAA、DPA 认证，目前正在接受 SOC2 和 FedRAMP JAB 评估。

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>为了遵从法规，即使是本地元数据也必须保留在同一个地理区域。 站点恢复可以帮助我们吗？
是的。 在某个区域中创建站点恢复保管库时，我们确保启用和协调复制与故障转移时所需的一切元数据都保留在该区域的地理边界范围内。

### <a name="does-site-recovery-encrypt-replication"></a>站点恢复是否将复制数据加密？
在本地站点之间复制虚拟机和物理服务器时，支持传输中加密。 将虚拟机和物理服务器复制到 Azure 时，同时支持传输中加密和[静态加密（Azure 中）](../storage/common/storage-service-encryption.md)。

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Azure 到 Azure Site Recovery 是否使用 TLS 1.2 进行 Azure 微服务之间的所有通信？
是，对于 Azure 到 Azure Site Recovery 方案，默认强制实施 TLS 1.2 协议。 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>如何在 VMware 到 Azure 和物理服务器到 Azure Site Recovery 方案中强制实施 TLS 1.2？
复制项上安装的移动代理仅通过 TLS 1.2 与进程服务器通信。 但是，从配置服务器到 Azure 以及从进程服务器到 Azure 的通信可以通过 TLS 1.1 或 1.0 进行。 请按照[指南](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)在设置的所有配置服务器和进程服务器上强制实施 TLS 1.2。

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>如何在 HyperV 到 Azure Site Recovery 方案中强制实施 TLS 1.2？
Azure Site Recovery 的微服务之间的所有通信均通过 TLS 1.2 协议进行。 Site Recovery 使用系统 (OS) 中配置的安全提供程序，并使用可用的最新 TLS 协议。 用户需要在注册表中显式启用 TLS 1.2，然后 Site Recovery 将开始使用 TLS 1.2 与服务进行通信。 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>如何对存储帐户强制实施受限访问权限，这些帐户由 Site Recovery 服务访问以读取/写入复制数据？
可以通过转到 " *标识* " 设置来切换恢复服务保管库的托管标识。 将保管库注册到 Azure Active Directory 后，你可以进入存储帐户，并向保管库提供以下角色分配：

- 基于资源管理器的存储帐户 (标准类型) ：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
-  (高级类型) 资源管理器的存储帐户：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 经典存储帐户：
  - [经典存储帐户参与者](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [经典存储帐户密钥操作员服务角色](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

## <a name="disaster-recovery"></a>灾难恢复

### <a name="what-can-site-recovery-protect"></a>站点恢复可以保护哪些计算机？
* **Azure VM**：Site Recovery 可复制受支持 Azure VM 上运行的任何工作负载
* **Hyper-V 虚拟机**：站点恢复可以保护 Hyper-V VM 上运行的任何工作负载。
* **物理服务器**：站点恢复可以保护运行 Windows 或 Linux 的物理服务器。
* **VMware 虚拟机**：站点恢复可以保护 VMware VM 上运行的任何工作负载。

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>我可以使用站点恢复来保护哪些工作负荷？
可以使用站点恢复来保护在支持的 VM 或物理服务器上运行的大多数工作负荷。 站点恢复为应用程序感知型复制提供支持，因此，应用可以恢复为智能状态。 它除了与 Microsoft 应用程序（例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory）集成之外，还能与行业领先的供应商（包括 Oracle、SAP、IBM 及 Red Hat）紧密配合。 [详细了解](site-recovery-workload.md)工作负荷保护。

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>我可以使用站点恢复来管理分支机构的灾难恢复吗？
是的。 使用站点恢复来协调分支机构的复制与故障转移时，可以在一个中心位置获得所有分支机构工作负载的统一视图。 不需要前往分支机构，就可以从总部轻松对所有分支机构运行故障转移和管理灾难恢复。


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Azure VM 是否支持灾难恢复？

是，Site Recovery 支持 Azure 区域之间的 Azure VM 灾难恢复。 [查看有关 Azure VM 灾难恢复的常见问题](azure-to-azure-common-questions.md)。 如果要在同一大洲的两个 Azure 区域之间进行复制，请使用 Azure 到 Azure DR 产品/服务。 无需设置配置服务器/进程服务器和 ExpressRoute 连接。

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>VMware VM 是否支持灾难恢复？

是，Site Recovery 支持本地 VMware VM 的灾难恢复。 [查看有关 VMware VM 灾难恢复的常见问题](vmware-azure-common-questions.md)。

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Hyper-V VM 是否支持灾难恢复？
是，Site Recovery 支持本地 Hyper-V VM 的灾难恢复。 [查看有关 Hyper-V VM 灾难恢复的常见问题](hyper-v-azure-common-questions.md)。

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>物理服务器是否支持灾难恢复？
是，Site Recovery 支持将运行 Windows 和 Linux 的本地物理服务器灾难恢复到 Azure 或辅助站点。 了解灾难恢复到 [Azure](vmware-physical-azure-support-matrix.md#replicated-machines) 和[辅助站点](vmware-physical-secondary-support-matrix.md#replicated-vm-support)的要求。
请注意，故障转移后，物理服务器将在 Azure 中作为 VM 运行。 当前不支持从 Azure 故障回复到本地物理服务器。 只能故障回复到 VMware 虚拟机。





## <a name="replication"></a>复制

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>能否通过站点到站点 VPN 复制到 Azure？
Azure Site Recovery 通过公共终结点将数据复制到 Azure 存储帐户或托管磁盘。 复制不是通过站点到站点 VPN 进行。 

### <a name="why-cant-i-replicate-over-vpn"></a>为何不能通过 VPN 复制？

复制到 Azure 时，复制流量会到达 Azure 存储的公共终结点。 因此，只能通过公共 Internet 或 ExpressRoute（Microsoft 对等互连或现有公共对等互连）进行复制。

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>是否可以使用 Riverbed SteelHeads 进行复制？

我们的合作伙伴 Riverbed 提供有关使用 Azure Site Recovery 的详细指导。 查看其[解决方案指南](https://community.riverbed.com/s/article/DOC-4627)。

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>能否使用 ExpressRoute 将虚拟机复制到 Azure？
能，[可以使用 ExpressRoute](concepts-expressroute-with-site-recovery.md) 将本地虚拟机复制到 Azure。

- Azure Site Recovery 通过公共终结点将数据复制到 Azure 存储。 需要设置 [Microsoft 对等互连](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)或使用现有[公共对等互连](../expressroute/about-public-peering.md)（新线路已弃用）将 ExpressRoute 用于 Site Recovery 复制。
- 在复制时，建议使用 Microsoft 对等互连作为路由域。
- 私有对等互连不支持复制。
- 如果要保护 VMware 计算机或物理计算机，请确保也满足配置服务器的[网络要求](vmware-azure-configuration-server-requirements.md#network-requirements)。 配置服务器需要与特定 URL 连接才能编排 Site Recovery 复制。 ExpressRoute 不能用于此连接。
- 将虚拟机故障转移到 Azure 虚拟网络以后，即可使用通过 Azure 虚拟网络设置的[专用对等互连](../expressroute/expressroute-circuit-peerings.md#privatepeering)对其进行访问。


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>如果要复制到 Azure，我需要哪种存储帐户或托管磁盘？

需要 LRS 或 GRS 存储。 建议使用 GRS，以便在发生区域性故障或无法恢复主要区域时，能够复原数据。 该帐户必须位于与恢复服务保管库相同的区域中。 在 Azure 门户中部署 Site Recovery 时，支持将高级存储用于 VMware VM、Hyper-V VM 和物理服务器复制。 托管磁盘仅支持 LRS。

### <a name="how-often-can-i-replicate-data"></a>我可以多久复制数据一次？
* **Hyper-V**：可以每隔 30 秒（高级存储除外）、5 分钟或 15 分钟复制一次 Hyper-V VM。
* **Azure VM、VMware VM、物理服务器**：复制频率无关紧要。 复制是连续的。

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>我可以将复制从现有的恢复站点扩展到其他站点吗？
不支持扩展或链式复制。 请在[反馈论坛](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中请求此功能。

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>在首次复制到 Azure 时可以进行脱机复制吗？
不支持此操作。 请在[反馈论坛](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中请求此功能。

### <a name="can-i-exclude-specific-disks-from-replication"></a>可以从复制中排除特定的磁盘吗？
使用 Azure 门户将 VMware VM 和 Hyper-V VM 复制到 Azure 时支持此操作。

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>可以使用动态磁盘来复制虚拟机吗？
复制 Hyper-V 虚拟机以及将 VMware VM 和物理计算机复制到 Azure 时，支持动态磁盘。 操作系统磁盘必须为基本磁盘。


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>是否可以限制为复制流量分配的带宽？
是的。 可从以下文章详细了解如何限制带宽：

* [复制 VMware VM 和物理服务器的容量规划](site-recovery-plan-capacity-vmware.md)
* [将 Hyper-V VM 复制到 Azure 的容量规划](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>能否在 Linux 服务器中启用应用一致性复制？ 
是的。 适用于 Linux 操作系统的 Azure Site Recovery 支持通过应用程序自定义脚本实现应用一致性。 在保障应用程序一致性时，Azure Site Recovery 移动代理将使用带有 pre 和 post 选项的自定义脚本。 以下是启用此功能的步骤。

1. 以 root 身份登录计算机。
2. 将目录更改为 Azure Site Recovery 移动代理安装位置。 默认位置为“/usr/local/ASR”<br>
    `# cd /usr/local/ASR`
3. 在安装位置下将目录更改为“VX/scripts”<br>
    `# cd VX/scripts`
4. 使用 root 用户的执行权限创建名为“customscript.sh”的 bash shell 脚本。<br>
    a. 脚本应支持“--pre”和“--post”（请注意双短划线）命令行选项<br>
    b. 使用 pre 选项调用脚本时，它应冻结应用程序输入/输出；使用 post 选项调用时，它应解冻应用程序输入/输出。<br>
    c. 示例模板 -<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. 对于需要应用一致性的应用程序，请在 pre 和 post 步骤中添加冻结和解冻输入/输出命令。 可以选择添加另一个脚本来指定这些命令，并使用 pre 和 post 选项从“customscript.sh”调用该脚本。

>[!Note]
>Site Recovery 代理版本应为 9.24 或更高版本才能支持自定义脚本。

## <a name="replication-policy"></a>复制策略

### <a name="what-is-a-replication-policy"></a>什么是复制策略？

复制策略定义了恢复点的保留历史记录设置。 此策略还定义了应用一致性快照的频率。 默认情况下，Azure Site Recovery 使用以下默认设置创建新的复制策略：

- 恢复点历史记录的保留期为 24 小时。
- 应用一致的快照的频率为4小时。

### <a name="what-is-a-crash-consistent-recovery-point"></a>什么是崩溃一致性恢复点？

故障一致性恢复点包含，与在快照期间从服务器拔下电源线时一样的磁盘上数据。 故障一致性恢复点不包含在拍摄快照时的任何内存中数据。

目前，大多数应用程序都可以从崩溃一致性快照正常恢复。 对于无数据库的操作系统以及文件服务器、DHCP 服务器、打印服务器等应用程序而言，崩溃一致性恢复点通常已足够。

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>崩溃一致性恢复点生成的频率是多少？

Site Recovery 每隔 5 分钟创建崩溃一致性恢复点。

### <a name="what-is-an-application-consistent-recovery-point"></a>什么是应用程序一致性恢复点？

应用程序一致性恢复点是从应用程序一致性快照创建的。 应用一致性恢复点除了捕获与故障一致性快照相同的数据，还捕获内存中数据以及进程中的所有事务。

由于包含额外内容，因此应用一致性快照涉及最多且耗时最长。 我们建议对数据库操作系统以及 SQL Server 等应用程序使用应用程序一致性恢复点。

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>应用程序一致性恢复点对应用程序性能有何影响？

应用一致性恢复点捕获内存中和进程中的所有数据。 因为恢复点捕获此类数据，所以它们需要 Windows 上的卷影复制服务等框架来让应用处于静止状态。 如果捕获过程频繁发生，当工作负荷已经很忙时，它可能会影响性能。 对于非数据库工作负荷，建议不要对应用一致性恢复点使用低频率。 即使对于数据库工作负荷，1 小时也足够了。

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>应用程序一致性恢复点生成的最低频率是多少？

Site Recovery 可以创建最低频率为 1 小时的应用一致性恢复点。

### <a name="how-are-recovery-points-generated-and-saved"></a>如何生成和保存恢复点？

为了理解 Site Recovery 如何生成恢复点，让我们来看一个复制策略示例。 此复制策略有一个恢复点，它的保留期为 24 小时，应用一致性快照频率为 1 小时。

Site Recovery 每隔 5 分钟创建崩溃一致性恢复点。 你无法更改此频率。 对于过去一个小时，你可以从 12 个故障一致性恢复点和 1 个应用一致性恢复点中进行选择。 随着时间的推移，Site Recovery 会删除过去一个小时之外的所有恢复点，并且每小时只保存 1 个恢复点。

以下屏幕截图演示了该示例。 在屏幕截图中：

- 在过去一个小时内，有一些频率为 5 分钟的恢复点。
- 在过去一个小时之外，Site Recovery 只保留 1 个恢复点。

   ![生成的恢复点列表](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>可以恢复到哪个最早的时间点？

可以使用的最早恢复点是 72 小时。

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>我有保留期为 24 小时的复制策略。 如果某个问题导致 Site Recovery 超过 24 小时无法生成恢复点，将会怎样？ 以前的恢复点是否将丢失？

不会，Site Recovery 将保留以前的所有恢复点。 根据恢复点的保留期，Site Recovery 仅在生成新点时才替换最早的点。 由于此问题，Site Recovery 无法生成任何新的恢复点。 在有新的恢复点之前，所有旧的恢复点都将在到达保留期后保留。

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>在 VM 上启用复制后，如何更改复制策略？

转到“Site Recovery 保管库” > “Site Recovery 基础结构” > “复制策略”。   选择要编辑的策略，然后保存所做的更改。 任何更改也会应用到现有的所有复制。

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>所有恢复点是包含 VM 的完整副本还是差异副本？

生成的第一个恢复点包含完整副本。 任何后续恢复点包含增量更改。

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>增大恢复点保留期是否会增加存储成本？

是，如果你将保留期从 24 小时延长到 72 小时，Site Recovery 会额外保存恢复点 48 小时。 增加的时间会产生存储费用。 例如，一个恢复点可能有 10GB 的增量更改，每月每 GB 的费用为 $0.16。 额外费用为每月 $1.60 × 48。


## <a name="failover"></a>故障转移
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>如果故障转移到 Azure，则在故障转移后如何访问 Azure VM？

可以通过安全的 Internet 连接或者站点到站点 VPN 或 Azure ExpressRoute 访问 Azure VM。 在连接之前需要做许多准备。 [了解详细信息](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>如果我故障转移到 Azure，Azure 如何确保我的数据可恢复？
Azure 具有复原能力。 Site Recovery 已能够根据 Azure SLA 故障转移到辅助 Azure 数据中心。 发生此情况时，我们确保元数据和保管库都保留在为保管库选择的相同地理区域。  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>如果我在两个数据中心之间进行复制，当我的主数据中心发生意外的服务中断时，会出现什么情况？
可以从辅助站点触发非计划的故障转移。 站点恢复不需要来自主站点的连接即可执行故障转移。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？
故障转移不是自动的。 可以在门户中单击一下来启动故障转移，或者使用[站点恢复 PowerShell](/powershell/module/az.recoveryservices) 来触发故障转移。 在站点恢复门户中可以轻松进行故障回复。

要自动化，可以使用本地 Orchestrator 或 Operations Manager 来检测虚拟机故障，并使用 SDK 来触发故障转移。

* [详细了解](site-recovery-create-recovery-plans.md)恢复计划。
* [详细了解](site-recovery-failover.md)故障转移。
* [详细了解](./vmware-azure-failback.md) VMware VM 和物理服务器故障回复的信息

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

[了解](site-recovery-whats-new.md)有关新更新的信息，并[获取汇总信息](service-updates-how-to.md)。

## <a name="next-steps"></a>后续步骤
* 阅读 [站点恢复概述](site-recovery-overview.md)
