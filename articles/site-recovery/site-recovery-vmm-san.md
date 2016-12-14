---
title: "通过 Azure Site Recovery 使用 SAN 将 VMM 云中的 Hyper-V VM 复制到辅助站点 | Microsoft 文档"
description: "本文介绍了如何通过 Azure Site Recovery 使用 SAN 复制在两个站点之间复制 Hyper-V 虚拟机。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: eb480459-04d0-4c57-96c6-9b0829e96d65
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1a78f00c1a47438280facd736ec9c374885d1147


---
# <a name="replicate-hyper-v-vms-in-a-vmm-cloud-to-a-secondary-site-with-azure-site-recovery-using-san"></a>通过 Azure Site Recovery 使用 SAN 将 VMM 云中的 Hyper-V VM 复制到辅助站点
在本文中，你将了解如何部署站点恢复以安排和自动化 SAN 复制以及位于 System Center VMM 云中的 Hyper-V 虚拟机故障转移到辅助 VMM 站点。

阅读完本文后，请在本文底部或 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)发表任何评论或提问。 

## <a name="overview"></a>概述
组织需要制定业务连续性和灾难恢复 (BCDR) 策略来确定应用、工作负荷和数据如何在计划和非计划停机期间保持运行和可用，并尽快恢复正常运行情况。 BCDR 策略的重点在于，发生灾难时提供确保业务数据的安全性和可恢复性以及工作负荷的持续可用性的解决方案。

站点恢复是一项 Azure 服务，可以通过协调从本地物理服务器和虚拟机到云 (Azure) 或辅助数据中心的的复制，来为 BCDR 策略提供辅助。 当主要位置发生故障时，你可以故障转移到辅助站点，使应用和工作负荷保持可用。 当主要位置恢复正常时，你可以故障转移回到主要位置。 站点恢复可用于许多方案，并可保护许多工作负荷。 在 [什么是 Azure Site Recovery？](site-recovery-overview.md)

本文说明了使用 SAN 复制设置从一个 VMM 站点到其他 VMM 站点的 Hyper-V 虚拟机的复制。 本文包含架构概述、部署先决条件和说明。 你可以发现和分类 VMM 中的 SAN 存储，设置 LUN，以及向 Hyper-V 群集分配存储。 本文最后指导你测试故障转移，以确保一切都按预期进行。

## <a name="why-replicate-with-san"></a>为何使用 SAN 进行复制？
以下是此方案所提供的优势：

* 提供可由 Site Recovery 自动实现的企业可缩放复制解决方案。
* 利用企业存储合作伙伴通过光纤通道和 iSCSI 存储提供的 SAN 复制功能。 请查看我们的 [SAN 存储合作伙伴](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)。
* 利用现有的 SAN 基础结构来保护 Hyper-V 群集中部署的任务关键型应用程序。
* 为来宾群集提供支持。
* 确保使用 RTO 和 RPO 较低的同步复制以及灵活性较高的异步复制（取决于存储阵列功能），在不同的应用程序层实现复制一致性。  
* 与 VMM 的集成可以在 VMM 控制台中提供 SAN 管理，VMM 中的 SMI-S 可发现现有的存储。  

## <a name="architecture"></a>体系结构
此方案会通过使用 SAN 复制将 Hyper-V 虚拟机从一个本地 VMM 站点备份到另一个站点，保护你的工作负载。

![SAN 体系结构](./media/site-recovery-vmm-san/architecture.png)

此方案中的组件包括：

* **本地虚拟机** - 在 VMM 私有云中管理的本地 Hyper-V 服务器包含你要保护的虚拟机。
* **本地 VMM 服务器** - 可以在要保护的主站点以及辅助站点上运行一个或多个 VMM 服务器。
* **SAN 存储** - 主站点和辅助站点中各有一个 SAN 阵列
* **Azure Site Recovery 保管库** - 保管库协调和安排本地站点之间的数据副本、故障转移和恢复。
* **Azure Site Recovery 提供程序** - 提供程序安装在每个 VMM 服务器上。

## <a name="before-you-start"></a>开始之前
确保已满足以下先决条件：

| **先决条件** | **详细信息** |
| --- | --- |
| **Azure** |你将需要一个 [Microsoft Azure](https://azure.microsoft.com/) 帐户。 你可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。 [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 定价。 |
| **VMM** |你至少需要一台部署为单独的物理或虚拟服务器（或虚拟群集）的 VMM 服务器。 <br/><br/>VMM 服务器应当运行具有最新累积更新的 System Center 2012 R2。<br/><br/>在主 VMM 服务器上至少配置一个你要保护的云，在辅助 VMM 服务器上配置一个你要用于保护和恢复的云<br/><br/>你要保护的源云必须包含一个或多个 VMM 主机组。<br/><br/>所有 VMM 云都必须设置 Hyper-V 容量配置文件。<br/><br/>若要详细了解如何设置 VMM 云，请参阅[配置 VMM 云结构](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)和[演练：使用 System Center 2012 SP1 VMM 创建私有云](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)。 |
| **Hyper-V** |你将需要一个或多个位于主站点和辅助站点中的 Hyper-V 群集，以及一个或多个位于源 Hyper-V 群集中的 VM。 主位置和辅助位置中的每个 VMM 主机组应具有一个或多个 Hyper-V 群集。<br/><br/>主机和目标 Hyper-V 服务器至少必须运行具有 Hyper-V 角色且安装了最新更新程序的 Windows Server 2012。<br/><br/>包含你想要保护的 VM 的任何 Hyper-V 服务器必须位于 VMM 云中。<br/><br/>如果你在群集中运行 Hyper-V，请注意，如果你具有基于静态 IP 地址的群集，则不会自动创建群集代理。 你需要手动配置群集代理。 在 Aidan Finn 的博客文章中[了解详细信息](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters)。 |
| **SAN 存储** |借助 SAN 复制，你可以使用 iSCSI 或光纤通道存储或使用共享虚拟硬盘 (vhdx) 复制来宾群集虚拟机。<br/><br/>需要设置两个 SAN 阵列，一个主站点中，一个辅助站点中。<br/><br/>应该在阵列之间设置网络基础结构。 应该配置对等互连和复制。 应该根据存储阵列要求设置复制许可证。<br/><br/>应该在 Hyper-V 主机服务器与存储阵列之间设置网络，使主机能够使用 ISCSI 或光纤通道与存储 LUN 通信。<br/><br/> 请参阅[支持的存储阵列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)列表。<br/><br/>应安装存储阵列制造商提供的 SMI-S 提供程序，并且 SAN 阵列应由提供程序管理。 根据提供程序的文档设置提供程序。<br/><br/>确保阵列的 SMI-S 提供程序在 VMM 服务器可以使用 IP 地址或 FQDN 通过网络访问的服务器上。<br/><br/>每个 SAN 阵列应该包含一个或多个可在此部署中使用的存储池。主站点上的 VMM 服务器需要管理主阵列，辅助 VMM 服务器需要管理辅助阵列。<br/><br/>主站点上的 VMM 服务器应该管理主阵列，辅助 VMM 服务器应该管理辅助阵列。 |
| **网络映射** |你可以配置网络映射，以确保在故障转移后以最佳方式将复制的虚拟机放置在辅助 Hyper-V 主机服务器上，并确保它们连接到适当的 VM 网络。 如果不配置网络映射，则故障转移之后，副本 VM 将不会连接到任何网络。<br/><br/>若要在部署过程中设置网络映射，请确保源 Hyper-V 主机服务器上的虚拟机已连接 VMM VM 网络。 该网络应当链接到与云相关联的逻辑网络。<br/<br/>辅助 VMM 服务器上你用于恢复的目标云应当配置了相应的 VM 网络，并且该网络应当链接到与目标云关联的相应逻辑网络。<br/><br/>[详细了解](site-recovery-network-mapping.md)网络映射。 |

## <a name="step-1-prepare-the-vmm-infrastructure"></a>步骤 1：准备 VMM 基础结构
若要准备 VMM 基础结构，你需要：

1. 确保已设置 VMM 云
2. 集成并分类 VMM 中的 SAN 存储
3. 创建 LUN 并分配存储
4. 创建复制组
5. 设置 VM 网络

### <a name="ensure-vmm-clouds-are-set-up"></a>确保已设置 VMM 云
Site Recovery 将协调 VMM 云中 Hyper-V 主机服务器上的虚拟机的保护。 在开始部署 Site Recovery 之前，需要确保已正确设置这些云。 有关详细信息，请参阅 Keith Mayer 的博文[创建私有云](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)。

### <a name="integrate-and-classify-san-storage-in-vmm"></a>集成并分类 VMM 中的 SAN 存储
在 VMM 控制台中添加并分类 SAN：

1. 在“**结构**”工作区中，单击“**存储**”。 依次单击“**主页**” > “**添加资源**” > “**存储设备**”，启动“添加存储设备”向导。
2. 在“**选择存储提供程序类型**”页上，选择“**SMI-S 提供程序发现和管理的 SAN 和 NAS 设备**”。
   
    ![提供程序类型](./media/site-recovery-vmm-san/provider-type.png)
3. 在“**指定存储 SMI-S 提供程序的协议和地址**”页上，选择“**SMI-S CIMXML**”，并指定用于连接提供程序的设置。
4. 在“**提供程序 IP 地址或 FQDN**”和“**TCP/IP 端口**”中，指定用于连接提供程序的设置。 只能对 SMI-S CIMXML 使用 SSL 连接。
   
    ![提供程序连接](./media/site-recovery-vmm-san/connect-settings.png)
5. 在“**运行方式帐户**”中，指定可以访问提供程序的 VMM 运行方式帐户，或新建一个帐户。
6. 在“**收集信息**”页上，VMM 会自动尝试发现并导入存储设备信息。 若要重试发现，请单击“**扫描提供程序**”。 如果发现过程成功，发现的存储阵列、存储池、制造商、型号和容量将列在页中。
   
    ![发现存储](./media/site-recovery-vmm-san/discover.png)
7. 在“**选择要接受管理的存储池并分配分类**”中，选择 VMM 将要管理的存储池，并为其分配一个分类。 将从存储池导入 LUN 信息。 根据需要保护的应用程序、其容量要求以及你需要同时复制的内容创建 LUN。
   
    ![为存储分类](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>创建 LUN 并分配存储
1. 将 SAN 存储集成到 VMM 后，需要创建（设置）逻辑单元 (LUN)。
   
   * [如何选择用于在 VMM 中创建逻辑单元的方法](https://technet.microsoft.com/library/gg610624.aspx)
   * [如何在 VMM 中预配存储逻辑单元](https://technet.microsoft.com/library/gg696973.aspx)
     
     > [!NOTE]
     > 启用计算机的复制后，不应添加 VHD 到没有位于站点恢复复制组中的 LUN。 如果这样做，站点恢复将不会检测到它们。
     > 
     > 
2. 然后，向 Hyper-V 主机群集分配存储容量，使 VMM 能够将虚拟机数据部署到设置的存储中：
   
   * 在向群集分配存储之前，需要向群集所在的 VMM 主机组分配存储。 请参阅[如何向主机组分配存储逻辑单元](https://technet.microsoft.com/library/gg610686.aspx)和[如何向主机组分配存储池](https://technet.microsoft.com/library/gg610635.aspx)。</a>。
   * 然后，根据[如何在 VMM 中的 Hyper-V 主机群集上配置存储](https://technet.microsoft.com/library/gg610692.aspx)中所述，向群集分配存储容量。</a>。

### <a name="create-replication-groups"></a>创建复制组
创建一个复制组，其中包含需要一起复制的所有 LUN。

1. 在 VMM 控制台中，打开存储阵列属性的“**复制组**”选项卡，然后单击“**新建**”。
2. 然后创建复制组。
   
    ![SAN 复制组](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>设置网络
如果你要配置网络映射，请执行以下操作：

1. [了解](site-recovery-network-mapping.md)网络映射。
2. 在 VMM 中准备 VM 网络：
   
   * [设置逻辑网络](https://technet.microsoft.com/library/jj721568.aspx)。
   * [设置 VM 网络](https://technet.microsoft.com/library/jj721575.aspx)。

## <a name="step-2-create-a-vault"></a>步骤 2：创建保管库
1. 从你要注册的 VMM 服务器登录到 [管理门户](https://portal.azure.com) 。
2. 展开“**数据服务**” > “**恢复服务**”，并单击“**Site Recovery 保管库**”。
3. 单击“**新建**” > “**快速创建**”。
4. 在“名称”中，输入一个友好名称以标识此保管库。
5. 在“**区域**”中，为保管库选择地理区域。 若要查看受支持的区域，请参阅 Azure Site Recovery 价格详细信息中的“地域可用性” [](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 单击“创建保管库” 。
   
    ![新保管库](./media/site-recovery-vmm-san/create-vault.png)

检查状态栏以确认保管库已成功创建。 保管库将以“**活动**”状态列在主要的“**恢复服务**”页上。

### <a name="register-the-vmm-servers"></a>注册 VMM 服务器
1. 在“**恢复服务**”页中，打开“快速启动”页。 也可随时使用该图标打开“快速启动”。
   
    ![“快速启动”图标](./media/site-recovery-vmm-san/quick-start-icon.png)
2. 在下拉列表中，选择“**在 Hyper-V 本地站点之间使用阵列复制**”。
   
    ![注册密钥](./media/site-recovery-vmm-san/select-san.png)
3. 在“**准备 VMM 服务器**”中，下载最新版 Azure Site Recovery 提供程序安装文件。
4. 在源 VMM 服务器上运行此文件。 如果 VMM 部署到群集中并且你是首次安装该提供程序，请将其安装在一个活动节点上并完成安装以在保管库中注册 VMM 服务器。 然后在其他节点上安装该提供程序。 请注意，如果你是在升级提供程序，则需要在所有节点上进行升级，因为所有节点都应当运行相同的提供程序版本。
5. 安装程序将执行简单的“**先决条件检查**”，并请求授权停止 VMM 服务以开始安装提供程序。 VMM 服务将在安装程序完成时自动重新启动。 如果你是在 VMM 群集上进行安装，则会提示你停止群集角色。
6. 在“Microsoft 更新”中，你可以选择获取更新  。 当启用了此设置时，将根据你的 Microsoft 更新策略自动安装提供程序更新。
   
    ![Microsoft 更新](./media/site-recovery-vmm-san/ms-update.png)
7. 安装位置设置为 **<SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin**。 单击“安装”按钮，开始安装提供程序。
   
    ![InstallLocation](./media/site-recovery-vmm-san/install-location.png)
8. 安装提供程序之后，请单击“注册”按钮，以在保管库中注册服务器。
   
    ![InstallComplete](./media/site-recovery-vmm-san/install-complete.png)
9. 在“Internet 连接”中，指定在 VMM 服务器上运行的提供程序如何连接到 Internet。 选择“使用默认系统代理设置”以使用服务器上配置的默认 Internet 连接设置。
   
    ![Internet 设置](./media/site-recovery-vmm-san/proxy-details.png)
   
   * 如果希望使用自定义代理，则应当在安装该提供程序之前设置它。 当配置自定义代理设置时，会运行测试来检查代理连接。
   * 如果你确实使用自定义代理，或者你的默认代理要求进行身份验证，则需要输入代理详细信息，包括代理地址和端口。
   * 以下 URL 应可从 VMM 服务器和 Hyper-v 主机访问
     * *.hypervrecoverymanager.windowsazure.com
     * *.accesscontrol.windows.net
     * *.backup.windowsazure.com
     * *.blob.core.windows.net
     * *.store.core.windows.net
   * 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653) 中所述的 IP 地址，以及 HTTPS (443) 协议。 必须将你打算使用的 Azure 区域以及美国西部的 IP 范围加入允许列表。
   * 如果你使用自定义代理，则将使用指定的代理凭据自动创建一个 VMM 运行身份帐户 (DRAProxyAccount)。 对代理服务器进行配置以便该帐户可以成功通过身份验证。 可以在 VMM 控制台中修改 VMM 运行身份帐户设置。 若要执行此操作，请打开“设置”工作区，展开“安全性”，单击“运行身份帐户”，然后修改 DRAProxyAccount 的密码。 你将需要重新启动 VMM 服务以使此设置生效。
10. 在“**注册密钥**”中，选择你从 Azure Site Recovery 下载并复制到 VMM 服务器的密钥。
11. 在“保管库名称”中，验证将要在其中注册服务器的保管库的名称。 
    
    ![服务器注册](./media/site-recovery-vmm-san/vault-creds.png)
12. 加密设置仅适用于 VMM 到 Azure 方案，如果你是仅从 VMM 到 VMM 的用户，则可以忽略此屏幕。
    
    ![服务器注册](./media/site-recovery-vmm-san/encrypt.png)
13. 在“服务器名称”中，指定一个友好名称以在保管库中标识该 VMM 服务器。 在群集配置中，请指定 VMM 群集角色名称。
14. 在“**初始云元数据同步**”中，指定保管库中将显示的服务器的易记名称，然后选择是否要将 VMM 服务器上所有云的元数据与保管库进行同步。 此操作在每个服务器上只需执行一次。 如果你不希望同步所有云，可以将此设置保留为未选中状态并在 VMM 控制台中的云属性中分别同步各个云。
    
    ![服务器注册](./media/site-recovery-vmm-san/friendly-name.png)
15. 单击“下一步”以完成此过程。 注册后，Azure Site Recovery 将检索 VMM 服务器中的元数据。 服务器显示在保管库中“服务器”页上的“VMM 服务器”选项卡中。

### <a name="command-line-installation"></a>命令行安装
也可使用以下命令行来安装 Azure Site Recovery 提供程序。 此方法可用来将提供程序安装在 Server CORE for Windows Server 2012 R2 上。

1. 将提供程序安装文件和注册密钥下载到某个文件夹（例如 C:\ASR）中
2. 停止 System Center Virtual Machine Manager 服务
3. 使用**管理员**特权从命令提示符处运行以下命令，以便提取提供程序安装程序：
   
        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. 运行以下命令以安装提供程序：
   
        C:\ASR> setupdr.exe /i
5. 运行以下命令以注册提供程序：
   
        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

其中的参数如下：

* **/Credentials** ：用于指定注册密钥文件所在位置的必需参数  
* **/FriendlyName** ：在 Azure Site Recovery 门户中显示的 Hyper-V 主机服务器名称的必需参数。
* **/EncryptionEnabled**：只有当需要在 Azure 中以静止方式为虚拟机加密时，才需要在 VMM 到 Azure 方案中使用这个可选参数。 请确保提供的文件名具有 **.pfx** 扩展名。
* **/proxyAddress** ：可选参数，用于指定代理服务器的地址。
* **/proxyport** ：可选参数，用于指定代理服务器的端口。
* **/proxyUsername**：可选参数，用于指定代理用户名（如果代理要求身份验证的话）。
* **/proxyPassword**：可选参数，用于指定密码，以便通过代理服务器进行身份验证（如果代理要求身份验证的话）。

## <a name="step-3-map-storage-arrays-and-pools"></a>步骤 3：映射存储阵列和池
映射阵列，以指定哪个辅助存储池要从主池接收复制数据。 之所以要在配置云保护之前映射存储，是因为当你为复制组启用保护时要使用映射信息。

在开始操作之前，请检查云是否已显示在保管库中。 云检测方式分为两种：一种是在安装提供程序时选择同步所有云，另一种是在 VMM 控制台中云属性的“**常规**”选项卡上选择同步特定的云。 然后按如下所述映射存储阵列：

1. 依次单击“**资源**” > “**服务器存储**” > “**映射源和目标阵列**”。
    ![服务器注册](./media/site-recovery-vmm-san/storage-map.png)
2. 选择主站点上的存储阵列，并将其映射到辅助站点上的存储阵列。
3. 映射阵列中的源和目标存储池。 为此，请在“**存储池**”中选择要映射的源和目标存储池。
   
   ![服务器注册](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-cloud-protection-settings"></a>步骤 4：配置云保护设置
在注册 VMM 服务器后，你可以配置云保护设置。 你在安装提供程序时启用了“将云数据与保管库同步”选项，所以 VMM 服务器上的所有云都将出现在保管库中的“受保护的项”<b></b>选项卡中。

![已发布的云](./media/site-recovery-vmm-san/clouds-list.png)

1. 在“快速启动”页上，单击“为 VMM 云设置保护”。
2. 在“**受保护的项**”选项卡上，选择要配置的云，然后转到“**配置**”选项卡。 请注意：
3. 在“目标”中，选择“VMM”。
4. 在“目标位置”中，选择管理着你要用于恢复的云的现场 VMM 服务器。
5. 在“目标云”中，选择要用于源云中虚拟机故障转移的目标云。 请注意：
   * 我们建议你选择可满足你要保护的虚拟机的恢复要求的目标云。
   * 一个云只能属于一个云对 — 作为主云或目标云。
6. Azure Site Recovery 将验证云是否有权访问支持 SAN 复制的存储，并且是否为存储阵列建立了对等互连。 将显示参与的阵列对等方。
7. 如果验证成功，请选择“**复制类型**”中的“**SAN**”。

在保存设置后，将创建一个作业，可以在“作业”选项卡上监视该作业。 可以在“**配置**”选项卡上修改云设置。 如果希望修改目标位置或目标云，必须删除云配置，然后重新配置该云。

## <a name="step-5-enable-network-mapping"></a>步骤 5：启用网络映射
1. 在“快速启动”页上，单击“映射网络”。
2. 选择你要映射其中的网络的源 VMM 服务器，然后选择要将网络映射到的目标 VMM 服务器。 此时将显示源网络及其关联的目标网络的列表。 对于当前未映射的网络将显示空值。 单击源和目标网络名称旁的信息图标以查看每个网络的子网。
3. 在“**源的网络**”中选择一个网络，然后单击“**映射**”。 服务将检测目标服务器上的 VM 网络并显示它们。
   
    ![SAN 体系结构](./media/site-recovery-vmm-san/network-map1.png)
4. 在显示的对话框中，从目标 VMM 服务器选择其中一个 VM 网络。
   
    ![SAN 体系结构](./media/site-recovery-vmm-san/network-map2.png)
5. 当选择某个目标网络时，会显示使用源网络的受保护云。 此时也会显示与用于保护的云关联的可用目标网络。 建议你选择可供你用于保护的所有云使用的一个目标网络。
6. 单击复选标记以完成映射过程。 作业开始跟踪映射进度。 你可以在“**作业**”选项卡上查看该作业。

## <a name="step-6-enable-replication-for-replication-groups"></a>步骤 6：为复制组启用复制
在为虚拟机启用保护之前，需要为存储复制组启用复制。

1. 在 Azure Site Recovery 门户中，在主云的属性页上打开“**虚拟机**”选项卡。 单击“**添加复制组**”。
2. 选择与云关联的一个或多个 VMM 复制组，验证源和目标阵列，然后指定复制频率。

完成此操作后，Azure Site Recovery 以及 VMM 和 SMI-S 提供程序将设置目标站点存储 LUN，并启用存储复制。 如果复制组已复制，Azure Site Recovery 将重用现有的复制关系并更新 Azure Site Recovery 中的信息。

## <a name="step-7-enable-protection-for-virtual-machines"></a>步骤 7：为虚拟机启用保护
存储组开始复制后，请在 VMM 控制台中使用以下方法之一为虚拟机启用保护：

* **新的虚拟机** - 在新建虚拟机时，可以在 VMM 控制台中启用 Azure Site Recovery 保护，并将虚拟机与复制组相关联。
  如果选择此选项，VMM 将使用智能定位以最佳方式将虚拟机存储放置在复制组的 LUN 上。 Azure Site Recovery 将协调辅助站点上的阴影虚拟机的创建并分配容量，以便在故障转移后可以启动副本虚拟机。
* **现有虚拟机** - 如果已在 VMM 中部署虚拟机，可以启用 Azure Site Recovery 保护，并存储迁移到复制组。 完成此操作后，VMM 和 Azure Site Recovery 将检测新虚拟机，并开始在 Azure Site Recovery 中对它进行管理，以提供保护。 将在辅助站点上创建阴影虚拟机并为其分配容量，以便在故障转移后可以启动副本虚拟机。
  
    ![启用保护](./media/site-recovery-vmm-san/enable-protect.png)

为虚拟机启用保护后，它们将显示在 Azure Site Recovery 控制台中。 你可以查看虚拟机属性，跟踪状态，以及故障转移包含多个虚拟机的复制组。 请注意，在 SAN 复制中，与复制组关联的所有虚拟机必须一起故障转移。 这是因为，故障转移会先在存储层发生。 必须正确组合复制组，并只将关联的虚拟机放置在一起。

> [!NOTE]
> 启用计算机的复制后，不应添加 VHD 到没有位于站点恢复复制组中的 LUN。 如果这样做，站点恢复将不会检测到它们。
> 
> 

可以在“**作业**”选项卡中跟踪“启用保护”操作的进度，包括初始复制。 在“完成保护”作业运行之后，虚拟机就可以进行故障转移了。

![虚拟机保护作业](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>步骤 8：测试部署
测试你的部署，以确保虚拟机和数据可按预期方式故障转移。 为此，你将要通过选择复制组创建恢复计划。然后，对该计划运行测试故障转移。

1. 在“**恢复计划**”选项卡上，单击“**创建恢复计划**”。
2. 为恢复计划指定一个名称，并指定源和目标 VMM 服务器。 源服务器必须具有启用了故障转移和恢复的虚拟机。 选择“**SAN**”，仅查看为 SAN 复制配置的云。
   3.
    ![创建恢复计划](./media/site-recovery-vmm-san/r-plan.png)
3. 在“**选择虚拟机**”中，选择复制组。 将选择与复制组关联的所有虚拟机，并将其添加到恢复计划。 这些虚拟机将添加到恢复计划的默认组（组 1）中。 如果需要，你可以添加更多的组。 请注意，在复制后，各个虚拟机将根据恢复计划组的顺序启动。
   
    ![添加虚拟机](./media/site-recovery-vmm-san/r-plan-vm.png)
4. 在创建恢复计划后，它将出现在“**恢复计划**”选项卡上的列表中。
5. 在“**恢复计划**”选项卡上，选择该计划并单击“**测试故障转移**”。
6. 在“**确认测试故障转移**”页面上，选择“**无**”。 请注意，当启用了此选项时，故障转移后的副本虚拟机不会连接到任何网络。 这将测试虚拟机是否按预期进行故障转移，但是不会测试你的复制网络环境。 有关如何使用不同网络选项的详细信息，请查看[如何运行测试故障转移](site-recovery-failover.md#run-a-test-failover)。

    ![选择测试网络](./media/site-recovery-vmm-san/test-fail1.png)

1. 将在副本虚拟机所在的同一主机上创建测试虚拟机。 它不会被添加到副本虚拟机所在的云中。
2. 在复制之后，副本虚拟机将具有与主虚拟机的 IP 地址不同的 IP 地址。 如果你是通过 DHCP 颁发地址，则 DNS 将自动更新。 如果你没有使用 DHCP，并且希望确保地址相同，则需要运行两个脚本。
3. 运行此示例脚本来检索 IP 地址。
   
       $vm = Get-SCVirtualMachine -Name <VM_NAME>
       $na = $vm[0].VirtualNetworkAdapters>
       $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
       $ip.address  
4. 运行此示例脚本来更新 DNS 并指定你通过前一个示例脚本检索到的 IP 地址。
   
       [string]$Zone,
       [string]$name,
       [string]$IP
       )
       $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
       $newrecord = $record.clone()
       $newrecord.RecordData[0].IPv4Address  =  $IP
       Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>后续步骤
运行测试性故障转移以确保环境功能正常以后，请[了解](site-recovery-failover.md)不同类型的故障转移。




<!--HONumber=Nov16_HO3-->


