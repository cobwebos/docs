---
title: "使用 Azure Site Recovery 复制到 Azure 的先决条件 | Microsoft 文档"
description: "了解使用 Azure Site Recovery 服务将 VM 和物理计算机复制到 Azure 的先决条件。"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: raynew
ms.openlocfilehash: cb7c3892dc0acbffdec636e5e7b3ce063660153c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2017
---
#  <a name="prerequisites-for-replication-from-on-premises-to-azure-by-using-site-recovery"></a>使用 Site Recovery 从本地复制到 Azure 的先决条件

> [!div class="op_single_selector"]
> * [从 Azure 复制到 Azure](site-recovery-azure-to-azure-prereq.md)
> * [从本地复制到 Azure](site-recovery-prereq.md)

Azure Site Recovery 可以安排从 Azure 虚拟机 (VM) 到其他 Azure 区域的复制，从而帮助实现业务连续性和灾难恢复 (BCDR) 策略。 Site Recovery 还可以将本地物理服务器和 VM 复制到云 (Azure) 或辅助数据中心。 如果主位置发生故障，可以故障转移到辅助位置，使应用和工作负荷保持可用。 当主位置恢复正常运行时，可以故障回复到主要位置。 有关 Site Recovery 的详细信息，请参阅[什么是 Site Recovery？](site-recovery-overview.md)。

在本文中，我们汇总了通过 Site Recovery 开始从本地计算机复制到 Azure 需要满足的先决条件。

可以在本文末尾发表任何评论。 还可以在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中询问技术问题。

## <a name="azure-requirements"></a>Azure 要求

**要求** | **详细信息**
--- | ---
**Azure 帐户** | 一个 [Microsoft Azure 帐户](http://azure.microsoft.com/)。 可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。
**Site Recovery 服务** | 有关 Azure Site Recovery 服务定价的详细信息，请参阅 [Site Recovery 定价](https://azure.microsoft.com/pricing/details/site-recovery/)。 |
**Azure 存储** | 需要使用一个 Azure 存储帐户来存储复制的数据。 存储帐户必须与 Azure 恢复服务保管库位于相同的区域中。 当发生故障转移时会创建 Azure VM。<br/><br/> 根据要用于 Azure VM 故障转移的资源模型，可以使用 [Azure 资源管理器部署模型](../storage/common/storage-create-storage-account.md)或[经典部署模型](../storage/common/storage-create-storage-account.md)设置帐户。<br/><br/>可使用[异地冗余存储](../storage/common/storage-redundancy.md#geo-redundant-storage)或本地冗余存储。 建议使用异地冗余存储。 借助异地冗余存储，在发生区域性故障或无法恢复主要区域时可复原数据。<br/><br/> 可以使用标准 Azure 存储帐户，也可以使用 Azure 高级存储。 [高级存储](https://docs.microsoft.com/azure/storage/storage-premium-storage)通常用于需要稳定的高 I/O 性能和低延迟的 VM。 使用高级存储，VM 可以承载 I/O 密集型工作负荷。 如果为复制的数据使用高级存储，则还需要一个标准存储帐户。 标准存储帐户用于存储复制日志，以便捕获对本地数据所做的更改。<br/><br/>
**存储限制** | 无法将在 Site Recovery 中使用的存储帐户移动到其他资源组，也无法将其移动到或用于其他订阅。<br/><br/> 目前不支持复制到印度中部和印度南部的高级存储帐户。
**Azure 网络** | 需要一个 Azure 网络，在故障转移后 Azure VM 将连接到该网络。 该 Azure 网络必须与恢复服务保管库位于相同的区域中。<br/><br/> 在 Azure 门户中，可以使用 [Resource Manager 部署模型](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)或[经典部署模型](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)创建 Azure 网络。<br/><br/> 如果从 System Center Virtual Machine Manager (VMM) 复制到 Azure，则必须在 VMM VM 网络与 Azure 网络之间创建网络映射。 这可以确保 Azure VM 在故障转移后连接到合适的网络。
**网络限制** | 无法将在 Site Recovery 中使用的网络帐户移动到其他资源组，也无法将其移动到或用于其他订阅。
**网络映射** | 如果复制 VMM 云中的 Microsoft Hyper-V VM，则必须设置网络映射。 这可以确保在故障转移后创建 Azure VM 时将其连接到合适的网络。

> [!NOTE]
> 以下各部分介绍了客户环境中各组件的先决条件。 有关对特定配置的支持的详细信息，请参阅[支持矩阵](site-recovery-support-matrix.md)。
>

## <a name="disaster-recovery-of-vmware-vms-or-physical-windows-or-linux-servers-to-azure"></a>在发生灾难后将 VMware VM 或物理 Windows/Linux 服务器恢复到 Azure
下面的组件是对 VMware VM 或物理 Windows 或 Linux 服务器进行灾难恢复所必需的。 这些是 [Azure 要求](#azure-requirements)中描述的组件之外的组件。


### <a name="configuration-server-or-additional-process-server"></a>配置服务器或附加进程服务器

将一台本地计算机设置为配置服务器来安排本地站点与 Azure 之间的通信。 下表介绍了可配置为配置服务器的虚拟机的系统和软件需求。

> [!IMPORTANT]
> 部署配置服务器以保护 VMware 虚拟机时，我们建议将其部署为高可用性 (HA) 虚拟机。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

### <a name="vmware-vcenter-or-vsphere-host-prerequisites"></a>VMware vCenter 或 vSphere 主机先决条件

| **组件** | **要求** |
| --- | --- |
| **vSphere** | 必须有一个或多个 VMware vSphere 虚拟机监控程序。<br/><br/>虚拟机监控程序必须运行包含最新更新的 vSphere 版本 6.0、5.5 或 5.1。<br/><br/>建议将 vSphere 主机和 vCenter 服务器与进程服务器置于同一网络中。 这就是指配置服务器所在的网络，除非已设置了专用进程服务器。 |
| **vCenter** | 建议部署 VMware vCenter 服务器来管理 vSphere 主机。 它必须运行包含最新更新的 vCenter 6.0 或 5.5 版。<br/><br/>**限制**：Site Recovery 不支持在 vCenter vMotion 的实例之间进行复制。 在执行重新保护操作之后，主目标 VM 也不支持存储 DRS 和存储 vMotion。|

### <a name="replicated-machine-prerequisites"></a>复制的计算机先决条件

| **组件** | **要求** |
| --- | --- |
| **本地计算机** (VMware VM) | 复制的 VM 上必须已安装并运行 VMware 工具。<br/><br/> VM 必须满足创建 Azure VM 时的 [Azure 先决条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。<br/><br/>每台受保护计算机上的磁盘容量不能超过 1,023 GB。 <br/><br/>安装驱动器上至少需要 2 GB 的可用空间才能安装组件。<br/><br/>如果想要实现多 VM 一致性，必须在 VM 本地防火墙中打开端口 20004。<br/><br/>计算机名称必须包含 1 到 63 个字符（可以使用字母、数字和连字符）。 名称必须以字母或数字开头，并以字母或数字结尾。 <br/><br/>为计算机启用复制后，可以修改 Azure 名称。<br/><br/> |
| **Windows 计算机**（物理机或 VMware） | 计算机必须运行下列受支持的 64 位操作系统之一： <br/>- Windows Server 2016（服务器核心、带桌面体验的服务器）<br/>- Windows Server 2012 R2<br/>- Windows Server 2012<br/>- 包含 SP1 的 Windows Server 2008 R2 或更高版本<br/><br/> 操作系统必须安装在驱动器 C 上。OS 磁盘必须为 Windows 基本磁盘而不是动态磁盘。 数据磁盘可以是动态磁盘。<br/><br/>|
| **Linux 计算机**（物理机或 VMware） | 计算机必须运行下列受支持的 64 位操作系统之一： <br/>- Red Hat Enterprise Linux 5.2 到 5.11、6.1 到 6.9、7.0 到 7.3<br/>- CentOS 5.2 到 5.11、6.1 到 6.9、7.0 到 7.3<br/>- Ubuntu 14.04 LTS 服务器（有关受支持的 Ubuntu 内核版本的列表，请参阅[支持的操作系统](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)）<br/>- Ubuntu 16.04 LTS 服务器（有关受支持的 Ubuntu 内核版本的列表，请参阅[支持的操作系统](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)）<br/>- Debian 7 或 Debian 8<br/>-  Oracle Enterprise Linux 6.5 或 6.4（运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel Release 3 (UEK3)）<br/>- SUSE Linux Enterprise Server 11 SP4 或 SUSE Linux Enterprise Server 11 SP3<br/><br/>在受保护计算机上的 /etc/hosts 文件中，必须有条目将本地主机名映射到与所有网络适配器相关联的 IP 地址。<br/><br/>在故障转移后，如果希望使用安全外壳 (SSH) 客户端连接到运行 Linux 的 Azure VM，请确保受保护计算机上的 SSH 服务设置为在系统启动时自动启动。 另请确保防火墙规则允许与受保护的计算机建立 SSH 连接。<br/><br/>主机名、装载点、设备名称以及 Linux 系统路径和文件名（例如 /etc/ 和 /usr）必须采用英文形式。<br/><br/>下列目录（如果设置为单独的分区或文件系统）必须位于源服务器上的同一磁盘（OS 磁盘）中：<br/>- / (root)<br/>- /boot<br/>- /usr<br/>- /usr/local<br/>- /var<br/>- /etc<br/><br/>目前，XFS 文件系统上的 Site Recovery 不支持 XFS v5 功能，例如元数据校验和。 请确保 XFS 文件系统未使用任何 v5 功能。 可使用 xfs_info 实用工具来检查分区的 XFS 超级块。 如果 **ftype** 设置为 **1**，则在使用 XFS v5 功能。<br/><br/>在 Red Hat Enterprise Linux 7 和 CentOS 7 服务器上，必须安装和提供 lsof 实用工具。<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-to-azure-no-vmm"></a>在发生灾难后将 Hyper-V VM 恢复到 Azure（无 VMM）

下面的组件是对 VMM 云中的 Hyper-V VM 进行灾难恢复所必需的。 这些是 [Azure 要求](#azure-requirements)中描述的组件之外的组件。

| **先决条件** | **详细信息** |
| --- | --- |
| **Hyper-V 主机** |一台或多台本地服务器必须运行包含最新更新的 Windows Server 2012 R2 并已启用 Hyper-V 角色，或者运行 Microsoft Hyper-V Server 2012 R2。<br/><br/>Hyper-V 服务器必须有一个或多个 VM。<br/><br/>Hyper-V 服务器必须直接或通过代理连接到 Internet。<br/><br/>Hyper-V 服务器必须已安装知识库文章 [2961977](https://support.microsoft.com/kb/2961977) 中所述的修补程序。
|**提供程序和代理**| 在部署 Site Recovery 期间，需要安装 Azure Site Recovery 提供程序。 安装该提供程序还会在运行所要保护的 VM 的每台 Hyper-V 服务器上安装 Azure 恢复服务代理。 <br/><br/>Site Recovery 保管库中的所有 Hyper-V 服务器必须具有相同版本的提供程序和代理。<br/><br/>提供程序需要通过 Internet 连接到 Site Recovery。 流量可以直接发送或通过代理发送。 不支持基于 HTTPS 的代理。 代理服务器必须允许访问下列 URL：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>如果在服务器上设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。<br/><br/> 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS（端口 443）。<br/><br/> 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure"></a>在发生灾难后将 VMM 云中的 Hyper-V VM 恢复到 Azure

下面的组件是对 VMM 云中的 Hyper-V VM 进行灾难恢复所必需的。 这些是 [Azure 要求](#azure-requirements)中描述的组件之外的组件。

| **先决条件** | **详细信息** |
| --- | --- |
| **Virtual Machine Manager** |必须具有一台或多台运行 System Center 2012 R2 或更高版本的 VMM 服务器。 每台 VMM 服务器必须配置有一个或多个云。 <br/><br/>云必须具有：<br/>- 一个或多个 VMM 主机组。<br/>- 每个主机组中有一个或多个 Hyper-V 主机服务器或群集。<br/><br/>有关设置 VMM 云的详细信息，请参阅 [How to create a cloud in Virtual Machine Manager 2012](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx)（如何在 Virtual Machine Manager 2012 中创建云）。 |
| **Hyper-V** |Hyper-V 主机服务器必须至少运行启用了 Hyper-V 角色的 Windows Server 2012 R2 或者运行 Microsoft Hyper-V Server 2012 R2。 必须安装最新更新。<br/><br/> Hyper-V 服务器必须有一个或多个 VM。<br/><br/> 必须在 VMM 云中管理包含要复制的 VM 的 Hyper-V 主机服务器或群集。<br/><br/>Hyper-V 服务器必须直接或通过代理连接到 Internet。<br/><br/>Hyper-V 服务器必须已安装知识库文章 [2961977](https://support.microsoft.com/kb/2961977) 中所述的修补程序。<br/><br/>Hyper-V 主机服务器需要访问 Internet 才可将数据复制到 Azure。 |
| **提供程序和代理** |在部署 Azure Site Recovery 期间，需要在 VMM 服务器上安装 Azure Site Recovery 提供程序。 在 Hyper-V 主机上安装恢复服务代理。 提供程序和代理需要通过 Internet 直接连接到或通过代理连接到 Azure。 不支持基于 HTTPS 的代理。 VMM 服务器和 Hyper-V 主机上的代理服务器必须允许访问： <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>如果在 VMM 服务器上设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。<br/><br/> 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS（端口 443）。<br/><br/>允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>复制的计算机先决条件

| **组件** | **详细信息** |
| --- | --- |
| **受保护的 VM** | Site Recovery 支持 [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) 支持的所有操作系统。<br/><br/>VM 必须满足创建 Azure VM 时的 [Azure 先决条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。 计算机名称必须包含 1 到 63 个字符（可以使用字母、数字和连字符）。 名称必须以字母或数字开头，并以字母或数字结尾。 <br/><br/>在为 VM 启用复制后可以修改 VM 名称。 <br/><br/> 每台受保护计算机的磁盘容量不能超过 1,023 GB。 一台 VM 最多可以有 16 个磁盘（最大容量为 16 TB）。<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-a-customer-owned-site"></a>在发生灾难后将 VMM 云中的 Hyper-V VM 恢复到客户拥有的站点

下面的组件是在发生灾难后将 VMM 云中的 Hyper-V VM 恢复到客户拥有的站点所必需的。 这些是 [Azure 要求](#azure-requirements)中描述的组件之外的组件。

| **组件** | **详细信息** |
| --- | --- |
| **Virtual Machine Manager** |  建议在主站点和辅助站点中各部署一台 VMM 服务器。<br/><br/> 若要通过单台 VMM 服务器在云之间进行复制，需要在该 VMM 服务器上配置至少两个云。<br/><br/> VMM 服务器必须至少运行具有最新更新的 System Center 2012 SP1。<br/><br/> 每台 VMM 服务器必须有一个或多个云。 所有云都必须设置 Hyper-V 容量配置文件。 <br/><br/>云必须具有一个或多个 VMM 主机组。 有关设置 VMM 云的详细信息，请参阅[准备 Azure Site Recovery 部署](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)。 |
| **Hyper-V** | Hyper-V 服务器必须至少运行启用了 Hyper-V 角色且安装了最新更新的 Windows Server 2012。<br/><br/> Hyper-V 服务器必须有一个或多个 VM。<br/><br/>  Hyper-V 主机服务器必须位于主要和辅助 VMM 云中的主机组内。<br/><br/> 如果在 Windows Server 2012 R2 上的群集中运行 Hyper-V，建议安装知识库文章 [2961977](https://support.microsoft.com/kb/2961977) 中所述的更新。<br/><br/> 如果在 Windows Server 2012 上的群集中运行 Hyper-V，并且具有基于静态 IP 地址的群集，则不会自动创建群集代理。 必须手动配置群集代理。 有关群集代理的详细信息，请参阅 [Configure the replica broker role for cluster-to-cluster replication](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)（为群集到群集复制配置副本代理角色）。 |
| **提供程序** | 在部署 Site Recovery 期间，需要在 VMM 服务器上安装 Azure Site Recovery 提供程序。 该提供程序通过 HTTPS（端口 443）与 Site Recovery 进行通信来安排复制。 数据复制是通过 LAN 或通过 VPN 连接在主要和辅助 Hyper-V 服务器之间进行的。<br/><br/> 在 VMM 服务器上运行的提供程序需要访问以下 URL：<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Site Recovery 提供程序必须允许从 VMM 服务器到 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)的防火墙通信，并允许 HTTPS (端口 443) 协议。 |


## <a name="url-access"></a>URL 访问
必须可以从 VMware、VMM 和 Hyper-V 主机服务器访问以下 URL：

|**URL** | **VMM 到 VMM** | **VMM 到 Azure** | **Hyper-V 到 Azure** | **VMware 到 Azure** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | ALLOW | 允许 | 允许 | ALLOW |
|``*.backup.windowsazure.com`` | 不是必需 | 允许 | 允许 | 允许 |
|``*.hypervrecoverymanager.windowsazure.com`` | 允许 | 允许 | 允许 | 允许 |
|``*.store.core.windows.net`` | 允许 | 允许 | 允许 | ALLOW |
|``*.blob.core.windows.net`` | 不是必需 | 允许 | 允许 | ALLOW |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | 不是必需 | 不是必需 | 不是必需 | 允许 SQL 下载 |
|``time.windows.com`` | ALLOW | 允许 | 允许 | 允许|
|``time.nist.gov`` | 允许 | 允许 | 允许 | ALLOW |
