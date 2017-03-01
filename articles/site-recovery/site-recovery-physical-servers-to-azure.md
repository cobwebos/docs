---
title: "将物理服务器复制到 Azure | Microsoft Docs"
description: "介绍如何使用 Azure 门户通过部署 Azure Site Recovery 来安排本地 Windows/Linux 物理服务器到 Azure 的复制、故障转移和恢复"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3396818cd177330b7123f3a346b1591a4bcb1e4e
ms.openlocfilehash: c9994e4e826bc35e1de439684ed5877db91fa069
ms.lasthandoff: 02/22/2017


---
# <a name="replicate-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>使用 Azure 门户通过 Azure Site Recovery 将物理计算机复制到 Azure


欢迎使用 Azure Site Recovery 服务！

站点恢复就是能够帮助实现业务连续性和灾难恢复 (BCDR) 策略的一个 Azure 服务。 站点恢复可安排从本地物理服务器和虚拟机到云 (Azure) 或辅助数据中心的复制。 当主要位置发生故障时，你可以故障转移到辅助位置，使应用和工作负荷保持可用。 当主要位置恢复正常时，你可以故障转移回到主要位置。 在 [什么是 Azure Site Recovery？](site-recovery-overview.md)

本文介绍如何在 Azure 门户中使用 Azure Site Recovery 将本地 Windows/Linux 物理服务器复制到 Azure。

阅读本文后，请在底部的 Disqus 意见区域中发表任何意见。 如有技术问题，请在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上提出。


## <a name="steps"></a>步骤

下面是需要执行的操作：

1. 验证先决条件和限制。
2. 设置 Azure 网络和存储帐户。
3. 准备要部署为配置服务器的本地计算机。
4. 创建恢复服务保管库。 保管库包含配置设置，并且安排复制。
5. 指定源、目标和复制设置。
6. 将移动服务部署到想要复制的 VM 上。
7. 为 VM 启用复制。
7. 运行测试故障转移，确保一切按预期运行

## <a name="prerequisites"></a>先决条件

**支持要求** | **详细信息**
--- | ---
**Azure** | 了解 [Azure 要求](site-recovery-prereq.md#azure-requirements)
**本地配置服务器** | 运行 Windows Server 2012 R2 或更高版本的本地计算机。 将在 Site Recovery 部署过程中设置此服务器。<br/><br/> 默认情况下，进程服务器和主目标服务器也安装在此 VM 上。 进行扩展时，可能需要使用单独的进程服务器，且其要求与配置服务器相同。<br/><br/> 可在[此处](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)了解有关这些组件的详细信息
**本地 VM** | 想要复制的虚拟机应正在运行[受支持的操作系统](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)，并且符合 [Azure 先决条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
**URLs** | 配置服务器需要以下 URL 的访问权限：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> 如果设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。<br/></br> 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。<br/></br> 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。<br/><br/> 允许以下用于下载 MySQL 的 URL：http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi。
**移动服务** | 已安装在每个复制的 VM 上。


## <a name="site-recovery-in-the-azure-portal"></a>Azure 门户中的 Site Recovery
Azure 提供了两个不同的[部署模型](../azure-resource-manager/resource-manager-deployment-model.md)来创建和处理资源 – Azure Resource Manager 模型和经典模型。 Azure 还提供两个门户 – Azure 经典门户和 Azure 门户。

本文介绍了如何在 Azure 门户中进行部署，该门户提供新功能以及精简流畅的部署体验。 可以使用经典门户维护现有的保管库。 无法使用经典门户创建新的保管库。


## <a name="set-up-azure"></a>设置 Azure

1. [设置 Azure 网络](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。
    - 在故障转移后创建 Azure VM 时，Azure VM 将置于此网络中。
    - 可以在 [Resource Manager](../resource-manager-deployment-model.md) 中设置网络，也可以在经典模式下设置网络。

2. 设置用于所复制数据的 [Azure 存储帐户](../storage/storage-create-storage-account.md#create-a-storage-account)。
    - 该帐户可以是标准帐户或[高级](../storage/storage-premium-storage.md)帐户。
    - 可以在 Resource Manager 中设置帐户，也可以在经典模式下设置帐户。

## <a name="prepare-the-configuration-server"></a>准备配置服务器

1. 在本地服务器上安装 Windows Server 2012 R2 或更高版本。
2. 请确保 VM 有权访问[先决条件](#prerequisites)中列出的 URL。

**准备一个帐户以推送移动服务**：如果要将移动服务推送到 VM，则需要一个可由进程服务器用来访问 VM 的帐户。 该帐户仅用于推送安装。 可以使用域或本地帐户：

  - 对于 Windows，如果你使用的不是域帐户，则需在本地计算机上禁用远程用户访问控制。 为此，请在注册表中的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下添加值为 1 的 DWORD 项 **LocalAccountTokenFilterPolicy**。
    - 如果想要从 CLI 为 Windows 添加注册表项，请键入：  ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
  - 对于 Linux，该帐户应该是源 Linux 服务器上的 root 用户。

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-the-protection-goal"></a>选择保护目标

选择要复制的内容以及要复制到的位置。

1. 单击“恢复服务保管库”> 保管库。
2. 在“资源”菜单中，单击“Site Recovery” > “步骤 1: 准备基础结构” > “保护目标”。

    ![选择目标](./media/site-recovery-vmware-to-azure/choose-goal-physical.PNG)
3. 在“保护目标”中选择“到 Azure”，然后选择“未虚拟化/其他”。


## <a name="set-up-the-source-environment"></a>设置源环境

设置配置服务器，将它注册到保管库中，并且发现 VM。

1. 单击“Site Recovery” > “步骤 1: 准备基础结构” > “源”。
2. 如果没有配置服务器，请单击“+ 配置服务器”。

    ![设置源](./media/site-recovery-vmware-to-azure/set-source1.png)
3. 在“添加服务器”中，检查“配置服务器”是否已显示在“服务器类型”中。
4. 下载站点恢复统一安装程序安装文件。
5. 下载保管库注册密钥。 运行统一安装程序时需要用到此密钥。 生成的密钥有效期为&5; 天。

   ![设置源](./media/site-recovery-vmware-to-azure/set-source2.png)
6. 在配置服务器 VM 上，确保系统时钟与[时间服务器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)同步，然后运行统一安装程序安装配置服务器、进程服务器和主目标服务器。

## <a name="run-site-recovery-unified-setup"></a>运行站点恢复统一安装程序

开始之前：

- 请确保 VM 上的时间与本地时区中的时间相同。 它应与之匹配。 如果它提前或落后 15 分钟，安装程序可能会失败。
- 在配置服务器 VM 上以本地管理员身份运行安装程序。
- 确保在 VM 上启用了 TLS 1.0。

然后在配置服务器上运行统一安装程序安装文件。


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 可以通过命令行安装配置服务器。 有关详细信息，请参阅[使用命令行工具安装配置服务器](http://aka.ms/installconfigsrv)。

## <a name="set-up-the-target"></a>设置目标

设置目标环境之前，请检查你是否有 [Azure 存储帐户和网络](#set-up-azure)

1. 单击“准备基础结构” > “目标”，然后选择要使用的 Azure 订阅。
2. 指定你的目标部署模型是基于 Resource Manager 的还是经典的。
3. Site Recovery 将检查是否有一个或多个兼容的 Azure 存储帐户和网络。

   ![目标](./media/site-recovery-vmware-to-azure/gs-target.png)
4. 如果尚未创建存储帐户或网络，请单击“+ 存储帐户”或“+ 网络”，创建 Resource Manager 帐户或内联网络。

## <a name="set-up-replication-settings"></a>设置复制设置

1. 若要创建新的复制策略，请单击“Site Recovery 基础结构” > “复制策略” > “+ 复制策略”。
2. 在“创建复制策略”中指定策略名称。
3. 在“RPO 阈值”中：指定 RPO 限制。 此值指定创建数据恢复点的频率。 如果连续复制超出此限制，将生成警报。
4. 在“恢复点保留期”中，针对每个恢复点指定保留期窗口的长度（以小时为单位）。 可以将复制的虚拟机恢复到窗口中的任何点。 复制到高级存储的计算机最多支持 24 小时的保留期，复制到标准存储的计算机最多支持 72 小时的保留期。
5. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（以分钟为单位）。 单击“确定”创建该策略。

    ![复制策略](./media/site-recovery-vmware-to-azure/gs-replication2.png)
8. 当你创建新策略时，该策略将自动与配置服务器关联。 默认情况下将自动创建一个匹配策略以用于故障回复。 例如，如果复制策略是 **rep-policy**，则故障回复策略将是 **rep-policy-failback**。 你从 Azure 启动故障回复之前，不会使用此策略。  


## <a name="plan-capacity"></a>计划容量

1. 设置基本的基础结构后，可以考虑容量计划并确定是否需要额外的资源。 [了解详细信息](site-recovery-plan-capacity-vmware.md)。
2. 完成容量计划后，请在“你是否已完成容量计划?”中选择“是”。

   ![容量计划](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>准备用于复制的 VM

要复制的所有计算机都必须安装移动服务。 可以通过多种方式安装移动服务：

1. 从进程服务器使用推送安装进行安装。 需要准备 VM 以使用此方法。
2. 使用 System Center Configuration Manager 或 Azure 自动化 DSC 等部署工具进行安装。
3.  手动安装。

[了解详细信息](site-recovery-vmware-to-azure-install-mob-svc.md)


## <a name="enable-replication"></a>启用复制

开始之前：

- 添加或修改 VM 时，可能需要 15 分钟或更长时间，更改才会生效并显示在门户中。
- 可以在“配置服务器” > “上次联系位置”查看上次发现 VM 的时间。
- 若要添加 VM 而不想要等待执行计划的发现，请突出显示配置服务器（不要单击它），然后单击“刷新”。
* 如果已为推送安装准备了 VM，那么在启用复制时，进程服务器将自动安装移动服务。


### <a name="exclude-disks-from-replication"></a>从复制中排除磁盘

默认情况下将复制计算机上的所有磁盘。 你可以从复制中排除磁盘。 例如，你可能不想要复制包含临时数据，或者每当重新启动计算机或应用程序时刷新的数据（例如 pagefile.sys 或 SQL Server tempdb）的磁盘。

### <a name="replicate-vms"></a>复制 VM

1. 单击“步骤 2: 复制应用程序” > “源”。
2. 在“源”中选择“本地”
3. 在“源位置”中选择配置服务器名称
4. 在“计算机类型”中选择“物理计算机”。
4. 在“vCenter/vSphere 虚拟机监控程序”中，选择管理 vSphere 主机的 vCenter 服务器，或选择该主机。
5. 在“进程服务器”中选择进程服务器。 如果尚未创建任何额外的进程服务器，该进程服务器将是配置服务器。 。

    ![启用复制](./media/site-recovery-physical-to-azure/chooseVM.png)

6. 在“目标”中，选择要创建故障转移 VM 的订阅和资源组。 选择要在 Azure 中用于故障转移 VM 的部署模型（经典或资源管理）。

7. 选择要用于复制数据的 Azure 存储帐户。 如果不想使用已设置的帐户，可以创建一个新帐户。

8. 选择 Azure VM 在故障转移后创建时所要连接的 Azure 网络和子网。 选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。 选择“稍后配置”以选择每个计算机的 Azure 网络。 如果不想使用现有网络，可以创建一个网络。

    ![启用复制](./media/site-recovery-physical-to-azure/targetsettings.png)
9. 在“物理计算机”中，单击“+”按钮，然后输入要复制的计算机的**名称**、**IP地址**并选择 OS 类型。
  ![启用复制](./media/site-recovery-physical-to-azure/machineselect.png)。需要稍等几分钟才能发现计算机并将其显示在列表中。

10. 在“属性” > “配置属性”中，选择进程服务器在计算机上自动安装移动服务时使用的帐户。
11. 默认情况下会复制所有磁盘。 单击“所有磁盘”并清除不想要复制的所有磁盘。 。 可以稍后再设置其他 VM 属性。

    ![启用复制](./media/site-recovery-physical-to-azure/configprop.png)
11. 在“复制设置” > “配置复制设置”中，检查是否选择了正确的复制策略。 如果修改策略，更改将应用到复制计算机和新计算机。
12. 如果要将计算机集合到复制组，请启用“多 VM 一致性”并指定组的名称。 。 请注意：

    * 复制组中的计算机将一起复制，并在故障转移时具有在崩溃时保持一致且应用保持一致的共享恢复点。
    * 我们建议你将 VM 和物理服务器集合在一起，使其镜像你的工作负荷。 启用多 VM 一致性可能会影响工作负荷性能，因此，仅当计算机运行相同的工作负荷并且你需要一致性时，才应使用该设置。
    ![启用复制](./media/site-recovery-physical-to-azure/policy.png)

13. 单击“启用复制”。 可以在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。

启用复制后，将安装移动服务（如果设置了推送安装）。 在 VM 上对移动服务进行推送安装后，保护作业将启动并失败。 发生这种失败后，需要手动重新启动每台计算机。 然后，保护作业将重新开始，并且初始复制将会进行。


### <a name="view-and-manage-vm-properties"></a>查看和管理 VM 属性

我们建议验证 VM 属性，并进行任何所需的更改。

1. 单击“复制的项”，然后选择计算机。 “概要”边栏选项卡显示有关计算机设置和状态的信息。
2. 在“属性”中，可以查看 VM 的复制和故障转移信息。
3. 在“计算和网络” > “计算属性”中，可以指定 Azure VM 名称和目标大小。 根据需要修改名称，使其符合 [Azure 要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
4. 修改要分配给 Azure VM 的目标网络、子网和 IP 地址的设置：

   - 可以设置目标 IP 地址。

    - 如果未提供地址，故障转移的计算机将使用 DHCP。
    - 如果设置了在故障转移时不可用的地址，故障转移将不会正常工作。
    - 如果地址可用于测试故障转移网络，则同一个目标 IP 地址可用于测试故障转移。

   - 网络适配器数目根据你为目标虚拟机指定的大小来确定：

     - 如果源计算机上的网络适配器数小于或等于目标计算机大小允许的适配器数，则目标的适配器数将与源相同。
     - 如果源虚拟机的适配器数大于目标大小允许的数目，则将使用目标大小允许的最大数目。
     - 例如，如果源计算机有两个网络适配器，而目标计算机大小支持四个，则目标计算机将有两个适配器。 如果源计算机有两个适配器，但支持的目标大小只支持一个，则目标计算机只有一个适配器。     
   - 如果虚拟机有多个网络适配器，它们将全部连接到同一个网络。
   - 如果虚拟机有多个网络适配器，列表中显示的第一个适配器将成为 Azure 虚拟机中的*默认*网络适配器。
5. 在“磁盘”中，可以看到 VM 操作系统，以及将要复制的数据磁盘。

## <a name="run-a-test-failover"></a>运行测试故障转移

完成全部设置后，运行测试故障转移，确保一切按预期运行。


1. 若要故障转移单个计算机，请在“设置”>“复制的项”中，单击“VM” > “+测试故障转移”图标。

    ![测试故障转移](./media/site-recovery-vmware-to-azure/TestFailover.png)

1. 若要故障转移某个恢复计划，请在“设置” > “恢复计划”中，右键单击该计划 >“测试性故障转移”。 若要创建恢复计划，请[遵循这些说明](site-recovery-create-recovery-plans.md)。  

1. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的 Azure 网络。

1. 单击“确定”开始故障转移。 若要跟踪进度，可以单击 VM 以打开其属性，或者在保管库名称 >“设置” > “作业” > “Site Recovery 作业”中选择“测试故障转移”作业。

1. 故障转移完成后，你还应该能够看到副本 Azure 计算机显示在 Azure 门户的“虚拟机”中。 应确保 VM 的大小适当、已连接到相应的网络，并且正在运行。

1. 如果[已准备好故障转移后的连接](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)，应该能够连接到 Azure VM。

1. 完成后，在恢复计划上单击“清理测试故障转移”。 在“**说明**”中，记录并保存与测试性故障转移相关联的任何观测结果。 此时会删除在测试性故障转移期间创建的虚拟机。

有关更多详细信息，请参阅[测试性故障转移到 Azure](site-recovery-test-failover-to-azure.md)文档。

## <a name="next-steps"></a>后续步骤

复制启动并运行后，当发生中断时会故障转移到 Azure，并且将从复制的数据创建 Azure VM。 然后可以在 Azure 中访问工作负荷和应用，直到它返回到正常操作时你故障回复到主位置。

- [详细了解](site-recovery-failover.md)不同类型的故障转移，以及如何运行它们。
- 如果要迁移计算机而不执行复制和故障回复，请[阅读详细信息](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers)。
- 复制物理计算机时，只能故障回复到 VMWare 环境。 [了解故障回复](site-recovery-failback-azure-to-vmware.md)。

## <a name="third-party-software-notices-and-information"></a>第三方软件通知和信息
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

完整文件可以在 [ Microsoft 下载中心](http://go.microsoft.com/fwlink/?LinkId=529428)上找到。 Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

