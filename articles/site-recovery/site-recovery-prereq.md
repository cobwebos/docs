---
title: "使用 Azure Site Recovery 复制到 Azure 的先决条件 | Microsoft 文档"
description: "本文总结了使用 Azure Site Recovery 服务将 VM 和物理计算机复制到 Azure 的先决条件。"
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
ms.date: 12/11/2016
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 46ea97f5c13b7befc78466fae92a737107be8f83
ms.openlocfilehash: 31cae0cf625552c0e9547d0e514c6017c6a179ef

---

#  <a name="prerequisites-for-replication-to-azure-using-azure-site-recovery"></a>使用 Azure Site Recovery 复制到 Azure 的先决条件

 Site Recovery 是一项 Azure 服务，可以通过协调从本地物理服务器和虚拟机到云 (Azure) 或辅助数据中心的的复制，来为 BCDR 策略提供辅助。 当主要位置发生故障时，可以故障转移到辅助位置，使应用和工作负荷保持可用。 当主要位置恢复正常时，可以故障回复到主要位置。 有关详细信息，请参阅 [什么是 Site Recovery？](site-recovery-overview.md)

本文总结了开始使用 Site Recovery 复制到 Azure 所需的先决条件。

欢迎在本文底部发表任何看法，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上咨询技术问题。


## <a name="azure-requirements"></a>Azure 要求

**要求** | **详细信息**
--- | ---
**Azure 帐户** | 一个 [Microsoft Azure](http://azure.microsoft.com/) 帐户。<br/><br/> 你可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。
**Site Recovery 服务** | [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 定价。 |
**Azure 存储空间** | 需要使用一个 Azure 存储帐户来存储复制数据，并且该 Azure 存储帐户必须与恢复服务保管库在同一区域中。 复制的数据存储在 Azure 存储中，发生故障转移时将创建 Azure VM。<br/><br/> 根据要用于故障转移 Azure VM 的资源模型，可以在 [Resource Manager 模式](../storage/storage-create-storage-account.md)或[经典模式](../storage/storage-create-storage-account-classic-portal.md)下设置帐户。<br></br>可以使用 [GRS](../storage/storage-redundancy.md#geo-redundant-storage) 或 LRS 存储。 建议使用 GRS，以便在发生区域性故障或无法恢复主要区域时，能够复原数据。<br/><br/> 如果在 Azure 门户中复制 VMware VM 或物理服务器，支持使用高级存储。 [高级存储](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)通常用于 IO 性能一贯较高且延迟一贯较低、托管 IO 密集型工作负荷的虚拟机。 如果使用高级存储来存储复制数据，则还需要一个标准存储帐户来存储复制日志，以便捕获本地数据正在发生的更改。<br/><br/>
**存储限制** | 无法在资源组之间、订阅中或订阅之间移动 Site Recovery 中使用的存储帐户。<br/><br/> 目前不支持复制到印度中部和印度南部的高级存储帐户。
**Azure 网络** | 需要一个 Azure 网络，以便故障转移后 Azure VM 可以连接到该网络，并且该网络必须与恢复服务保管库在同一区域中。<br/><br/> 在 Azure 门户中，可以在 [Resource Manager 模型](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)或[经典模型](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)中创建网络。<br/><br/> 如果从 VMM 复制到 Azure，需在 VMM VM 网络与 Azure 网络之间设置[网络映射](site-recovery-network-mapping.md)，确保 Azure VM 在故障转移后连接到适当的网络。
**网络限制** | 无法在资源组之间、订阅中或订阅之间移动 Site Recovery 中使用的网络帐户。
**网络映射** | 如果复制 VMM 云中的 Hyper-V VM，则需要设置[网络映射](site-recovery-network-mapping.md)，以便在故障转移后创建 Azure VM 时，这些 VM 可以连接到相应的网络。

>[!NOTE]
>以下部分介绍客户环境中各种组件的先决条件。 有关对特定配置的支持的详细信息，请阅读[支持矩阵](site-recovery-support-matrix.md)。
>

## <a name="disaster-recovery-of-vmware-virtual-machines-or-physical-windowslinux-servers-to-azure"></a>将 VMware 虚拟机或 Windows/Linux 物理服务器灾难恢复到 Azure
除了[上面](#Azure requirements)提到的组件外，以下是对 VMware 虚拟机或物理 Windows/Linux 服务器进行灾难恢复所需的组件。


1. **配置服务器或其他进程服务器**：需要设置一台本地计算机作为配置服务器，以协调本地站点与 Azure 之间的通信并管理数据复制。 <br></br>

2. **VMware vCenter/vSphere 主机**

| **组件** | **要求** |
| --- | --- |
| **vSphere** | 一个或多个 VMware vSphere 虚拟机监控程序。<br/><br/>虚拟机监控程序应运行 vSphere 版本 6.0、5.5 或 5.1 并装有最新更新。<br/><br/>建议 vSphere 主机和 vCenter 服务器位于进程服务器所在的同一网络中（除非设置了专用的进程服务器，否则，这是配置服务器所在的网络）。 |
| **vCenter** | 建议部署 VMware vCenter 服务器来管理 vSphere 主机。 它应该运行的是带有最新更新的 vCenter 6.0 或 5.5 版。<br/><br/>**限制**：Site Recovery 不支持新的 vCenter 和 vSphere 6.0 功能，例如跨 vCenter vMotion、虚拟卷和存储 DRS。 Site Recovery 支持仅限也可在 5.5 版中使用的功能。||

3.**复制的计算机先决条件**


| **组件** | **要求** |
| --- | --- |
| **本地 VMware VM** | 复制的 VM 上应已安装并运行 VMware 工具。<br/><br/> VM 应符合创建 Azure VM 的 [Azure 先决条件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。<br/><br/>受保护计算机上单个磁盘的容量不应超过 1023 GB。 <br/><br/>安装驱动器上至少需要 2 GB 的可用空间才能安装组件。<br/><br/>如果想要实现多个 VM 之间的一致性，应在 VM 本地防火墙中打开**端口 20004**。<br/><br/>计算机名称应包含 1 到 63 个字符（字母、数字和连字符）。 名称必须以字母或数字开头，并以字母或数字结尾。 为计算机启用复制后，可以修改 Azure 名称。<br/><br/> |
| **Windows 计算机（物理机或 VMware）** | 计算机应运行支持的 64 位操作系统：Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 SP1 及更高版本。<br/><br/> 操作系统应安装在 C:\ 驱动器上。 OS 磁盘应是 Windows 基本磁盘而不是动态磁盘。 数据磁盘可以是动态磁盘。<br/><br/>|
| **Linux 计算机**（物理机或 VMware） | 需要安装支持的 64 位操作系统：Red Hat Enterprise Linux 6.7、7.1、7.2；Centos 6.5、6.6、6.7、7.0、7.1、7.2；运行 Red Hat 兼容内核或 Unbreakable Enterprise Kern Release 3 (UEK3) 的 Oracle Enterprise Linux 6.4、6.5；SUSE Linux Enterprise Server 11 SP3。<br/><br/>受保护计算机上的 /etc/hosts 文件包含的条目应将本地主机名映射到与所有网络适配器相关联的 IP 地址。<br/><br/>如果要在故障转移后使用安全外壳客户端 (ssh) 连接运行 Linux 的 Azure 虚拟机，请确保将受保护的计算机上的安全外壳服务设置为在系统启动时自动启动，并且防火墙规则允许与其建立 ssh 连接。<br/><br/>主机名、装载点、设备名称，以及 Linux 系统路径和文件名（例如 /etc/；/usr）只能采用英文形式。<br/><br/>

## <a name="disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-vmm"></a>将 Hyper-V 虚拟机灾难恢复到 Azure（无 VMM）

除了[上面](#Azure requirements)提到的组件外，以下是对 VMM 云中的 Hyper-V 虚拟机进行灾难恢复所需的组件。

| **先决条件** | **详细信息** |
| --- | --- |
| **Hyper-V 主机** |一台或多台本地服务器，运行具有最新更新的 Windows Server 2012 R2 并已启用 Hyper-V 角色，或者运行 Microsoft Hyper-V Server 2012 R2。<br></br>Hyper-V 服务器应包含一个或多个虚拟机。<br></br>Hyper-V 服务器应直接或通过代理连接到 Internet。<br></br>Hyper-V 服务器上应已安装 [KB2961977](https://support.microsoft.com/en-us/kb/2961977) 中提到的修补程序。
|**提供程序和代理**| 在 Azure Site Recovery 部署期间，将安装 Azure Site Recovery 提供程序。 安装提供程序还将在运行所要保护的虚拟机的每台 Hyper-V 服务器上安装 Azure 恢复服务代理。 <br></br>Site Recovery 保管库中的所有 Hyper-V 服务器应有相同版本的提供程序和代理。<br></br>提供程序将需要通过 Internet 连接到 Azure Site Recovery。 流量可以直接发送或通过代理发送。 请注意，不支持基于 HTTPS 的代理。 代理服务器应允许访问：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>如果在服务器上设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。<br></br> 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。<br></br> 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>将 VMM 云中的 Hyper-V 虚拟机灾难恢复到 Azure

除了[上面](#Azure requirements)提到的组件外，以下是对 VMM 云中的 Hyper-V 虚拟机进行灾难恢复所需的组件。

| **先决条件** | **详细信息** |
| --- | --- |
| **VMM** |在 **System Center 2012 R2 或更高版本**上运行的一个或多个 VMM 服务器。 应在每个 VMM 服务器上配置一个或多个云。 <br></br>云应当包含<br>- 一个或多个 VMM 主机组。<br/>- 每个主机组中有一个或多个 Hyper-V 主机服务器或群集。<br/><br/>[详细了解](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx)设置 VMM 云的信息。 |
| **Hyper-V** |Hyper-V 主机服务器必须至少运行具有 Hyper-V 角色的 **Windows Server 2012 R2** 或 **Microsoft Hyper-V Server 2012 R2** 并且安装了最新的更新。<br/><br/> Hyper-V 服务器应包含一个或多个 VM。<br/><br/> 必须在 VMM 云中管理 Hyper-V 主机服务器或群集，其中包含了想要复制的 VM。<br/><br/>Hyper-V 服务器应直接或通过代理连接到 Internet。<br/><br/>Hyper-V 服务器上应已安装文章 [2961977](https://support.microsoft.com/kb/2961977) 中提到的修补程序。<br/><br/>Hyper-V 主机服务器需要 Internet 访问权限，以便将数据复制到 Azure。 |
| **提供程序和代理** |在 Azure Site Recovery 的部署期间，要在 VMM 服务器上安装 Azure Site Recovery 提供程序，并在 Hyper-V 主机上安装恢复服务代理。 提供程序和代理需要通过 Internet 直接连接或通过代理连接到 Azure。 不支持基于 HTTPS 的代理。 VMM 服务器和 Hyper-V 主机上的代理服务器应允许访问： <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>如果在 VMM 服务器上设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。<br/><br/> 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。<br/><br/> 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>复制的计算机先决条件
| **组件** | **详细信息** |
| --- | --- |
| **受保护的 VM** | Site Recovery 支持 [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) 支持的所有操作系统。<br></br>VM 应符合创建 Azure VM 的 [Azure 先决条件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。 计算机名称应包含 1 到 63 个字符（字母、数字和连字符）。 名称必须以字母或数字开头，并以字母或数字结尾。 <br></br>可以在启用 VM 的复制后修改此名称。 <br/><br/> 受保护计算机上单个磁盘的容量不应超过 1023 GB。 一台 VM 最多可以有 16 个磁盘（因此最大容量为 16 TB）。<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-a-customer-owned-site"></a>将 VMM 云中的 Hyper-V 虚拟机灾难恢复到客户拥有的站点

除了[上面](#Azure requirements)提到的组件外，以下是将 VMM 云中的 Hyper-V 虚拟机灾难恢复到客户拥有的站点所需的组件。

| **组件** | **详细信息** |
| --- | --- |
| **VMM** |  建议在主站点和辅助站点中各部署一个 VMM 服务器。<br/><br/> 可以[在单个 VMM 服务器上的云之间复制](site-recovery-single-vmm.md)。 为此，至少需要在 VMM 服务器上配置两个云。<br/><br/> VMM 服务器应当至少运行具有最新更新的 **System Center 2012 SP1**。<br/><br/> 每个 VMM 服务器必须至少有一个或多个云。 必须为所有云设置 Hyper-V 容量配置文件。 <br/><br/>云必须包含一个或多个 VMM 主机组。 [详细了解](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)设置 VMM 云的信息。<br/><br/> VMM 服务器需要 Internet 访问权限。 |
| **Hyper-V** | Hyper-V 服务器必须至少运行**具有 Hyper-V 角色且安装了最新更新的 Windows Server 2012**。<br/><br/> Hyper-V 服务器应包含一个或多个 VM。<br/><br/>  Hyper-V 主机服务器应位于主要和辅助 VMM 云中的主机组内。<br/><br/> 如果在 Windows Server 2012 R2 上的群集中运行 Hyper-V，应安装[更新 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> 如果在 Windows Server 2012 上的群集中运行 Hyper-V，请注意，如果使用基于静态 IP 地址的群集，将不会自动创建群集代理。 需要手动配置群集代理。 [了解详细信息](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。 |
| **提供程序** | 在 Site Recovery 部署期间，需要在 VMM 服务器上安装 Azure Site Recovery 提供程序。 提供程序通过 HTTPS 443 与 Site Recovery 通信，以协调复制。 数据复制是通过 LAN 或 VPN 连接在主要和辅助 Hyper-V 服务器之间发生的。<br/><br/> 在 VMM 服务器上运行的提供程序需要访问以下 URL：<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>允许从 VMM 服务器到 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)的防火墙通信，并允许 HTTPS (443) 协议。 |



<!--HONumber=Jan17_HO2-->


