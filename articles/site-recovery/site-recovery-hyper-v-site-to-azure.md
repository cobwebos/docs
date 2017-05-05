---
title: "将 Hyper-V VM 复制到 Azure | Microsoft 文档"
description: "介绍如何安排本地 Hyper-V VM 到 Azure 的复制、故障转移和恢复"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/05/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 2254b06d37b9090e1ca5e4e7db83e35e732e01a3
ms.lasthandoff: 04/06/2017

---

# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>结合使用 Azure Site Recovery 和 Azure 门户将 Hyper-V 虚拟机（不包含 VMM）复制到 Azure

> [!div class="op_single_selector"]
> * [Azure 门户](site-recovery-hyper-v-site-to-azure.md)
> * [Azure 经典](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - 资源管理器](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

本文介绍如何在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 将本地 Hyper-V 虚拟机复制到 Azure。 在此部署中，Hyper-V VM 不由 System Center Virtual Machine Manager (VMM) 托管。

阅读本文后，欢迎在页面底部发表任何看法，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上咨询技术问题。

如果要将计算机（不带故障回复）迁移到 Azure，请通过[此文](site-recovery-migrate-to-azure.md)了解详细信息。



## <a name="deployment-steps"></a>部署步骤

按照该文操作，完成以下部署步骤：

1. [深入了解](site-recovery-components.md#hyper-v-to-azure)此部署的体系结构。 此外，[深入了解](site-recovery-hyper-v-azure-architecture.md) Hyper-V 复制在 Site Recovery 中的工作原理。
2. 验证先决条件和限制。
3. 设置 Azure 网络和存储帐户。
4. 准备 Hyper-V 主机。
5. 创建恢复服务保管库。 保管库包含配置设置，并且安排复制。
6. 指定源设置。 创建包含 Hyper-V 主机的 Hyper-V 站点，并在保管库中注册该站点。 在 Hyper-V 主机上安装 Azure Site Recovery 提供程序，以及 Microsoft 恢复服务代理。
7. 设置目标和复制设置。
8. 为 VM 启用复制。
9. 运行测试故障转移，确保一切按预期运行。



## <a name="prerequisites"></a>先决条件


**要求** | **详细信息** |
--- | --- |
**Azure** | 了解 [Azure 要求](site-recovery-prereq.md#azure-requirements)。
**本地服务器** | [详细了解](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager)本地 Hyper-V 主机的要求。
**本地 Hyper-V VM** | 想要复制的虚拟机应正在运行[受支持的操作系统](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)，并且符合 [Azure 先决条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
**Azure URL** | Hyper-V 主机需访问以下 URL：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> 如果设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。<br/></br> 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。<br/></br> 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。



## <a name="prepare-for-deployment"></a>准备部署

若要准备部署，需要：

1. [设置 Azure 网络](#set-up-an-azure-network)，Azure VM 在故障转移后，将在该网络中创建。
2. [设置 Azure 存储帐户](#set-up-an-azure-storage-account) 。
3. [准备 Hyper-V 主机](#prepare-the-hyper-v-hosts)，确保它们可以访问所需的 URL。

### <a name="set-up-an-azure-network"></a>设置 Azure 网络

设置 Azure 网络。 需要一个 Azure 网络，以便在故障转移后创建的 Azure VM 连接到网络。

* 该网络应位于与恢复服务保管库相同的区域。
* 根据要用于故障转移 Azure VM 的资源模型，需在 [Resource Manager 模式](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)或[经典模式](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)下设置 Azure 网络。
* 建议在开始之前先设置网络。 否则，需要在 Site Recovery 部署期间执行此操作。
- 用于 Site Recovery 的存储帐户不能在同一订阅中或者跨不同的订阅[移动](../azure-resource-manager/resource-group-move-resources.md)。


### <a name="set-up-an-azure-storage-account"></a>设置 Azure 存储帐户

- 需要标准/高级 Azure 存储帐户来保存复制到 Azure 的数据。[高级存储](../storage/storage-premium-storage.md)通常用于 IO 性能一贯较高且延迟一贯较低、托管 IO 密集型工作负荷的虚拟机。
- 如果要将高级帐户用于存储复制的数据，则还需要创建一个标准存储帐户来存储复制日志，这些日志将捕获本地数据正在发生的更改。
- 根据要用于故障转移 Azure VM 的资源模型，需在 [Resource Manager 模式](../storage/storage-create-storage-account.md)或[经典模式](../storage/storage-create-storage-account-classic-portal.md)下设置帐户。
- 建议在开始之前先设置存储帐户。 否则，需要在 Site Recovery 部署期间执行此操作。 这些帐户必须位于与恢复服务保管库相同的区域中。
- 无法在同一订阅中的资源组之间或者在不同的订阅之间移动 Site Recovery 使用的存储帐户。


### <a name="prepare-the-hyper-v-hosts"></a>准备 Hyper-V 主机

* 确保 Hyper-V 主机符合[先决条件](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager)。
- 确保主机可以访问所需的 URL。


## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“新建” > “监视和管理” > “备份和 Site Recovery (OMS)”。  

    ![新保管库](./media/site-recovery-hyper-v-site-to-azure/new-vault.png)

3. 在“名称”中，指定一个友好名称以标识该保管库。 如果你有多个订阅，请选择其中一个。

4. [创建新资源组](../azure-resource-manager/resource-group-template-deploy-portal.md)或选择现有的资源组，并指定 Azure 区域。 计算机将复制到此区域。 若要查看受支持的区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”。

5. 若要从仪表板快速访问保管库，请单击“固定到仪表板”，然后单击“创建”。

    ![新保管库](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

新保管库将显示在“仪表板” > “所有资源”列表中，以及“恢复服务保管库”边栏选项卡上。



## <a name="select-the-protection-goal"></a>选择保护目标

选择要复制的内容以及要复制到的位置。

1. 在“恢复服务保管库”中，选择保管库。
2. 在“快速启动”中，单击“Site Recovery” > “准备基础结构” > “保护目标”。

    ![选择目标](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)
3. 在“保护目标”中选择“到 Azure”，然后选择“是，使用 Hyper-V”。 选择“否”，确认未使用 VMM。 。

    ![选择目标](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>设置源环境

设置 Hyper-V 站点，在 Hyper-V 主机上安装 Azure Site Recovery 提供程序和 Azure 恢复服务代理，并在保管库中注册站点。

1. 在“准备基础结构”中，单击“源”。 若要添加新的 Hyper-V 站点作为 Hyper-V 主机或群集的容器，请单击“+Hyper-V 站点”。

    ![设置源](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)
2. 在“创建 Hyper-V 站点”中指定站点的名称。 。 现在，请选择已创建的站点，然后单击“+Hyper-V 服务器”向站点添加服务器。

    ![设置源](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. 在“添加服务器” > “服务器类型”中，确保显示“Hyper-V 服务器”。

    - 确保要添加的 Hyper-V 服务器符合[先决条件](#on-premises-prerequisites)并且能够访问指定的 URL。
    - 下载 Azure Site Recovery 提供程序安装文件。 你将运行此文件，以在每个 Hyper-V 主机上安装提供程序和恢复服务代理。

    ![设置源](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)


## <a name="install-the-provider-and-agent"></a>安装提供程序和代理

1. 在添加到 Hyper-V 站点的每个主机上运行提供程序安装文件。 如果要在 Hyper-V 群集上进行安装，则在每个群集节点上运行安装程序。 通过安装并注册每个 Hyper-V 群集节点，可确保 VM 即使在节点之间迁移也会受到保护。
2. 在“Microsoft 更新” 中，可以选择进行更新，以便根据 Microsoft 更新策略安装提供程序更新。
3. 在“安装”中接受或修改默认提供程序安装位置，然后单击“安装”。
4. 在“保管库设置”中，单击“浏览”以选择所下载的保管库密钥文件。 指定 Azure Site Recovery 订阅、保管库名称和 Hyper-V 服务器属于的 Hyper-V 站点。

    ![服务器注册](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. 在“代理设置”中，指定 Hyper-V 主机上运行的提供程序如何通过 Internet 连接到 Azure Site Recovery。

    * 如果希望提供程序直接进行连接，请选择“不使用代理直接连接 Azure Site Recovery”。
    * 如果现有代理要求身份验证，或者你想要使用自定义代理进行填充程序连接，请选择“使用代理服务器连接 Azure Site Recovery”。
    * 如果使用代理，请执行以下操作：
        - 指定地址、端口和凭据
        - 确保允许通过代理访问[先决条件](#prerequisites)中所述的 URL。

    ![Internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6. 安装完成后，单击“注册”，在保管库中注册服务器。

    ![安装位置](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7. 完成注册后，Azure Site Recovery 将检索 Hyper-V 服务器中的元数据，该服务器显示在“Site Recovery 基础结构” > “Hyper-V 主机”中。


## <a name="set-up-the-target-environment"></a>设置目标环境

指定要用于复制的 Azure 存储帐户，以及 Azure VM 在故障转移后连接到的 Azure 网络。

1. 单击“准备基础结构” > “目标”。
2. 选择故障转移后要在其中创建 Azure VM 的订阅和资源组。 选择要在 Azure 中用于 VM 的部署模型（经典或资源管理）。

3. Site Recovery 将检查是否有一个或多个兼容的 Azure 存储帐户和网络。

    - 如果没有存储帐户，请单击“+存储”创建基于 Resource Manager 的内联帐户。 阅读[存储要求](site-recovery-prereq.md#azure-requirements)。
    - 如果没有 Azure 网络，请单击“+网络”创建基于 Resource Manager 的内联网络。

    ![存储](./media/site-recovery-vmware-to-azure/enable-rep3.png)

    


## <a name="configure-replication-settings"></a>配置复制设置

1. 若要创建新的复制策略，请单击“准备基础结构” > “复制设置” > “+创建和关联”。

    ![网络](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)
2. 在“创建和关联策略”中指定策略名称。
3. 在“复制频率” 中，指定要在初始复制后复制增量数据的频率（每隔 30 秒、5 或 15 分钟）。

    > [!NOTE]
    > 复制到高级存储时，不支持 30 秒的频率。 该限制取决于高级存储支持的每 blob 快照数 (100)。 [了解详细信息](../storage/storage-premium-storage.md#snapshots-and-copy-blob)。

4. 在“恢复点保留期”中，针对每个恢复点指定保留期窗口的长度（以小时为单位）。 VM 可以恢复到某个时段内的任意时间点。
5. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（1-12 小时）。

    - Hyper-V 使用两种类型的快照 — 标准快照，它提供整个虚拟机的增量快照；与应用程序一致的快照，它生成虚拟机内的应用程序数据的时间点快照。
    - 与应用程序一致的快照使用卷影复制服务 (VSS) 来确保应用程序在拍摄快照时处于一致状态。
    - 如果启用了与应用程序一致的快照，它将影响在源虚拟机上运行的应用程序的性能。 请确保你设置的值小于你配置的额外恢复点的数目。

6. 在“初始复制开始时间”中，指定开始初始复制的时间。 复制通过 Internet 带宽进行，因此你可能需要将它安排在非繁忙时间。 。

    ![复制策略](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

当创建新策略时，该策略自动与 Hyper-V 站点关联。 可以在“复制”> 策略名称 >“关联 Hyper-V 站点”中，将一个 Hyper-V 站点（以及其中的 VM）与多个复制策略关联。

## <a name="capacity-planning"></a>容量计划

设置基本的基础结构后，可以考虑容量计划并确定是否需要额外的资源。

Site Recovery 提供 Capacity Planner，帮助你为计算、网络和存储分配适当的资源。 可以在快速模式下运行 Planner，以便根据 VM、磁盘和存储的平均数量进行估计，或者在详细模式下运行 Planner，在工作负荷级别对数字进行自定义。 开始之前，需要：

* 收集有关复制环境的信息，包括 VM 数、每个 VM 的磁盘数和每个磁盘的存储空间。
* 估计复制数据的每日更改（变动）率。 可以使用 [Hyper-V 副本 Capacity Planner](https://www.microsoft.com/download/details.aspx?id=39057) 来帮助执行此操作。

1. 单击“下载”下载该工具，然后运行该工具。 [阅读](site-recovery-capacity-planner.md)该工具随附的文章。
2. 完成后，请在“是否已运行 Capacity Planner?”中选择“是”。

   ![容量计划](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

详细了解如何[控制网络带宽](#network-bandwidth-considerations)



## <a name="enable-replication"></a>启用复制

为 VM 启用复制，如下所示：          

1. 单击“复制应用程序” > “源”。 首次设置复制后，单击“+复制”即可对其他计算机启用复制。

    ![启用复制](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)
2. 在“源”中选择 Hyper-V 站点。 。
3. 在“目标”中，选择保管库订阅，以及故障转移后要在 Azure 中使用的故障转移模型（经典或资源管理）。
4. 选择要使用的存储帐户。 如果没有要使用的帐户，可以[创建一个](#set-up-an-azure-storage-account)。 。
5. 选择 Azure VM 在故障转移后创建时所要连接的 Azure 网络和子网。

    - 若要将网络设置应用到所有启用复制功能的计算机，请选择“立即为选定的计算机配置”。
    - 选择“稍后配置”以选择每个计算机的 Azure 网络。
    - 如果没有要使用的网络，可以[创建一个](#set-up-an-azure-network)。 选择适用的子网。 。

   ![启用复制](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. 在“虚拟机” > “选择虚拟机”中，单击并选择要复制的每个计算机。 只能选择可以启用复制的计算机。 。

    ![启用复制](./media/site-recovery-hyper-v-site-to-azure/enable-replication5-for-exclude-disk.png)

7. 在“属性” > “配置属性”中，选择所选 VM 的操作系统和 OS 磁盘。
8. 验证 Azure VM 名称（目标名称）是否符合 [Azure 虚拟机要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
9. 默认情况下，为复制选择了 VM 的所有磁盘，但可以通过清除磁盘将其排除。
    - 可能要排除磁盘来减少复制带宽。 例如，可能不需要复制包含临时数据的磁盘，或包含每次重启计算机/应用时刷新的数据（例如 pagefile.sys 或 Microsoft SQL Server tempdb）的磁盘。 通过取消选中该磁盘，可从复制中排除磁盘。
    - 只能排除基本磁盘。 不能排除 OS 磁盘。
    - 建议不要排除动态磁盘。 Site Recovery 无法识别来宾 VM 内的虚拟硬盘是基本磁盘还是动态磁盘。 如果未排除所有相关动态卷磁盘，则受保护的动态磁盘将在故障转移 VM 时显示为故障磁盘，且该磁盘上的数据将无法访问。
        - 启用复制后，无法添加或删除要复制的磁盘。 如果要添加或排除磁盘，需要禁用 VM 保护，然后重启保护。
        - 在 Azure 中手动创建的磁盘不会故障回复。 例如，如果对三个磁盘进行故障转移，并直接在 Azure VM 中创建两个磁盘，只会对那三个故障转移的磁盘进行从 Azure 到 Hyper-V 的故障回复。 在故障回复中或从 Hyper-V 到 Azure 的反向复制中，不能包括手动创建的磁盘。
        - 如果排除了运行某个应用程序所需的磁盘，则故障转移到 Azure 后，需要在 Azure 中手动创建该磁盘，以便复制的应用程序可以运行。 或者，可以将 Azure 自动化集成到恢复计划中，以便在故障转移计算机期间创建磁盘。

10. 单击“确定”保存更改。 可以稍后再设置其他属性。

     ![启用复制](./media/site-recovery-hyper-v-site-to-azure/enable-replication6-with-exclude-disk.png)

11. 在“复制设置” > “配置复制设置”中，选择要应用于受保护 VM 的复制策略。 。 可以在“复制策略”> 策略名称 >“编辑设置”中修改复制策略。 应用的更改将用于已在复制的计算机和新计算机。


   ![启用复制](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

可以在“作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。

### <a name="view-and-manage-vm-properties"></a>查看和管理 VM 属性

建议你验证源计算机的属性。

1. 在“受保护的项”中，单击“复制的项”，然后选择计算机。

    ![启用复制](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)
2. 在“属性”中，可以查看 VM 的复制和故障转移信息。

    ![启用复制](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)
3. 在“计算和网络” > “计算属性”中，可以指定 Azure VM 名称和目标大小。 根据需要修改名称，使其符合 Azure 要求。 你还可以查看和修改目标网络、子网的相关信息，以及要分配到 Azure VM 的 IP 地址。 注意以下事项：

   * 可以设置目标 IP 地址。 如果未提供地址，故障转移的计算机将使用 DHCP。 如果设置了无法用于故障转移的地址，故障转移将会失败。 如果地址可用于测试故障转移网络，则同一个目标 IP 地址可用于测试故障转移。
   * 网络适配器数目根据你为目标虚拟机指定的大小来确定，如下所述：

     * 如果源计算机上的网络适配器数小于或等于目标计算机大小允许的适配器数，则目标的适配器数将与源相同。
     * 如果源虚拟机的适配器数大于目标大小允许的数目，则使用目标大小允许的最大数目。
     * 例如，如果源计算机有两个网络适配器，而目标计算机大小支持四个，则目标计算机将有两个适配器。 如果源计算机有两个适配器，但支持的目标大小只支持一个，则目标计算机只有一个适配器。     
     * 如果 VM 有多个网络适配器，它们将全部连接到同一个网络。
     * 如果虚拟机有多个网络适配器，列表中显示的第一个适配器将成为 Azure 虚拟机中的*默认*网络适配器。

     ![启用复制](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

4. 在“磁盘”中，可以看到 VM 上将要复制的操作系统和数据磁盘。




## <a name="test-the-deployment"></a>测试部署

为了对部署进行测试，可以针对单个虚拟机或单个恢复计划（其中包含一个或多个虚拟机）运行测试故障转移。

### <a name="before-you-start"></a>开始之前

 - 如果要在故障转移后使用 RDP 连接到 Azure VM，请了解如何[准备连接](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。
 - 若要全面测试，需要在测试环境中复制 Active Directory 和 DNS。 [了解详细信息](site-recovery-active-directory.md#test-failover-considerations)。

### <a name="run-a-test-failover"></a>运行测试故障转移

1. 若要故障转移单个计算机，请在“复制的项”中，单击该 VM >“+测试故障转移”图标。
2. 若要故障转移某个恢复计划，请在“恢复计划”中，右键单击该计划 >“测试故障转移”。 若要创建恢复计划，请[遵循这些说明](site-recovery-create-recovery-plans.md)。
3. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的 Azure 网络。
4. 单击“确定”开始故障转移。 若要跟踪进度，可以单击 VM 以打开其属性，或者在保管库名称 >“作业” > “Site Recovery 作业”中单击“测试故障转移”作业。
5. 故障转移完成后，你还应该能够看到副本 Azure 计算机显示在 Azure 门户的“虚拟机”中。 应确保 VM 的大小适当、已连接到相应的网络，并且正在运行。
6. 如果已准备好故障转移后的连接，应该能够连接到 Azure VM。
7. 完成后，在恢复计划上单击“清理测试故障转移”。 在“**说明**”中，记录并保存与测试性故障转移相关联的任何观测结果。 此时会删除在测试性故障转移期间创建的虚拟机。

有关更多详细信息，请参阅[测试故障转移到 Azure](site-recovery-test-failover-to-azure.md)一文。



## <a name="monitor-the-deployment"></a>监视部署

监视 Site Recovery 部署的配置设置、状态和运行状况：

1. 单击保管库名称以访问“概要”仪表板。 在此仪表板中，可以跟踪 Site Recovery 作业、复制状态、恢复计划、服务器运行状况和事件。  

    ![概要](./media/site-recovery-hyper-v-site-to-azure/essentials.png)
2. 在“运行状况”磁贴中，可以监视发生问题的站点服务器，以及 Site Recovery 在过去 24 小时内引发的事件。 可以自定义“概要”以显示最有用的磁贴和布局，包括其他 Site Recovery 和备份保管库的状态。
3. 可以在“复制的项”、“恢复计划”和“Site Recovery 作业”磁贴中管理和监视复制。 可通过“作业” > “Site Recovery 作业”钻取到不同的作业，以获取更多详细信息。

## <a name="command-line-provider-and-agent-installation"></a>命令行提供程序和代理安装

也可使用以下命令行来安装 Azure Site Recovery 提供程序和代理。 此方法可用来将提供程序安装在 Server Core for Windows Server 2012 R2 上。

1. 将提供程序安装文件和注册密钥下载到某个文件夹中。 例如 C:\ASR。
2. 从提升的命令提示符处，运行以下命令提取提供程序安装程序：

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. 运行以下命令来安装组件：

            C:\ASR> setupdr.exe /i
4. 然后运行以下命令在保管库中注册服务器：

            CD C:\Program Files\Microsoft Azure Site Recovery Provider\
            C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file>

<br/>
其中：

* **/Credentials**：用于指定注册密钥文件所在位置的必需参数  
* **/FriendlyName**：在 Azure Site Recovery 门户中显示的 Hyper-V 主机服务器名称的必需参数。
* **/proxyAddress**：可选参数，用于指定代理服务器的地址。
* **/proxyport** ：可选参数，用于指定代理服务器的端口。
* **/proxyUsername**：可选参数，用于指定代理用户名（如果代理要求身份验证）。
* **/proxyPassword**：可选参数，用于指定密码，以便通过代理服务器进行身份验证（如果代理服务器要求身份验证）。


## <a name="network-bandwidth-considerations"></a>网络带宽注意事项
可以使用 [Hyper-V Capacity Planner 工具](site-recovery-capacity-planner.md)来计算复制（初始复制，然后是增量复制）所需的带宽。 若要控制复制所用的带宽量，可以使用几个选项：

* **限制带宽**：复制到 Azure 的 Hyper-V 流量经过特定的 Hyper-V 主机。 可以在主机服务器上限制带宽。
* **调整带宽**：可以使用几个注册表项来控制用于复制的带宽。

### <a name="throttle-bandwidth"></a>限制带宽
1. 在 Hyper-V 主机服务器上打开 Microsoft Azure 备份 MMC 管理单元。 默认情况下，Microsoft Azure 备份的快捷方式位于桌面上或 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 中。
2. 在管理单元中，单击“更改属性”。
3. 在“限制”选项卡上，选择“为备份操作启用 Internet 带宽使用限制”，然后设置工作时间和非工作时间的限制。 有效范围为 512 Kbps 到 102 Mbps。

    ![限制带宽](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

也可以使用 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet 来设置限制。 下面是一个示例：

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 表示不需要限制。

### <a name="influence-network-bandwidth"></a>影响网络带宽
1. 在注册表中导航到 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsofte Azure Backup\Replication**。
   * 若要影响复制磁盘上的带宽流量，请修改 **UploadThreadsPerVM** 值，或者创建该项（如果不存在）。
   * 若要影响用于从 Azure 故障回复流量的带宽，请修改 **DownloadThreadsPerVM** 值。
2. 默认值为 4。 在“过度预配型”网络中，这些注册表项需要更改，不能使用默认值。 最大值为 32。 监视流量以优化值。

## <a name="next-steps"></a>后续步骤

完成虚拟机的初始复制，并已测试部署后，可以根据需要调用故障转移。 [详细了解](site-recovery-failover.md)不同类型的故障转移，以及如何运行它们。

