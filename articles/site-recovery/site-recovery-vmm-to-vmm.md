---
title: "使用 Azure 门户将 VMM 云中的 Hyper-V 虚拟机复制到辅助 VMM 站点 | Microsoft 文档"
description: "介绍如何部署 Azure Site Recovery，以便使用 Azure 门户来协调 VMM 云中 Hyper-V VM 到辅助 VMM 站点的复制、故障转移和恢复。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: b33a1922-aed6-4916-9209-0e257620fded
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 4e1521d1a49a83c4936bf64ba0177c5834a6c374
ms.openlocfilehash: 0737a9015ff1584a8c82681dbb809f86ae44f48c


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>使用 Azure 门户将 VMM 云中的 Hyper-V 虚拟机复制到辅助 VMM 站点
> [!div class="op_single_selector"]
> * [Azure 门户](site-recovery-vmm-to-vmm.md)
> * [经典门户](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - 资源管理器](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

欢迎使用 Azure Site Recovery 服务！

站点恢复就是能够帮助实现业务连续性和灾难恢复 (BCDR) 策略的一个 Azure 服务。 站点恢复可安排从本地物理服务器和虚拟机到云 (Azure) 或辅助数据中心的复制。 当主要位置发生故障时，你可以故障转移到辅助位置，使应用和工作负荷保持可用。 当主要位置恢复正常时，你可以故障转移回到主要位置。 在 [什么是 Azure Site Recovery？](site-recovery-overview.md)

本文介绍如何在 Azure 门户中使用 Site Recovery 将 System Center Virtual Machine Manager (VMM) 云中管理的本地 Hyper-V 虚拟机复制到辅助站点。

阅读本文后，请在底部的 Disqus 意见区域中发表任何意见。 如有技术问题，请在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上提出。


## <a name="quick-summary"></a>快速摘要
对于完整部署，我们强烈建议遵循本文中的步骤。 但是，如果你时间很紧张，可以查看快速摘要。

| **区域** | **详细信息** |
| --- | --- |
| **部署方案** |在 VMM 云之间复制 Hyper-V 主机上的 Hyper-V VM|
| **本地要求** | 本地 VMM 服务器应至少运行装有最新更新的 System Center 2012 SP1。<br/><br/> 建议在每个站点部署一台 VMM 服务器，但你可以在同一台 VMM 服务器上的云之间复制。 [了解详细信息](site-recovery-single-vmm.md)<br/><br/> VMM 服务器上有一个或多个包含 Hyper-V 主机的云。<br/><br/> Hyper-V 服务器应至少运行装有最新更新的 Windows Server 2012。<br/><br/> VMM 服务器和 Hyper-V 主机必须能够访问 Internet。<br/><br/> [所需的 URL](#on-premises-prerequisites)（直接或通过代理）  |
| **本地限制** | 不支持基于 HTTPS 的代理
| **Azure 要求** | Azure 帐户<br/><br/> 恢复服务保管库 |
| **VM 复制** | Hyper-V 支持的任何 VM |
| **代理** | VMM 服务器上安装的 Azure Site Recovery 提供程序<br/><br/> Hyper-V 主机上不需要代理 |
| **部署步骤** | **1)** 准备本地 VMM 服务器 -> **2)** 创建恢复服务保管库 -> **3)** 设置 VMM 服务器并在保管库中注册 -> **4)** 配置复制设置 -> **5)** 设置网络映射 -> **6)** 启用复制 -> **7)** 测试复制和故障转移


## <a name="site-recovery-in-the-azure-portal"></a>Azure 门户中的 Site Recovery

Azure 提供了两个不同的[部署模型](../azure-resource-manager/resource-manager-deployment-model.md)来创建和处理资源 – Azure Resource Manager 模型和经典模型。 Azure 还提供两个门户 – Azure 经典门户和 Azure 门户。

本文介绍如何在 Azure 门户中进行部署，该门户可提供改进的部署体验。 可以使用经典门户维护现有的保管库。 无法使用经典门户创建新的保管库。


## <a name="site-recovery-in-your-business"></a>企业中的 Site Recovery
组织需要制定 BCDR 策略来确定应用和数据如何在计划和非计划停机期间保持运行和可用，并尽快恢复正常运行情况。 以下是站点恢复可以提供的帮助：

- 为 Hyper-V VM 上运行的业务工作负荷提供站外保护。
- 提供单个位置用于设置、管理和监视复制、故障转移以及恢复。
- 跨本地站点进行简单的故障转移和故障回复。
- 复制和故障转移多个 VM。  可以在恢复计划中收集多台计算机，使分层在多台计算机上的应用程序工作负荷一起故障转移。

## <a name="scenario-architecture"></a>方案体系结构

* **主站点**：主站点中需有一台或多台 VMM 服务器。 这些服务器上部署了 VMM 私有云，其中的 Hyper-V 主机服务器包含所要复制的 VM。
* **辅助站点**：辅助站点中需有一台或多台 VMM 服务器。 VMM 云中的 Hyper-V 主机服务器运行主 VM 所要复制到的目标 VM。
* **提供程序**：在部署 Site Recovery 期间，可在 VMM 服务器上安装 Azure Site Recovery 提供程序，并在恢复服务保管库中注册这些服务。 提供程序通过 HTTPS 443 与 Site Recovery 通信，以复制业务流程。 数据复制在主要和辅助 Hyper-V 主机服务器之间发生。 复制的数据保留在本地站点和网络中，而不发送到 Azure。 阅读有关[隐私](#privacy-information-for-site-recovery)的详细信息。

![E2E 拓扑](./media/site-recovery-vmm-to-vmm/architecture.png)

### <a name="data-privacy-overview"></a>数据隐私概述

下表汇总了如何在此方案中存储数据：

- - -
| 操作 | **详细信息** | **收集的数据** | **使用** | **必需** |
| --- | --- | --- | --- | --- |
| **注册** | 在恢复服务保管库中注册 VMM 服务器。 如果以后想要取消注册服务器，可通过从 Azure 门户删除服务器信息来实现此目的。 | 注册 VMM 服务器之后，站点恢复将收集、处理和传输有关 VMM 服务器的元数据以及站点恢复检测到的 VMM 云的名称。 | 数据用于识别相应的 VMM 服务器并与其通信，以及配置相应 VMM 云的设置。 | 这是必需的功能。 如果不想要将此信息发送到站点恢复，则不应使用站点恢复服务。 |
| **启用复制** | Azure Site Recovery 提供程序安装在 VMM 服务器上，是用于与站点恢复服务通信的通道。 该提供程序是 VMM 进程中承载的一个动态链接库 (DLL)。 在安装该提供程序后，会在 VMM 管理员控制台中启用“数据中心恢复”功能。 新的和现有的 VM 可以启用此功能来为 VM 提供保护。 |设置此属性后，提供程序会将 VM 的名称和 ID 发送到站点恢复。  复制是通过 Windows Server 2012 或 Windows Server 2012 R2 Hyper-V 副本启用的。 虚拟机数据将从一台 Hyper-V 主机复制到另一台主机（通常位于一个不同的“恢复”数据中心内）。 |站点恢复使用元数据填充 Azure 门户中的 VM 信息。 | 此功能是服务必不可少的组成部分，不可关闭。 如果你不想要发送此信息，请不要为 VM 启用站点恢复保护。 请注意，该提供程序发送到站点恢复的所有数据都是通过 HTTPS 发送的。 |
| **恢复计划** | 恢复计划可帮助你为恢复数据中心构建业务流程计划。 你可以定义多个 VM 或一组虚拟机在恢复站点上启动时应当遵循的顺序。 你还可以指定在恢复每个 VM 时要运行的任何自动脚本，或者要采取的任何手动操作。 通常会在恢复计划级别触发故障转移以实现协调的恢复。 | 站点恢复将收集、处理和传输恢复计划的元数据，包括虚拟机元数据以及任何自动化脚本和手动操作说明的元数据。 |元数据用于在 Azure 门户中构建恢复计划。 |此功能是服务必不可少的组成部分，不可关闭。 如果不想要将此信息发送到站点恢复，请不要创建恢复服务。 |
| **网络映射** | 将主数据中心内的网络信息映射到恢复数据中心。 当在恢复站点上恢复 VM 时，网络映射可帮助建立网络连接。 |站点恢复将收集、处理和传输每个站点（主站点和数据中心）的逻辑网络的元数据。 |元数据用于填充网络设置，以便可以映射网络信息。 | 此功能是服务必不可少的组成部分，不可关闭。 如果不想要将此信息发送到站点恢复，请不要使用网络映射。 |
| **故障转移（计划内/计划外/测试）** | 故障转移将 VM 从 VMM 管理的一个数据中心故障转转到另一个数据中心。 故障转移操作是在 Azure 门户中手动触发的。 |VMM 服务器上的提供程序将从站点恢复那里收到故障转移事件通知，并通过 VMM 接口在 Hyper-V 主机上运行故障转移操作。 VM 的实际故障转移是从一台 Hyper-V 主机到另一台主机，并通过 Windows Server 2012 或 Windows Server 2012 R2 Hyper-V 副本来处理。 Site Recovery 使用发送的信息来填充 Azure 门户中故障转移操作信息的状态。 | 此功能是服务必不可少的组成部分，不可关闭。 如果不想要将此信息发送到站点恢复，请不要使用故障转移。 |

## <a name="azure-prerequisites"></a>Azure 先决条件
下面是在 Azure 中部署此方案所需的项：

| **先决条件** | **详细信息** |
| --- | --- |
| **Azure** |需要一个 [Microsoft Azure](http://azure.microsoft.com/) 帐户。 你可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。 [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 定价。 |

## <a name="on-premises-prerequisites"></a>本地先决条件
下面是在主要和辅助本地站点中部署此方案所要满足的条件：

| **先决条件** | **详细信息** |
| --- | --- |
| **VMM** | 建议在主站点和辅助站点中各部署一个 VMM 服务器。<br/><br/> 可以[在单个 VMM 服务器上的云之间复制](site-recovery-single-vmm.md)。 为此，至少需要在 VMM 服务器上配置两个云。<br/><br/> VMM 服务器应当至少运行具有最新更新的 System Center 2012 SP1。<br/><br/> 每个 VMM 服务器必须有一个或多个云。 必须为所有云设置 Hyper-V 容量配置文件。 <br/><br/>云必须包含一个或多个 VMM 主机组。<br/><br/> [详细了解](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)设置 VMM 云的信息。<br/><br/> VMM 服务器需要 Internet 访问权限。 |
| **Hyper-V** | Hyper-V 服务器必须至少运行具有 Hyper-V 角色且安装了最新更新的 Windows Server 2012。<br/><br/> Hyper-V 服务器应包含一个或多个 VM。<br/><br/>  Hyper-V 主机服务器应位于主要和辅助 VMM 云中的主机组内。<br/><br/> 如果在 Windows Server 2012 R2 上的群集中运行 Hyper-V，应安装[更新 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> 如果在 Windows Server 2012 上的群集中运行 Hyper-V，请注意，如果使用基于静态 IP 地址的群集，将不会自动创建群集代理。 需要手动配置群集代理。 [了解详细信息](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。 |
| **提供程序** | 在 Site Recovery 部署期间，需要在 VMM 服务器上安装 Azure Site Recovery 提供程序。 提供程序通过 HTTPS 443 与 Site Recovery 通信，以协调复制。 数据复制是通过 LAN 或 VPN 连接在主要和辅助 Hyper-V 服务器之间发生的。<br/><br/> 在 VMM 服务器上运行的提供程序需要访问以下 URL：<br/><br/> ``*.accesscontrol.windows.net``<br/><br/> ``*.backup.windowsazure.com``<br/><br/> ``*.hypervrecoverymanager.windowsazure.com``<br/><br/>  ``*.store.core.windows.net``<br/><br/> ``*.blob.core.windows.net`` <br/><br/> ``https://www.msftncsi.com/ncsi.txt``<br/><br/> ``time.windows.com``<br/><br/> ``time.nist.gov``<br/><br/> 允许从 VMM 服务器到 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)的防火墙通信，并允许 HTTPS (443) 协议。 |

## <a name="prepare-for-deployment"></a>准备部署
若要准备部署，需要：

1. [准备 VMM 服务器](#prepare-the-vmm-server) 以便进行站点恢复部署。
2. [准备网络映射](#prepare-for-network-mapping)。 设置网络，以便能够配置网络映射。

### <a name="prepare-the-vmm-server"></a>准备 VMM 服务器
确保 VMM 服务器符合[先决条件](#on-premises-prerequisites)并可以访问所列的 URL。

### <a name="prepare-for-network-mapping"></a>准备网络映射
网络映射在主要和辅助 VMM 服务器上的 VMM VM 网络之间进行映射：

* 故障转移后，以最佳方式在辅助 Hyper-V 主机上放置副本 VM。
* 将副本 VM 连接到适当的 VM 网络。
* 如果不配置网络映射，则故障转移之后，副本 VM 将不会连接到任何网络。
* 如果想要在 Site Recovery 部署期间设置网络映射，则需要执行以下操作：

  * 确保源 Hyper-V 主机服务器上的 VM 已连接到 VMM VM 网络。 该网络应当该链接到与该云相关联的逻辑网络。
  * 验证用于恢复的辅助云中是否配置了相应的 VM 网络。 该 VM 网络应该链接到与辅助云关联的逻辑网络。
* [详细了解](site-recovery-network-mapping.md) 网络映射的工作原理。

## <a name="prepare-for-deployment-with-a-single-vmm-server"></a>准备使用单个 VMM 服务器进行部署

如果只有一个 VMM 服务器，可以在 VMM 云中将 Hyper-V 主机上的 VM 复制到 [Azure](site-recovery-vmm-to-azure.md)，或复制到辅助 VMM 云。 建议使用第一个选项，因为云之间的复制不是无缝的，但是，若要这样做，需要执行以下操作：

1. **在 Hyper-V VM 上设置 VMM**。 为此，建议将 VMM 所用的 SQL Server 实例共置到同一 VM 上。 这可以节省时间，因为只需创建一个 VM。 如果希望使用 SQL Server 的远程实例，而此时发生了中断，则需先恢复该实例，然后再恢复 VMM。
2. **确保该 VMM 服务器至少配置了两个云**。 一个云将包含你要复制的 VM，另一个云将充当辅助位置。 包含要保护的 VM 的云应该满足[先决条件](#on-premises-prerequisites)。
3. 如本文中所述设置站点恢复。 在保管库中创建并注册 VMM 服务器、设置复制策略，然后启用复制。 应该指定初始复制要通过网络进行。
4. 设置网络映射时，应将主云的 VM 网络映射到辅助云的 VM 网络。
5. 在 Hyper-V 管理器控制台中，在包含 VMM VM 的 Hyper-V 主机上启用 Hyper-V 副本，在 VM 上启用复制。 请确保不将 VMM 虚拟机添加到受 Site Recovery 保护的云中，使得 Hyper-V 副本设置不会被 Site Recovery 重写。
6. 如果为故障转移创建恢复方案，应为源和目标使用同一个 VMM 服务器。
7. 按如下所述进行故障转移和恢复：

   * 使用 Hyper-V 管理器通过计划的故障转移将 VMM VM 手动故障转移到辅助站点。
   * 故障转移 VM。
   * 恢复主 VMM VM 之后，请登录到 Azure 门户 -> 恢复服务保管库，然后运行从辅助站点到主站点的计划外 VM 故障转移。
   * 完成非计划的故障转移之后，可再次通过主站点访问所有资源。

### <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“新建” > “管理” > “恢复服务”。 或者，可以单击“浏览” > “恢复服务”保管库>“添加”。

    ![新保管库](./media/site-recovery-vmm-to-vmm/new-vault3.png)
3. 在“名称”中，指定一个友好名称以标识该保管库。 如果你有多个订阅，请选择其中一个。
4. [创建新资源组](../azure-resource-manager/resource-group-template-deploy-portal.md)或选择现有的资源组，并指定 Azure 区域。 计算机将复制到此区域。 若要查看受支持的区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”
5. 如果要从仪表板快速访问保管库，请单击“固定到仪表板” > “创建保管库”。

    ![新保管库](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

新保管库将显示在“仪表板” > “所有资源”中，以及“恢复服务保管库”主边栏选项卡上。

## <a name="getting-started"></a>入门
Site Recovery 提供的“快速启动”体验可帮助你尽快完成部署。 “快速启动”将检查先决条件，并引导你以正确的顺序完成 Site Recovery 部署步骤。

在“快速启动”中，选择要复制的计算机类型，以及要复制到的位置。 设置本地服务器，创建复制策略并执行容量规划。 设置好基础结构之后，可以启用 VM 复制。 可对特定计算机运行故障转移，或创建恢复计划以故障转移多个计算机。

选择部署 Site Recovery 的方式，以开始“快速启动”。 根据复制要求，“快速启动”流程会稍有不同。

## <a name="step-1-choose-your-protection-goal"></a>步骤 1：选择保护目标
选择要复制的内容以及要复制到的位置。

1. 在“恢复服务保管库”中选择你的保管库，然后单击“设置”。
2. 在“设置” > “快速启动”中，单击“Site Recovery” > “步骤 1: 准备基础结构” > “保护目标”。
3. 在“保护目标”中选择“到恢复站点”，然后选择“是，使用 Hyper-V”。
4. 选择“是”指明要使用 VMM 来管理 Hyper-V 主机，如果有辅助 VMM 服务器，请选择“是”。 如果要在单个 VMM 服务器上的云之间部署复制，请单击“否”。 。

    ![选择目标](./media/site-recovery-vmm-to-vmm/choose-goals.png)

## <a name="step-2-set-up-the-source-environment"></a>步骤 2：设置源环境
在 VMM 服务器上安装 Azure Site Recovery 提供程序，并在保管库中注册该服务器。

1. 单击“步骤 2: 准备基础结构” > “源”。

    ![设置源](./media/site-recovery-vmm-to-vmm/goals-source.png)
2. 在“准备源”中单击“+VMM”以添加 VMM 服务器。

    ![设置源](./media/site-recovery-vmm-to-vmm/set-source1.png)
3. 在“添加服务器”中，检查“System Center VMM 服务器”是否出现在“服务器类型”中，以及 VMM 服务器是否符合[先决条件和 URL 要求](#on-premises-prerequisites)。
4. 下载 Azure Site Recovery 提供程序安装文件。
5. 下载注册密钥。 运行安装程序时需要用到此密钥。 生成的密钥有效期为 5 天。

    ![设置源](./media/site-recovery-vmm-to-vmm/set-source3.png)
6. 在 VMM 服务器上安装 Azure Site Recovery 提供程序。

> [!NOTE]
> 不需要在 Hyper-V 主机服务器上显式安装任何组件。
>
>

### <a name="set-up-the-azure-site-recovery-provider"></a>安装 Azure Site Recovery 提供程序
1. 在每个 VMM 服务器上运行提供程序安装文件。 如果 VMM 部署到群集中并且你是首次安装该提供程序，请将其安装在一个活动节点上并完成安装以在保管库中注册 VMM 服务器。 然后在其他节点上安装该提供程序。 所有群集节点应运行相同版本的提供程序。
2. 安装程序将运行几项先决条件检查，并请求停止 VMM 服务的权限。 VMM 服务将在安装程序完成时自动重新启动。 如果在 VMM 群集上进行安装，系统会提示停止群集角色。
3. 在“Microsoft 更新”中，可以选择进行更新，以便根据 Microsoft 更新策略安装提供程序更新。
4. 在“安装”中接受或修改默认提供程序安装位置，然后单击“安装”。

    ![安装位置](./media/site-recovery-vmm-to-vmm/provider-location.png)
5. 安装完成后，单击“注册”，以便在保管库中注册服务器。

    ![安装位置](./media/site-recovery-vmm-to-vmm/provider-register.png)
6. 在“保管库名称”中，验证将要在其中注册服务器的保管库的名称。 单击“资源组名称” 的 Azure 数据工厂。

    ![服务器注册](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)
7. 在“Internet 连接”中，指定在 VMM 服务器上运行的提供程序如何连接到 Internet。 **使用现有代理设置进行连接** ”以使用服务器上配置的默认 Internet 连接设置。

    ![Internet 设置](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

   * 如果希望使用自定义代理，则应当在安装该提供程序之前设置它。 当配置自定义代理设置时，会运行测试来检查代理连接。
   * 如果确实使用自定义代理，或者默认代理要求进行身份验证，则需要输入代理详细信息，包括代理地址和端口。
   - 验证是否可从 VMM 服务器和 Hyper-V 主机访问所需的 URL。
   * 如果使用自定义代理，则系统将使用指定的代理凭据自动创建一个 VMM 运行方式帐户 (DRAProxyAccount)。 对代理服务器进行配置以便该帐户可以成功通过身份验证。 可以在 VMM 控制台中修改 VMM 运行身份帐户设置。 若要执行此操作，请打开“设置”工作区，展开“安全性”，单击“运行身份帐户”，然后修改 DRAProxyAccount 的密码。 需要重新启动 VMM 服务才能使此设置生效。
8. 在“ **注册密钥**”中，选择你从 Azure Site Recovery 下载并复制到 VMM 服务器的密钥。
9. 仅当你要将 VMM 云中的 Hyper-V VM 复制到 Azure 时，才使用加密设置。 如果要复制到辅助站点，则不使用加密设置。
10. 在“服务器名称”中，指定一个友好名称以在保管库中标识该 VMM 服务器。 在群集配置中，请指定 VMM 群集角色名称。
11. 在“同步云元数据”中，选择是否要将 VMM 服务器上所有云的元数据与保管库进行同步。 此操作在每个服务器上只需执行一次。 如果不想要同步所有云，可以将此设置保留为未选中状态并在 VMM 控制台中的云属性中分别同步各个云。
12. 单击“下一步”以完成此过程。 注册后，Azure Site Recovery 将检索 VMM 服务器中的元数据。 服务器显示在保管库中“服务器”页上的“VMM 服务器”选项卡中。

    ![服务器](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)
13. 当服务器出现在 Site Recovery 控制台中后，请在“源” > “准备源”中选择 VMM 服务器，然后选择 Hyper-V 主机所在的云。 。

#### <a name="command-line-installation"></a>命令行安装
可通过以下命令行来安装 Azure Site Recovery 提供程序。 此命令可用来将提供程序安装在 Server Core for Windows Server 2012 R2 上。

1. 将提供程序安装文件和注册密钥下载到某个文件夹中。 例如 C:\ASR。
2. 停止 System Center Virtual Machine Manager 服务。
3. 从提升的命令提示符处，运行以下命令提取提供程序安装程序：

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. 运行以下命令来安装提供程序：

        C:\ASR> setupdr.exe /i
5. 然后运行以下命令在保管库中注册服务器：

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

其中的参数如下：

* **/Credentials**：用于指定注册密钥文件所在位置的必需参数  
* **/FriendlyName**：在 Azure Site Recovery 门户中显示的 Hyper-V 主机服务器名称的必需参数。
* **/EncryptionEnabled**：可选参数，只会在从 VMM 复制到 Azure 时用到。
* **/proxyAddress**：可选参数，用于指定代理服务器的地址。
* **/proxyport**：可选参数，用于指定代理服务器的端口。
* **/proxyUsername**：可选参数，用于指定代理用户名（如果代理要求身份验证）。
* **/proxyPassword**：可选参数，用于指定密码，以便通过代理服务器进行身份验证（如果代理服务器要求身份验证）。  

## <a name="step-3-set-up-the-target-environment"></a>步骤 3：设置目标环境
选择目标 VMM 服务器和云。

1. 单击“准备基础结构” > “目标”，然后选择要使用的目标 VMM 服务器。
2. 此时会显示该服务器上已与站点恢复同步的云。 选择目标云。

   ![目标](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="step-4-set-up-replication-settings"></a>步骤 4：设置复制设置
1. 若要创建新的复制策略，请单击“准备基础结构” > “复制设置” > “+创建和关联”。

    ![网络](./media/site-recovery-vmm-to-vmm/gs-replication.png)
2. 在“创建和关联策略”中指定策略名称。 源和目标类型应为 **Hyper-V**。
3. 在“Hyper-V 主机版本”中，选择主机上运行的操作系统。

   > [!NOTE]
   > VMM 云可以包含运行不同（支持）版本的 Windows Server 的 Hyper-V 主机，但复制策略将应用到运行相同操作系统的主机。 如果主机运行多个操作系统版本，请创建不同的复制策略。
   >
   >
4. 在“身份验证类型”和“身份验证端口”中，指定如何对主 Hyper-V 主机服务器和恢复 Hyper-V 主机服务器之间的流量进行身份验证。 除非你有正常运行的 Kerberos 环境，否则请选择“证书”。 Azure Site Recovery 将为 HTTPS 身份验证自动配置证书。 无需手动执行任何操作。 默认情况下，端口 8083 和 8084（用于证书）在 Hyper-V 主机服务器上的 Windows 防火墙中将处于打开状态。 如果选择“Kerberos”，将使用 Kerberos 票证执行主机服务器的相互身份验证。 请注意，此设置仅适用于在 Windows Server 2012 R2 上运行的 Hyper-V 主机服务器。
5. 在“复制频率” 中，指定要在初始复制后复制增量数据的频率（每隔 30 秒、5 或 15 分钟）。
6. 在“恢复点保留期”中，针对每个恢复点指定保留期的时长（以小时为单位）。 受保护的计算机可以恢复到某个时段内的任意时间点。
7. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（1-12 小时）。 Hyper-V 使用两种类型的快照 — 标准快照，它提供整个虚拟机的增量快照；与应用程序一致的快照，它生成虚拟机内的应用程序数据的时间点快照。 与应用程序一致的快照使用卷影复制服务 (VSS) 来确保应用程序在拍摄快照时处于一致状态。 如果启用了与应用程序一致的快照，它将影响在源虚拟机上运行的应用程序的性能。 请确保你设置的值小于你配置的额外恢复点的数目。
8. 在“数据传输压缩”中，指定是否应压缩所传输的复制数据。
9. 选择“删除副本 VM”，指定禁用对源 VM 的保护时应删除副本虚拟机。 如果启用此设置，当你禁用对源 VM 的保护时，将从站点恢复控制台中删除该 VM、在 VMM 控制台中删除 VMM 的站点恢复设置，并删除副本。
10. 如果要通过网络进行复制，请在“初始复制方法”中指定是要启动还是计划初始复制。 若要节省网络带宽，可以将它计划在非高峰时间运行。 。

     ![复制策略](./media/site-recovery-vmm-to-vmm/gs-replication2.png)
11. 当创建新策略时，该策略自动与 VMM 云关联。 在“复制策略”中单击“确定”。 可以在“设置” > “复制”“策略名称”>“关联 VMM 云”中，将其他 VMM 云（及其中的 VM）与此复制策略相关联。

     ![复制策略](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### <a name="prepare-for-offline-initial-replication"></a>为脱机初始复制做准备
可以使用脱机复制来执行初始数据复制。 可按如下所述做好准备：

* 在源服务器上，需要指定从中进行数据导出的路径位置。 在导出路径上分配对 NTFS 的“完全控制”权限和对 VMM 服务的“共享”权限。 在目标服务器上，指定要从中导入数据的路径位置。 在此路径上分配同样的权限。
* 如果导入或导出路径是共享的，请为共享路径所在的远程计算机上的 VMM 服务帐户分配 Administrator、Power User、Print Operator 或 Server Operator 组成员资格。
* 如果使用任何运行方式帐户来添加主机，请在 VMM 中在导入和导出路径上为运行方式帐户分配读取和写入权限。
* 导入和导出共享不应当位于用作 Hyper-V 主机服务器的任何计算机上，因为 Hyper-V 不支持环回配置。
* 在 Active Directory 中，在包含你要保护的虚拟机的每个 Hyper-V 主机服务器上，启用并配置约束委派以信任导入和导出路径所在的远程计算机，如下所述：
  1. 在域控制器上，打开“Active Directory 用户和计算机”。
  2. 在控制台树中，单击“域名” > “计算机”。
  3. 右键单击 Hyper-V 主机服务器名称 >“属性”。
  4. 在“委托”选项卡上，单击“仅信任此计算机来委派指定的服务”。
  5. 单击“使用任意身份验证协议”。
  6. 单击“添加” > “用户和计算机”。
  7. 键入托管导入路径的计算机的名称，然后单击“确定”。 在可用服务列表中，按住 CTRL 键的同时单击“cifs” >  单击“确定”。 针对承载着导入路径的主机的名称重复该步骤。 根据需要针对其他 Hyper-V 主机服务器重复该步骤。

### <a name="configure-network-mapping"></a>配置网络映射
设置源与目标网络之间的网络映射。

* [阅读此文](#prepare-for-network-mapping)获取网络映射的快速概述。 此外，请[阅读此文](site-recovery-network-mapping.md)获取更深入的说明。
* 验证 VMM 服务器上的虚拟机是否已连接到 VM 网络。

按如下所述配置映射：

1. 在“设置” > “Site Recovery 基础结构” > “网络映射” > “网络映射”中，单击“+网络映射”。

    ![网络映射](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. 在“添加网络映射”选项卡中，选择源和目标 VMM 服务器。 系统将检索与 VMM 服务器关联的 VM 网络。
3. 在“源网络”中，从与主 VMM 服务器关联的 VM 网络列表中选择要使用的网络。
4. 在“目标网络”中，选择要在辅助 VMM 服务器上使用的网络。 。

    ![网络映射](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

下面是网络映射开始时发生的事情：

* 与源 VM 网络对应的任何现有副本虚拟机都将连接到目标 VM 网络。
* 在复制后，连接到源 VM 网络的新虚拟机将连接到目标映射网络。
* 如果你修改了与新网络之间的映射，则会使用新设置来连接副本虚拟机。
* 如果目标网络具有多个子网，并且其中一个子网与源虚拟机所在的子网同名，则在故障转移后副本虚拟机将连接到该目标子网。 如果没有具有匹配名称的目标子网，则虚拟机将连接到网络中的第一个子网。

### <a name="configure-storage-mapping"></a>配置存储映射
默认情况下，将 Hyper-V VM 复制到目标 Hyper-V 服务器时，复制的数据将存储在 Hyper-V 管理器中目标 Hyper-V 主机的默认位置。 若要控制复制的数据的存储位置，可以配置存储映射。<br/><br/> 为此，请在开始部署之前，在源和目标 VMM 服务器上设置存储分类。

新的 Azure 门户目前不支持存储映射。 但是，可以使用 Powershell 启用存储映射。 [了解详细信息](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-7-configure-storage-mapping)。

## <a name="step-5-capacity-planning"></a>步骤 5：容量规划

你已经设置了基本基础结构，请考虑容量计划并确定是否需要额外的资源。

站点恢复提供基于 Excel 的 Capacity Planner 帮助你为源环境、站点恢复组件、网络和存储分配适当的资源。 可以在快速模式下运行 Planner，以便根据 VM、磁盘和存储的平均数量进行估计；或者在详细模式下运行 Planner，以输入工作负荷级别的数据。 开始之前，需要：

* 收集有关复制环境的信息，包括 VM 数、每个 VM 的磁盘数和每个磁盘的存储空间。
* 估计复制的增量数据的每日更改（变动）率。 可以使用 [Hyper-V 副本 Capacity Planner](https://www.microsoft.com/download/details.aspx?id=39057) 来帮助执行此操作。

1. 单击“下载”下载该工具，然后运行该工具。 [阅读](site-recovery-capacity-planner.md)该工具随附的文章。
2. 完成后，请在“是否已运行 Capacity Planner?”中选择“是”。

   ![容量规划](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="control-bandwidth"></a>控制带宽
在使用 Capacity Planner Hyper-V 副本收集实时的增量复制信息之后，基于 Excel 的 Capacity Planner 工具可帮助计算复制（初始和增量）所需的带宽。 若要控制用于复制的带宽量，可以使用组策略或 Windows PowerShell 来配置 NetQos 策略。 可通过多种方式来执行此操作：

| **PowerShell** | **详细信息** |
| --- | --- |
| **New -NetQosPolicy "QoS to destination subnet"** |限制从运行 Windows Server 2012 R2 的 Hyper-V 主机发往辅助子网的流量。 如果主子网和辅助子网不同，请使用此方法。 |
| **New -NetQosPolicy "QoS to destination port"** |限制从运行 Windows Server 2012 R2 的 Hyper-V 主机发往目标端口的流量。 |
| **New -NetQosPolicy "Throttle traffic from VMM"** |限制来自 vmms.exe 的流量。 这将限制 Hyper-V 流量和实时迁移流量。 可以通过匹配 IP 协议和端口来进行细化。 |

可以使用带宽权重设置，或按每个辅助站点的位数来限制流量。 如果使用群集，需要在所有群集节点上执行此操作。 有关详细信息，请参阅：

* Thomas Maurer 的博客 [Throttling Hyper-V Replica Traffic](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)（限制 Hyper-V 复本流量）
* 有关 [New-NetQosPolicy cmdlet](https://technet.microsoft.com/library/hh967468.aspx) 的更多信息。

## <a name="step-6-enable-replication"></a>步骤 6：启用复制
现在，请按如下所述启用复制：

1. 单击“步骤 2: 复制应用程序” > “源”。 首次启用复制后，请在保管库中单击“+复制”，对其他计算机启用复制。

    ![启用复制](./media/site-recovery-vmm-to-vmm/enable-replication1.png)
2. 在“源”中，选择 VMM 服务器和要复制的 Hyper-V 主机所在的云。 。

    ![启用复制](./media/site-recovery-vmm-to-vmm/enable-replication2.png)
3. 在“目标”中，确认辅助 VMM 服务器和云。
4. 在“虚拟机”中，从列表中选择要保护的 VM。

    ![启用虚拟机保护](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

可以在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”操作的进度。 在“完成保护”作业运行之后，虚拟机就可以进行故障转移了。

> [!NOTE]
> 你还可以在 VMM 控制台中为虚拟机启用保护。 在工具栏上的虚拟机属性的“Azure Site Recovery”选项卡中单击“启用保护”。
>
>

启用复制后，可以在“设置” > “复制的项”中查看 VM 的属性。 在“概要”仪表板中，可以看到有关 VM 复制策略及其状态的信息。 单击“属性”可查看详细信息。

### <a name="onboard-existing-virtual-machines"></a>将现有虚拟机加入进来
如果在 VMM 中已有使用 Hyper-V 副本进行复制的虚拟机，可以将它们加入到 Azure Site Recovery 复制中，如下所述：

1. 确保托管现有 VM 的 Hyper-V 服务器位于主云中，托管副本虚拟机的 Hyper-V 服务器位于辅助云中。
2. 确保为主 VMM 云配置了复制策略。
3. 为主虚拟机启用复制。 Azure Site Recovery 和 VMM 将确保检测到相同的副本主机和虚拟机，并且 Azure Site Recovery 将使用指定的设置重用并重新建立复制。

## <a name="step-7-test-your-deployment"></a>步骤 7：测试部署
为了对部署进行测试，可以针对单个虚拟机运行测试故障转移，或者创建包含一个或多个虚拟机的恢复计划。

### <a name="prepare-for-failover"></a>准备故障转移
* 若要全面测试部署，需要一个基础结构，使复制的计算机能够按预期工作。 如果要测试 Active Directory 和 DNS，可以通过 DNS 创建虚拟机作为域控制器，并使用 Azure Site Recovery 将此虚拟机复制到 Azure。 在 [Active Directory 测试性故障转移注意事项](site-recovery-active-directory.md#test-failover-considerations)中了解详细信息。
* 此文中的说明介绍了如何不使用网络来运行测试故障转移。 此选项将测试 VM 故障转移，但不测试 VM 的网络设置。 [详细了解](site-recovery-failover.md#run-a-test-failover)其他选项。
* 如果要运行非计划的故障转移而不是测试故障转移，请注意：

  * 在运行非计划的故障转移之前，请尽可能关闭主计算机。 这样可确保不会同时运行源计算机和副本计算机。
  * 运行非计划的故障转移时，将会停止从主计算机复制数据，因此在非计划的故障转移开始以后，将不会传输任何数据增量。 此外，如果针对恢复计划运行非计划的故障转移，则即使发生错误，该计划也会一直运行到完成。

### <a name="run-a-test-failover"></a>运行测试故障转移
1. 若要故障转移单个 VM，请在“设置” > “复制的项”中，单击“VM”>“+测试故障转移”。

    ![测试故障转移](./media/site-recovery-vmm-to-vmm/test-failover.png)
2. 若要故障转移某个恢复计划，请在“设置” > “恢复计划”中，右键单击该计划 >“测试性故障转移”。 若要创建恢复计划，请[遵循这些说明](site-recovery-create-recovery-plans.md)。
3. 在“测试故障转移”中，选择“无”。 使用此选项可测试 VM 是否按预期进行故障转移，但复制的 VM 不会连接到任何网络。

    ![测试故障转移](./media/site-recovery-vmm-to-vmm/test-failover1.png)
4. 单击“确定”开始故障转移。 若要跟踪进度，可单击 VM 打开其属性，或者在“设置” > “作业” > “Site Recovery 作业”中选择“测试故障转移”作业。
5. 当故障转移作业达到“完成测试”阶段时，请执行以下操作：

   * 查看辅助 VMM 云中的副本 VM。
   * 单击“完成测试”以完成测试故障转移。
   * 单击“说明”以记录并保存与测试故障转移相关联的任何观测结果。
6. 将在副本虚拟机所在的同一主机上创建测试虚拟机。 它将被添加到副本虚拟机所在的同一个云中。
7. 确认 VM 已成功启动后，单击“测试故障转移已完成”。 在此阶段，将删除 Site Recovery 在测试故障转移期间自动创建的所有元素。  

   > [!NOTE]
   > 如果测试故障转移持续了两周以上，系统会强行将其结束。
   >
   >

### <a name="update-dns-with-the-replica-vm-ip-address"></a>使用副本 VM IP 地址更新 DNS
故障转移之后，副本 VM 的 IP 地址可能与主虚拟机不同。

* 虚拟机在启动后将更新它们使用的 DNS 服务器。
* 也可以按如下所述手动更新 DNS：

#### <a name="retrieve-the-ip-address"></a>检索 IP 地址
运行此示例脚本来检索 IP 地址。

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="update-dns"></a>更新 DNS
运行此示例脚本来更新 DNS 并指定你通过前一个示例脚本检索到的 IP 地址。

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>后续步骤
设置并运行部署后，请[详细了解](site-recovery-failover.md)不同类型的故障转移。



<!--HONumber=Nov16_HO4-->


