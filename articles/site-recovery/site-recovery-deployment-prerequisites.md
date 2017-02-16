---
title: "Azure Site Recovery 部署先决条件 | Microsoft Docs"
description: "本文介绍通过 Azure Site Recovery 进行复制设置的先决条件。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 92abadf68e4acbd9daae9e15dcfdce53563f6eb2
ms.openlocfilehash: a407f5ebe096db74820ff93354a636bc63894b1d

---

# <a name="site-recovery-deployment-prerequisites"></a>Site Recovery 部署先决条件

组织需要制定 BCDR 策略来确定应用、工作负荷和数据如何在计划和非计划停机期间保持运行和可用，并尽快恢复正常运行情况。 Site Recovery 是一项 Azure 服务，可以通过协调从本地物理服务器和虚拟机到云 (Azure) 或辅助数据中心的的复制，来为 BCDR 策略提供辅助。 当主要位置发生故障时，你可以故障转移到辅助位置，使应用和工作负荷保持可用。 当主要位置恢复正常时，你可以故障转移回到主要位置。 有关详细信息，请参阅 [什么是 Site Recovery？](site-recovery-overview.md)

本文汇总了 Site Recovery 复制方案的部署先决条件。  

欢迎在本文底部发表任何看法，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上咨询技术问题。



## <a name="azure-deployment-models"></a>Azure 部署模型

Azure 提供两种不同的[部署模型](../azure-resource-manager/resource-manager-deployment-model.md)用于创建和处理资源：Azure Resource Manager 模型和经典模型。 Azure 还有两个门户 – 支持经典部署模型的 [Azure 经典门户](https://manage.windowsazure.com/)，以及支持两种部署模型的 [Azure 门户](https://ms.portal.azure.com/)。

应在 [Azure 门户](https://portal.azure.com)中部署新的 Site Recovery 方案。 此门户提供新的功能以及改进的部署体验。 可以在经典门户中维护保管库，但不能创建新的保管库。


## <a name="azure-account-requirements"></a>Azure 帐户要求

**要求** | **详细信息**
--- | --- 
**Azure 帐户** | 一个 [Microsoft Azure](http://azure.microsoft.com/) 帐户。<br/><br/> 你可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。 [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 定价。


## <a name="azure-storage-requirements"></a>Azure 存储要求

复制的数据存储在 Azure 存储中，发生故障转移时将创建 Azure VM。

**要求** | **详细信息**
--- | --- 
[Azure 存储帐户](../storage/storage-introduction.md) | 可以使用 [GRS](../storage/storage-redundancy.md#geo-redundant-storage) 或 LRS 存储。<br/><br/> 建议使用 GRS，以便在发生区域性故障或无法恢复主要区域时，能够复原数据。 [了解详细信息](https://docs.microsoft.com/en-us/azure/storage/storage-redundancy)
**Azure 门户** | 在 Azure 门户中，可以使用 Resource Manager 存储，也可使用经典存储帐户。
**高级存储** | 如果使用 Azure 门户将 VMware VM 或物理服务器复制到 Azure，则支持[高级存储](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)。<br/><br/> 高级存储通常用于 IO 性能一贯较高且延迟一贯较低、托管 IO 密集型工作负荷的虚拟机。<br/><br/> 如果使用高级存储，还需要一个标准存储帐户来存储复制日志，以便捕获对本地数据所做的更改。
**存储限制** | 经典门户仅支持 GRS。<br/><br/> 在 Azure 门户中创建的存储帐户不能跨资源组移动。<br/><br/> 目前不支持复制到印度中部和印度南部的高级存储帐户。

## <a name="azure-network-requirements"></a>Azure 网络要求

需要一个 Azure 网络，以便故障转后创建的 Azure VM 连接到该网络。

**要求** | **详细信息**
--- | ---
**网络区域** | 该网络必须位于与保管库相同的区域中。
**Azure 门户** | 在 Azure 门户中，可以使用 Resource Manager 网络或经典网络。
**网络映射** | 如果从 VMM 复制到 Azure，需在 VMM VM 网络与 Azure 网络之间设置[网络映射](site-recovery-network-mapping.md)，确保 Azure VM 在故障转移后连接到适当的网络。


## <a name="vmware-replication-requirements-to-azure"></a>VMware 复制要求（复制到 Azure）

**组件** | **要求**
--- | ---
**配置服务器** | 一台运行 Windows Server 2012 R2 的本地物理机或虚拟机。 所有本地 Site Recovery 组件都安装在此计算机上。<br/><br/>若要进行 VMware VM 复制，建议将该服务器部署为高可用性 VMware VM。 <br/><br/>如果服务器是 VMware VM，则网络适配器类型应为 VMXNET3。 如果使用不同类型的网络适配器，请在 vSphere 5.5 服务器上安装 [VMware 更新](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)。<br/><br/>服务器应拥有一个静态 IP 地址。<br/><br/>服务器不应为域控制器。<br/><br/>该服务器的主机名最多包含 15 个字符。<br/><br/>操作系统只能为英文版。<br/><br/> 安装 VMware vSphere PowerCLI 6.0。 服务器。<br/><br/>配置服务器需要访问 Internet。 出站访问权限需符合以下要求：<br/><br/>在安装 Site Recovery 组件过程中在 HTTP 80 上进行临时访问（以下载 MySQL）<br/><br/>在 HTTPS 443 上进行持续的出站访问，以便进行复制管理<br/><br/>在 HTTPS 9443 上进行针对复制流量的持续出站访问（此端口可以修改）<br/><br/> 允许使用以下 URL 进行 MySQL 下载：``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> 服务器也需要[这些 URL](#requirements-for-url-access) 的访问权限
**进程服务器** | 默认安装在配置服务器上<br/><br/> 充当复制网关。 此服务器接收受保护源计算机提供的复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储空间。 它还处理用于保护计算机的移动服务的推送安装，并执行 VMware VM 的自动发现。 随着部署扩大，可以添加作为进程服务器运行的独立专用进程服务器，目的仅在于处理更大的复制流量。
**主目标服务器** | 默认安装在配置服务器上。 处理从 Azure 进行故障回复期间产生的复制数据。
**vSphere 主机** | 一个或多个 VMware vSphere 虚拟机监控程序。<br/><br/>虚拟机监控程序应运行 vSphere 版本 6.0、5.5 或 5.1 并装有最新更新。<br/><br/>建议 vSphere 主机和 vCenter 服务器位于进程服务器所在的同一网络中（除非设置了专用的进程服务器，否则，这是配置服务器所在的网络）。 
**vCenter 服务器** | 建议部署 VMware vCenter 服务器来管理 vSphere 主机。 它应该运行的是带有最新更新的 vCenter 6.0 或 5.5 版。<br/><br/>**限制**：Site Recovery 不支持新的 vCenter 和 vSphere 6.0 功能，例如跨 vCenter vMotion、虚拟卷和存储 DRS。 Site Recovery 支持仅限也可在 5.5 版中使用的功能

### <a name="vmware-vm-requirements-to-azure"></a>VMware VM 要求（复制到 Azure）

**组件** | **要求**
--- | ---
**VMware VM** | 复制的 VM 上应已安装并运行 VMware 工具。<br/><br/> VM 应符合创建 Azure VM 的 [Azure 先决条件](site-recovery-support-matrix.md#support-for-azure-vms)。<br/><br/>受保护计算机上单个磁盘的容量不应超过 1023 GB。 一台 VM 最多可以有 64 个磁盘（因此最大容量为 64 TB）。 <br/><br/>安装驱动器上至少需要 2 GB 的可用空间才能安装组件。<br/><br/>**限制**：不支持保护带有加密磁盘的 VM。<br/><br/>**限制**：不支持共享磁盘来宾群集。<br/><br/>如果想要实现多个 VM 之间的一致性，应在 VM 本地防火墙中打开**端口 20004**。<br/><br/>不支持使用不支持统一可扩展固件接口 (UEFI)/可扩展固件接口 (EFI) 引导模式的计算机。<br/><br/>计算机名称应包含 1 到 63 个字符（字母、数字和连字符）。 名称必须以字母或数字开头，并以字母或数字结尾。 为计算机启用复制后，可以修改 Azure 名称。<br/><br/>如果源 VM 具有 NIC 组合，在故障转移到 Azure 后，它将转换为单个 NIC。<br/><br/>如果受保护的虚拟机有 iSCSI 磁盘，则 Site Recovery 会在 VM 故障转移到 Azure 时将受保护的 VM iSCSI 磁盘转换为 VHD 文件。 如果 iSCSI 目标可供 Azure VM 访问，后者会连接到该目标且实际上会看到两个磁盘 – Azure VM 上的 VHD 磁盘，以及源 iSCSI 磁盘。 在这种情况下，需要断开连接 Azure VM 上显示的 iSCSI 目标。
**运行 Windows 的 VM** | Windows 计算机应运行[支持的](site-recovery-support-matrix.md#support-for-replicated-machines) 64 位操作系统。<br/><br/> 操作系统应安装在 C:\ 驱动器上。<br/><br/> OS 磁盘应是 Windows 基本磁盘而不是动态磁盘。 数据磁盘可以是动态磁盘。<br/><br/> Site Recovery 支持包含 RDM 磁盘的 VM。 在故障回复期间，如果原始的源 VM 和 RDM 磁盘可用，Site Recovery 会重复使用 RDM 磁盘。 如果这些磁盘不可用，则 Site Recovery 会在故障回复期间为每个磁盘创建一个新的 VMDK 文件。
**运行 Linux 的 VM** | Linux 计算机应运行[支持的操作系统](site-recovery-support-matrix.md#support-for-replicated-machines)。<br/><br/> 受保护计算机上的 /etc/hosts 文件包含的条目应将本地主机名映射到与所有网络适配器相关联的 IP 地址。<br/><br/> 主机名、装载点、设备名称，以及 Linux 系统路径和文件名（例如 /etc/；/usr）只能采用英文形式。<br/><br/> 应[支持](site-recovery-support-matrix.md#support-for-replicated-machines)存储<br/><br/> Site Recovery 支持包含 RDM 磁盘的 VM。  在针对 Linux 进行故障回复期间，Site Recovery 不重复使用 RDM 磁盘。 相反，它会为每个相应的 RDM 磁盘创建新的 VMDK 文件。<br/><br/> 确保在 VMware 上的 VM 配置参数中设置 disk.enableUUID=true。 如果该条目不存在，请创建它。 需要为 VMDK 提供一致的 UUID，以便能够正确装载。 添加此设置还可确保在故障回复只将增量更改而不是整个复制内容传输回到本地。



## <a name="physical-servers-replication-requirements-to-azure"></a>物理服务器复制要求（复制到 Azure）

**组件** | **要求**
--- | ---
**配置服务器** | 一台运行 Windows Server 2012 R2 的本地物理机或虚拟机。 所有本地 Site Recovery 组件都安装在此计算机上。<br/><br/> 若要复制物理机，该计算机可以是物理服务器。<br/><br/>  配置服务器需要访问 Internet。 出站访问权限需符合以下要求：<br/><br/> 在安装 Site Recovery 组件过程中在 HTTP 80 上进行临时访问（以下载 MySQL）<br/><br/>在 HTTPS 443 上进行持续的出站访问，以便进行复制管理<br/><br/>在 HTTPS 9443 上进行针对复制流量的持续出站访问（此端口可以修改）<br/><br/> 允许使用以下 URL 进行 MySQL 下载：``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> 服务器也需要[这些 URL](#requirements-for-url-access) 的访问权限
**故障回复** | 即使复制了物理服务器，从 Azure 执行的故障回复的目标始终是 VMware VM。 如果不将配置服务器部署为 VMware VM，则需在进行故障回复之前，将 VMware VM 设置为接收故障回复流量。

### <a name="physical-machine-requirements-to-azure"></a>物理机要求（复制到 Azure）

**组件** | **要求**
--- | ---
**运行 Windows 的物理服务器** | Windows 计算机应运行[支持的](site-recovery-support-matrix.md#support-for-replicated-machines) 64 位操作系统。<br/><br/> 操作系统应安装在 C:\ 驱动器上。<br/><br/> OS 磁盘应是 Windows 基本磁盘而不是动态磁盘。 数据磁盘可以是动态磁盘。<br/><br/> Site Recovery 支持包含 RDM 磁盘的 VM。 在故障回复期间，如果原始的源 VM 和 RDM 磁盘可用，Site Recovery 会重复使用 RDM 磁盘。 如果这些磁盘不可用，则 Site Recovery 会在故障回复期间为每个磁盘创建一个新的 VMDK 文件。
**运行 Linux 的物理服务器** | Linux 计算机应运行[支持的操作系统](site-recovery-support-matrix.md#support-for-replicated-machines)。<br/><br/> 受保护计算机上的 /etc/hosts 文件包含的条目应将本地主机名映射到与所有网络适配器相关联的 IP 地址。<br/><br/> 主机名、装载点、设备名称，以及 Linux 系统路径和文件名（例如 /etc/；/usr）只能采用英文形式。<br/><br/> 应[支持](site-recovery-support-matrix.md#support-for-replicated-machines)存储。

## <a name="hyper-v-replication-requirements-to-azure"></a>Hyper-V 复制要求（复制到 Azure）

**组件** | **要求**
--- | ---
**VMM（可选）** | 可以选择性地在 VMM 云中托管的 Hyper-V 主机上复制 VM。<br/><br/> 如果不使用 VMM，则可将一个或多个 Hyper-V 主机或群集收集到 Hyper-V 站点中，然后为站点设置复制。<br/><br/> 如果使用 VMM，它应在 System Center 2012 R2 上运行。<br/><br/> 如果使用 VMM，应在每个 VMM 服务器上配置一个或多个云。 云应包含一个或多个 VMM 主机组，每个主机组中包含一个或多个 Hyper-V 主机服务器或群集。<br/><br/> VMM 服务器应连接到 Internet，并可访问[所需 URL](#requirements-for-url-access)。<br/><br/> 如果在 VMM 服务器上设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。<br/><br/> 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。<br/><br/> 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围。
**Hyper-V 主机** | Hyper-V 主机服务器必须至少运行具有 Hyper-V 角色或 **Microsoft Hyper-V Server 2012 R2** 且安装了最新更新的 **Windows Server 2012 R2**。<br/><br/> Hyper-V 服务器应包含一个或多个 VM。<br/><br/>Hyper-V 服务器应直接或通过代理连接到 Internet。<br/><br/>Hyper-V 服务器上应已安装文章 [2961977](https://support.microsoft.com/kb/2961977) 中提到的修补程序。<br/><br/>Hyper-V 主机服务器需要 Internet 访问权限（包括[所需 URL](#requirements-for-url-access) 的访问权限），以便将数据复制到 Azure。 另外，允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。<br/><br/> 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围。<br/><br/> 如果 Hyper-V 主机位于 VMM 云中，请确保安装 [KB 2961977](https://support.microsoft.com/en-us/kb/2961977) 中所述的修补程序

### <a name="hyper-v-vm-requirements-to-azure"></a>Hyper-V VM 要求（复制到 Azure）

**组件** | **要求**
--- | ---
**VM 合规性** |故障转移 VM 之前，请确保赋予 Azure VM 的名称符合 [Azure 先决条件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。 可以在启用 VM 的复制后修改此名称。
**磁盘** | 受保护计算机上单个磁盘的容量不应超过 1023 GB。 一台 VM 最多可以有 64 个磁盘（因此最大容量为 64 TB）。<br/><br/> 不支持共享的磁盘来宾群集。
**UEFI** | 不支持统一可扩展固件接口 (UEFI)/可扩展固件接口 (EFI)。
**NIC 组合** | 如果源 VM 具有 NIC 组合，在故障转移到 Azure 后，它将转换为单个 NIC。
**Linux 静态** | 不支持对运行 Linux 的、使用静态 IP 地址的 Hyper-V VM 进行保护。



## <a name="vmwarephysical-servers-replication-requirements-secondary-site"></a>VMware/物理服务器复制要求（辅助站点）

此方案由 InMage 产品处理，当用户选择将 VMware VM 或物理服务器复制到辅助站点时，可以从 Site Recovery 保管库下载该产品。 请注意，此方案无法在 Azure 门户中可用。

**组件** | **要求**
--- | ---
**主站点** | 需要进程服务器来处理缓存、压缩和数据优化操作。 该服务器还处理统一代理的推送安装，这在需要复制的每台计算机上都是必需的。<br/><br/> 若要复制 VMware VM，则需一个或多个 VMware ESX/ESXi 服务器，并可选择性地使用 VMware vCenter 服务器。
**辅助站点** | 需在辅助站点中设置一个配置服务器，以便配置和管理环境。<br/><br/> 主目标服务器安装在辅助站点中，用于保存复制的数据。<br/><br/> vContinuum 服务器默认安装在主目标服务器上，并提供一个控制台来管理和监视环境。

### <a name="vmware-vmphysical-machine-requirements-secondary-site"></a>VMware VM/物理机要求（辅助站点）

验证 [InMage 支持矩阵](https://aka.ms/asr-scout-cm)中的要求。


## <a name="hyper-v-replication-requirements-secondary-site"></a>Hyper-V 复制要求（辅助站点）

**组件** | **要求**
--- | ---
**VMM** | 若要将 Hyper-V VM 复制到辅助站点，必须在 System Center VMM 云中管理 Hyper-V host 主机。 <br/><br/> VMM 必须至少运行具有最新更新的 System Center 2012 SP1。<br/><br/> 建议在主站点和辅助站点中各有一个 VMM 服务器。 在此方案中，用户可以在[同一 VMM 服务器的不同云](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-single-vmm)之间复制，但需要进行一些手动配置。<br/><br/> VMM 服务器应连接到 Internet，并可访问[所需 URL](#requirements-for-url-access)。<br/><br/> 如果在 VMM 服务器上设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。<br/><br/> 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。<br/><br/> 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围。
**Hyper-V** | Hyper-V 服务器必须至少运行安装了 Hyper-V 角色和最新更新的 Windows Server 2012。<br/><br/> Hyper-V 服务器应包含一个或多个 VM<br/><br/> Hyper-V 主机应位于主要和辅助 VMM 服务器中的主机组内。<br/><br/> 如果在 Windows Server 2012 R2 上的群集中运行 Hyper-V，应安装更新 [2961977](https://support.microsoft.com/kb/2961977)。 如果用户在 Windows Server 2012 上有 Hyper-V 群集，并且有基于静态 IP 地址的群集，则不会自动创建群集代理。 [参阅更多](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)有关手动配置的内容。

### <a name="hyper-v-vm-requirements-secondary-site"></a>Hyper-V VM 要求（辅助站点）

**组件** | **详细信息**
--- | ---
**VMM 云** | VM 必须位于 VMM 云中的 Hyper-V 主机上。






## <a name="url-access-requirements"></a>URL 访问要求

应该可从 VMware、VMM 和 Hyper-V 主机服务器访问以下 URL。

**URL** | **VMM 到 VMM** | **VMM 到 Azure** | **Hyper-V 到 Azure** | **VMware 到 Azure** 
--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | ALLOW | 允许 | 允许 | ALLOW
``*.backup.windowsazure.com`` | 不是必需 | 允许 | 允许 | 允许
``*.hypervrecoverymanager.windowsazure.com`` | 允许 | 允许 | 允许 | 允许
``*.store.core.windows.net`` | 允许 | 允许 | 允许 | ALLOW
``*.blob.core.windows.net`` | 不是必需 | 允许 | 允许 | 允许
``https://www.msftncsi.com/ncsi.txt`` | 允许 | 允许 | 允许 | ALLOW
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | 不是必需 | 不是必需 | 不是必需 | 允许 SQL 下载
``time.windows.com`` | ALLOW | 允许 | 允许 | 允许
``time.nist.gov`` | 允许 | 允许 | 允许 | 允许

## <a name="failover-requirements"></a>故障转移要求

**方案** | **Test** | **已计划** | **计划外**
--- | --- | --- | ---
VMware 到 Azure | 支持 | 不支持 | 支持
物理机到 Azure | 支持 | 不支持 | 支持
Hyper-V (VMM) 到 Azure | 支持 | 支持 | 支持
Hyper-V（无 VMM）到 Azure | 支持 | 支持 | 不支持
Hyper-V（主 VMM/云）到辅助站点 | 支持 | 支持 | 支持

## <a name="failback-requirements"></a>故障回复要求

**方案** | **Test** | **已计划** | **计划外**
--- | --- | --- | ---
**Azure 到 VMware** | 不支持 | 不支持 | 支持
**Azure 到物理服务器** | 不支持 | 不支持 | 仅限从支持的到 VMware
**Azure 到 VMM** | 不支持 | 支持 | 不支持
**Azure 到 Hyper-V（无 VMM）** | 不支持 | 支持 | 不支持
**Hyper-V（辅助 VMM/云）到主站点** | 支持 | 支持 | 支持

### <a name="failback-from-azure-to-vmware-vms"></a>从 Azure 到 VMware VM 的故障回复

**组件** | **详细信息**
--- | ---
**Azure 中的临时进程服务器** | 若要在故障转移后从 Azure 故障回复，需通过设置将 Azure VM 配置为进程服务器，处理来自 Azure 的复制。 故障回复完成后，可以删除此 VM。
**VPN 连接** | 若要进行故障回复，需要设置从 Azure 网络到本地站点的 VPN 连接（或 Azure ExpressRoute）。
**单独的本地主目标服务器** | 本地主目标服务器处理故障回复。 主目标服务器默认安装在管理服务器上，但如果要故障回复大量流量，应该设置独立的本地主目标服务器。



### <a name="failback-from-azure-to-physical-machines"></a>从 Azure 故障回复到物理机

**组件** | **详细信息**
--- | ---
**物理机到物理机故障回复** | 不支持此操作。 这意味着，如果将物理服务器故障转移到 Azure，然后故障回复，则必须故障回复到 VMware VM。 不能故障回复到物理服务器。
**VM** | 需要一个可以故障回复到其中的本地 VMware VM。
**配置服务器** | 如果未将本地配置服务器部署为 VMware VM，则需单独设置一台主目标服务器作为 VMware VM。 之所以需要这样做，是因为主目标服务器需与 VMware 存储交互并与其连接，才能将磁盘还原到 VMware VM。



## <a name="deployment-optimization"></a>部署优化

使用以下提示来优化和缩放部署。

- **操作系统卷大小**：将虚拟机复制到 Azure 时，操作系统卷必须小于 1TB。 如果你的卷容量超过此值，可以在开始部署之前，手动将卷容量转移到另一个磁盘。
- **数据磁盘大小**：如果要复制到 Azure，一个虚拟机中最多可以包含 64 个数据磁盘，每个磁盘的最大大小为 1 TB。 可以有效地复制和故障转移约 64 TB 的虚拟机。
- **恢复计划限制**：Site Recovery 可以扩展到数千个虚拟机。 恢复计划旨在用作应一起故障转移的应用程序的模型，因此，我们可以将一个恢复计划中的计算机数目限制为 50。
- **Azure 服务限制**：每个 Azure 订阅在核心、云服务等方面附带了一组默认限制。建议运行测试故障转移，验证订阅中资源的可用性。 可以通过 Azure 支持人员修改这些限制。
- **容量规划**：阅读 Site Recovery 的[容量规划](site-recovery-capacity-planner.md)相关信息。
- **复制带宽**：如果你的复制带宽不足，请注意：
- **ExpressRoute**：Site Recovery 可以与 Azure ExpressRoute 和 WAN 优化器（如 Riverbed）配合使用。 [阅读更多](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx)有关 ExpressRoute 的信息。
- **复制流量**：Site Recovery 用户只能使用数据块（而不是整个 VHD）执行智能初始复制。 在复制过程中，只会复制更改。
- **网络流量**：可以通过使用基于目标 IP 地址和端口的策略设置 [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx)，来控制用于复制的网络流量。  此外，如果要使用 Azure 备份代理复制到 Azure Site Recovery，可以为该代理配置限制。 [了解详细信息](https://support.microsoft.com/kb/3056159)。
- **RTO**：若要度量使用 Site Recovery 时预期的恢复时间目标 (RTO)，我们建议运行测试故障转移并查看 Site Recovery 作业，分析完成操作所花费的时间。 如果你要故障转移到 Azure，为实现最佳 RTO，我们建议你通过与 Azure 自动化和恢复计划集成来自动化所有手动操作。
- **RPO**：复制到 Azure 时，Site Recovery 支持近乎同步的恢复点目标 (RPO)。 这假设数据中心和 Azure 之间有足够的带宽。



## <a name="next-steps"></a>后续步骤
查看常规部署要求之后，请阅读详细的先决条件并部署方案。

* [将 VMware 虚拟机复制到 Azure](site-recovery-vmware-to-azure.md)
* [将物理服务器复制到 Azure](site-recovery-vmware-to-azure.md)
* [将 VMM 云中的 Hyper-V 服务器复制到 Azure](site-recovery-vmm-to-azure.md)
* [将 Hyper-V 虚拟机（不在 VMM 中）复制到 Azure](site-recovery-hyper-v-site-to-azure.md)
* [将 Hyper-V VM 复制到辅助站点](site-recovery-vmm-to-vmm.md)
* [使用 SAN 将 Hyper-V VM 复制到辅助站点](site-recovery-vmm-san.md)
* [使用单个 VMM 服务器复制 Hyper-V VM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


