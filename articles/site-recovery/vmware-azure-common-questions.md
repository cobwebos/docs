---
title: 常见问题 - 使用 Azure Site Recovery 进行 VMware 到 Azure 的灾难恢复 | Microsoft Docs
description: 本文汇总了使用 Azure Site Recovery 设置将本地 VMware VM 灾难恢复到 Azure 时的常见问题
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 12/11/2018
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: d7b3919d0f970190238dbc5899a20f2d9e7d8cd4
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256498"
---
# <a name="common-questions---vmware-to-azure-replication"></a>常见问题 - VMware 到 Azure 的复制

本文提供将本地 VMware VM 的灾难恢复部署到 Azure 时可能遇到的常见问题的解答。 如果在阅读本文后有任何问题，请在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)上发布问题。


## <a name="general"></a>常规
### <a name="how-is-site-recovery-priced"></a>Site Recovery 如何计费？
请查看 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)。

### <a name="how-do-i-pay-for-azure-vms"></a>Azure VM 如何计费？
在复制期间，数据将复制到 Azure 存储，因此，VM 不会产生任何费用。 故障转移到 Azure 时，Site Recovery 会自动创建 Azure IaaS 虚拟机。 然后，在 Azure 中使用的计算资源会产生费用。

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>可通过哪些方法实现 VMware 到 Azure 的复制？
- **灾难恢复**：可以设置完整的灾难恢复。 在此场景中，可将本地 VMware VM 复制到 Azure 存储。 如果本地基础结构不可用，则可以故障转移到 Azure。 在故障转移时，将使用复制的数据创建 Azure VM。 在本地数据中心再次可用之前，你仍可访问 Azure VM 上的应用和工作负荷。 然后，可从 Azure 故障回复到本地站点。
- **迁移**：可以使用 Site Recovery 将本地 VMware VM 迁移到 Azure。 在此场景中，可将本地 VMware VM 复制到 Azure 存储。 然后从本地故障转移到 Azure。 故障转移后，应用和工作负荷可供使用并在 Azure VM 上运行。



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>需要在 Azure 中做好哪些准备？
需要一个 Azure 订阅、一个恢复服务保管库、一个存储帐户和一个虚拟网络。 保管库、存储帐户和网络必须位于同一区域。

### <a name="what-azure-storage-account-do-i-need"></a>需要哪个 Azure 存储帐户？
需要 LRS 或 GRS 存储帐户。 建议使用 GRS，以便在发生区域性故障或无法恢复主要区域时，能够复原数据。 支持高级存储。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>我的 Azure 帐户是否需要拥有创建 VM 的权限？
如果你是订阅管理员，则已经获得了所需的复制权限。 否则，需要有权在配置 Site Recovery 时指定的资源组和虚拟网络中创建 Azure VM，并有权写入选定的存储帐户。 [了解详细信息](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。



## <a name="on-premises"></a>本地

### <a name="what-do-i-need-on-premises"></a>需要在本地做好哪些准备？

在本地，你需要：
- 在单个 VMware VM 上安装 Site Recovery 组件。
- 至少包含一台 ESXi 主机的 VMware 基础结构。我们建议使用 vCenter 服务器。
- 一个或多个可供复制的 VMware VM。

[详细了解](vmware-azure-architecture.md) VMware 到 Azure 复制体系结构。

本地配置服务器可通过如下所示方式进行部署：

- 我们建议使用预先安装了配置服务器的 OVA 模板将配置服务器部署为 VMware VM。
- 如果由于任何原因无法使用模板，则可以手动设置配置服务器。 [了解详细信息](physical-azure-disaster-recovery.md#set-up-the-source-environment)。



### <a name="where-do-on-premises-vms-replicate-to"></a>本地 VM 将复制到哪个位置？
数据将复制到 Azure 存储。 运行故障转移时，Site Recovery 会自动从存储帐户创建 Azure VM。

## <a name="replication"></a>复制

### <a name="what-apps-can-i-replicate"></a>可以复制哪些应用？
可以复制 VMware VM 中运行的、符合[复制要求](vmware-physical-azure-support-matrix.md##replicated-machines)的任何应用或工作负荷。 Site Recovery 支持应用程序感知型复制，因此，应用可以故障转移或故障回复到智能状态。 Site Recovery 除了与 Microsoft 应用程序（例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory）集成之外，还能与行业领先的供应商（包括 Oracle、SAP、IBM 及 Red Hat）紧密配合。 [详细了解](site-recovery-workload.md)工作负荷保护。

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>是否可以使用站点到站点 VPN 复制到 Azure？
Site Recovery 通过公共终结点或使用 ExpressRoute 公共对等互连将数据从本地复制到 Azure 存储。 不支持通过站点到站点 VPN 网络进行的复制。

### <a name="can-i-replicate-to-azure-with-expressroute"></a>是否可以使用 ExpressRoute 复制到 Azure？
可以使用 ExpressRoute 将 VM 复制到 Azure。 Site Recovery 通过公共终结点将数据复制到 Azure 存储帐户。需要设置[公共对等互连](../expressroute/expressroute-circuit-peerings.md#publicpeering)才能进行 Site Recovery 复制。 将 VM 故障转移到 Azure 虚拟网络后，可以使用[专用对等互连](../expressroute/expressroute-circuit-peerings.md#privatepeering)访问这些 VM。


### <a name="why-cant-i-replicate-over-vpn"></a>为何不能通过 VPN 复制？

复制到 Azure 时，复制流量会到达 Azure 存储帐户的公共终结点，因此，只能使用 ExpressRoute（公共对等互连）通过公共 Internet 进行复制，而不能使用 VPN。


### <a name="what-are-the-replicated-vm-requirements"></a>复制的 VM 要满足哪些要求？

若要复制某个 VMware VM，该 VM 必须运行受支持的操作系统。 此外，该 VM 必须满足 Azure VM 的要求。 在支持矩阵中[了解详细信息](vmware-physical-azure-support-matrix.md##replicated-machines)。

### <a name="how-often-can-i-replicate-to-azure"></a>可以多久复制到 Azure 一次？
将 VMware VM 复制到 Azure 时，复制是持续性的。

### <a name="can-i-extend-replication"></a>是否可以扩展复制？
不支持扩展扩展或链式复制。 请在[反馈论坛](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)中请求此功能。

### <a name="can-i-do-an-offline-initial-replication"></a>是否可以执行脱机初始复制？
不支持此操作。 请在[反馈论坛](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中请求此功能。

### <a name="can-i-exclude-disks"></a>是否可以排除磁盘？
可以从复制中排除磁盘。

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>是否可以复制包含动态磁盘的 VM？
可以复制动态磁盘。 操作系统磁盘必须为基本磁盘。

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>如果我使用复制组实现多 VM 一致性，是否可以将新 VM 添加到现有复制组？
是的，可以在为新 VM 启用复制时将这些 VM 添加到现有复制组。 启动复制后，无法将 VM 添加到现有复制组，也无法为现有 VM 创建复制组。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>是否可以通过添加磁盘或调整磁盘大小来修改正在复制的 VM？

对于从 VMware 复制到 Azure，可以修改磁盘大小。 如果要添加新磁盘，则需要为 VM 添加磁盘并重新启用保护。

## <a name="configuration-server"></a>配置服务器

### <a name="what-does-the-configuration-server-do"></a>配置服务器的作用是什么？
配置服务器运行本地 Site Recovery 组件，包括：
- 在本地与 Azure 之间协调通信和管理数据复制的配置服务器。
- 充当复制网关的进程服务器。 该服务器接收复制数据；使用缓存、压缩和加密来优化数据；将数据发送到 Azure 存储。进程服务器还在要复制的 VM 上安装移动服务，并执行本地 VMware VM 的自动发现。
- 处理从 Azure 进行故障回复期间生成的复制数据的主目标服务器。

[详细了解](vmware-azure-architecture.md)配置服务器组件和流程。

### <a name="where-do-i-set-up-the-configuration-server"></a>要在哪个位置设置配置服务器？
需要为配置服务器提供一个高度可用的本地 VMware VM。

### <a name="what-are-the-requirements-for-the-configuration-server"></a>配置服务器需要满足哪些要求？

请查看[先决条件](vmware-azure-deploy-configuration-server.md#prerequisites)。

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>是否可以手动设置配置服务器，而不使用模板进行设置？
我们建议使用最新版本的 OVF 模板来[创建配置服务器 VM](vmware-azure-deploy-configuration-server.md)。 如果出于某种原因无法使用该模板（例如，无法访问 VMware 服务器），可以从门户[下载统一安装程序文件](physical-azure-set-up-source.md)，并在 VM 上运行此程序。

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>是否可将配置服务器复制到多个区域？
不是。 若要执行此操作，需在每个区域中设置一个配置服务器。

### <a name="can-i-host-a-configuration-server-in-azure"></a>是否可以在 Azure 中托管配置服务器？
虽然可以这样做，但运行配置服务器的 Azure VM 需要与本地的 VMware 基础结构和 VM 通信。 这可能会增加延迟并影响正在进行的复制。

### <a name="how-do-i-update-the-configuration-server"></a>如何更新配置服务器？
[了解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)如何更新配置服务器。 可以在 [Azure 更新页](https://azure.microsoft.com/updates/?product=site-recovery)中找到最新的更新信息。 另外，还可以直接从 [Microsoft 下载中心](https://aka.ms/asrconfigurationserver)下载最新版本的配置服务器。

### <a name="should-i-backup-the-deployed-configuration-server"></a>是否应该备份部署的配置服务器？
建议定期备份配置服务器。 若想成功进行故障回复，进行故障回复的虚拟机必须存在于配置服务器数据库中，并且配置服务器必须正在运行且处于已连接状态。 可以在[此处](vmware-azure-manage-configuration-server.md)了解有关常见配置服务器管理任务的详细信息。

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>设置配置服务器时，是否可以手动下载并安装 MySQL？
是的。 请下载 MySQL 并将其置于 **C:\Temp\ASRSetup** 文件夹中。 然后手动安装它。 设置配置服务器 VM 并接受条款后，MySQL 在“下载并安装”中将列出为“已安装”。

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>我是否可以避免下载 MySQL 但让 Site Recovery 安装它？
是的。 请下载 MySQL 安装程序并将其置于 **C:\Temp\ASRSetup** 文件夹中。  在设置配置服务器 VM 时，接受条款并单击“下载并安装”后，门户将使用你添加的安装程序来安装 MySQL。
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>是否可以将配置服务器 VM 用于任何其他项？
否，只能将该 VM 用于配置服务器。 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>是否可以克隆配置服务器并将其用于业务流程？
否，应设置新配置服务器以避免注册问题。

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>是否可以更改在配置服务器中注册的保管库？
不是。 将保管库注册到配置服务器后，它无法更改。 请查看[此文](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)以了解重新注册步骤。

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>是否可以将同一配置服务器同时用于 VMware VM 和物理服务器的灾难恢复？
可以，但请注意，物理计算机仅可故障回复到 VMware VM。

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>在哪里可以下载配置服务器的密码？
请[查看此文章](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)来了解如何下载该通行短语。

### <a name="where-can-i-download-vault-registration-keys"></a>在哪里可以下载保管库注册密钥？

在“恢复服务保管库”中，“管理” > “Site Recovery 基础结构” > “配置服务器”。 在“服务器”中，选择“下载注册密钥”以下载保管库凭据文件。



## <a name="mobility-service"></a>移动服务

### <a name="where-can-i-find-the-mobility-service-installers"></a>在哪里可以找到移动服务安装程序？
安装程序保存在配置服务器上的 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** 文件夹中。

## <a name="how-do-i-install-the-mobility-service"></a>如何安装移动服务？
可以使用[推送安装](vmware-azure-install-mobility-service.md)，或者通过 UI 或 Powershell 使用[手动安装](vmware-physical-mobility-service-install-manual.md)，在要复制的每个 VM 上安装移动服务。 或者，可以使用 [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) 等部署工具进行部署。



## <a name="security"></a>安全

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Site Recovery 需要对 VMware 服务器拥有哪些访问权限？
Site Recovery 需要访问 VMware 服务器，才能够：

- 设置运行配置服务器以及其他本地 Site Recovery 组件的 VMware VM。 [了解详细信息](vmware-azure-deploy-configuration-server.md)
- 自动发现要复制的 VM。 了解如何准备用于自动发现的帐户。 [了解详细信息](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Site Recovery 需要对 VMware VM 拥有哪些访问权限？

- 若要完成复制，必须在 VMware VM 上安装并运行 Site Recovery 移动服务。 可以手动部署所需的工具，或者在为 VM 启用复制时，指定 Site Recovery 应执行服务的推送安装。 选择推送安装时，Site Recovery 需要一个可用于安装服务组件的帐户。 [了解详细信息](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation)。 进程服务器（默认在配置服务器上运行）可执行此安装。 [详细了解](vmware-azure-install-mobility-service.md)移动服务的安装。
- 在复制期间，VM 将与 Site Recovery 通信，如下所述：
    - VM 通过 HTTPS 443 端口与配置服务器通信，以进行复制管理。
    - VM 通过 HTTPS 9443 端口（可修改）将复制数据发送到进程服务器。
    - 如果启用了多 VM 一致性，则 VM 通过端口 20004 相互通信。


### <a name="is-replication-data-sent-to-site-recovery"></a>复制数据是否会发送到 Site Recovery？
不会。Site Recovery 不会拦截复制的数据，也不包含 VM 上运行的组件的任何相关信息。 复制数据在 VMware 虚拟机监控程序与 Azure 存储之间交换。 站点恢复并不具有拦截该数据的能力。 只有协调复制与故障转移所需的元数据将发送到站点恢复服务。  

站点恢复已通过 ISO 27001:2013、27018、HIPAA、DPA 认证，目前正在接受 SOC2 和 FedRAMP JAB 评估。

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>是否可将本地元数据保留在某个地理区域中？
是的。 当你在某个区域中创建保管库时，我们会确保 Site Recovery 使用的所有元数据保留在该区域的地理边界内。

### <a name="does-site-recovery-encrypt-replication"></a>站点恢复是否将复制数据加密？
是的，传输中加密和 [Azure 中加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)均受支持。


## <a name="failover-and-failback"></a>故障转移和故障回复
### <a name="how-far-back-can-i-recover"></a>可以恢复到哪个最早的时间点？
对于 VMware 到 Azure 的复制，可以使用的最早恢复点是 72 小时。

### <a name="how-do-i-access-azure-vms-after-failover"></a>故障转移后如何访问 Azure VM？
故障转移后，可以通过安全的 Internet 连接、站点到站点 VPN 或 Azure ExpressRoute 来访问 Azure VM。 在连接之前需要做许多准备。 [了解详细信息](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>故障转移的数据是否有复原能力？
Azure 具有复原能力。 Site Recovery 能够根据 Azure SLA 故障转移到辅助 Azure 数据中心。 发生故障转移时，我们会确保元数据和保管库保留在为保管库选择的同一地理区域中。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？
[故障转移](site-recovery-failover.md)不是自动的。 可以在门户中单击一下鼠标来启动故障转移，或者使用 [PowerShell](/powershell/module/azurerm.siterecovery) 来触发故障转移。

### <a name="can-i-fail-back-to-a-different-location"></a>是否可以故障回复到不同位置？
可以。故障转移到 Azure 后，如果原始位置不可用，可以故障回复到不同的位置。 [了解详细信息](concepts-types-of-failback.md#alternate-location-recovery-alr)。

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>为何需要使用 VPN 或 ExpressRoute 进行故障回复？

从 Azure 故障回复时，Azure 中的数据将复制回到本地 VM，这需要提供私人访问权限。



## <a name="automation-and-scripting"></a>自动化和脚本

### <a name="can-i-set-up-replication-with-scripting"></a>是否可以使用脚本设置复制？
是的。 可以使用 Rest API、PowerShell 或 Azure SDK 将 Site Recovery 工作流自动化。[了解详细信息](vmware-azure-disaster-recovery-powershell.md)。

## <a name="performance-and-capacity"></a>性能和容量
### <a name="can-i-throttle-replication-bandwidth"></a>是否可以限制复制带宽？
是的。 [了解详细信息](site-recovery-plan-capacity-vmware.md)。


## <a name="next-steps"></a>后续步骤
* [查看](vmware-physical-azure-support-matrix.md)支持要求。
* [设置](vmware-azure-tutorial.md) VMware 到 Azure 的复制。
