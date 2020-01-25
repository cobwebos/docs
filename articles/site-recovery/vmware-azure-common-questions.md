---
title: 有关 VMware 灾难恢复的常见问题 Azure Site Recovery
description: 使用 Azure Site Recovery 获取有关将本地 VMware Vm 灾难恢复到 Azure 的常见问题的解答。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 11/14/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 11f593bae6b3aab8355a8f39b56639cad8133b51
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719545"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>VMware에서 Azure로 복제에 대한 일반적인 질문

本文解答了在将本地 VMware 虚拟机（Vm）的灾难恢复部署到 Azure 时可能出现的常见问题。

## <a name="general"></a>일반

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM 灾难恢复需要执行哪些操作？

了解 VMware Vm 灾难恢复中[涉及的组件](vmware-azure-architecture.md)。

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>能否使用 Site Recovery 将 VMware Vm 迁移到 Azure？

예. 除了使用 Site Recovery 为 VMware Vm 设置完全灾难恢复以外，还可以使用 Site Recovery 将本地 VMware Vm 迁移到 Azure。 在此方案中，将本地 VMware Vm 复制到 Azure 存储。 그런 다음, 온-프레미스에서 Azure로 장애 조치합니다. 장애 조치(failover) 후 Azure VM에서 앱과 워크로드가 실행되며 사용할 수 있습니다. 此过程类似于设置完全灾难恢复，但在迁移过程中，不能从 Azure 进行故障回复。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>내 Azure 계정에 VM을 만들 수 있는 권한이 필요한가요?

구독 관리자인 경우 필요한 복제 권한을 갖고 있습니다. 如果您不是管理员，则需要执行以下操作的权限：

- 在配置 Site Recovery 时指定的资源组和虚拟网络中创建 Azure VM。
- 根据配置写入选定的存储帐户或托管磁盘。

[了解](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)有关所需权限的详细信息。

### <a name="what-applications-can-i-replicate"></a>어떤 애플리케이션을 복제할 수 있나요?

你可以复制在 VMware VM 上运行的满足[复制要求](vmware-physical-azure-support-matrix.md#replicated-machines)的任何应用或工作负荷。

- Site Recovery 支持识别应用程序的复制，因此可将应用程序故障转移并故障回复到智能状态。
- Site Recovery 与 Microsoft 应用程序（例如 SharePoint、Exchange、Dynamics、SQL Server 和 Active Directory）集成。 它还与领先供应商密切合作，包括 Oracle、SAP、IBM 和 Red Hat。

[자세히 알아봅니다](site-recovery-workload.md) .

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>能否在 Azure 上使用来宾操作系统服务器许可证？

是的，Microsoft 软件保障客户可以使用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来节省迁移到 Azure 的 Windows Server 计算机的许可成本，或使用 Azure 进行灾难恢复。

## <a name="security"></a>보안

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Site Recovery 需要对 VMware 服务器的哪些访问权限？

Site Recovery에서는 다음 작업을 위해 VMware 서버에 액세스해야 합니다.

- 设置运行 Site Recovery 配置服务器的 VMware VM。
- 복제할 VM을 자동으로 검색합니다.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Site Recovery 需要对 VMware Vm 的哪些访问权限？

- 若要复制，VMware VM 必须已安装并运行 Site Recovery 移动服务。 你可以手动部署该工具，也可以指定在为 VM 启用复制时 Site Recovery 执行该服务的推送安装。
- 복제 중에 VM은 Site Recovery와 다음과 같이 통신합니다.
    - Vm 与 HTTPS 端口443上的配置服务器进行通信，以进行复制管理。
    - Vm 将复制数据发送到 HTTPS 端口9443上的进程服务器。 （可以修改此设置。）
    - 다중 VM 일관성을 사용하도록 설정하면 VM에서 20004 포트를 통해 서로 통신합니다.

### <a name="is-replication-data-sent-to-site-recovery"></a>복제 데이터를 Site Recovery로 보내는가요?

不可以，Site Recovery 不会截获复制的数据，也不会有任何有关 Vm 上运行内容的信息。 复制数据在 VMware 虚拟机监控程序和 Azure 存储之间交换。 Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.  

Site Recovery 针对 ISO 27001:2013 和27018、HIPAA 和 DPA 进行了认证。 它在 SOC2 和 FedRAMP JAB 评估过程中。

## <a name="pricing"></a>가격 책정

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>如何实现计算 VMware 灾难恢复的大约收费？

使用[定价计算器](https://aka.ms/asr_pricing_calculator)估算 Site Recovery 时的成本。

有关成本的详细估计，请运行[VMware](https://aka.ms/siterecovery_deployment_planner)部署规划器工具，并使用[成本估算报表](https://aka.ms/asr_DP_costreport)。

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>复制到存储或直接复制到托管磁盘之间是否存在开销变化？

托管磁盘的收取方式与存储帐户略有不同。 [详细了解](https://azure.microsoft.com/pricing/details/managed-disks/)托管磁盘定价。

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>复制到常规用途 v2 存储帐户时，开销是否有任何变化？

你通常会发现，GPv2 存储帐户产生的事务成本会增加，因为 Azure Site Recovery 的事务量很高。 [阅读更多](../storage/common/storage-account-upgrade.md#pricing-and-billing)以估算更改。

## <a name="mobility-service"></a>모바일 서비스

### <a name="where-can-i-find-the-mobility-service-installers"></a>모바일 서비스 설치 관리자는 어디서 찾을 수 있나요?

安装程序位于配置服务器上的均位于%programdata%\asr\home\svsystems\pushinstallsvc\repository 文件夹中。

## <a name="how-do-i-install-the-mobility-service"></a>모바일 서비스를 설치하려면 어떻게 할까요?

在要复制的每个 VM 上，使用以下几种方法之一安装该服务：

- [推送安装](vmware-physical-mobility-service-overview.md#push-installation)
- 通过 UI 或 PowerShell[手动安装](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui)
- 使用[Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)的部署工具进行部署

## <a name="managed-disks"></a>Managed Disks

### <a name="where-does-site-recovery-replicate-data-to"></a>Site Recovery 将数据复制到何处？

Site Recovery 将本地 VMware Vm 和物理服务器复制到 Azure 中的托管磁盘。

- Site Recovery 进程服务器将复制日志写入目标区域中的缓存存储帐户。
- 这些日志用于在具有**asrseeddisk**前缀的 Azure 托管磁盘上创建恢复点。
- 发生故障转移时，你选择的恢复点将用于创建新的目标托管磁盘。 此托管磁盘已附加到 Azure 中的 VM。
- 之前复制到存储2019帐户的 Vm 不会受到影响。

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>能否将新计算机复制到存储帐户？

아닙니다. 从2019年3月开始，在 Azure 门户中，只能复制到 Azure 托管磁盘。

只有使用 PowerShell 或 REST API （版本2018-01-10 或2016-08-10），才能将新 Vm 复制到存储帐户。

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>复制到托管磁盘的好处是什么？

[了解如何](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/)Site Recovery 利用托管磁盘简化灾难恢复。

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>计算机受保护后，是否可以更改托管磁盘的类型？

是的，你可以轻松地为当前复制[更改托管磁盘的类型](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage)。 在更改类型之前，请确保未在托管磁盘上生成共享访问签名 URL：

1. 请在 "**概述**" 边栏选项卡上，访问 Azure 门户上的**托管磁盘**资源，并检查是否有 "共享访问签名 URL" 标题。
1. 如果有横幅，请选择它以取消正在进行的导出。
1. 在接下来的几分钟内更改磁盘的类型。 如果更改托管磁盘类型，请等待 Azure Site Recovery 生成新的恢复点。
1. 在将来使用新的恢复点进行任何测试故障转移或故障转移。

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>是否可以将复制从托管磁盘切换到非托管磁盘？

아닙니다. 不支持从托管到非托管的切换。

## <a name="replication"></a>복제

### <a name="what-are-the-replicated-vm-requirements"></a>복제된 VM에 대한 요구 사항은 무엇인가요?

[详细了解](vmware-physical-azure-support-matrix.md#replicated-machines)VMware vm 和物理服务器的支持要求。

### <a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?

VMware VM을 Azure에 복제하는 경우에는 복제가 계속됩니다.

### <a name="can-i-extend-replication"></a>복제를 확장할 수 있나요?

확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-do-an-offline-initial-replication"></a>오프라인 초기 복제를 수행할 수 있나요?

不支持脱机复制。 [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="what-is-asrseeddisk"></a>什么是 asrseeddisk？

对于每个源磁盘，会将数据复制到 Azure 中的托管磁盘。 此磁盘的前缀为**asrseeddisk**。 它存储源磁盘和所有恢复点快照的副本。

### <a name="can-i-exclude-disks-from-replication"></a>能否从复制中排除磁盘？

是的，你可以排除磁盘。

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>能否复制包含动态磁盘的 Vm？

예, 동적 디스크는 복제할 수 있습니다. 운영 체제 디스크는 기본 디스크여야 합니다.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>다중 VM 일관성에 대해 복제 그룹을 사용하는 경우 새 VM을 기존 복제 그룹에 추가할 수 있나요?

예, 복제를 활성화하면 새 VM을 기존 복제 그룹에 추가할 수 있습니다. 但是：

- 开始复制后，不能将 VM 添加到现有的复制组。
- 不能为现有 Vm 创建复制组。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>디스크를 추가하거나 크기를 조정하여 복제 중인 VM을 수정할 수 있나요?

对于到 Azure 的 VMware 复制，可以修改源 Vm 的磁盘大小。 如果要添加新磁盘，则必须添加磁盘并重新启用 VM 保护。

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>是否可以将本地计算机迁移到新的 vCenter Server，而不会影响正在进行的复制？

아닙니다. VMware Vcenter 或迁移的更改将影响正在进行的复制。 使用新的 vCenter Server 设置 Site Recovery，并再次启用计算机的复制。

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>是否可以复制到配置了虚拟网络（使用 Azure 防火墙）的缓存或目标存储帐户？

不需要，Site Recovery 不支持复制到虚拟网络上的 Azure 存储。

## <a name="component-upgrade"></a>组件升级

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>我的移动服务代理或配置服务器版本较旧，升级失败。 어떻게 하나요?

Site Recovery 遵循 N-4 支持模型。 [了解](https://aka.ms/asr_support_statement)有关如何从非常旧的版本升级的详细信息。

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>在哪里可以找到 Azure Site Recovery 的发行说明和更新汇总？

[了解新的更新](site-recovery-whats-new.md)，并[获取汇总信息](service-updates-how-to.md)。

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>在哪里可以找到有关灾难恢复到 Azure 的升级信息？

[了解升级](https://aka.ms/asr_vmware_upgrades)。

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>是否需要重新启动每次升级的源计算机？

建议在每次升级时重新启动。 [자세히 알아보기](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>구성 서버

### <a name="what-does-the-configuration-server-do"></a>구성 서버에서 수행하는 작업은 무엇인가요?

구성 서버는 다음을 포함하여 온-프레미스 Site Recovery 구성 요소를 실행합니다.

- 配置服务器本身。 服务器协调本地组件和 Azure 之间的通信，并管理数据复制。
- 充当复制网关的进程服务器。 此服务器：
    1. 복제 데이터를 받습니다.
    2. 캐싱, 압축 및 암호화를 사용하여 데이터를 최적화합니다.
    3. 将数据发送到 Azure 存储。
  进程服务器还会在 Vm 上执行移动服务的推送安装，并执行本地 VMware Vm 的自动发现。
- 主目标服务器，在从 Azure 进行故障回复期间处理复制数据。

구성 서버 구성 요소 및 프로세스에 대해 [자세히 알아보세요](vmware-azure-architecture.md).

### <a name="where-do-i-set-up-the-configuration-server"></a>구성 서버를 설정해야 하는 위치는 어떻게 되나요?

需要为配置服务器提供一个高度可用的本地 VMware VM。 对于物理服务器灾难恢复，请在物理计算机上安装配置服务器。

### <a name="what-do-i-need-for-the-configuration-server"></a>配置服务器需要执行哪些操作？

[필수 구성 요소](vmware-azure-deploy-configuration-server.md#prerequisites)를 검토하세요.

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>구성 서버는 템플릿을 사용하는 대신 수동으로 설정할 수 있나요?

建议使用最新版本的开放虚拟化格式（OVF）模板[创建配置服务器 VM](vmware-azure-deploy-configuration-server.md) 。 如果你无法使用该模板（例如，如果你无权访问 VMware 服务器），请从门户[下载](physical-azure-set-up-source.md)安装程序文件，并设置配置服务器。

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>구성 서버에서 둘 이상의 지역에 복제할 수 있나요?

아닙니다. 若要复制到多个区域，需要在每个区域中配置服务器。

### <a name="can-i-host-a-configuration-server-in-azure"></a>Azure에서 구성 서버를 호스팅할 수 있나요?

尽管可能，运行配置服务器的 Azure VM 需要与本地 VMware 基础结构和 Vm 进行通信。 此通信会增加延迟并影响正在进行的复制。

### <a name="how-do-i-update-the-configuration-server"></a>구성 서버를 업데이트하려면 어떻게 할까요?

[了解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)如何更新配置服务器。

- 可以在 " [Azure 更新" 页](https://azure.microsoft.com/updates/?product=site-recovery)中找到最新的更新信息。
- 可以从门户下载最新版本。 或者，你可以直接从[Microsoft 下载中心](https://aka.ms/asrconfigurationserver)下载最新版本的配置服务器。
- 如果版本比当前版本早于四个版本，请参阅升级指南的[支持声明](https://aka.ms/asr_support_statement)。

### <a name="should-i-back-up-the-configuration-server"></a>是否应备份配置服务器？

구성 서버의 예약된 정기 백업을 수행하는 것이 좋습니다.

- 若要成功进行故障回复，配置服务器数据库中必须存在要进行故障回复的虚拟机。
- 配置服务器必须正在运行且处于连接状态。
- [详细了解](vmware-azure-manage-configuration-server.md)常见的配置服务器管理任务。

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>구성 서버를 설정할 때 MySQL을 수동으로 다운로드하여 설치할 수 있나요?

예. 下载 MySQL，并将其放在 C:\Temp\ASRSetup 文件夹中。 然后，手动安装它。 구성 서버 VM을 설정하고 조건에 동의하면 MySQL이 **다운로드 및 설치**에 **이미 설치됨**으로 표시됩니다.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>MySQL을 다운로드하지 않고 Site Recovery에서 설치할 수 있나요?

예. 下载 MySQL 安装程序，并将其放在 C:\Temp\ASRSetup 文件夹中。 设置配置服务器 VM 时，请接受条款，然后选择 "**下载并安装**"。 门户将使用你添加的安装程序来安装 MySQL。

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>다른 용도로 구성 서버 VM을 사용할 수 있나요?

아닙니다. 仅将 VM 用于配置服务器。

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>구성 서버를 복제하고 오케스트레이션에 사용할 수 있나요?

아닙니다. 设置新的配置服务器，以避免注册问题。

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>能否更改配置服务器注册到的保管库？

아닙니다. 将保管库与配置服务器关联后，无法对其进行更改。 [了解](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)如何将配置服务器注册到不同的保管库。

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>能否为 VMware Vm 和物理服务器的灾难恢复使用相同的配置服务器？

是的，但是请注意，物理计算机只能故障回复到 VMware VM。

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>구성 서버의 암호는 어디서 다운로드할 수 있나요?

[了解](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)如何下载通行短语。

### <a name="where-can-i-download-vault-registration-keys"></a>자격 증명 모음 등록 키는 어디서 다운로드할 수 있나요?

在恢复服务保管库中，选择 " **Site Recovery 基础结构** > **管理**" 中的 "**配置服务器**"。 然后，在 "**服务器**" 中，选择 "**下载注册密钥**" 以下载保管库凭据文件。

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>是否可以使用单个配置服务器来保护多个 vCenter 实例？

是，单个配置服务器可以保护多个 Vcenter 中的 Vm。  对于可以添加到配置服务器的 vCenter 实例的数量没有限制，但是，单个配置服务器可以保护的虚拟机的限制适用。

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>单个配置服务器是否可以在 vCenter 内保护多个群集？

是的，Azure Site Recovery 可以保护不同群集上的 Vm。

## <a name="process-server"></a>프로세스 서버

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>为什么当启用复制时无法选择进程服务器？

版本9.24 及更高版本中的更新现在[会在启用复制时显示进程服务器的运行状况](vmware-azure-enable-replication.md#enable-replication)。 此功能有助于避免进程服务器限制，并最大程度地减少对不正常进程服务器的使用。

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>如何实现将进程服务器更新到版本9.24 或更高版本以获取准确的运行状况信息吗？

从[版本 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups)开始，添加了更多警报，指示进程服务器的运行状况。 [将 Site Recovery 组件更新到版本9.24 或更高版本](service-updates-how-to.md#links-to-currently-supported-update-rollups)，以便生成所有警报。

## <a name="failover-and-failback"></a>장애 조치 및 장애 복구

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>是否可以使用本地进程服务器进行故障回复？

强烈建议在 Azure 中创建进程服务器以进行故障回复，以避免数据传输延迟。 此外，如果在配置服务器中将源 Vm 网络与面向 Azure 的网络分离，则必须使用在 Azure 中创建的进程服务器进行故障回复。

### <a name="can-i-keep-the-ip-address-on-failover"></a>能否在故障转移时保留 IP 地址？

是的，你可以在故障转移时保留 IP 地址。 在故障转移之前，请确保在 VM 的 "**计算和网络**" 设置中指定目标 IP 地址。 同时，在故障转移时关闭计算机，以避免在故障回复期间发生 IP 地址冲突。

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>能否在故障转移之前更改目标 VM 大小或 VM 类型？

是的，你可以在故障转移前随时更改 VM 的类型或大小。 在门户中，使用复制的 VM 的 "**计算和网络**" 设置。

### <a name="how-far-back-can-i-recover"></a>복구할 수 있는 가장 오랜 복구 지점은 어떻게 되나요?

对于 VMware 到 Azure，可使用的最旧恢复点为72小时。

### <a name="how-do-i-access-azure-vms-after-failover"></a>장애 조치 후 Azure VM에 액세스하려면 어떻게 할까요?

故障转移后，可以通过安全的 internet 连接、站点到站点 VPN 或 Azure ExpressRoute 访问 Azure Vm。 若要进行连接，必须准备几项。 [자세히 알아보기](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>故障转移的数据是否复原能力？

Azure는 복원을 위해 디자인되었습니다. 根据 Azure 服务级别协议（SLA）的要求，将 Site Recovery 设计为故障转移到辅助 Azure 数据中心。 发生故障转移时，我们确保元数据和保管库保留在为保管库选择的相同地理区域。

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?

[장애 조치](site-recovery-failover.md)는 자동이 아닙니다. 通过在门户中进行一项选择开始故障转移，或者可以使用[PowerShell](/powershell/module/az.recoveryservices)来触发故障转移。

### <a name="can-i-fail-back-to-a-different-location"></a>다른 위치로 장애 조치할 수 있나요?

예. 如果故障转移到 Azure，则可以故障回复到另一个位置（如果原始位置不可用）。 [자세히 알아보기](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>为什么需要具有专用对等互连的 VPN 或 ExpressRoute 才能进行故障回复？

从 Azure 故障回复时，将 Azure 中的数据复制回本地 VM，并要求进行专用访问。


## <a name="automation-and-scripting"></a>자동화 및 스크립팅

### <a name="can-i-set-up-replication-with-scripting"></a>스크립팅을 사용하여 복제를 설정할 수 있나요?

예. 可以使用 Rest API、PowerShell 或 Azure SDK 自动执行 Site Recovery 工作流。 [자세히 알아보기](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>성능 및 용량

### <a name="can-i-throttle-replication-bandwidth"></a>복제 대역폭을 제한할 수 있나요?

예. [자세히 알아보기](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>다음 단계

- 지원 요구 사항을 [검토](vmware-physical-azure-support-matrix.md)합니다.
- VMware에서 Azure로의 복제를 [설정](vmware-azure-tutorial.md)합니다.
