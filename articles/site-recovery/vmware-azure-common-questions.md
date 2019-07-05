---
title: 有关常见问题 VMware 到 Azure 的灾难恢复使用 Azure Site Recovery |Microsoft Docs
description: 使用 Azure Site Recovery 以获取有关在本地 VMware vm 到 Azure 的灾难恢复的常见问题的解答。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 06/27/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 79118fb053c7064fa29730680feb0434f45f031a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491692"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>有关常见问题 VMware 到 Azure 的复制

本文回答了灾难恢复的本地 VMware 虚拟机 (Vm) 部署到 Azure 时可能出现的常见问题。

## <a name="general"></a>常规

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM 灾难恢复需用到哪些组件？

[了解有关所涉及的组件](vmware-azure-architecture.md)中的 VMware Vm 灾难恢复。

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>是否可以使用 Site Recovery 将 VMware VM 迁移到 Azure？

是的。 除了使用 Site Recovery 为 VMware Vm 设置完整的灾难恢复，还可以使用 Site Recovery 将本地 VMware Vm 迁移到 Azure。 在此方案中，在本地 VMware Vm 复制到 Azure 存储。 然后从本地故障转移到 Azure。 故障转移后，应用和工作负荷可供使用并在 Azure VM 上运行。 该过程就像设置完整的灾难恢复，只不过在迁移中您不能从 Azure 故障回复。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>我的 Azure 帐户是否需要拥有创建 VM 的权限？

如果你是订阅管理员，则已经获得了所需的复制权限。 如果您不是管理员，则需要权限执行以下操作：

- 创建 Azure VM 在资源组和虚拟网络配置 Site Recovery 时指定。
- 将写入到所选的存储帐户或根据配置的托管的磁盘。

[了解详细信息](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)有关所需的权限。

### <a name="what-applications-can-i-replicate"></a>可以复制哪些应用程序？

可以复制任何应用或符合在 VMware VM 上运行的工作负荷[复制要求](vmware-physical-azure-support-matrix.md#replicated-machines)。

- Site Recovery 支持应用程序感知复制，以便应用可以故障转移和故障回复到智能状态。
- Site Recovery 集成 Microsoft 应用程序，如 SharePoint、 Exchange、 Dynamics、 SQL Server 和 Active Directory。 它还与领先供应商密切，包括 Oracle、 SAP、 IBM 和 Red Hat。

[详细了解](site-recovery-workload.md)工作负荷保护。

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>是否可以在 Azure 上使用来宾 OS 服务器许可证？

是的可以使用 Microsoft 软件保障客户[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来节省迁移到 Azure，Windows Server 计算机的许可成本，或将 Azure 用于灾难恢复。

## <a name="security"></a>安全

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Site Recovery 需要拥有哪些访问 VMware 服务器？

Site Recovery 需要访问 VMware 服务器，才能够：

- 设置运行 Site Recovery 配置服务器的 VMware VM。
- 自动发现要复制的 VM。

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Site Recovery 需要 VMware Vm 到什么访问权限？

- 若要复制，VMware VM 必须已安装并运行 Site Recovery 移动服务。 可以手动部署工具，也可以指定 Site Recovery 执行推送安装服务时为 VM 启用复制。
- 在复制期间，VM 将与 Site Recovery 通信，如下所述：
    - Vm 与配置服务器的复制管理的 HTTPS 端口 443 进行通信。
    - Vm 将复制数据发送到 HTTPS 端口 9443 上的进程服务器。 （可以修改此设置）。
    - 如果启用了多 VM 一致性，则 VM 通过端口 20004 相互通信。

### <a name="is-replication-data-sent-to-site-recovery"></a>复制数据是否会发送到 Site Recovery？

不，站点恢复不拦截复制的数据，而不具有有关在 Vm 上运行的任何信息。 VMware 虚拟机监控程序与 Azure 存储之间交换复制数据。 站点恢复并不具有拦截该数据的能力。 只有协调复制与故障转移所需的元数据将发送到站点恢复服务。  

站点恢复已获得 ISO 27001: 2013 和 27018、 HIPAA 和 DPA 认证。 它正在 SOC2 和 FedRAMP JAB 评估。

## <a name="pricing"></a>定价

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>如何计算近似费用以便进行 VMware 灾难恢复？

使用[定价计算器](https://aka.ms/asr_pricing_calculator)若要使用 Site Recovery 时估计成本。

对于一个详细的成本估计，运行的部署规划器工具[VMware](https://aka.ms/siterecovery_deployment_planner)并用[成本估算报表](https://aka.ms/asr_DP_costreport)。

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>复制到存储的费用与直接复制到托管磁盘的费用是否有任何差别？

托管的磁盘收费从存储帐户的方式略有不同。 [了解详细信息](https://azure.microsoft.com/pricing/details/managed-disks/)有关托管磁盘定价。

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>将复制到常规用途 v2 存储帐户时有成本的任何差异吗？

通常会产生在 GPv2 存储帐户，由于 Azure Site Recovery 是大量的事务的事务成本的增加。 [阅读更多](../storage/common/storage-account-upgrade.md#pricing-and-billing)来估计此更改。

## <a name="mobility-service"></a>移动服务

### <a name="where-can-i-find-the-mobility-service-installers"></a>在哪里可以找到移动服务安装程序？

安装程序均位于 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 文件夹配置服务器上。

## <a name="how-do-i-install-the-mobility-service"></a>如何安装移动服务？

在每个想要复制的 VM 上安装服务通过几种方法之一：

- [推送安装](vmware-physical-mobility-service-overview.md#push-installation)
- [手动安装](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui)从 UI 或 PowerShell
- 通过使用部署工具，如部署[System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>托管磁盘

### <a name="where-does-site-recovery-replicate-data-to"></a>Site Recovery 将数据复制到何处？

Site Recovery 将本地 VMware VM 和物理服务器复制到 Azure 中的托管磁盘。

- Site Recovery 进程服务器将复制日志写入到目标区域中的缓存存储帐户。
- 这些日志用于具有前缀的 Azure 托管磁盘上创建恢复点**asrseeddisk**。
- 故障转移时，将使用所选的恢复点来创建新的目标托管磁盘。 此托管磁盘附加到 Azure 中的 VM。
- 以前已复制到存储帐户 （在之前年 3 月 2019) 的 Vm 不会受到影响。

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>是否可将新计算机复制到存储帐户？

否。 在 Azure 门户中，从开始年 3 月 2019年可以复制仅对 Azure 托管磁盘。

新 Vm 复制到存储帐户是只能通过使用 PowerShell 或 REST API （版本 2018年-01-10 或 2016年-08-10） 可用。

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>将复制到托管磁盘的优点是什么？

[了解](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery 如何使用托管磁盘简化灾难恢复。

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>在计算机受到保护后是否可以更改托管磁盘类型？

是的，可以轻松针对正在进行的复制[更改托管磁盘的类型](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage)。 更改类型前,，请确保 URL 生成托管磁盘没有共享的访问签名：

1. 转到**托管磁盘**在 Azure 门户上的资源，并检查是否具有共享的访问签名 URL 横幅**概述**边栏选项卡。
1. 如果存在标题，则选择它以取消正在进行导出。
1. 接下来的几分钟内更改磁盘类型。 如果您更改托管磁盘类型，请等待要生成的 Azure Site Recovery 的最新恢复点。
1. 在将来针对任何测试故障转移或故障转移中使用新的恢复点。

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>是否可将复制目标从托管磁盘切换为非托管磁盘？

不。 切换从托管到非托管不受支持。

## <a name="replication"></a>复制

### <a name="what-are-the-replicated-vm-requirements"></a>复制的 VM 要满足哪些要求？

[了解详细信息](vmware-physical-azure-support-matrix.md#replicated-machines)VMware Vm 和物理服务器的支持要求。

### <a name="how-often-can-i-replicate-to-azure"></a>可以多久复制到 Azure 一次？

将 VMware VM 复制到 Azure 时，复制是持续性的。

### <a name="can-i-extend-replication"></a>是否可以扩展复制？

不支持扩展扩展或链式复制。 请在[反馈论坛](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中请求此功能。

### <a name="can-i-do-an-offline-initial-replication"></a>是否可以执行脱机初始复制？

不支持脱机复制。 请在[反馈论坛](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中请求此功能。

### <a name="what-is-asrseeddisk"></a>什么是 asrseeddisk？

对于每个源的磁盘，数据将复制到 Azure 中的托管磁盘。 此磁盘具有的前缀**asrseeddisk**。 它存储源磁盘和所有恢复点快照的副本。

### <a name="can-i-exclude-disks-from-replication"></a>是否可从复制中排除磁盘？

是的，可以排除磁盘。

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>可以复制包含任何动态磁盘的 Vm？

可以复制动态磁盘。 操作系统磁盘必须为基本磁盘。

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>如果我使用复制组实现多 VM 一致性，是否可以将新 VM 添加到现有复制组？

是的，可以在为新 VM 启用复制时将这些 VM 添加到现有复制组。 但是：

- 开始复制后，不能向现有的复制组中添加 VM。
- 无法为现有的 VM 创建复制组。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>是否可以通过添加磁盘或调整磁盘大小来修改正在复制的 VM？

对于 VMware 复制到 Azure，您可以修改磁盘大小。 如果你想要添加新磁盘，必须将磁盘添加并重新启用对 VM 的保护。

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>是否可以在不影响进行中复制的情况下将本地计算机迁移到新的 vCenter Server？

否。 VMware Vcenter 或迁移的更改会影响正在进行的复制。 与新的 vCenter 服务器设置 Site Recovery 并再次启用复制的计算机。

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>可以复制到包含其上配置一个虚拟网络 （具有 Azure 防火墙） 的缓存或目标存储帐户？

否，Site Recovery 在虚拟网络上不支持复制到 Azure 存储。

## <a name="component-upgrade"></a>组件升级

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>我的移动服务代理或配置服务器的版本变旧之后，并升级失败。 我该怎么办？

Site Recovery 遵循 N-4 支持模型。 [详细了解](https://aka.ms/asr_support_statement)如何从很低的版本升级。

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>在其中找到发行说明和更新汇总的 Azure Site recovery？

[了解新的更新](site-recovery-whats-new.md)，并[获取汇总信息](service-updates-how-to.md)。

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>在哪里可以找到有关灾难恢复到 Azure 的升级信息？

[了解有关升级](https://aka.ms/asr_vmware_upgrades)。

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>每次升级后是否都需要重新启动源计算机？

重新启动是的但不是必需的为每个升级建议。 [了解详细信息](https://aka.ms/asr_vmware_upgrades)。

## <a name="configuration-server"></a>配置服务器

### <a name="what-does-the-configuration-server-do"></a>配置服务器的作用是什么？

配置服务器运行本地 Site Recovery 组件，包括：

- 配置服务器本身。 在服务器上本地组件和 Azure 之间协调通信并管理数据复制。
- 进程服务器，用于充当复制网关。 此服务器：
    1. 接收复制数据。
    2. 通过缓存、压缩和解密来优化数据。
    3. 将数据发送到 Azure 存储。
  进程服务器还执行虚拟机上的移动服务推送安装，并执行本地 VMware Vm 的自动发现。
- 主目标服务器，用于处理从 Azure 故障回复期间复制数据。

[详细了解](vmware-azure-architecture.md)配置服务器组件和流程。

### <a name="where-do-i-set-up-the-configuration-server"></a>要在哪个位置设置配置服务器？

配置服务器需要单一、 高度可用的本地 VMware VM。 对于物理服务器灾难恢复，在物理计算机上安装配置服务器。

### <a name="what-do-i-need-for-the-configuration-server"></a>配置服务器需要满足哪些条件？

请查看[先决条件](vmware-azure-deploy-configuration-server.md#prerequisites)。

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>是否可以手动设置配置服务器，而不使用模板进行设置？

我们建议您[创建配置服务器 VM](vmware-azure-deploy-configuration-server.md)使用打开虚拟机格式 (OVF) 模板的最新版本。 如果您不能使用模板 （例如，如果你无权访问与 VMware 服务器），[下载](physical-azure-set-up-source.md)从门户和设置配置服务器的安装程序文件。

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>是否可将配置服务器复制到多个区域？

否。 若要将复制到多个区域，需要每个区域中的配置服务器。

### <a name="can-i-host-a-configuration-server-in-azure"></a>是否可以在 Azure 中托管配置服务器？

虽然可以，但运行配置服务器的 Azure VM 需要与你的本地 VMware 基础结构和虚拟机进行通信。 此通信会增加延迟，并会影响正在进行的复制。

### <a name="how-do-i-update-the-configuration-server"></a>如何更新配置服务器？

[了解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)如何更新配置服务器。

- 可以在 [Azure 更新页](https://azure.microsoft.com/updates/?product=site-recovery)中找到最新的更新信息。
- 可从门户下载最新版本。 或者，可以下载最新版本的直接从配置服务器[Microsoft Download Center](https://aka.ms/asrconfigurationserver)。
- 如果你的版本早于当前版本的四个版本，请参阅[支持语句](https://aka.ms/asr_support_statement)有关的升级指南。

### <a name="should-i-back-up-the-configuration-server"></a>是否应备份配置服务器？

建议定期备份配置服务器。

- 若要成功进行故障回复，要故障回复的 VM 必须位于配置服务器数据库中。
- 配置服务器必须正在运行且处于连接状态。
- [详细了解](vmware-azure-manage-configuration-server.md)常见的配置服务器管理任务。

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>设置配置服务器时，是否可以手动下载并安装 MySQL？

是的。 下载 MySQL 并将其放在 C:\Temp\ASRSetup 文件夹中。 然后，手动安装它。 设置配置服务器 VM 并接受条款后，MySQL 在“下载并安装”中将列出为“已安装”。  

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>我是否可以避免下载 MySQL 但让 Site Recovery 安装它？

是的。 下载 MySQL 安装程序并将其放在 C:\Temp\ASRSetup 文件夹中。 如果设置了配置服务器 VM 时，接受条款，然后选择**下载并安装**。 门户将使用的安装程序添加到安装 MySQL。

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>是否可以将配置服务器 VM 用于任何其他项？

不。 使用仅针对配置服务器 VM。

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>是否可以克隆配置服务器并将其用于业务流程？

不。 设置新配置服务器以避免注册问题。

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>是否可以更改配置服务器注册到的保管库？

不。 将某个保管库关联到配置服务器后，无法更改该保管库。 [了解](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)有关向不同的保管库注册配置服务器。

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>可以为 VMware Vm 和物理服务器的灾难恢复使用相同的配置服务器？

是的但请注意该物理计算机可以进行故障回复到 VMware VM 仅。

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>在哪里可以下载配置服务器的密码？

[了解](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)如何下载该通行短语。

### <a name="where-can-i-download-vault-registration-keys"></a>在哪里可以下载保管库注册密钥？

在恢复服务保管库中，选择**配置服务器**中**Site Recovery 基础结构** > **管理**。 然后，在**服务器**，选择**下载注册密钥**保管库凭据文件下载。

## <a name="process-server"></a>进程服务器

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>为什么我无法在启用复制时选择的进程服务器？

在版本 9.24 和更高版本现在会显示更新[进程服务器启用复制时的运行状况](vmware-azure-enable-replication.md#enable-replication)。 此功能可帮助避免进程服务器限制并尽量减少使用的不正常的进程服务器。

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>如何更新到版本 9.24 或更高版本的准确运行状况信息的进程服务器？

开头[版本 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups)，已添加更多警报以指示进程服务器的运行状况。 [Site Recovery 组件更新为使用 9.24 或更高版本](service-updates-how-to.md#links-to-currently-supported-update-rollups)，以便在生成的所有警报。

## <a name="failover-and-failback"></a>故障转移和故障回复

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>可以将本地进程服务器用于故障回复？

我们强烈建议在用于故障回复，Azure，以避免数据传输延迟中创建进程服务器。 此外，如果已在配置服务器将源 VM 网络与面向 Azure 的网络相隔离，则必须使用 Azure 中创建的进程服务器进行故障回复。

### <a name="can-i-keep-the-ip-address-on-failover"></a>可以在故障转移时保留的 IP 地址？

是的您可以在故障转移时保留的 IP 地址。 请确保指定中的目标 IP 地址**计算和网络**VM 故障转移之前的设置。 此外，故障转移时请关闭计算机，以免故障回复期间发生 IP 地址冲突。

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>在故障转移之前是否可以更改目标 VM 大小或 VM 类型？

是的可以在故障转移前，随时更改类型或 VM 的大小。 在门户中，使用**计算和网络**复制 VM 的设置。

### <a name="how-far-back-can-i-recover"></a>可以恢复到哪个最早的时间点？

对于 VMware 到 Azure，可以使用的最早的恢复点是 72 小时。

### <a name="how-do-i-access-azure-vms-after-failover"></a>故障转移后如何访问 Azure VM？

故障转移后，可以通过安全的 internet 连接、 通过站点到站点 VPN，或通过 Azure ExpressRoute 访问 Azure Vm。 若要连接，必须准备几件事情。 [了解详细信息](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。

### <a name="is-failed-over-data-resilient"></a>是故障转移数据可复原？

Azure 具有复原能力。 Site Recovery 能够故障转移到辅助 Azure 数据中心，所需的 Azure 服务级别协议 (SLA)。 故障转移时，我们要确保你的元数据和保管库保留在为你的保管库选择的同一地理区域中。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？

[故障转移](site-recovery-failover.md)不是自动的。 通过在门户中，进行一次选择启动的故障转移，也可以使用[PowerShell](/powershell/module/az.recoveryservices)触发故障转移。

### <a name="can-i-fail-back-to-a-different-location"></a>是否可以故障回复到不同位置？

是的。 如果您故障转移到 Azure，你将可以故障回复到不同位置，如果原始不可用。 [了解详细信息](concepts-types-of-failback.md#alternate-location-recovery-alr)。

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>为什么使用专用对等互连进行故障回复需要 VPN 或 ExpressRoute？

当你从 Azure 故障回复时，数据从 Azure 复制回在本地 VM 和私有访问是必需的。

### <a name="can-i-resize-the-azure-vm-after-failover"></a>是否可以在故障转移后调整 Azure VM 的大小？

否，不能在故障转移后更改的大小或 VM 为目标的类型。

## <a name="automation-and-scripting"></a>自动化和脚本

### <a name="can-i-set-up-replication-with-scripting"></a>是否可以使用脚本设置复制？

是的。 您可以通过使用 Rest API、 PowerShell 或 Azure SDK 自动化 Site Recovery 工作流。 [了解详细信息](vmware-azure-disaster-recovery-powershell.md)。

## <a name="performance-and-capacity"></a>性能和容量

### <a name="can-i-throttle-replication-bandwidth"></a>是否可以限制复制带宽？

是的。 [了解详细信息](site-recovery-plan-capacity-vmware.md)。

## <a name="next-steps"></a>后续步骤

- [查看](vmware-physical-azure-support-matrix.md)支持要求。
- [设置](vmware-azure-tutorial.md) VMware 到 Azure 的复制。
