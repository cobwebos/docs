<properties
	pageTitle="在 Azure 门户中使用 Azure Site Recovery 将 VMware 虚拟机和物理服务器复制到 Azure | Azure" 
	description="介绍如何使用 Azure 门户通过部署 Azure Site Recovery 来协调本地 VMware 虚拟机和 Windows/Linux 物理服务器到 Azure 的复制、故障转移和恢复。" 
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.date="08/12/2016"
	wacn.date=""/>  


# 在 Azure 门户中使用 Azure Site Recovery 将 VMware 虚拟机和物理机复制到 Azure

> [AZURE.SELECTOR]
- [Azure 门户](/documentation/articles/site-recovery-vmware-to-azure/)
- [Azure 经典](/documentation/articles/site-recovery-vmware-to-azure-classic/)
- [Azure 经典（传统）](/documentation/articles/site-recovery-vmware-to-azure-classic-legacy/)

欢迎使用 Azure Site Recovery！ 如果你要在 Azure 门户中使用 Azure Site Recovery 将本地 VMware 虚拟机或 Windows/Linux 物理服务器复制到 Azure，请参考本文。

> [AZURE.NOTE] Azure 提供两个不同的[部署模型](/documentation/articles/resource-manager-deployment-model/)来创建和处理资源：Azure Resource Manager (ARM) 和经典。Azure 还有两个门户 – 支持经典部署模型的 Azure 经典门户，以及支持两种部署模型的 Azure 门户。

Azure 门户中的站点恢复提供许多新功能：

- Azure 备份和 Azure Site Recovery 服务合并成单个恢复服务保管库，以便从单个位置设置和管理业务连续性与灾难恢复 (BCDR)。在统一仪表板中，可以监视和管理对本地站点与 Azure 公有云的操作。
- 具有与云解决方案提供程序 (CSP) 一起预配的 Azure 订阅的用户现在可以在 Azure 门户中管理 Site Recovery 操作。
- Azure 门户中的 Site Recovery 可将计算机复制到 ARM 存储帐户。故障转移时，站点恢复将在 Azure 中创建基于 ARM 的 VM。
- 站点恢复继续支持复制到经典存储帐户。故障转移时，站点恢复将使用经典模型创建 VM。

阅读本文后，请在底部的 Disqus 意见区域中发表任何意见。如有技术问题，请在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/zh-cn/home?forum=hypervrecovmgr)上提出。

## 概述

组织需要制定 BCDR 策略来确定应用、工作负荷和数据如何在计划和非计划停机期间保持运行和可用，并尽快恢复正常运行情况。BCDR 策略应保持业务数据的安全性和可恢复性，并确保在发生灾难时工作负荷持续可用。

站点恢复是一项 Azure 服务，可以通过协调从本地物理服务器和虚拟机到云 (Azure) 或辅助数据中心的的复制，来为 BCDR 策略提供辅助。当主要位置发生故障时，你可以故障转移到辅助位置，使应用和工作负荷保持可用。当主要位置恢复正常时，你可以故障转移回到主要位置。在[什么是 Azure Site Recovery？](/documentation/articles/site-recovery-overview/)中了解详细信息

本文提供将本地 VMware VM 和 Windows/Linux 物理服务器复制到 Azure 所需的全部信息。其中包括体系结构概述、规划信息，以及配置 Azure、本地服务器、复制设置和容量规划的部署步骤。设置基础结构之后，可以在要保护的计算机上启用复制，并检查故障转移是否可以正常工作。

## 业务优势

- 站点恢复为在 VMware VM 和物理服务器上运行的业务工作负荷和应用程序提供站外保护。
- 恢复服务门户提供单个位置用于设置、管理和监视复制、故障转移及恢复。
- 站点恢复可以自动发现已添加到 vSphere 主机的 VMware VM。
- 你可以轻松运行从本地基础结构到 Azure 的故障转移，以及从 Azure 故障回复（还原）到本地站点中的 VMware VM 服务器。
- 可以启用多 VM 并创建复制组，以便同时复制跨多个计算机分层的应用程序工作负荷。复制组中的所有计算机在故障转移时，将获得崩溃一致且应用一致的恢复点。对于故障转移，可以在恢复计划中收集多个计算机，以便分层应用程序工作负荷一起故障转移。

## 方案体系结构

方案组件如下：

- **配置服务器**：一个本地计算机，用于协调通信，同时管理数据复制和恢复过程。在此计算机上，你将运行一个安装程序文件来安装配置服务器和以下附加组件：
	- **进程服务器**：充当复制网关。此服务器接收受保护源计算机提供的复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储空间。它还处理用于保护计算机的移动服务的推送安装，并执行 VMware VM 的自动发现。默认的进程服务器安装在配置服务器上。你可以部署更多的独立进程服务器以扩展部署。
	- **主目标服务器**：处理从 Azure 进行故障回复期间产生的复制数据。

- **移动服务**：此组件部署在要复制到 Azure 的每个计算机（VMware VM 或物理服务器）上。它可以捕获计算机上的数据写入，并将其转发到进程服务器。
- **Azure**：不需创建任何 Azure VM 来处理复制和故障转移到 Azure。需要 Azure 订阅、Azure 存储帐户来存储复制的数据，并需要 Azure 虚拟网络，使 Azure VM 能够在故障转移后连接到网络。存储帐户和网络必须位于与恢复服务保管库相同的区域中。
- **故障回复**：故障转移之后，当你准备好从 Azure 故障回复到本地站点时，需要创建一个 Azure VM 用作临时进程服务器。故障回复完成后，可以删除它。若要进行故障回复，还需要设置在本地站点与 Azure VM 所在的 Azure 网络之间设置 VPN（或 Azure ExpressRoute）连接。如果故障回复流量较大，则可能还需要在本地设置一台专用的主目标服务器计算机。如果流量较小，可以使用配置服务器上运行的默认主目标服务器。


下图显示了这些组件的交互方式。

![体系结构](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**图 1：VMware/物理机到 Azure**（由 Henry Robalino 创建）

## Azure 先决条件

下面是在 Azure 中部署此方案所需的项。

**先决条件** | **详细信息**
--- | ---
**Azure 帐户**| 需要一个 [Azure](http://azure.cn/) 帐户。你可以从[试用版](/pricing/1rmb-trial/)开始。[详细了解](/pricing/details/site-recovery/) Site Recovery 定价。 
**Azure 存储空间** | 复制的数据存储在 Azure 空间，Azure VM 在发生故障转移时创建。<br/><br/>若要存储数据，在与恢复服务保管库所在的同一区域中需要有标准或高级存储帐户。<br/><br/>可以使用 LRS 或 GRS 存储帐户。我们建议使用 GRS，以便在发生区域性服务中断或无法恢复主要区域时，能够恢复数据。[了解详细信息](/documentation/articles/storage-redundancy/)。<br/><br/> [高级存储](/documentation/articles/storage-premium-storage/)通常用于 IO 性能一贯较高且延迟一贯较低、托管 IO 密集型工作负荷的虚拟机。<br/><br/> 如果要将高级帐户用于存储复制的数据，则还需要创建一个标准存储帐户来存储复制日志，这些日志将捕获本地数据正在发生的更改。<br/><br/> 请注意，在 Azure 门户中创建的存储帐户不能跨资源组移动。<br/><br/> [详细了解](/documentation/articlesstorage/storage-introduction) Azure 存储空间。
**Azure 网络** | 你需要一个 Azure 虚拟网络，以便发生故障转移时 Azure VM 能够连接到其中。Azure 虚拟网络必须位于与恢复服务保管库相同的区域中。 
**从 Azure 故障回复** | 需将一个临时进程服务器设置为 Azure VM。可以在准备好故障回复时创建此服务器，并在故障回复完成后将其删除。<br/><br/> 若要故障回复，需要设置从 Azure 网络到本地站点的 VPN 连接（或 Azure ExpressRoute）。

## 配置服务器先决条件

将一台本地计算机设置为配置服务器。

**先决条件** | **详细信息**
--- | ---
**配置服务器**| 需要一台运行 Windows Server 2012 R2 的本地物理机或虚拟机。所有本地站点恢复组件都安装在此计算机上。<br/><br/>若要进行 VMware VM 复制，建议你将该服务器部署成高度可用的 VMware VM。如果你要复制物理机，该计算机可以是物理服务器。<br/><br/> 从 Azure 故障回复到本地站点时，将始终故障回复到 VMware VM，不管你是对 VM 还是物理服务器进行了故障转移。如果不将配置服务器部署为 VMware VM，则需要将一台独立的主目标服务器设置为 VMware VM，以接收故障回复流量。<br/><br/>如果服务器是 VMware VM，则网络适配器类型应为 VMXNET3。如果使用不同类型的网络适配器，则需要在 vSphere 5.5 服务器上安装 [VMware 更新](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)。<br/><br/>该服务器应该有一个静态 IP 地址。<br/><br/>该服务器的主机名最多为 15 个字符。<br/><br/>操作系统只能为英语版。<br/><br/> 需要在配置务器上安装 VMware vSphere PowerCLI 6.0。<br/><br/>配置服务器需要能够访问 Internet。出站访问权限需符合以下要求：<br/><br/>在安装站点恢复组件过程中在 HTTP 80 上进行临时访问（以下载 MySQL）<br/><br/>在 HTTPS 443 上进行持续的出站访问，以便进行复制管理<br/><br/>在 HTTPS 9443 上进行针对复制流量的持续出站访问（此端口可以修改）<br/><br/>服务器还需要访问以下 URL 以便能够连接到 Azure：*.hypervrecoverymanager.windowsazure.cn; *.accesscontrol.chinacloudapi.cn; *.backup.windowsazure.cn; *.blob.core.chinacloudapi.cn; *.store.core.chinacloudapi.cn<br/><br/>如果你在服务器上设置了基于 IP 地址的防火墙规则，请检查这些规则是否允许与 Azure 通信。需要允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (433) 协议。<br/><br/>允许订阅的 Azure 区域的 IP 地址范围。<br/><br/>允许使用以下 URL 进行 MySQL 下载：http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## VMware vCenter/vSphere 主机先决条件

**先决条件** | **详细信息**
--- | ---
**vSphere**| 需要一个或多个 VMware vSphere 虚拟机监控程序。<br/><br/>虚拟机监控程序应运行 vSphere 版本 6.0、5.5 或 5.1 并装有最新更新。<br/><br/>建议 vSphere 主机和 vCenter 服务器位于进程服务器所在的同一网络中（除非你设置了专用的进程服务器，否则，这是配置服务器所在的网络）。
**vCenter** | 建议你部署 VMware vCenter 服务器来管理 vSphere 主机。该服务器应运行 vCenter 6.0 或 5.5 版并装有最新更新。<br/><br/>请注意，Site Recovery 不支持新的 vCenter 和 vSphere 6.0 功能，例如跨 vCenter vMotion、虚拟卷和存储 DRS。Site Recovery 支持仅限也可在 5.5 版中使用的功能。


## 受保护的计算机先决条件

**先决条件** | **详细信息**
--- | ---
**本地 VMware VM** | 要保护的 VMware VM 上应已安装并正在运行 VMware 工具。<br/><br/> 要保护的计算机应符合创建 Azure VM 的 [Azure 先决条件](/documentation/articles/site-recovery-best-practices/#azure-virtual-machine-requirements)。<br/><br/>受保护计算机上的单个磁盘的容量不应超过 1023 GB。一台 VM 最多可以有 64 个磁盘（因此最大容量为 64 TB）。<br/><br/>不支持共享的磁盘来宾群集。<br/><br/>不支持统一可扩展固件接口 (UEFI)/可扩展固件接口 (EFI) 启动。<br/><br/>计算机名应包含 1 到 63 个字符（字母、数字和连字符）。名称必须以字母或数字开头，并以字母或数字结尾。为计算机启用复制后，可以修改 Azure 名称。<br/><br/>如果源 VM 有 NIC 组合，它在故障转移到 Azure 之后将转换成单个 NIC。<br/><br/>如果受保护 VM 有一个 iSCSI 磁盘，则在 VM 故障转移到 Azure 时，Site Recovery 会将受保护的 VM iSCSI 磁盘转换成 VHD 文件。如果 iSCSI 目标可供 Azure VM 访问，后者会连接到该目标且实际上会看到两个磁盘 – Azure VM 上的 VHD 磁盘，以及源 iSCSI 磁盘。在这种情况下，需要断开连接 Azure VM 上显示的 iSCSI 目标。
**Windows 计算机（物理或 VMware））** | 计算机应运行支持的 64 位操作系统：Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 SP1 及更高版本。<br/><br/> 操作系统应安装在 C:\\ 驱动器上。OS 磁盘应是 Windows 基本磁盘而不是动态磁盘。数据磁盘可以是动态磁盘。<br/><br/>Site Recovery 支持包含 RDM 磁盘的 VM。在故障回复期间，如果原始的源 VM 和 RDM 磁盘可用，站点恢复会重复使用 RDM 磁盘。如果这些磁盘不可用，则站点恢复会在故障回复期间为每个磁盘创建一个新的 VMDK 文件。
**Linux 计算机** | 需要安装支持的 64 位操作系统：Red Hat Enterprise Linux 6.7；Centos 6.5、6.6、6.7；运行 Red Hat 兼容内核或 Unbreakable Enterprise Kern Release 3 (UEK3) 的 Oracle Enterprise Linux 6.4、6.5；SUSE Linux Enterprise Server 11 SP3。<br/><br/>受保护计算机上的 /etc/hosts 文件应该包含将本地主机名映射到所有网络适配器关联的 IP 地址的条目。<br/><br/>如果要在故障转移后使用 Secure Shell 客户端 (ssh) 连接到运行 Linux 的 Azure 虚拟机，请确保将受保护的计算机上的 Secure Shell 服务设置为在系统启动时自动启动，并且防火墙规则允许建立到它的 ssh 连接。<br/><br/>主机名、装载点、设备名称以及 Linux 系统路径和文件名（例如 /etc/；/usr）只能采用英文形式。<br/><br/>只能对使用以下存储的 Linux 计算机启用保护：文件系统（EXT3、ETX4、ReiserFS、XFS）；多路径软件-设备映射器（多路径）；卷管理器：(LVM2)。不支持使用 HP CCISS 控制器存储的物理服务器。ReiserFS 文件系统仅在 SUSE Linux Enterprise Server 11 SP3 上受支持。<br/><br/>Site Recovery 支持使用 RDM 磁盘的 VM。在针对 Linux 进行故障回复期间，Site Recovery 不重复使用 RDM 磁盘。相反，它会为每个相应的 RDM 磁盘创建新的 VMDK 文件。<br/><br/>确保在 VMware 上的 VM 配置参数中设置 disk.enableUUID=true。如果该条目不存在，请创建它。需要为 VMDK 提供一致的 UUID，以便能够正确装载。添加此设置还可确保在故障回复只将增量更改而不是整个复制内容传输回到本地。
**移动服务** | **Windows**：若要自动将移动服务推送到运行 Windows 的 VM，需要提供一个管理员帐户（Windows 计算机上的本地管理员），以便进程服务器能够执行推送安装。<br/><br/> **Linux**：若要自动将移动服务推送到运行 Linux 的 VM，需要创建一个可由进程服务器用来执行推送安装的帐户。<br/><br/> 默认情况下，将复制计算机上的所有磁盘。若要[从复制中排除某个磁盘](#exclude-disks-from-replication)，在启用复制之前，必须手动在计算机上安装移动服务。

## 准备部署

若要准备部署，需要：

1. [设置 Azure 网络](#set-up-an-azure-network)，Azure VM 在故障转移后，将在该网络中启动。此外，若要进行故障回复，需要设置从 Azure 网络到本地站点的 VPN 连接（或 Azure ExpressRoute）。
2. 针对复制的数据[设置 Azure 存储帐户](#set-up-an-azure-storage-account)。
3. 在 vCenter 服务器或 vSphere 主机上[准备一个帐户](#prepare-an-account-for-automatic-discovery)，以便 Site Recovery 可以自动检测到添加的 VMware VM。
4. [准备配置服务器](#prepare-the-configuration-server)，确保它可以访问所需的 URL 并安装 vSphere PowerCLI 6.0。


###<a id="set-up-an-azure-network"></a> 设置 Azure 网络

- 该网络应与要部署恢复服务保管库的网络位于同一 Azure 区域。
- 根据要用于故障转移 Azure VM 的资源模型，你将在 [ARM 模式](/documentation/articles/virtual-networks-create-vnet-arm-pportal/)或[经典模式](/documentation/articles/virtual-networks-create-vnet-classic-pportal/)下设置 Azure 网络。
- 若要从 Azure 故障回复到本地 VMware 站点，需要设置从复制的 Azure VM 所在的 Azure 网络，到配置服务器所在的本地网络的 VPN 连接（或 Azure ExpressRoute 连接）。
- [了解](/documentation/articles/vpn-gateway-site-to-site-create/) VPN 站点到站点连接支持的部署模型，以及如何[设置连接](/documentation/articles/vpn-gateway-site-to-site-create/#create-your-virtual-network)。
- 或者，你可以设置 [Azure ExpressRoute](/documentation/articles/expressroute-introduction/)。[了解](/documentation/articles/expressroute-howto-vnet-portal-classic/)如何设置包含 ExpressRoute 的 Azure 网络。

###<a id="set-up-an-azure-storage-account"></a> 设置 Azure 存储帐户

- 需要使用标准或高级 Azure 存储帐户来保存复制到 Azure 的数据。该帐户必须位于与恢复服务保管库相同的区域中。根据用于故障转移 Azure VM 的资源模型，你将在 [ARM 模式](/documentation/articles/storage-create-storage-account/)或[经典模式](/documentation/articles/storage-create-storage-account-classic-portal/)下设置帐户。
- 如果将高级帐户用于复制的数据，则需要创建其他标准帐户来存储复制日志，这些日志将捕获本地数据正在发生的更改。

###<a id="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户

站点恢复进程服务器可以自动发现 vSphere 主机或 vCenter 服务器上用于管理主机的 VMware VM。若要执行自动发现，站点恢复需要能够访问 VMware 服务器的凭据。如果你只复制物理机，则与此无关。

1. 若要使用专用帐户进行自动发现，请在 vCenter 级别创建一个具有[所需权限](#vmware-account-permissions)的角色（例如 Azure\_Site\_Recovery）。
2. 在 vSphere 主机或 vCenter 服务器上创建一个新用户，并向其分配该角色。稍后，你将要让站点恢复知道这些凭据，以便它能够执行自动发现。

	>[AZURE.NOTE] 具有只读角色的 vCenter 用户帐户可以运行故障转移，但无法关闭受保护的源计算机。如果你想要关闭这些计算机，则需 [Azure\_Site\_Recovery](#vmware-account-permissions) 角色。如果你只是将 VM 从 VMware 迁移到 Azure 且不需要进行故障回复，则只读角色是足够的。

###<a id="prepare-the-configuration-server"></a>准备配置服务器

1.	确保用于配置服务器的计算机符合[先决条件](#configuration-server-prerequisites)。具体而言，请确保该计算机已使用以下设置连接到 Internet：

	- 允许访问以下 URL：*.hypervrecoverymanager.windowsazure.cn; *.accesscontrol.chinacloudapi.cn; *.backup.windowsazure.cn; *.blob.core.chinacloudapi.cn; *.store.core.chinacloudapi.cn
	- 允许访问 [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) 以下载 MySQL。
	- 允许使用 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (433) 协议通过防火墙来与 Azure 通信。
	
2.	在配置服务器上下载并安装 [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)。


## 创建恢复服务保管库

1. 登录到 [Azure 门户](https://portal.azure.cn)。
2. 单击“新建”>“混合集成”>“恢复服务”。或者，可以单击“浏览”>“恢复服务保管库”>“添加”。

	![新保管库](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. 在“名称”中，指定一个友好名称以标识该保管库。如果你有多个订阅，请选择其中一个。
4. [创建一个新资源组](/documentation/articles/resource-group-portal/#create-resource-group)或选择一个现有的资源组。指定 Azure 区域。计算机将复制到此区域。请注意，用于站点恢复的 Azure 存储空间和网络需在同一个区域。若要查看受支持的区域，请参阅 Azure Site Recovery 价格详细信息中的“地域可用性”。[](/pricing/details/site-recovery/)
4. 若要从仪表板快速访问保管库，请单击“固定到仪表板”，然后单击“创建”。

	![新保管库](./media/site-recovery-vmware-to-azure/new-vault-settings.png)  


新保管库将显示在“仪表板”>“所有资源”中，以及“恢复服务保管库”主边栏选项卡上。

## 入门

站点恢复提供“快速启动”体验，旨在帮助你尽快启动和运行项目。它将检查先决条件，并引导你完成部署站点恢复所要执行的步骤。

需要选择要复制的计算机类型，以及要复制到的位置。设置基础结构，包括本地服务器、Azure 设置、复制策略和容量规划。基础结构准备就绪后，可以为 VM 和物理服务器启用复制。然后，可对特定计算机运行故障转移，或创建恢复计划以故障转移多个计算机。

选择部署站点恢复的方式，以开始体验“快速启动”。根据复制要求，“快速启动”流程会稍有不同。


## 步骤 1：选择保护目标

选择要复制的内容以及要复制到的位置。

1. 在“恢复服务保管库”边栏选项卡中选择保管库，然后单击“设置”。
2. 在“设置”>“快速启动”中，单击“Site Recovery”>“步骤 1: 准备基础结构”>“保护目标”。

	![选择目标](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. 在“保护目标”中选择“到 Azure”，然后选择“是，使用 VMware vSphere 虚拟机监控程序”。然后，单击“确定”。

	![选择目标](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## 步骤 2：设置源环境

设置配置服务器并将它注册到恢复服务保管库中。如果你要复制 VMware VM，请指定用于自动发现的 VMware 帐户。

1. 单击“步骤 1: 准备基础结构”>“源”。如果你没有配置服务器，请在“准备源”中单击“+配置服务器”以添加一个。

	![设置源](./media/site-recovery-vmware-to-azure/set-source1.png)  


2. 在“添加服务器”边栏选项卡中，检查“配置服务器”是否已出现在“服务器类型”中。
3. 设置配置服务器之前，请检查[先决条件](#configuration-server-prerequisites)。具体而言，请检查计算机是否可以访问所需的 URL。
4.	下载站点恢复统一安装程序安装文件。
5.	下载保管库注册密钥。运行统一安装程序时需要用到此密钥。生成的密钥有效期为 5 天。

	![设置源](./media/site-recovery-vmware-to-azure/set-source2.png)

6.	在用作配置服务器的计算机上，运行统一安装程序以安装配置服务器、进程服务器和主目标服务器。


### 运行站点恢复统一安装程序

1.	运行统一安装程序安装文件。
2.	在“开始之前”中选择“安装配置服务器和进程服务器”。

	![开始之前](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. 在“第三方软件许可证”中，单击“我接受”以下载并安装 MySQL。

	![第三方软件](./media/site-recovery-vmware-to-azure/combined-wiz2.png)

4. 在“Internet 设置”中，指定配置服务器上运行的提供程序如何通过 Internet 连接到 Azure Site Recovery。

	- 如果你希望使用当前已在计算机上设置的代理进行连接，请选择“使用现有代理设置进行连接”。
	- 如果你希望提供程序直接进行连接，请选择“不使用代理直接连接”。
	- 如果现有代理要求身份验证，或者你希望使用自定义代理进行提供程序连接，请选择“使用自定义代理设置进行连接”。
		- 如果你使用自定义代理，则需指定地址、端口和凭据
		- 如果你使用代理，应事先允许[先决条件](#configuration-server-prerequisites)中所述的 URL。

	![防火墙](./media/site-recovery-vmware-to-azure/combined-wiz3.png)  


5. 在“先决条件检查”设置中，运行检查以确保安装可以运行。如果看到有关**全局时间同步检查**的警告，请检查系统时钟的时间（“日期和时间”设置）是否与时区相同。

	![先决条件](./media/site-recovery-vmware-to-azure/combined-wiz4.png)  


6. 在“MySQL 配置”中，创建用于登录到要安装的 MySQL 服务器实例的凭据。

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. 在“环境详细信息”中，选择是否要复制 VMware VM。如果随后进行安装，请检查 PowerCLI 6.0 是否已安装。

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. 在“安装位置”中，选择要安装二进制文件和存储缓存的位置。可以选择至少有 5 GB 可用存储空间的驱动器，但我们建议选择至少有 600 GB 可用空间的缓存驱动器。

	![安装位置](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. 在“网络选择”中，指定侦听器（网络适配器和 SSL 端口），以便配置服务器在其上发送和接收复制数据。你可以修改默认端口 (9443)。除了此端口，还会打开端口 443，以便发送和接收有关复制业务流程的信息。不应使用 443 来接收复制流量。


	![网络选择](./media/site-recovery-vmware-to-azure/combined-wiz8.png)  


10. 在“注册”中，通过浏览查找并选择从保管库下载的注册密钥。

	![注册](./media/site-recovery-vmware-to-azure/combined-wiz9.png)  


11.  在“摘要”中复查信息，然后单击“安装”。安装完成后，将生成通行短语。启用复制时需要用到它，因此请复制并将它保存在安全的位置。

	![摘要](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  注册完成后，服务器将显示在保管库的“设置”>“服务器”边栏选项卡中。

	

#### 从命令行运行安装程序

可以从命令行设置配置服务器：

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

安装完成后，请完成注册：

1. 从 Windows“开始”菜单启动名为“Microsoft Azure 恢复服务 Shell”的应用程序。
2. 在打开的命令窗口中，运行以下命令集以配置代理服务器设置。

		PS C:\Windows\System32> $pwd = ConvertTo-SecureString -String ProxyUserPassword
		PS C:\Windows\System32> Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
		PS C:\Windows\System32> net stop obengine.exe

参数：

- /ServerMode：必需。指定是要同时安装配置服务器和进程服务器，还是只安装进程服务器。输入值：CS、PS。
- InstallLocation：必需。用于安装组件的文件夹。
- /MySQLCredsFilePath。必需。MySQL 服务器凭据存储到的文件路径。该文件的格式应是：
	- [MySQLCredentials]
	- MySQLRootPassword = "<密码>"
	- MySQLUserPassword = "<密码>"
- /VaultCredsFilePath。必需。保管库凭据文件的位置
- /EnvType。必需。安装类型。值：VMware、NonVMware
- /PSIP 和 /CSIP。必需。进程服务器和配置服务器的 IP 地址。
- /PassphraseFilePath。必需。通行短语文件的位置。
- /BypassProxy。可选。指定配置服务器不使用代理连接到 Azure。
- /ProxySettingsFilePath。可选。代理设置（默认代理需要身份验证，或自定义代理）。该文件的格式应是：
	- [ProxySettings]
	- ProxyAuthentication = "Yes/No"
	- Proxy IP = "IP Address>"
	- ProxyPort = "<端口>"
	- ProxyUserName="<用户名>"
	- ProxyPassword="<密码>"
- DataTransferSecurePort。可选。用于复制数据的端口号。
- SkipSpaceCheck。可选。跳过缓存的空间检查。
- AcceptThirdpartyEULA。必需。该标志表示接受第三方 EULA。
- ShowThirdpartyEULA。必需。显示第三方 EULA。如果作为输入提供，将忽略所有其他参数。

### 添加用于自动发现的 VMware 帐户

 当你为部署做好准备时，应已[创建一个 VMware 帐户](#prepare-an-account-for-automatic-discovery)，Site Recovery 可以使用它来执行自动发现。按如下所述添加此帐户：

1. 打开 **CSPSConfigtool.exe**。该帐户作为快捷方式位于桌面上，并位于 [安装位置]\\home\\svsystems\\bin 文件夹中。
2. 单击“管理帐户”>“添加帐户”。

	![添加帐户](./media/site-recovery-vmware-to-azure/credentials1.png)

3. 在“帐户详细信息”中，添加用于自动发现的帐户。请注意，帐户名称出现在门户中可能需要 15 分钟或更长时间。若要立即更新，请单击“配置服务器”> 服务器名称 >“刷新服务器”。

	![详细信息](./media/site-recovery-vmware-to-azure/credentials2.png)

### 连接到 vSphere 主机和 vCenter 服务器

如果要复制 VMware VM，请连接到 vSphere 主机和 vCenter 服务器。

1. 验证配置服务器是否能够通过网络访问 vSphere 主机和 vCenter 服务器。
2. 单击“准备基础结构”>“源”。在“准备源”中选择配置服务器，然后单击“+vCenter”以添加 vSphere 主机或 vCenter 服务器。
3. 在“添加 vCenter”中，指定 vSphere 主机或 vCenter 服务器的友好名称，并指定服务器的 IP 地址或 FQDN。除非已将 VMware 服务器配置为在不同的端口上侦听请求，否则请保留 443 作为端口号。然后，选择用于连接到 VMware 服务器的帐户。单击**“确定”**。

	![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

	>[AZURE.NOTE] 如果你在添加 vCenter 服务器或 vSphere 主机时使用的帐户没有对 vCenter 或主机服务器的管理员权限，则请确保为帐户启用了以下权限：数据中心、数据存储、文件夹、主机、网络、资源、虚拟机、vSphere 分布式交换机。此外，vCenter 服务器需要存储视图权限。

站点恢复将使用你指定的设置连接到 VMware 服务器并发现 VM。

## 步骤 3：设置目标环境

确认是否设置了用于复制的存储帐户，以及 Azure VM 在故障转移后要连接到的 Azure 网络。

1.	单击“准备基础结构”>“目标”，然后选择要使用的 Azure 订阅。
2.	指定要在故障转移后用于 VM 的部署模型。
3.	Site Recovery 将检查是否有一个或多个兼容的 Azure 存储帐户和网络。

	![目标](./media/site-recovery-vmware-to-azure/gs-target.png)  


4.	如果尚未创建存储帐户并想要使用 ARM 来创建一个帐户，请单击“+存储帐户”以内联方式执行该操作。在“创建存储帐户”边栏选项卡中，指定帐户名、类型、订阅和位置。该帐户应位于与恢复服务保管库相同的区域。

	![存储](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

	请注意：

	- 如果想要使用经典模型创建存储帐户，请在 Azure 门户中执行该操作。[了解详细信息](/documentation/articles/storage-create-storage-account-classic-portal/)
	- 如果将高级存储帐户用于复制的数据，则需要设置其他标准存储帐户来存储复制日志，这些日志将捕获本地数据正在发生的更改。

4.	选择 Azure 网络。如果尚未创建网络并想要使用 ARM 创建网络，请单击“+网络”以内联方式执行该操作。在“创建虚拟网络”边栏选项卡上，指定网络名称、地址范围、子网详细信息、订阅和位置。该网络应位于与恢复服务保管库相同的位置。
	
	![网络](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)  


	如果想要使用经典模型创建网络，请在 Azure 门户中执行该操作。[了解详细信息](/documentation/articles/virtual-networks-create-vnet-classic-pportal/)。

## 步骤 4：设置复制设置

1. 若要创建新的复制策略，请单击“准备基础结构”>“复制设置”>“+创建和关联”。
2. 在“创建和关联策略”中指定策略名称。
3. 在“RPO 阈值”中：指定 RPO 限制。当连续复制超出此限制时，将生成警报。
5. 在“恢复点保留期”中，针对每个恢复点指定保留期的时长（以小时为单位）。受保护的计算机可以恢复到某个时段内的任意时间点。复制到高级存储的计算机最长支持 24 小时的保留期。
6. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（以分钟为单位）。
7. 创建复制策略时，默认情况下将自动为故障回复创建一个匹配策略。例如，如果复制策略是 **rep-policy**，则故障回复策略将是 **rep-policy-failback**。在启动故障回复之前，请不会使用此策略。
8. 单击“确定”以创建该策略。

	![复制策略](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. 当你创建新策略时，该策略将自动与配置服务器关联。单击**“确定”**。

	![复制策略](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## 步骤 5：容量规划

你已经设置了基本基础结构，现在可以考虑容量计划并确定是否需要额外的资源。

站点恢复提供 Capacity Planner 帮助你为源环境、站点恢复组件、网络和存储分配适当的资源。可以在快速模式下运行 Planner，以便根据 VM、磁盘和存储的平均数量进行估计；或者在详细模式下运行 Planner，以输入工作负荷级别的数据。开始之前，需要：

- 收集有关复制环境的信息，包括 VM 数、每个 VM 的磁盘数和每个磁盘的存储空间。
- 估计复制数据的每日更改（变动）率。可以使用 [vSphere 容量规划工具](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)来帮助执行此操作。

1.	单击“下载”以下载该工具，然后开始运行。[阅读](/documentation/aticles/site-recovery-capacity-planner)该工具随附的文章。
2.	完成后，请在“是否已完成容量规划?”中选择“是”。

	![容量规划](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

下表中列出的一些要点可帮助你针对此方案进行容量规划。


**组件** | **详细信息**
--- | --- | --- 
**复制** | **每日最大更改率** - 一台受保护的计算机只能使用一个进程服务器，一个进程服务器可处理的每日更改率最高可达 2 TB。因此，2 TB 是受保护计算机支持的每日数据更改率上限。<br/><br/> **最大吞吐量** - 在 Azure 中，一个复制的计算机可能属于一个存储帐户。标准存储帐户每秒最多可以处理 20,000 个请求，因此建议你将源计算机中的 IOPS 数目保持在 20,000。例如，如果你的源计算机有 5 个磁盘，每个磁盘在源上生成 120 IOPS（8K 大小），则处于 Azure 的单磁盘 IOPS 限制 (500) 内。所需的存储帐户数 = 源 IOPS 总计/20000。
**配置服务器** | 配置服务器应该能够处理跨所有工作负荷（运行在受保护的计算机上）的每日更改率容量，并有足够的带宽，可以持续地将数据复制到 Azure 存储空间。<br/><br/> 根据最佳实践，我们建议你将配置服务器置于与所要保护的计算机相同的网络和 LAN 网段上。可以将它置于另一网络中，但所要保护的计算机应可通过 L3 网络来查看它。<br/><br/> 下表汇总了配置服务器的建议大小。
**进程服务器** | 默认情况下，第一个进程服务器安装在配置服务器上。你可以部署更多的进程服务器以扩展环境。请注意：<br/><br/>进程服务器接收受保护计算机提供的复制数据，并在发送到 Azure 之前对其通过缓存、压缩和加密进行优化。进程服务器计算机应有足够的资源来执行这些任务。<br/><br/> 处理服务器使用基于磁盘的缓存。建议在出现网络瓶颈或中断时，使用单独的大小至少为 600 GB 的缓存磁盘来处理存储的数据更改。 

### 配置服务器的建议大小

**CPU** | **内存** | **缓存磁盘大小** | **数据更改率** | **受保护的计算机**
--- | --- | --- | --- | ---
8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5GHz） | 16 GB | 300 GB | 500 GB 或更少 | 复制少于 100 台计算机。
12 个 vCPU（2 个插槽 * 6 个核心 @ 2.5GHz） | 18 GB | 600 GB | 500 GB 到 1 TB | 复制 100-150 台计算机。
16 个 vCPU（2 个插槽 * 8 个核心 @ 2.5GHz） | 32 GB | 1 TB | 1 TB 到 2 TB | 复制 150-200 台计算机。
部署另一个进程服务器 | | | > 2 TB | 如果你要复制 200 多台计算机，或者每日数据更改率超过 2 TB，则需部署额外的进程服务器。

其中：

- 每台源计算机均配置 3 个磁盘，每个磁盘 100 GB。
- 我们使用了包含 8 个 SAS 驱动器 (10 K RPM) 的基准测试存储空间，使用 RAID 10 进行缓存磁盘度量。

### 进程服务器的建议大小

如果需要保护的计算机超过 200 台，或者每日更改率大于 2 TB，则可添加更多进程服务器来处理复制负载。若要横向扩展，你可以执行以下操作：

- 增加配置服务器的数目。例如，你可以通过两台配置服务器来保护最多 400 台计算机。
- 添加更多进程服务器，使用这些服务器来处理流量，而不是添加配置服务器。

下表介绍了一种方案，在该方案中：

- 你不打算使用配置服务器作为进程服务器。
- 你已设置额外的进程服务器。
- 你已将受保护的虚拟机配置为使用这个额外的进程服务器。
- 每台受保护的源计算机配置为了三个磁盘，每个磁盘 100 GB。

**配置服务器** | **额外的进程服务器**| **缓存磁盘大小** | **数据更改率** | **受保护的计算机**
--- | --- | --- | --- | --- 
8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5GHz），16 GB 内存 | 4 个 vCPU（2 个插槽 * 2 个核心 @ 2.5GHz），8 GB 内存 | 300 GB | 250 GB 或更少 | 复制 85 台或更少的计算机。
8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5GHz），16 GB 内存 | 8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5GHz），12 GB 内存 | 600 GB | 250 GB 到 1 TB | 复制 85-150 台计算机。
12 个 vCPU（2 个插槽 * 6 个核心 @ 2.5GHz），18 GB 内存 | 12 个 vCPU（2 个插槽 * 6 个核心 @ 2.5GHz），24 GB 内存 | 1 TB | 1 TB 到 2 TB | 复制 150-225 台计算机。


使用哪种方式来扩展服务器将取决于你是喜欢向上扩展模型还是横向扩展模型。向上扩展时，需要部署一些高端配置服务器和进程服务器，而向外扩展时，需要部署较多服务器，需要的资源较少。例如，如果你需要对 220 台计算机进行保护，则可执行以下某项操作：

- 设置具有 12vCPU、18 GB 内存的配置服务器，以及具有 12vCPU、24 GB 内存的进程服务器，并将受保护计算机配置为仅使用额外的进程服务器。
- 也可以配置两个配置服务器（2 x 8vCPU，16 GB RAM）和两个额外的进程服务器（1 x 8vCPU 和 4vCPU x 1，可处理 135 + 85 (220) 台计算机），并将受保护计算机配置为仅使用额外的进程服务器。
  
[按照这些说明](#deploy-additional-process-servers)设置额外的进程服务器。

### 网络带宽注意事项

可以使用 Capacity Planner 工具来计算复制（初始复制，然后是增量复制）所需的带宽。若要控制复制所用的带宽量，可以使用几个选项：

- **限制带宽**：复制到 Azure 的 VMware 流量会经过特定的进程服务器。可以在运行进程服务器的计算机上限制带宽。
- **影响带宽**：可以使用几个注册表项来控制用于复制的带宽：
	- **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft Azure Backup\\UploadThreadsPerVM** 注册表值指定用于磁盘数据传输（初始或增量复制）的线程数。使用较大的值会增加复制所用的网络带宽。
	- **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft Azure Backup\\DownloadThreadsPerVM** 指定故障回复期间用于数据传输的线程数。

#### 限制带宽

1. 在充当进程服务器的计算机上打开 Microsoft Azure 备份 MMC 管理单元。默认情况下，Microsoft Azure 备份的快捷方式位于桌面上或 C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin 中。
2. 在管理单元中，单击“更改属性”。

	![限制带宽](./media/site-recovery-vmware-to-azure/throttle1.png)

3. 在“限制”选项卡上，选择“为备份操作启用 Internet 带宽使用限制”，然后设置工作时间和非工作时间的限制。有效范围为 512 Kbps 到 102 Mbps。

	![限制带宽](./media/site-recovery-vmware-to-azure/throttle2.png)

也可以使用 [Set-OBMachineSetting](https://technet.microsoft.com/zh-cn/library/hh770409.aspx) cmdlet 来设置限制。下面是一个示例：

    $mon = [System.DayOfWeek]::Monday 
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 表示不需要限制。


#### 影响网络带宽

1. 在注册表中导航到 **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsofte Azure Backup\\Replication**。
	- 若要影响复制磁盘上的带宽流量，请修改 **UploadThreadsPerVM** 值，或者创建该项（如果不存在）。
	- 若要影响用于从 Azure 故障回复流量的带宽，请修改 **DownloadThreadsPerVM** 值。
2. 默认值为 4。在“过度预配型”网络中，这些注册表项需要更改，不能使用默认值。最大值为 32。监视流量以优化值。

## 步骤 6：复制应用程序

请确保要复制的计算机已准备好安装移动服务，然后启用复制。

### 安装移动服务

为虚拟机和物理服务器启用保护时，第一步是安装移动服务。可以通过多种方式实现此目的：

- **进程服务器推送**：在计算机上启用复制时，将从进程服务器推送并安装移动服务组件。请注意，如果计算机已在运行最新版本的组件，则不会执行推送安装。
- **企业推送**：使用企业推送过程（例如 WSUS 或 System Center Configuration Manager）自动安装该组件。执行此操作之前，请设置配置服务器。
- **手动安装**：在要复制的每台计算机上手动安装该组件。执行此操作之前，请设置配置服务器。


#### 准备在 Windows 计算机上自动推送 

下面介绍了如何准备 Windows 计算机，以便进程服务器能够自动安装移动服务。

1.  创建可供进程服务器用来访问计算机的帐户。该帐户应具有管理员权限（本地或域），并仅用于推送安装。

	>[AZURE.NOTE] 如果你使用的不是域帐户，则需在本地计算机上禁用远程用户访问控制。为此，请在注册表中的 HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System 下添加值为 1 的 DWORD 项 LocalAccountTokenFilterPolicy。若要从 CLI 添加该注册表项，请键入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。

2.  在要保护的计算机的 Windows 防火墙中，选择“允许应用或功能通过防火墙”。启用“文件和打印机共享”和“Windows Management Instrumentation”。对于属于某个域的计算机，你可以使用 GPO 配置防火墙设置。

	![防火墙设置](./media/site-recovery-vmware-to-azure/mobility1.png)

2. 添加已创建的帐户：

	- 打开 **cspsconfigtool**。该帐户作为快捷方式位于桌面上，并位于 [安装位置]\\home\\svsystems\\bin 文件夹中。
	- 在“管理帐户”选项卡中，单击“添加帐户”。
	- 添加已创建的帐户。添加帐户后，在为计算机启用复制时，需要提供这些凭据。


#### 准备在 Linux 服务器上自动推送

1.	确保要保护的 Linux 计算机受支持，如[受保护的计算机先决条件](#protected-machine-prerequisites)中所述。确保 Linux 计算机与进程服务器之间已建立网络连接。

2.	创建可供进程服务器用来访问计算机的帐户。该帐户应是源 Linux 服务器上的 root 用户，并仅用于推送安装。

	- 打开 **cspsconfigtool**。该帐户作为快捷方式位于桌面上，并位于 [安装位置]\\home\\svsystems\\bin 文件夹中。
	- 在“管理帐户”选项卡中，单击“添加帐户”。
	- 添加已创建的帐户。添加帐户后，在为计算机启用复制时，需要提供这些凭据。

3.	确保源 Linux 服务器上的 /etc/hosts 文件包含用于将本地主机名映射到所有网络适配器关联的 IP 地址的条目。
4.	在要复制的计算机上安装最新的 openssh、openssh-server 和 openssl 包。
5.	确保 SSH 已启用且正在端口 22 上运行。
6.	在 sshd\_config 文件中启用 SFTP 子系统与密码身份验证，如下所示：

	- 以 root 身份登录。
	- 在文件 /etc/ssh/sshd\_config 中，找到以 **PasswordAuthentication** 开头的行。
	- 取消注释该行，并将值从“no”更改为“yes”。
	- 找到以“Subsystem”开头的行，并取消注释该行。
 
		![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


#### 手动安装移动服务

进程服务器上的 **C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository** 中提供了安装程序。

源操作系统 | 移动服务安装文件
--- | ---
Windows Server（仅限 64 位） | Microsoft-ASR\_UA\_9.*.0.0_Windows_* release.exe
CentOS 6.4、6.5、6.6（仅限 64 位） | Microsoft-ASR\_UA\_9.*.0.0\_RHEL6-64\_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3（仅限 64 位） | Microsoft-ASR\_UA\_9.*.0.0\_SLES11-SP3-64\_*release.tar.gz
Oracle Enterprise Linux 6.4、6.5（仅限 64 位） | Microsoft-ASR\_UA\_9.*.0.0\_OL6-64\_*release.tar.gz


#### 在 Windows 服务器上手动安装


1. 下载并运行相关安装程序。
2. 在“开始之前”中选择“移动服务”。

	![移动服务](./media/site-recovery-vmware-to-azure/mobility3.png)

3. 在“配置服务器详细信息”中，指定配置服务器的 IP 地址，以及运行统一安装程序时生成的通行短语。你可以通过在配置服务器上运行以下命令来检索通行短语：**<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n**。

	![移动服务](./media/site-recovery-vmware-to-azure/mobility6.png)  


4. 保留“安装位置”中的默认设置，然后单击“下一步”开始安装。
5. 在“安装进度”中监视安装过程，并在系统提示的情况下重新启动计算机。安装该服务后，可能需要大约 15 分钟，状态才会在门户中更新。

你还可以从命令行进行安装：

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <安装目录>] [/CSIP <用于注册的 CS 的 IP 地址>] [/PassphraseFilePath <通行短语文件路径>] [/LogFilePath <日志文件路径>]

其中：

- /Role：必需。指定是否应安装移动服务。
- /InstallLocation：必需。指定服务安装位置。
- /PassphraseFilePath：必需。配置服务器通行短语。
- /LogFilePath：必需。日志安装程序文件位置。


#### 在 Linux 服务器上手动安装：

1. 根据上表，将相应的 tar 存档复制到要复制的 Linux 计算机。
2. 打开 shell 程序，并通过运行 `tar -xvzf Microsoft-ASR_UA_8.5.0.0*` 将压缩的 tar 存档解压缩到本地路径
3. 在 tar 存档内容解压缩到的本地目录中创建 passphrase.txt 文件。为此，请在配置服务器上从 C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase 复制通行短语，然后通过在 shell 中运行 *`echo <passphrase> >passphrase.txt`*，将其保存在 passphrase.txt 中。
4. 运行 *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`* 以安装移动服务。
5. 指定配置服务器的内部 IP 地址，确保选择端口 443。安装该服务后，可能需要大约 15 分钟，状态才会在门户中更新。

**你还可以从命令行进行安装**：

1. 在配置服务器上从 C:\\Program Files (x86)\\InMage Systems\\private\\connection 复制通行短语，再在配置服务器上将其另存为“passphrase.txt”。然后运行这些命令。在我们的示例中，配置服务器 IP 地址为 104.40.75.37，HTTPS 端口应该为 443：

在生产服务器上安装：

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt
 
在主目标服务器上安装：


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### 启用复制

#### 开始之前

如果你要复制 VMware 虚拟机，请注意以下事项：

- 系统会每隔 15 分钟发现 VMware VM 一次，在发现之后，可能需要 15 分钟或更长时间，虚拟机才会出现在门户中。同样，当你添加新的 vCenter 服务器或 vSphere 主机时，发现可能要花费 15 分钟或更长时间。
- 虚拟机上的环境更改（例如 VMware 工具安装）也可能需要 15 分钟或更长时间才能在门户中更新。
- 你可以在“配置服务器”边栏选项卡上 vCenter 服务器/vSphere 主机的“上次联系时间”字段中检查上次发现 VMware VM 的时间。
- 若要添加要复制的计算机而不想要等待完成计划的发现，请突出显示配置服务器（不要单击它），然后单击“刷新”按钮。
- 当你启用复制时，如果计算机已做好准备，则进程服务器将自动在其上安装移动服务。

#### 从复制中排除磁盘

当你启用复制时，默认情况下会复制计算机上的所有磁盘。你可以从复制中排除磁盘。例如，你可能不想要复制包含临时数据，或者每当重新启动计算机或应用程序时刷新的数据（例如 pagefile.sys 或 SQL Server tempdb）的磁盘。如果想要排除磁盘，请注意：

- 只能排除已安装移动服务的磁盘。需要[手动安装移动服务](#install-the-mobility-service-manually)，因为启用复制后只能使用推送机制安装移动服务。
- 只能从复制中排除基本磁盘。不能排除 OS 磁盘或动态磁盘。
- 启用复制后，无法添加或删除要复制的磁盘。如果想要添加或排除磁盘，需要禁用计算机保护，然后重新启用保护。
- 如果某个应用程序需要有排除的磁盘才能正常运行，则故障转移到 Azure 之后，需要在 Azure 中手动创建该磁盘，以便复制的应用程序可以运行。或者，可以将 Azure 自动化集成到恢复计划中，以在故障转移计算机期间创建磁盘。
- 在 Azure 中手动创建的磁盘将故障回复。例如，如果你故障转移三个磁盘，并直接在 Azure 中创建两个磁盘，则会故障回复所有五个磁盘。无法从故障回复中排除手动创建的磁盘。

**现在，请按如下所述启用复制**：

1. 单击“步骤 2: 复制应用程序”>“源”。首次启用复制后，请在保管库中单击“+复制”，对其他计算机启用复制。
2. 在“源”边栏选项卡 >“源”中，选择配置服务器。
3. 在“计算机类型”中，选择“虚拟机”或“物理机”。
4. 在“vCenter/vSphere 虚拟机监控程序”中，选择管理 vSphere 主机的 vCenter 服务器，或选择该主机。如果你要复制物理机，则此设置无关紧要。
5. 选择进程服务器。如果你未创建任何额外的进程服务器，则这是配置服务器的名称。然后，单击“确定”。

	![启用复制](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. 在“目标”中选择保管库订阅，然后在“故障转移后部署模型”中，选择故障转移后要在 Azure 中使用的模型（经典或资源管理）。
7. 选择用于复制数据的 Azure 存储帐户。请注意：

	- 可以选择高级或标准存储帐户。如果选择高级帐户，则需要指定其他标准存储帐户来持续写入复制日志。这些帐户必须位于与恢复服务保管库相同的区域中。
	- 如果要使用与现有存储帐户不同的存储帐户，可以[创建一个](#set-up-an-azure-storage-account)。若要使用 ARM 模型创建存储帐户，请单击“新建”。如果想要使用经典模型创建存储帐户，请[在 Azure 门户中](/documentation/articles/storage-create-storage-account-classic-portal/)执行该操作。

8. 选择 Azure VM 在故障转移后启动时所要启动的 Azure 网络和子网。该网络必须位于与恢复服务保管库相同的区域中。选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。选择“稍后配置”以选择每个计算机的 Azure 网络。如果没有网络，需要[创建一个](#set-up-an-azure-network)。若要使用 ARM 模型创建网络，请单击“新建”。若要使用经典模型创建网络，请[在 Azure 门户中](/documentation/articles/virtual-networks-create-vnet-classic-pportal/)执行该操作。选择适用的子网。然后，单击“确定”。

	![启用复制](./media/site-recovery-vmware-to-azure/enable-replication3.png)  


9. 在“虚拟机”>“选择虚拟机”中，单击并选择要复制的每个计算机。只能选择可以启用复制的计算机。然后，单击“确定”。

	![启用复制](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. 在“属性”>“配置属性”中，选择进程服务器在计算机上自动安装移动服务时使用的帐户。默认情况下会复制所有磁盘。单击“所有磁盘”并清除不想要复制的所有磁盘。然后，单击“确定”。可以稍后再设置其他属性。

	![启用复制](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. 在“复制设置”>“配置复制设置”中，检查是否选择了正确的复制策略。可以在“设置”>“复制策略”> 策略名称 >“编辑设置”中修改复制策略设置。应用到策略的更改将应用于复制计算机和新计算机。

12. 如果要将计算机集合到复制组，请启用“多 VM 一致性”并指定组的名称。然后，单击“确定”。请注意：

	- 复制组中的计算机将一起复制，并在故障转移时获得崩溃一致且应用一致的共享恢复点。
	- 我们建议你将 VM 和物理服务器集合在一起，使其镜像你的工作负荷。启用多 VM 一致性可能会影响工作负荷性能，因此，仅当计算机运行相同的工作负荷并且你需要一致性时，才应使用该设置。

	![启用复制](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. 单击“启用复制”。可以在“设置”>“作业”>“Site Recovery 作业”中，跟踪“启用保护”作业的进度。在“完成保护”作业运行之后，计算机就可以进行故障转移了。

> [AZURE.NOTE] 如果计算机已准备好进行推送安装，则启用保护后，将安装移动服务组件。在计算机上安装该组件后，某个保护作业将启动但会失败。发生这种失败后，需要手动重新启动每台计算机。重启后，保护作业重新启动，初始复制开始。

### 查看和管理 VM 属性

建议你验证源计算机的属性。请记住，Azure VM 名称应符合 [Azure 虚拟机要求](/documentation/articles/site-recovery-best-practices/#azure-virtual-machine-requirements)。

1. 单击“设置”>“复制的项”，然后选择计算机。“概要”边栏选项卡显示有关计算机设置和状态的信息。

2. 在“属性”中，可以查看 VM 的复制和故障转移信息。

	![启用复制](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. 在“计算和网络”>“计算属性”中，可以指定 Azure VM 名称和目标大小。根据需要修改名称，使其符合 Azure 要求。你还可以查看和添加目标网络、子网的相关信息，以及要分配到 Azure VM 的 IP 地址。注意以下事项：

	- 可以设置目标 IP 地址。如果未提供地址，故障转移的计算机将使用 DHCP。如果设置了无法用于故障转移的地址，故障转移将不会正常工作。如果地址可用于测试故障转移网络，则同一个目标 IP 地址可用于测试故障转移。
	- 网络适配器数目根据你为目标虚拟机指定的大小来确定，如下所述：

		- 如果源计算机上的网络适配器数小于或等于目标计算机大小允许的适配器数，则目标的适配器数将与源相同。
		- 如果源虚拟机的适配器数大于目标大小允许的数目，则使用目标大小允许的最大数目。
		- 例如，如果源计算机有两个网络适配器，而目标计算机大小支持四个，则目标计算机将有两个适配器。如果源计算机有两个适配器，但支持的目标大小只支持一个，则目标计算机只有一个适配器。
	- 如果 VM 有多个网络适配器，它们将全部连接到同一个网络。

	![启用复制](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. 在“磁盘”中，可以看到 VM 上将要复制的操作系统和数据磁盘。


## 步骤 7：测试部署

为了对部署进行测试，你可以针对单个虚拟机或单个恢复计划（其中包含一个或多个虚拟机）运行测试性故障转移。


### 准备故障转移

- 若要运行测试故障转移，我们建议你创建一个与你的 Azure 生产网络相隔离的新 Azure 网络（这是你在 Azure 中新建网络时的默认行为）。[详细了解](/documentation/articles/site-recovery-failover/#run-a-test-failover)如何运行测试性故障转移。
- 若要在故障转移到 Azure 时获得最佳性能，请在受保护的计算机上安装 Azure 代理。这可以加速引导，并帮助进行故障排除。安装 [Linux](https://github.com/Azure/WALinuxAgent) 或 [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) 代理。
- 若要全面测试部署，需要一个基础结构，使复制的计算机能够按预期工作。如果要测试 Active Directory 和 DNS，可以通过 DNS 创建虚拟机作为域控制器，并使用 Azure Site Recovery 将此虚拟机复制到 Azure。在 [Active Directory 测试性故障转移注意事项](/documentation/articles/site-recovery-active-directory/#considerations-for-test-failover)中了解详细信息。
- 确保配置服务器正在运行。否则故障转移将会失败。
- 如果已从复制中排除磁盘，则故障转移之后，可能需要在 Azure 中手动创建这些磁盘，以便应用程序可按预期运行。
- 如果你要运行非计划的故障转移而不是测试故障转移，请注意以下事项：

	- 在运行非计划的故障转移之前，请尽可能关闭主计算机。这样可确保不会同时运行源计算机和副本计算机。如果你要复制 VMware VM，则可以指定要求站点恢复尽量关闭源计算机。这可能有用，也可能无用，具体取决于主站点的状态。如果你要复制物理服务器，Site Recovery 不提供此选项。
	- 当你运行非计划的故障转移时，将会停止从主计算机复制数据，因此在非计划的故障转移开始以后，将不会传输任何数据差异。此外，如果针对恢复计划运行非计划的故障转移，则即使发生错误，该计划也会一直运行到完成。

### 准备在故障转移后连接到 Azure VM

如果想要在故障转移后使用 RDP 连接到 Azure VM，请确保执行以下操作：

**故障转移之前在本地计算机上**：

- 对通过 Internet 进行的访问启用 RDP，确保已针对“公共”添加 TCP 和 UDP 规则，并确保在“Windows 防火墙”->“允许的应用和功能”中针对所有配置文件允许 RDP。
- 针对通过站点到站点连接进行的访问，在计算机上启用 RDP，并确保在“Windows 防火墙”->“允许的应用和功能”中针对“域”和“专用”网络允许 RDP。
- 在本地计算机上安装 [Azure VM 代理](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
- 在计算机上[手动安装移动服务](#install-the-mobility-service-manually)，而不要使用进程服务器自动推送该服务。这是因为，推送安装只会在为复制启用计算机之后才会发生。
- 确保操作系统的 SAN 策略已设置为 OnlineAll。[了解详细信息](https://support.microsoft.com/zh-cn/kb/3031135)
- 在运行故障转移之前关闭 IPSec 服务。

**故障转移之后在 Azure VM 上**：

- 添加 RDP 协议（端口 3389）的公共终结点并指定用于登录的凭据。
- 确保没有任何域策略阻止你使用公共地址连接到虚拟机。
- 尝试连接。如果无法连接，请检查 VM 是否正在运行。有关更多故障排除提示，请阅读[此文](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。


如果要在故障转移后使用安全外壳客户端 (ssh) 访问运行 Linux 的 Azure VM，请执行以下操作：

**故障转移之前在本地计算机上**：

- 确保 Azure VM 上的安全外壳服务已设置为在系统引导时自动启动。
- 确保防火墙规则允许 SSH 连接。

**故障转移之后在 Azure VM 上**：

- 已故障转移的 VM 及其连接到的 Azure 子网上的网络安全组规则需要允许与 SSH 端口建立传入连接。
- 应创建公共终结点，以允许 SSH 端口（默认为 TCP 端口 22）上的传入连接。
- 如果通过 VPN 连接（Express Route 或站点到站点 VPN）访问 VM，则可以使用客户端通过 SSH 直接连接到 VM。



## 运行测试故障转移

1. 若要故障转移单个计算机，请在“设置”>“复制的项”中，单击“VM”>“+测试性故障转移”图标。

	![测试故障转移](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. 若要故障转移某个恢复计划，请在“设置”>“恢复计划”中，右键单击该计划 >“测试性故障转移”。若要创建恢复计划，请[遵循这些说明](/documentation/articles/site-recovery-create-recovery-plans/)。

3. 在“测试性故障转移”中，选择 Azure VM 在故障转移之后要连接到的 Azure 网络。
4. 单击“确定”开始故障转移。若要跟踪进度，可以单击 VM 以打开其属性，或者在保管库名称 >“设置”>“作业”>“Site Recovery 作业”中选择“测试性故障转移”作业。
5. 当故障转移进入“完成测试”状态时，请执行以下操作：

	1. 在 Azure 门户中查看副本虚拟机。验证虚拟机是否启动成功。
	2. 如果你已设置为从本地网络访问虚拟机，则可以启动与虚拟机的远程桌面连接。
	3. 单击“完成测试”以完成测试。

		![测试故障转移](./media/site-recovery-vmware-to-azure/test-failover6.png)


	4. 单击“说明”以记录并保存与测试故障转移相关联的任何观测结果。
	5. 单击“测试故障转移已完成”以自动清理测试环境。此操作完成后，测试性故障转移会显示“完成”状态。
	6.  在此阶段，将删除站点恢复在测试故障转移期间自动创建的所有元素或 VM。不会删除你为测试故障转移创建的其他任何元素。
	
	> [AZURE.NOTE] 如果测试故障转移持续了两周以上，系统会强行将其结束。
	 

6. 故障转移完成后，你还应该能够看到副本 Azure 计算机显示在 Azure 门户的“虚拟机”中。应确保 VM 的大小适当、已连接到相应的网络，并且正在运行。
7. 如果[已准备好故障转移后的连接](#prepare-to-connect-to-azure-vms-after-failover)，应该能够连接到 Azure VM。

## 监视部署

下面是监视 Site Recovery 部署的配置设置、状态和运行状况的方式：

1. 单击保管库名称以访问“概要”仪表板。在此仪表板中，可以看到 Site Recovery 作业、复制状态、恢复计划、服务器运行状况和事件。可以自定义“概要”以显示最有用的磁贴和布局，包括其他 Site Recovery 和备份保管库的状态。

![概要](./media/site-recovery-vmware-to-azure/essentials.png)

2. 在“运行状况”磁贴中，可以监视发生问题的站点服务器（VMM 或配置服务器），以及 Site Recovery 在过去 24 小时内引发的事件。
3. 可以在“复制的项”、“恢复计划”和“Site Recovery 作业”磁贴中管理和监视复制。可以在“设置”->“作业”->“Site Recovery 作业”中向下钻取到不同的作业。


## 部署额外的进程服务器

如果必须将部署扩展到 200 台源计算机以上，或者你的每日改动率总计超过 2 TB，则需要额外的进程服务器来处理流量。
 
请查看[进程服务器的大小建议](#size-recommendations-for-the-process-server)，然后按照这些说明来设置进程服务器。设置服务器后，可以迁移源计算机来使用它。

### 安装额外的进程服务器

1. 在“设置”>“Site Recovery 服务器”中，单击配置服务器 >“进程服务器”。

	![添加进程服务器](./media/site-recovery-vmware-to-azure/migrate-ps1.png)  


2. 在“服务器类型”中，单击“进程服务器(本地)”。

	![添加进程服务器](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. 下载并运行站点恢复统一安装程序文件以安装进程服务器，并将其注册到保管库中。
4. 在“开始之前”中选择“添加额外的进程服务器以扩大部署”。
5. 完成向导，完成方式与[安装](#step-2-set-up-the-source-environment)配置服务器时采用的方式相同。

	![添加进程服务器](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. 在“配置服务器详细信息”中，指定配置服务器的 IP 地址以及通行短语。若要获取通行短语，请在配置服务器上运行 **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n**。

	![添加进程服务器](./media/site-recovery-vmware-to-azure/add-ps2.png)

### 对计算机进行迁移，以使用新的进程服务器

1. 在“设置”>“Site Recovery 服务器”中，单击配置服务器，然后展开“进程服务器”。

	![更新进程服务器](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. 右键单击当前正在使用的进程服务器，然后单击“切换”。

	![更新进程服务器](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. 在“选择目标进程服务器”中，选择要使用的新进程服务器，然后选择新进程服务器将要处理的虚拟机。单击信息图标，获取服务器的相关信息。为了帮助你做出负载决策，随后会显示将每个所选虚拟机复制到新进程服务器所需的平均空间。单击复选标记，开始复制到新的进程服务器。

## VMware 帐户权限

进程服务器可以自动发现 vCenter 服务器上的 VM。若要执行自动发现，需[定义一个角色 (Azure\_Site\_Recovery)](#prepare-an-account-for-automatic-discovery)，以允许 Site Recovery 访问 VMware 服务器。请注意，如果只需将 VMware 计算机迁移到 Azure，不需从 Azure 进行故障回复，则可定义一个其功能足够的只读角色。下表汇总了所需的角色权限。

**角色** | **详细信息** | **权限**
--- | --- | ---
Azure\_Site\_Recovery 角色 | VMware VM 发现 |为 v-Center 服务器分配以下权限：<br/><br/>数据存储 -> 分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/>网络 -> 网络分配<br/><br/>资源 -> 将虚拟机分配到资源池、迁移关闭的虚拟机、迁移打开的虚拟机<br/><br/>任务 -> 创建任务、更新任务<br/><br/>虚拟机 -> 配置<br/><br/>虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 介质、配置软盘介质、关机、开机、VMware 工具安装<br/><br/>虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/>虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上载<br/><br/>虚拟机 -> 快照 -> 删除快照
vCenter 用户角色 | VMware VM 发现/在不关闭源 VM 的情况下进行故障转移 | 为 v-Center 服务器分配以下权限：<br/><br/>数据中心对象 –> 传播到子对象，角色=只读<br/><br/>用户是在数据中心级别分配的，因此有权访问数据中心的所有对象。若要限制访问权限，请将“禁止访问”角色（包含“传播到子对象”）分配给子对象（vSphere 主机、数据存储、VM 和网络）。 
vCenter 用户角色 | 故障转移和故障回复 | 为 v-Center 服务器分配以下权限：<br/><br/>数据中心对象 – 传播到子对象，角色=Azure\_Site\_Recovery<br/><br/>用户是在数据中心级别分配的，因此有权访问数据中心的所有对象。若要限制访问权限，请将“禁止访问”角色（包含“传播到子对象”）分配给子对象（vSphere 主机、数据存储、VM 和网络）。  
## 后续步骤

- [详细了解](/documentation/articles/site-recovery-failover/)不同类型的故障转移。
- [详细了解故障回复](/documentation/articles/site-recovery-failback-azure-to-vmware/)，以便将 Azure 中运行的已故障转移的计算机回复到本地环境。

## 第三方软件通知和信息

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).Microsoft is the not original author of the Third Party Code.The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below.Such licenses and information are provided for informational purposes only.This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428).Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

<!---HONumber=Mooncake_0822_2016-->