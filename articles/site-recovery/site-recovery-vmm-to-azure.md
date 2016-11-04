---
title: 在 Azure 门户中使用 Site Recovery 将 VMM 云中的 Hyper-V 虚拟机复制到 Azure | Microsoft Docs
description: 介绍如何部署 Azure Site Recovery，以便使用 Azure 门户来协调 VMM 云中 Hyper-V VM 到 Azure 的复制、故障转移和恢复
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: tysonn

ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/16/2016
ms.author: raynew

---
# 在 Azure 门户中使用 Azure Site Recovery 将 VMM 云中的 Hyper-V 虚拟机复制到 Azure | Microsoft Azure
> [!div class="op_single_selector"]
> * [Azure 门户](site-recovery-vmm-to-azure.md)
> * [Azure 经典](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell 经典](site-recovery-deploy-with-powershell.md)
> 
> 

欢迎使用 Azure Site Recovery！ 如果你要在 Azure 门户中使用 Azure Site Recovery 将 System Center Virtual Machine Manager (VMM) 云中管理的本地 Hyper-V 虚拟机复制到 Azure，请参考本文。

> [!NOTE]
> Azure 提供两个不同的[部署模型](../resource-manager-deployment-model.md)来创建和处理资源：Azure Resource Manager 和经典。Azure 还有两个门户 – 支持经典部署模型的 Azure 经典门户，以及支持两种部署模型的 Azure 门户。
> 
> 

Azure 门户中的 Azure Site Recovery 提供多项新功能：

* 在 Azure 门户中，Azure 备份和 Azure Site Recovery 服务合并成单个恢复服务保管库，以便从单个位置设置和管理业务连续性与灾难恢复 (BCDR)。统一仪表板可让用户监视和管理对本地站点与 Azure 公有云上的操作。
* 具有与云解决方案提供程序 (CSP) 一起预配的 Azure 订阅的用户现在可以在 Azure 门户中管理 Site Recovery 操作。
* Azure 门户中的 Site Recovery 可将计算机复制到 Azure Resource Manager 存储帐户。故障转移时，Site Recovery 将在 Azure 中创建基于 Resource Manager 的 VM。
* 站点恢复继续支持复制到经典存储帐户。故障转移时，站点恢复将使用经典模型创建 VM。

阅读本文后，请在底部的 Disqus 意见区域中发表任何意见。如有技术问题，请在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上提出。

## 概述
组织需要制定 BCDR 策略来确定应用、工作负荷和数据如何在计划和非计划停机期间保持运行和可用，并尽快恢复正常运行情况。BCDR 策略应保持业务数据的安全性和可恢复性，并确保在发生灾难时工作负荷持续可用。

Site Recovery 是一项 Azure 服务，可以通过协调从本地物理服务器和虚拟机到云 (Azure) 或辅助数据中心的的复制，来为 BCDR 策略提供辅助。当主要位置发生故障时，你可以故障转移到辅助位置，使应用和工作负荷保持可用。当主要位置恢复正常时，你可以故障转移回到主要位置。在[什么是 Azure Site Recovery？](site-recovery-overview.md)中了解详细信息

本文提供将 VMM 云中的本地 Hyper-V VM 复制到 Azure 所需的全部信息。其中包括体系结构概述、规划信息，以及配置 Azure、本地服务器、复制设置和容量规划的部署步骤。设置基础结构之后，可以在要保护的计算机上启用复制，并检查故障转移是否可以正常工作。

## 业务优势
* 站点恢复为在 Hyper-V VM 上运行的业务工作负荷和应用程序提供站外保护。
* 恢复服务门户提供单个位置用于设置、管理和监视复制、故障转移及恢复。
* 可以轻松运行从本地基础结构到 Azure 的故障转移，以及从 Azure 故障回复（还原）到本地站点中的 Hyper-V 主机服务器。
* 你可以配置包含多个计算机的恢复计划，以便分层应用程序工作负荷一起故障转移。

## 方案体系结构
方案组件如下：

* **VMM 服务器**：包含一个或多个云的本地 VMM 服务器。
* **Hyper-V 主机或群集**：在 VMM 云中管理的 Hyper-V 主机服务器或群集。
* **Azure Site Recovery 提供程序和恢复服务代理**：部署期间在 VMM 服务器上安装 Azure Site Recovery 提供程序，并在 Hyper-V 主机上安装 Microsoft Azure 恢复服务代理。VMM 上的提供程序通过 HTTPS 443 与站点恢复通信，以复制协调过程。默认情况下，Hyper-V 主机服务器上的代理通过 HTTPS 443 将数据复制到 Azure 存储空间。
* **Azure**：需要 Azure 订阅、Azure 存储帐户来存储复制的数据，并需要 Azure 虚拟网络，使 Azure VM 能够在故障转移后连接到网络。

![E2A 拓扑](./media/site-recovery-vmm-to-azure/architecture.png)

## Azure 先决条件
下面是在 Azure 中部署此方案所需的项。

| **先决条件** | **详细信息** |
| --- | --- |
| **Azure 帐户** |需要一个 [Microsoft Azure](http://azure.microsoft.com/) 帐户。你可以从[免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。[详细了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 定价。 |
| **Azure 存储空间** |需要使用标准 Azure 存储帐户来存储复制的数据。可以使用 LRS 或 GRS 存储帐户。建议使用 GRS，以便在发生区域性故障或无法恢复主要区域时，能够复原数据。[了解详细信息](../storage/storage-redundancy.md)。此帐户必须位于与恢复服务保管库相同的区域中。<br/><br/>不支持高级存储。<br/><br/> 复制的数据存储在 Azure 空间，Azure VM 在发生故障转移时创建。<br/><br/> [详细了解](../storage/storage-introduction.md) Azure 存储。 |
| **Azure 网络** |需要一个 Azure 虚拟网络，以便发生故障转移时 Azure VM 能够连接到它。该网络必须位于与恢复服务保管库相同的区域中。 |

## 本地先决条件
下面是在本地需要的项

| **先决条件** | **详细信息** |
| --- | --- |
| **VMM** |在 System Center 2012 R2 上运行的一个或多个 VMM 服务器。应在每个 VMM 服务器上配置一个或多个云。云应包含：<br/><br/>一个或多个 VMM 主机组。<br/><br/> 每个主机组中有一个或多个 Hyper-V 主机服务器或群集。<br/><br/>[详细了解](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html)如何设置 VMM 云。 |
| **Hyper-V** |Hyper-V 主机服务器必须至少运行具有 Hyper-V 角色且安装了最新更新的 Windows Server 2012 R2。<br/><br/> Hyper-V 服务器应包含一个或多个 VM。<br/><br/> 必须在 VMM 云中托管包含要复制的 VM 的 Hyper-V 主机服务器或群集。<br/><br/>Hyper-V 服务器应直接或通过代理连接到 Internet。<br/><br/>Hyper-V 服务器上应已安装文章 [2961977](https://support.microsoft.com/kb/2961977) 中所述的修复程序。<br/><br/>Hyper-V 主机服务器需有 Internet 访问权限，以便将数据复制到 Azure。 |
| **提供程序和代理** |在 Azure Site Recovery 部署期间，用户将在 VMM 服务器上安装 Azure Site Recovery 提供程序，并在 Hyper-V 主机上安装恢复服务代理。提供程序和代理需要通过 Internet 直接连接或通过代理连接到 Azure。请注意，不支持基于 HTTPS 的代理。VMM 服务器和 Hyper-V 主机上的代理服务器应该允许访问：<br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net <br/><br/> *.backup.windowsazure.com <br/><br/> *.blob.core.windows.net <br/><br/> *.store.core.windows.net<br/><br/>如果 VMM 服务器上存在基于 IP 地址的防火墙规则，请检查这些规则是否允许与 Azure 通信。需要允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (433) 协议。<br/><br/>允许订阅的 Azure 区域和美国西部的 IP 地址范围。<br/><br/>此外，VMM 服务器上的代理服务器需有权访问 https://www.msftncsi.com/ncsi.txt |

## 受保护的计算机先决条件
| **先决条件** | **详细信息** |
| --- | --- |
| **受保护的 VM** |在故障转移 VM 之前，需确保要分配给 Azure VM 的名称符合 [Azure 先决条件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。可以在启用 VM 的复制后修改此名称。<br/><br/> 受保护计算机上单个磁盘的容量不应超过 1023 GB。一个 VM 最多可以有 64 个磁盘（因此最大容量为 64 TB）。<br/><br/> 不支持共享的磁盘来宾群集。<br/><br/> 不支持统一可扩展固件接口 (UEFI)/可扩展固件接口 (EFI)。<br/><br/> 如果源 VM 有 NIC 组合，它在故障转移到 Azure 之后将转换成单个 NIC。<br/><br/>不支持保护运行 Linux 的、使用静态 IP 地址的 VM。 |

## 准备部署
若要准备部署，需要：

1. [设置 Azure 网络](#set-up-an-azure-network)，故障转移后，Azure VM 将位于该网络中。
2. 针对复制的数据[设置 Azure 存储帐户](#set-up-an-azure-storage-account)。
3. [准备 VMM 服务器](#prepare-the-vmm-server)以便进行站点恢复部署。
4. [准备网络映射](#prepare-for-network-mapping)。设置网络，以便在站点恢复部署期间配置网络映射。

### 设置 Azure 网络
需要一个 Azure 网络，以便在故障转移后创建的 Azure VM 能够连接到它。

* 此网络应与将部署恢复服务保管库的网络位于同一区域中。
* 根据要用于故障转移 Azure VM 的资源模型，用户将在 [Resource Manager 模式](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)或[经典模式](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)下设置 Azure 网络。
* 建议在开始之前先设置网络。否则，需要在 Site Recovery 部署期间执行此操作。

> [!NOTE]
> [Migration of networks](../resource-group-move-resources.md) 用于部署 Site Recovery 的网络不支持跨同一订阅中的资源组或跨订阅。
> 
> 

### 设置 Azure 存储帐户
* 需要使用标准 Azure 存储帐户来保存复制到 Azure 的数据。该帐户必须位于与恢复服务保管库相同的区域中。
* 根据用于故障转移 Azure VM 的资源模型，用户将在 [Resource Manager 模式](../storage/storage-create-storage-account.md)或[经典模式](../storage/storage-create-storage-account-classic-portal.md)下设置帐户。
* 建议在开始之前设置帐户。否则，需要在 Site Recovery 部署期间执行此操作。

> [!NOTE]
> [Migration of storage accounts](../resource-group-move-resources.md) 用于部署 Site Recovery 的存储帐户不支持跨同一订阅中的资源组或跨订阅。
> 
> 

### 准备 VMM 服务器
* 确保 VMM 服务器符合[先决条件](#on-premises-prerequisites)。
* 在 Site Recovery 部署期间，可以指定 VMM 服务器上的所有云应可在 Azure 门户中使用。如果只希望特定云出现在门户中，可以在 VMM 管理控制台中对此云启用该设置。

### 准备网络映射
需要在 Site Recovery 部署期间设置网络映射。网络映射将在源 VMM VM 网络与目标 Azure 网络之间映射，以便：

* 同一网络中故障转移的计算机可以彼此连接，即使它们不是以方式相同或在相同的恢复计划中故障转移。
* 如果在目标 Azure 网络上设置了网络网关，则 Azure 虚拟机可以连接到其他本地虚拟机。
* 若要设置网络映射，需要做好以下准备：
  
  * 确保源 Hyper-V 主机服务器上的 VM 已连接到 VMM VM 网络。该网络应当该链接到与该云相关联的逻辑网络。
  * [如上所述](#set-up-an-azure-network)的 Azure 网络
* [详细了解](site-recovery-network-mapping.md)网络映射的工作原理。

## 创建恢复服务保管库
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“新建”>“管理”>“恢复服务”。或者，可以单击“浏览”>“恢复服务保管库”>“添加”。
   
    ![新保管库](./media/site-recovery-vmm-to-azure/new-vault3.png)
3. 在“名称”中，指定一个友好名称以标识该保管库。如果你有多个订阅，请选择其中一个。
4. [创建一个资源组](../resource-group-template-deploy-portal.md)或选择一个现有的资源组。指定 Azure 区域。计算机将复制到此区域。若要查看受支持的区域，请参阅 Azure Site Recovery 价格详细信息中的“地域可用性”。[](https://azure.microsoft.com/pricing/details/site-recovery/)
5. 如果要从仪表板快速访问保管库，请单击“固定到仪表板”>“创建保管库”。
   
    ![新保管库](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

新保管库将显示在“仪表板”>“所有资源”中，以及“恢复服务保管库”主边栏选项卡上。

## 入门
Site Recovery 提供的“快速启动”体验可帮助你尽快完成部署。“快速启动”将检查先决条件，并引导你以正确的顺序完成 Site Recovery 部署步骤。

在“快速启动”中，选择要复制的计算机类型，以及要复制到的位置。设置本地服务器、Azure 存储帐户和网络。创建复制策略并执行容量计划。设置好基础结构之后，可以启用 VM 复制。可对特定计算机运行故障转移，或创建恢复计划以故障转移多个计算机。

选择部署 Site Recovery 的方式，以开始“快速启动”。根据复制要求，“快速启动”流程会稍有不同。

## 步骤 1：选择保护目标
选择要复制的内容以及要复制到的位置。

1. 在“恢复服务保管库”边栏选项卡中选择保管库，然后单击“设置”。
2. 在“开始使用”中，单击“Site Recovery”>“步骤 1: 准备基础结构”>“保护目标”。
   
    ![选择目标](./media/site-recovery-vmm-to-azure/choose-goals.png)
3. 在“保护目标”中选择“到 Azure”，然后选择“是，使用 Hyper-V”。选择“是”，以确认使用 VMM 来管理 Hyper-V 主机和恢复站点。然后，单击“确定”。
   
    ![选择目标](./media/site-recovery-vmm-to-azure/choose-goals2.png)

## 步骤 2：设置源环境
在 VMM 服务器上安装 Azure Site Recovery 提供程序，并在保管库中注册该服务器。在 Hyper-V 主机上安装 Azure 恢复服务代理。

1. 单击“步骤 2: 准备基础结构”>“源”。
   
    ![设置源](./media/site-recovery-vmm-to-azure/set-source1.png)
2. 在“准备源”中单击“+ VMM”以添加 VMM 服务器。
   
    ![设置源](./media/site-recovery-vmm-to-azure/set-source2.png)
3. 在“添加服务器”边栏选项卡中，检查“System Center VMM 服务器”是否出现在“服务器类型”中，以及 VMM 服务器是否符合[先决条件和 URL 要求](#on-premises-prerequisites)。
4. 下载 Azure Site Recovery 提供程序安装文件。
5. 下载注册密钥。运行安装程序时需要用到此密钥。生成的密钥有效期为 5 天。
   
    ![设置源](./media/site-recovery-vmm-to-azure/set-source3.png)
6. 在 VMM 服务器上安装 Azure Site Recovery 提供程序。

### 安装 Azure Site Recovery 提供程序
1. 运行提供程序安装程序文件。
2. 在“Microsoft 更新”中，可以选择进行更新，以便根据 Microsoft 更新策略安装提供程序更新。
3. 在“安装”中接受或修改默认提供程序安装位置，然后单击“安装”。
   
    ![安装位置](./media/site-recovery-vmm-to-azure/provider2.png)
4. 安装完成后，单击“注册”在保管库中注册 VMM 服务器。
5. 在“保管库设置”页中，单击“浏览”选择保管库密钥文件。指定 Azure Site Recovery 订阅和保管库名称。
   
    ![服务器注册](./media/site-recovery-vmm-to-azure/provider10.PNG)
6. 在“Internet 连接”中，指定在 VMM 服务器上运行的提供程序如何通过 Internet 连接到 Site Recovery。
   
   * 如果希望提供程序直接进行连接，请选择“不使用代理直接连接到 Azure Site Recovery”。
   * 如果现有代理要求身份验证，或者用户希望使用自定义代理，请选择“使用代理服务器连接到 Azure Site Recovery”。
   * 如果使用自定义代理，请指定地址、端口和凭据。
   * 如果你使用代理，应事先允许[先决条件](#on-premises-prerequisites)中所述的 URL。
   * 如果你使用自定义代理，则将使用指定的代理凭据自动创建一个 VMM 运行身份帐户 (DRAProxyAccount)。对代理服务器进行配置以便该帐户可以成功通过身份验证。可以在 VMM 控制台中修改 VMM 运行身份帐户设置。在“设置”中，展开“安全性”>“运行方式帐户”，然后修改 DRAProxyAccount 的密码。你将需要重新启动 VMM 服务以使此设置生效。
     
     ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)
7. 接受或修改用于保存为数据加密自动生成的 SSL 证书的位置。如果在 Azure Site Recovery 门户中为受 Azure 保护的云启用数据加密，则会使用此证书。请确保该证书安全。运行故障转移到 Azure 时，如果已启用数据加密，需要使用该证书来解密。
8. 在“服务器名称”中，指定一个友好名称以在保管库中标识该 VMM 服务器。在群集配置中，指定 VMM 群集角色名称。
9. 如果要将 VMM 服务器上所有云的元数据与保管库同步，请启用“同步云元数据”。此操作在每个服务器上只需执行一次。如果你不希望同步所有云，可以将此设置保留为未选中状态并在 VMM 控制台中的云属性中分别同步各个云。单击“注册”完成此过程。
   
    ![服务器注册](./media/site-recovery-vmm-to-azure/provider16.PNG)
10. 注册随即开始。注册完成后，服务器将显示在保管库的“设置”>“服务器”边栏选项卡中。

#### Azure Site Recovery 提供程序的命令行安装
可通过以下命令行来安装 Azure Site Recovery 提供程序。此命令可用来将提供程序安装在 Server Core for Windows Server 2012 R2 上。

1. 将提供程序安装文件和注册密钥下载到某个文件夹中。例如，C:\\ASR。
2. 从提升的命令提示符处，运行以下命令提取提供程序安装程序：
   
            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. 运行以下命令来安装组件：
   
            C:\ASR> setupdr.exe /i
4. 然后运行以下命令在保管库中注册服务器：
   
        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

其中：

* **/Credentials**：用于指定注册密钥文件所在位置的必需参数。
* **/FriendlyName**：在 Azure Site Recovery 门户中显示的 Hyper-V 主机服务器名称的必需参数。
* * **/EncryptionEnabled**：将 VMM 云中的 Hyper-V VM 复制到 Azure 时使用的可选参数。指定是否要在 Azure 中加密虚拟机（静态加密）。请确保该文件的名称具有 **.pfx** 扩展名。默认情况下，加密已关闭。
* **/proxyAddress**：可选参数，用于指定代理服务器的地址。
* **/proxyport**：可选参数，用于指定代理服务器的端口。
* **/proxyUsername**：可选参数，用于指定代理用户名（如果代理要求身份验证）。
* **/proxyPassword**：可选参数，用于指定密码，以便通过代理服务器进行身份验证（如果代理要求身份验证）。

### 在 Hyper-V 主机上安装 Azure 恢复服务代理
1. 设置提供程序之后，需要下载 Azure 恢复服务代理的安装文件。在 VMM 云中的每个 Hyper-V 服务器上运行安装程序。
   
    ![Hyper-V 站点](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)
2. 在“必备项检查”页上，单击“下一步”。将自动安装任何缺少的必备组件。
   
    ![恢复服务代理必备组件](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)
3. 在“安装设置”页上，接受或修改安装位置和缓存位置。可以在至少有 5 GB 可用存储空间的驱动器上配置缓存，但我们建议缓存驱动器有 600 GB 或更多可用空间。然后单击“安装”。
4. 安装完成后，单击“关闭”以完成整个过程。
   
    ![注册 MARS 代理](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### Azure Site Recovery 服务代理的命令行安装
可以使用以下命令从命令行安装 Microsoft Azure 恢复服务代理：

     marsagentinstaller.exe /q /nu

#### 设置从 Hyper-V 主机到站点恢复的 Internet 代理访问权限
Hyper-V 主机上运行的恢复服务代理需有权通过 Internet 访问 Azure 才能进行 VM 复制。如果通过代理访问 Internet，请按如下所示进行设置：

1. 在 Hyper-V 主机上打开 Microsoft Azure 备份 MMC 管理单元。默认情况下，Microsoft Azure 备份的快捷方式位于桌面上或 C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin 中。
2. 在管理单元中，单击“更改属性”。
3. 在“代理配置”选项卡上指定代理服务器信息。
   
    ![注册 MARS 代理](./media/site-recovery-vmm-to-azure/mars-proxy.png)
4. 确保代理可以访问[先决条件](#on-premises-prerequisites)中所述的 URL。

## 步骤 3：设置目标环境
指定要用于复制的 Azure 存储帐户，以及 Azure VM 在故障转移后连接到的 Azure 网络。

1. 单击“准备基础结构”>“目标”，然后选择要使用的 Azure 订阅。
2. 指定要在故障转移后用于 VM 的部署模型。
3. Site Recovery 将检查是否有一个或多个兼容的 Azure 存储帐户和网络。
   
   ![存储](./media/site-recovery-vmm-to-azure/compatible-storage.png)
4. 如果尚未创建存储帐户并想要使用 Resource Manager 来创建一个帐户，请单击“+存储帐户”以内联方式执行该操作。在“创建存储帐户”边栏选项卡中，指定帐户名、类型、订阅和位置。该帐户应位于与恢复服务保管库相同的位置。
   
   ![存储](./media/site-recovery-vmm-to-azure/gs-createstorage.png)
   
   请注意：
   
   * 如果想要使用经典模型创建存储帐户，请在 Azure 门户中执行该操作。[了解详细信息](../storage/storage-create-storage-account-classic-portal.md)
   * 如果使用高级存储帐户保存复制的数据，则需要设置其他标准存储帐户来存储复制日志，这些日志将捕获本地数据正在发生的更改。
5. 如果尚未创建 Azure 网络并想要使用 Resource Manager 创建 Azure 网络，请单击“+网络”以内联方式执行该操作。在“创建虚拟网络”边栏选项卡上，指定网络名称、地址范围、子网详细信息、订阅和位置。该网络应位于与恢复服务保管库相同的位置。
   
   ![网络](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)  

    如果想要使用经典模型创建网络，请在 Azure 门户中执行该操作。[了解详细信息](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。

### 配置网络映射
* [阅读](#prepare-for-network-mapping)网络映射的快速概述。[阅读此文](site-recovery-network-mapping.md)以获取更深入的说明。
* 确认 VMM 服务器上的虚拟机已连接到 VM 网络，并且至少创建了一个 Azure 虚拟网络。可以将多个 VM 网络映射到单个 Azure 网络。

按如下所述配置映射：

1. 在“设置”>“Site Recovery 基础结构”>“网络映射”>“网络映射”中，单击“+网络映射”图标。
   
    ![网络映射](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. 在“添加网络映射”中选择源 VMM 服务器，并选择“Azure”作为目标。
3. 在故障转移后，验证订阅和部署模型。
4. 在“源网络”中，从与 VMM 服务器关联的列表中，选择要映射的源本地 VM 网络。
5. 在“目标网络”中，选择副本 Azure VM 创建后所在的 Azure 网络。然后，单击“确定”。
   
    ![网络映射](./media/site-recovery-vmm-to-azure/network-mapping2.png)

下面是网络映射开始时发生的事情：

* 映射开始时，源 VM 网络上的现有 VM 将连接到目标网络。进行复制时，连接到源 VM 网络的新 VM 将连接到映射的 Azure 网络。
* 如果修改现有的网络映射，则副本虚拟机将使用新设置进行连接。
* 如果目标网络具有多个子网，并且其中一个子网与源虚拟机所在的子网同名，则在故障转移后副本虚拟机将连接到该目标子网。
* 如果没有具有匹配名称的目标子网，则虚拟机将连接到网络中的第一个子网。

## 步骤 4：设置复制设置
1. 若要创建新的复制策略，请单击“准备基础结构”>“复制设置”>“+创建和关联”。
   
    ![网络](./media/site-recovery-vmm-to-azure/gs-replication.png)
2. 在“创建和关联策略”中，指定策略名称。
3. 在“复制频率”中，指定要在初始复制后复制增量数据的频率（每隔 30 秒、5 或 15 分钟）。
4. 在“恢复点保留期”中，针对每个恢复点指定保留期的时长（以小时为单位）。受保护的计算机可以恢复到某个时段内的任意时间点。
5. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（1-12 小时）。Hyper-V 使用两种类型的快照 — 标准快照，它提供整个虚拟机的增量快照；与应用程序一致的快照，它生成虚拟机内的应用程序数据的时间点快照。与应用程序一致的快照使用卷影复制服务 (VSS) 来确保应用程序在拍摄快照时处于一致状态。请注意，如果你启用了与应用程序一致的快照，它将影响在源虚拟机上运行的应用程序的性能。请确保你设置的值小于你配置的额外恢复点的数目。
6. 在“初始复制开始时间”中，指定开始初始复制的时间。复制通过 Internet 带宽进行，因此你可能需要将它安排在非繁忙时间。
7. 在“加密 Azure 上存储的数据”中，指定是否加密 Azure 存储中的静态数据。然后，单击“确定”。
   
    ![复制策略](./media/site-recovery-vmm-to-azure/gs-replication2.png)
8. 当创建新策略时，该策略自动与 VMM 云关联。单击**“确定”**。可以在“设置”>“复制”> 策略名称 >“关联 VMM 云”中，将其他 VMM 云（及其中的 VM）与此复制策略相关联。
   
    ![复制策略](./media/site-recovery-vmm-to-azure/policy-associate.png)

## 步骤 5：容量规划
你已经设置了基本基础结构，现在可以考虑容量计划并确定是否需要额外的资源。

站点恢复提供 Capacity Planner 帮助你为源环境、站点恢复组件、网络和存储分配适当的资源。可以在快速模式下运行 Planner，以便根据 VM、磁盘和存储的平均数量进行估计；或者在详细模式下运行 Planner，以输入工作负荷级别的数据。开始之前，需要：

* 收集有关复制环境的信息，包括 VM 数、每个 VM 的磁盘数和每个磁盘的存储空间。
* 估计复制数据的每日更改（变动）率。可以使用 [Hyper-V 副本 Capacity Planner](https://www.microsoft.com/download/details.aspx?id=39057) 来帮助执行此操作。

1. 单击“下载”以下载该工具，然后开始运行。[阅读](site-recovery-capacity-planner.md)该工具随附的文章。
2. 完成后，请在“是否已运行 Capacity Planner?”中选择“是”。
   
   ![容量规划](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### 网络带宽注意事项
可以使用 Capacity Planner 工具来计算复制（初始复制，然后是增量复制）所需的带宽。若要控制复制所用的带宽量，可以使用几个选项：

* **限制带宽**：复制到辅助站点的 Hyper-V 流量经过特定的 Hyper-V 主机。可以在主机服务器上限制带宽。
* **调整带宽**：可以使用几个注册表项来控制用于复制的带宽。

#### 限制带宽
1. 在 Hyper-V 主机服务器上打开 Microsoft Azure 备份 MMC 管理单元。默认情况下，Microsoft Azure 备份的快捷方式位于桌面上或 C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin 中。
2. 在管理单元中，单击“更改属性”。
3. 在“限制”选项卡上，选择“为备份操作启用 Internet 带宽使用限制”，然后设置工作时间和非工作时间的限制。有效范围为 512 Kbps 到 102 Mbps。
   
    ![限制带宽](./media/site-recovery-vmm-to-azure/throttle2.png)

也可以使用 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet 来设置限制。下面是一个示例：

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 表示不需要限制。

#### 影响网络带宽
**UploadThreadsPerVM** 注册表值控制用于磁盘数据传输（初始或增量复制）的线程数。使用较大的值会增加复制所用的网络带宽。**DownloadThreadsPerVM** 注册表值指定在故障回复期间用于数据传输的线程数。

1. 在注册表中导航到 **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsofte Azure Backup\\Replication**。
   
   * 修改 **UploadThreadsPerVM** 值（如果该项不存在，请创建该项），控制用于磁盘复制的线程数。
   * 修改 **DownloadThreadsPerVM** 值（如果该项不存在，请创建该项），控制用于从 Azure 故障回复流量的线程数。
2. 默认值为 4。在“过度预配型”网络中，这些注册表项需要更改，不能使用默认值。最大值为 32。监视流量以优化值。

## 步骤 6：启用复制
现在，请按如下所述启用复制：

1. 单击“步骤 2: 复制应用程序”>“源”。首次启用复制后，请在保管库中单击“+复制”，对其他计算机启用复制。
   
    ![启用复制](./media/site-recovery-vmm-to-azure/enable-replication1.png)
2. 在“源”边栏选项卡中，选择 VMM 服务器和 Hyper-V 主机所在的云。然后，单击“确定”。
   
    ![启用复制](./media/site-recovery-vmm-to-azure/enable-replication-source.png)
3. 在“目标”中，选择订阅、故障转移后部署模型以及要用于复制的数据的存储帐户。
   
    ![启用复制](./media/site-recovery-vmm-to-azure/enable-replication-target.png)
4. 选择要使用的存储帐户。如果要使用与现有存储帐户不同的存储帐户，可以[创建一个](#set-up-an-azure-storage-account)。若要使用 Resource Manager 模型创建存储帐户，请单击“新建”。如果要使用经典模型创建存储帐户，请[在 Azure 门户中](../storage/storage-create-storage-account-classic-portal.md)执行该操作。然后，单击“确定”。
5. 选择 Azure VM 在故障转移后启动时所要连接的 Azure 网络和子网。选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。选择“稍后配置”以选择每个计算机的 Azure 网络。如果要使用与现有不同的网络，可以[创建一个](#set-up-an-azure-network)。若要使用 Resource Manager 模型创建网络，请单击“新建”。若要使用经典模型创建网络，请[在 Azure 门户中](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)执行该操作。选择适用的子网。然后，单击“确定”。
6. 在“虚拟机”>“选择虚拟机”中，单击并选择要复制的每个计算机。只能选择可以启用复制的计算机。然后，单击“确定”。
   
    ![启用复制](./media/site-recovery-vmm-to-azure/enable-replication5.png)
7. 在“属性”>“配置属性”中，为选定的 VM 选择操作系统和 OS 磁盘。然后，单击“确定”。可以稍后再设置其他属性。
   
    ![启用复制](./media/site-recovery-vmm-to-azure/enable-replication6.png)
8. 在“复制设置”>“配置复制设置”中，选择要应用于受保护 VM 的复制策略。然后，单击“确定”。可以在“设置”>“复制策略”> 策略名称 >“编辑设置”中修改复制策略。应用的更改将用于已在复制的计算机和新计算机。
   
   ![启用复制](./media/site-recovery-vmm-to-azure/enable-replication7.png)

可以在“设置”>“作业”>“Site Recovery 作业”中，跟踪“启用保护”作业的进度。在“完成保护”作业运行之后，计算机就可以进行故障转移了。

### 查看和管理 VM 属性
建议你验证源计算机的属性。请记住，Azure VM 名称应符合 [Azure 虚拟机要求](site-recovery-best-practices.md#azure-virtual-machine-requirements)。

1. 单击“设置”>“受保护的项”>“复制的项”，然后选择计算机以查看其详细信息。
   
    ![启用复制](./media/site-recovery-vmm-to-azure/vm-essentials.png)
2. 在“属性”中，可以查看 VM 的复制和故障转移信息。
   
    ![启用复制](./media/site-recovery-vmm-to-azure/test-failover2.png)
3. 在“计算和网络”>“计算属性”中，可以指定 Azure VM 名称和目标大小。根据需要修改名称，使其符合 [Azure 要求](site-recovery-best-practices.md#azure-virtual-machine-requirements)。还可以查看和修改目标网络、子网以及已分配给 Azure VM 的 IP 地址的相关信息。注意以下事项：
   
   * 可以设置目标 IP 地址。如果未提供地址，故障转移的计算机将使用 DHCP。如果设置了无法用于故障转移的地址，故障转移将会失败。如果地址可用于测试故障转移网络，则同一个目标 IP 地址可用于测试故障转移。
   * 网络适配器数目根据你为目标虚拟机指定的大小来确定，如下所述：
     
     * 如果源计算机上的网络适配器数小于或等于目标计算机大小允许的适配器数，则目标的适配器数将与源相同。
     * 如果源虚拟机的适配器数大于目标大小允许的数目，则使用目标大小允许的最大数目。
     * 例如，如果源计算机有两个网络适配器，而目标计算机大小支持四个，则目标计算机将有两个适配器。如果源计算机有两个适配器，但支持的目标大小只支持一个，则目标计算机只有一个适配器。
     * 如果 VM 有多个网络适配器，它们将全部连接到同一个网络。
     
     ![启用复制](./media/site-recovery-vmm-to-azure/test-failover4.png)
4. 在“磁盘”中，可以看到 VM 上将要复制的操作系统和数据磁盘。

## 步骤 7：测试部署
为了对部署进行测试，你可以针对单个虚拟机或单个恢复计划（其中包含一个或多个虚拟机）运行测试性故障转移。

### 准备故障转移
* 若要运行测试故障转移，我们建议你创建一个与你的 Azure 生产网络相隔离的新 Azure 网络（这是你在 Azure 中新建网络时的默认行为）。[详细了解](site-recovery-failover.md#run-a-test-failover)如何运行测试性故障转移。
* 若要在故障转移到 Azure 时获得最佳性能，请在受保护的计算机上安装 Azure 代理。这可以加速引导，并帮助进行故障排除。安装 [Linux](https://github.com/Azure/WALinuxAgent) 或 [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) 代理。
* 若要全面测试部署，需要一个基础结构，使复制的计算机能够按预期工作。如果要测试 Active Directory 和 DNS，可以通过 DNS 创建虚拟机作为域控制器，并使用 Azure Site Recovery 将此虚拟机复制到 Azure。在 [Active Directory 测试性故障转移注意事项](site-recovery-active-directory.md#considerations-for-test-failover)中了解详细信息。
* 如果要运行非计划的故障转移而不是测试故障转移，请注意以下事项：
  
  * 在运行非计划的故障转移之前，请尽可能关闭主计算机。这样可确保不会同时运行源计算机和副本计算机。
  * 当你运行非计划的故障转移时，将会停止从主计算机复制数据，因此在非计划的故障转移开始以后，将不会传输任何数据增量。此外，如果针对恢复计划运行非计划的故障转移，则即使发生错误，该计划也会一直运行到完成。

### 准备在故障转移后连接到 Azure VM
如果想要在故障转移后使用 RDP 连接到 Azure VM，请确保执行以下操作：

**故障转移之前在本地计算机上**：

* 对通过 Internet 进行的访问启用 RDP，确保已针对“公共”添加 TCP 和 UDP 规则，并确保在“Windows 防火墙”->“允许的应用和功能”中针对所有配置文件允许 RDP。
* 针对通过站点到站点连接进行的访问，在计算机上启用 RDP，并确保在“Windows 防火墙”->“允许的应用和功能”中针对“域”和“专用”网络允许 RDP。
* 在本地计算机上安装 [Azure VM 代理](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
* 确保操作系统的 SAN 策略已设置为 OnlineAll。[了解详细信息](https://support.microsoft.com/kb/3031135)
* 在运行故障转移之前关闭 IPSec 服务。

**故障转移之后在 Azure VM 上**：

* 添加 RDP 协议（端口 3389）的公共终结点并指定用于登录的凭据。
* 确保没有任何域策略阻止你使用公共地址连接到虚拟机。
* 尝试连接。如果无法连接，请检查 VM 是否正在运行。有关更多故障排除提示，请阅读[此文](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。

如果要在故障转移后使用安全外壳客户端 (ssh) 访问运行 Linux 的 Azure VM，请执行以下操作：

**故障转移之前在本地计算机上**：

* 确保 Azure VM 上的安全外壳服务已设置为在系统引导时自动启动。
* 确保防火墙规则允许 SSH 连接。

**故障转移之后在 Azure VM 上**：

* 已故障转移的 VM 及其连接到的 Azure 子网上的网络安全组规则需要允许与 SSH 端口建立传入连接。
* 应创建公共终结点，以允许 SSH 端口（默认为 TCP 端口 22）上的传入连接。
* 如果通过 VPN 连接（Express Route 或站点到站点 VPN）访问 VM，则可以使用客户端通过 SSH 直接连接到 VM。

### 运行测试故障转移
若要运行测试故障转移，请执行以下操作：

1. 若要故障转移单个 VM，请在“设置”>“复制的项”中，单击“VM”>“+测试故障转移”。
2. 若要故障转移某个恢复计划，请在“设置”>“恢复计划”中，右键单击该计划 >“测试性故障转移”。若要创建恢复计划，请[遵循这些说明](site-recovery-create-recovery-plans.md)。
3. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的 Azure 网络。
4. 单击“确定”开始故障转移。若要跟踪进度，可以单击 VM 以打开其属性，或者在“设置”>“Site Recovery 作业”中单击“测试故障转移”作业。
5. 当故障转移达到“完成测试”阶段时，请执行以下操作：
   
   1. 在 Azure 门户中查看副本虚拟机。验证虚拟机是否启动成功。
   2. 如果你已设置为从本地网络访问虚拟机，则可以启动与虚拟机的远程桌面连接。
   3. 单击“完成测试”以完成测试。
   4. 单击“说明”以记录并保存与测试故障转移相关联的任何观测结果。
   5. 单击“测试故障转移已完成”。清理测试环境以自动关闭电源，并删除测试虚拟机。
   6. 在此阶段，将删除 Site Recovery 在测试故障转移期间自动创建的所有元素或 VM。不会删除你为测试故障转移创建的其他任何元素。
      
      > [!NOTE]
      > 如果测试故障转移持续了两周以上，系统会强行将其结束。
      > 
      > 
6. 故障转移完成后，你还应该能够看到副本 Azure 计算机显示在 Azure 门户的“虚拟机”中。应确保 VM 的大小适当、已连接到相应的网络，并且正在运行。
7. 如果[已准备好故障转移后的连接](#prepare-to-connect-to-Azure-VMs-after-failover)，应该能够连接到 Azure VM。

## 监视部署
下面是监视 Site Recovery 部署的配置设置、状态和运行状况的方式：

1. 单击保管库名称以访问“概要”仪表板。在此仪表板中，可以看到 Site Recovery 作业、复制状态、恢复计划、服务器运行状况和事件。可以自定义“概要”以显示最有用的磁贴和布局，包括其他 Site Recovery 和备份保管库的状态。
   
    ![概要](./media/site-recovery-vmm-to-azure/essentials.png)
2. 在“运行状况”磁贴中，可以监视发生问题的站点服务器（VMM 或配置服务器），以及 Site Recovery 在过去 24 小时内引发的事件。
3. 可以在“复制的项”、“恢复计划”和“Site Recovery 作业”磁贴中管理和监视复制。可以在“设置”->“作业”->“Site Recovery 作业”中深入到不同的作业。

## 后续步骤
设置并运行部署后，请[详细了解](site-recovery-failover.md)不同类型的故障转移。

<!---HONumber=AcomDC_0921_2016-->