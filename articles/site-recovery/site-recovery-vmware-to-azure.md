---
title: "将 VMware VM 复制到 Azure | Microsoft 文档"
description: "总结了将 VMware VM 上运行的工作负荷复制到 Azure 存储所需的步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: b7b7347fef8ea6f6bf643e98bbcc0a6292c083ed
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017


---
# <a name="replicate-vmware-virtual-machines-to-azure-with-site-recovery"></a>通过 Site Recovery 将 VMware 虚拟机复制到 Azure

> [!div class="op_single_selector"]
> * [Azure 门户](site-recovery-vmware-to-azure.md)
> * [Azure 经典](site-recovery-vmware-to-azure-classic.md)


本文介绍如何在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务，将本地 VMware 虚拟机复制到 Azure。

如果希望将 VMware VM 迁移到 Azure（仅限故障转移），请阅读[此文](site-recovery-migrate-to-azure.md)以了解详细信息。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="deployment-steps"></a>部署步骤

下面是需要执行的操作：

1. 验证先决条件和限制。
2. 设置 Azure 网络和存储帐户。
3. 准备要部署为配置服务器的本地计算机。
4. 准备要用于自动发现 VM 以及（可选）对移动服务进行推送安装的 VMware 帐户。
4. 创建恢复服务保管库。 保管库包含配置设置，并且安排复制。
5. 指定源、目标和复制设置。
6. 将移动服务部署到想要复制的 VM 上。
7. 为 VM 启用复制。
7. 运行测试故障转移，确保一切按预期运行。

## <a name="prerequisites"></a>先决条件

**支持要求** | **详细信息**
--- | ---
**Azure** | 了解 [Azure 要求](site-recovery-prereq.md#azure-requirements)
**本地配置服务器** | 需要运行 Windows Server 2012 R2 或更高版本的 VMware VM。 将在 Site Recovery 部署过程中设置此服务器。<br/><br/> 默认情况下，进程服务器和主目标服务器也安装在此 VM 上。 进行扩展时，可能需要使用单独的进程服务器，且其要求与配置服务器相同。<br/><br/> 可在[此处](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)了解有关这些组件的详细信息
**本地 VMware 服务器** | 一台或多台 VMware vSphere 服务器，运行带有最新更新的 6.0、5.5 或 5.1。 服务器应位于配置服务器（或单独的进程服务器）所在的同一网络。<br/><br/> 建议使用一台 vCenter 服务器（运行带有最新更新的 6.0 或 5.5）来管理主机。 部署版本 6.0 时，只有 5.5 版中提供的功能才受支持。
**本地 VM** | 想要复制的虚拟机应正在运行[受支持的操作系统](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)，并且符合 [Azure 先决条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。 VM 应正在运行 VMware 工具。
**URLs** | 配置服务器需要以下 URL 的访问权限：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> 如果设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。<br/></br> 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。<br/></br> 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。<br/><br/> 允许以下用于下载 MySQL 的 URL：http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi。
**移动服务** | 已安装在每个复制的 VM 上。




## <a name="limitations"></a>限制

**限制** | **详细信息**
--- | ---
**Azure** | 存储帐户和网络帐户必须与保管库位于同一区域<br/><br/> 如果使用高级存储帐户，还需要一个用于存储复制日志的标准存储帐户<br/><br/> 不能复制到位于印度中部和印度南部的高级帐户。
**本地配置服务器** | VMware VM 适配器类型应为 VMXNET3。 如果它不是，请[安装此更新](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> 应安装 vSphere PowerCLI 6.0。<br/><br> 该计算机不应是域控制器。 该计算机应拥有一个静态 IP 地址。<br/><br/> 主机名长度应不超过 15 个字符，并且操作系统应采用英文。
**VMware** | 只有 5.5 版的功能才在 vCenter 6.0 中受支持。 Site Recovery 不支持新的 vCenter 和 vSphere 6.0 功能，例如跨 vCenter vMotion、虚拟卷和存储 DRS。
**VM** | 验证 [Azure VM 限制](site-recovery-prereq.md#azure-requirements)<br/><br/> 不能复制包含加密磁盘的 VM 或带有 UEFI/EFI 引导系统的 VM。<br/><br> 共享的磁盘群集不受支持。 如果源 VM 具有 NIC 组合，那么在故障转移后，它将转换为单个 NIC。<br/><br/> 如果 VM 有一个 iSCSI 磁盘，那么在故障转移后，Site Recovery 会将其转换为 VHD 文件。 如果 Azure VM 可以访问 iSCSI 目标，它将连接到该目标，并且会发现该目标和 VHD 这两者。 如果发生这种情况，请断开与 iSCSI 目标的连接。<br/><br/> 如果想要启用多 VM 一致性，从而使运行相同工作负荷的 VM 一起恢复到一个一致数据点，请打开 VM 上的端口 20004。<br/><br/> 必须在 C 驱动器上安装 Windows。 OS 磁盘应是基本磁盘而不是动态磁盘。 数据磁盘可以是动态磁盘。<br/><br/> VM 上的 Linux /etc/hosts 文件应包含将本地主机名映射到与所有网络适配器相关联的 IP 地址的条目。 主机名、装载点、设备名称、系统路径和文件名（/etc/；/usr）只能采用英文形式。<br/><br/> 特定类型的 [Linux 存储](site-recovery-support-matrix-to-azure.md#support-for-storage)受支持。<br/><br/>在 VM 设置中创建或设置 **disk.enableUUID=true**。 这会向 VMDK 提供一致的 UUID，以便它正确进行装载，并确保在故障回复期间仅将增量更改传输回本地而不执行完全复制。

## <a name="set-up-azure"></a>设置 Azure

1. [设置 Azure 网络](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。
    - 在故障转移后创建 Azure VM 时，Azure VM 将置于此网络中。
    - 可以在 [Resource Manager](../resource-manager-deployment-model.md) 中设置网络，也可以在经典模式下设置网络。

2. 设置用于所复制数据的 [Azure 存储帐户](../storage/storage-create-storage-account.md#create-a-storage-account)。
    - 该帐户可以是标准帐户或[高级](../storage/storage-premium-storage.md)帐户。
    - 可以在 Resource Manager 中设置帐户，也可以在经典模式下设置帐户。

3. 在 vCenter 服务器或 vSphere 主机上[准备一个帐户](#prepare-for-automatic-discovery-and-push-installation)，以便 Site Recovery 可以自动检测到 VMware VM。

## <a name="prepare-the-configuration-server"></a>准备配置服务器

1. 在 VMware 虚拟机上安装 Windows Server 2012 R2 或更高版本。
2. 请确保 VM 有权访问[先决条件](#prerequisites)中列出的 URL。
3. 安装 [VMware vSphere PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)。


## <a name="prepare-for-automatic-discovery-and-push-installation"></a>为自动发现和推送安装做准备

- **为自动发现准备一个帐户**：Site Recovery 进程服务器将自动发现 VM。 为此，Site Recovery 需要凭据，以便可以访问 vCenter 服务器和 vSphere ESXi 主机。

    1. 若要使用专用帐户，请创建一个角色（在 vCenter 级别，具有这些[权限](#vmware-account-permissions)）。 为其指定一个名称，例如 **Azure_Site_Recovery**。
    2. 然后在 vSphere 主机或 vCenter 服务器上创建一个用户，并向其分配该角色。 在 Site Recovery 部署过程中指定此用户帐户。

- **准备一个帐户以推送移动服务**：如果要将移动服务推送到 VM，则需要一个可由进程服务器用来访问 VM 的帐户。 该帐户仅用于推送安装。 可以使用域或本地帐户：

    - 对于 Windows，如果你使用的不是域帐户，则需在本地计算机上禁用远程用户访问控制。 为此，请在注册表中的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下添加值为 1 的 DWORD 项 **LocalAccountTokenFilterPolicy**。
    - 如果想要从 CLI 为 Windows 添加注册表项，请键入：  ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
    - 对于 Linux，该帐户应是源 Linux 服务器上的根。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-the-protection-goal"></a>选择保护目标

选择要复制的内容以及要复制到的位置。

1. 单击“恢复服务保管库”> 保管库。
2. 在“资源”菜单中，单击“Site Recovery” > “步骤 1: 准备基础结构” > “保护目标”。

    ![选择目标](./media/site-recovery-vmware-to-azure/choose-goals.png)
3. 在“保护目标”中选择“到 Azure” > “是，使用 VMware vSphere 虚拟机监控程序”。

    ![选择目标](./media/site-recovery-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>设置源环境

设置配置服务器，将它注册到保管库中，并且发现 VM。

1. 单击“Site Recovery” > “步骤 1: 准备基础结构” > “源”。
2. 如果没有配置服务器，请单击“+ 配置服务器”。

    ![设置源](./media/site-recovery-vmware-to-azure/set-source1.png)
3. 在“添加服务器”中，检查“配置服务器”是否已显示在“服务器类型”中。
4. 下载站点恢复统一安装程序安装文件。
5. 下载保管库注册密钥。 运行统一安装程序时需要用到此密钥。 生成的密钥有效期为 5 天。

   ![设置源](./media/site-recovery-vmware-to-azure/set-source2.png)


## <a name="run-site-recovery-unified-setup"></a>运行站点恢复统一安装程序

在开始之前，执行以下操作，然后运行统一安装程序来安装配置服务器、进程服务器和主目标服务器。
    - 获取短片概述

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - 在配置服务器 VM 上，确保将系统时钟与[时间服务器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)进行同步。 它应与之匹配。 如果它提前或落后 15 分钟，安装程序可能会失败。
    - 在配置服务器 VM 上以本地管理员身份运行安装程序。
    - 确保在 VM 上启用了 TLS 1.0。


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 还可以[通过命令行](http://aka.ms/installconfigsrv)安装配置服务器。



### <a name="add-the-account-for-automatic-discovery"></a>添加用于自动发现的帐户

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="connect-to-vmware-servers"></a>连接到 VMware 服务器

连接到 vSphere ESXi 主机或 vCenter 服务器以发现 VMware VM。

- 如果在服务器上使用没有管理员权限的帐户添加 vCenter 服务器或 vSphere 主机，该帐户需要启用以下权限：
    - 数据中心、数据存储、文件夹、主机、网络、资源、虚拟机、vSphere 分布式交换机。
    - vCenter 服务器需要“存储视图”权限。
- 添加 VMware 服务器之后，可能需要 15 分钟或更长时间，这些服务器才会显示在门户中。
若要允许 Azure Site Recovery 发现本地环境中运行的虚拟机，需要将 VMware vCenter 服务器或 vSphere ESXi 主机与 Site Recovery 连接。

选择“+vCenter”开始连接 VMware vCenter 服务器或 VMware vSphere ESXi 主机。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

Site Recovery 将使用指定的设置连接到 VMware 服务器，并且将发现 VM。

## <a name="set-up-the-target"></a>设置目标

设置目标环境之前，请检查你是否有 [Azure 存储帐户和网络](#set-up-azure)

1. 单击“准备基础结构” > “目标”，然后选择要使用的 Azure 订阅。
2. 指定你的目标部署模型是基于 Resource Manager 的还是经典的。
3. Site Recovery 将检查是否有一个或多个兼容的 Azure 存储帐户和网络。

   ![目标](./media/site-recovery-vmware-to-azure/gs-target.png)
4. 如果尚未创建存储帐户或网络，请单击“+ 存储帐户”或“+ 网络”，创建 Resource Manager 帐户或内联网络。

## <a name="set-up-replication-settings"></a>设置复制设置

在开始之前，获取短片概述：
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

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

必须在要复制的所有 VMware VM 上安装移动服务。 可以通过多种方式安装移动服务：

1. 从进程服务器使用推送安装进行安装。 需要准备 VM 以使用此方法。
2. 使用 System Center Configuration Manager 或 Azure 自动化 DSC 等部署工具进行安装。
3.  手动安装。

[了解详细信息](site-recovery-vmware-to-azure-install-mob-svc.md)


## <a name="enable-replication"></a>启用复制

开始之前：

- Azure 用户帐户需要具有某些[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能启用新的虚拟机到 Azure 的复制。
- 添加或修改 VM 时，可能需要 15 分钟或更长时间，更改才会生效并显示在门户中。
- 可以在“配置服务器” > “上次联系位置”查看上次发现 VM 的时间。
- 若要添加 VM 而不想要等待执行计划的发现，请突出显示配置服务器（不要单击它），然后单击“刷新”。
- 如果已为推送安装准备了 VM，那么在启用复制时，进程服务器将自动安装移动服务。


### <a name="exclude-disks-from-replication"></a>从复制中排除磁盘

默认情况下将复制计算机上的所有磁盘。 你可以从复制中排除磁盘。 例如，你可能不想要复制包含临时数据，或者每当重新启动计算机或应用程序时刷新的数据（例如 pagefile.sys 或 SQL Server tempdb）的磁盘。

### <a name="replicate-vms"></a>复制 VM

在开始之前，观看短片概述

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]

1. 单击“步骤 2: 复制应用程序” > “源”。
2. 在“源”中选择配置服务器。
3. 在“计算机类型”中，选择“虚拟机”。
4. 在“vCenter/vSphere 虚拟机监控程序”中，选择管理 vSphere 主机的 vCenter 服务器，或选择该主机。
5. 选择进程服务器。 如果尚未创建任何额外的进程服务器，该进程服务器将是配置服务器。 。

    ![启用复制](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. 在“目标”中，选择要创建故障转移 VM 的订阅和资源组。 选择要在 Azure 中用于故障转移 VM 的部署模型（经典或资源管理）。


7. 选择要用于复制数据的 Azure 存储帐户。 如果不想使用已设置的帐户，可以创建一个新帐户。

8. 选择 Azure VM 在故障转移后创建时所要连接的 Azure 网络和子网。 选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。 选择“稍后配置”以选择每个计算机的 Azure 网络。 如果不想使用现有网络，可以创建一个网络。

    ![启用复制](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. 在“虚拟机” > “选择虚拟机”中，单击并选择要复制的每个计算机。 只能选择可以启用复制的计算机。 。

    ![启用复制](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. 在“属性” > “配置属性”中，选择进程服务器在计算机上自动安装移动服务时使用的帐户。
11. 默认情况下会复制所有磁盘。 单击“所有磁盘”并清除不想要复制的所有磁盘。 。 可以稍后再设置其他 VM 属性。

    ![启用复制](./media/site-recovery-vmware-to-azure/enable-replication6.png)
11. 在“复制设置” > “配置复制设置”中，检查是否选择了正确的复制策略。 如果修改策略，更改将应用到复制计算机和新计算机。
12. 如果要将计算机集合到复制组，请启用“多 VM 一致性”并指定组的名称。 。 请注意：

    * 复制组中的计算机将一起复制，并在故障转移时具有在崩溃时保持一致且应用保持一致的共享恢复点。
    * 我们建议你将 VM 和物理服务器集合在一起，使其镜像你的工作负荷。 启用多 VM 一致性可能会影响工作负荷性能，因此，仅当计算机运行相同的工作负荷并且你需要一致性时，才应使用该设置。

    ![启用复制](./media/site-recovery-vmware-to-azure/enable-replication7.png)
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
4. 在“磁盘”中，可以看到 VM 操作系统，以及将要复制的数据磁盘。

#### <a name="managed-disks"></a>托管磁盘

如果要在故障转移到 Azure 时将托管磁盘附加到虚拟机上，可以在“计算和网络” > “计算属性”中，将 VM 的“使用托管磁盘”设置设为“是”。 托管磁盘通过管理与 VM 磁盘关联的存储帐户简化了 Azure IaaS VM 的磁盘管理。 [详细了解托管磁盘](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview)。

   - 仅在故障转移到 Azure 时创建托管磁盘并将其附加到虚拟机上。 启用保护时，本地计算机中的数据将继续复制到存储帐户。  只能为使用 Resource Manager 部署模型部署的虚拟机创建托管磁盘。  

   - 将“使用托管磁盘”设置为“是”时，资源组中只有“使用托管磁盘”设置为“是”的可用性集可供选择。 这是因为使用托管磁盘的虚拟机只能属于“使用托管磁盘”属性设置为“是”的可用性集。 请确保基于想要在故障转移时使用托管磁盘的意图，创建设置了“使用托管磁盘”属性的可用性集。  同样，将“使用托管磁盘”设置为“否”时，资源组中只有“使用托管磁盘”属性设置为“否”的可用性集可供选择。 [详细了解托管磁盘和可用性集](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set)。

  > [!NOTE]
  > 如果用于复制的存储帐户已在任何时间点使用存储服务加密进行加密，则在故障转移期间创建托管磁盘将失败。 可以将“使用托管磁盘”设置为“否”并重试故障转移，或者对虚拟机禁用保护并将虚拟机保护到在任何时间点均未启用存储服务加密的存储帐户。
  > [详细了解存储服务加密和托管磁盘](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption)。


## <a name="run-a-test-failover"></a>运行测试故障转移


完成全部设置后，运行测试故障转移，确保一切按预期运行。 在开始之前，获取短片概述
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


1. 若要故障转移单个计算机，请在“设置”>“复制的项”中，单击“VM” > “+测试故障转移”图标。

    ![测试故障转移](./media/site-recovery-vmware-to-azure/TestFailover.png)

1. 若要故障转移某个恢复计划，请在“设置” > “恢复计划”中，右键单击该计划 >“测试性故障转移”。 若要创建恢复计划，请[遵循这些说明](site-recovery-create-recovery-plans.md)。  

1. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的 Azure 网络。

1. 单击“确定”开始故障转移。 若要跟踪进度，可以单击 VM 以打开其属性，或者在保管库名称 >“设置” > “作业” > “Site Recovery 作业”中选择“测试故障转移”作业。

1. 故障转移完成后，你还应该能够看到副本 Azure 计算机显示在 Azure 门户的“虚拟机”中。 应确保 VM 的大小适当、已连接到相应的网络，并且正在运行。

1. 如果[已准备好故障转移后的连接](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)，应该能够连接到 Azure VM。

1. 完成后，在恢复计划上单击“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。 这将删除在执行测试故障转移期间创建的 VM。

[详细了解](site-recovery-test-failover-to-azure.md)测试故障转移。


## <a name="vmware-account-permissions"></a>VMware 帐户权限

Site Recovery 需要 VMware 的访问权限，以便进程服务器可以自动发现 VM，以及实现 VM 的故障转移和故障回复。

- **迁移**：如果只想要将 VMware VM 迁移到 Azure，而不对其进行故障回复，可以使用具有只读角色的 VMware 帐户。 这样的角色可以运行故障转移，但无法关闭受保护的源计算机。 这不是执行迁移所必需的。
- **复制/恢复**：如果要部署完全复制（复制、故障转移和故障回复），该帐户必须能够运行创建和删除磁盘，VM 开机等操作。
- **自动发现**：至少需要一个只读帐户。


**任务** | **所需的帐户/角色** | **权限** | **详细信息**
--- | --- | --- | ---
**进程服务器自动发现 VMware VM** | 至少需要一个只读用户 | 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 若要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。
**故障转移** | 至少需要一个只读用户 | 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 若要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。<br/><br/> 用于迁移，但不用于完全复制、故障转移和故障回复。
**故障转移和故障回复** | 建议创建一个拥有所需权限的角色 (Azure_Site_Recovery)，然后将它分配到 VMware 用户或组。 | 数据中心对象 – 传播到子对象、角色=Azure_Site_Recovery<br/><br/> 数据存储->分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/> 网络 -> 网络分配<br/><br/> 资源 -> 将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虚拟机 -> 配置<br/><br/> 虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关闭电源、打开电源、安装 VMware 工具<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上载<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 若要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。


## <a name="next-steps"></a>后续步骤

复制启动并运行后，当发生中断时会故障转移到 Azure，并且将从复制的数据创建 Azure VM。 然后可以在 Azure 中访问工作负荷和应用，直到它返回到正常操作时你故障回复到主位置。

- [详细了解](site-recovery-failover.md)不同类型的故障转移，以及如何运行它们。
- 如果要迁移计算机而不执行复制和故障回复，请[阅读详细信息](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers)。
- [详细了解故障回复](site-recovery-failback-azure-to-vmware.md)，以便将 Azure VM 故障回复和复制回本地主站点。

## <a name="third-party-software-notices-and-information"></a>第三方软件通知和信息
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

完整文件可以在 [ Microsoft 下载中心](http://go.microsoft.com/fwlink/?LinkId=529428)上找到。 Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

