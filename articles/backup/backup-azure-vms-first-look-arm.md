---
title: "初步了解：使用恢复服务保管库保护 Azure VM | Microsoft Docs"
description: "使用恢复服务保管库保护 Azure VM。 使用 Resource Manager 部署型 VM、经典部署型 VM 和高级存储 VM 的备份来保护数据。 创建并注册恢复服务保管库。 在 Azure 中注册 VM、创建策略和保护 VM。"
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keyword: backups; vm backup
ms.assetid: 45e773d6-c91f-4501-8876-ae57db517cd1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/10/2016
ms.author: markgal; jimpark
translationtype: Human Translation
ms.sourcegitcommit: d18cd2c117ced64e407e87bcc96da38b0351a341
ms.openlocfilehash: 0b3409074e0b4929fdf1f5a6b915e3814facedf6


---
# <a name="first-look-protect-azure-vms-with-a-recovery-services-vault"></a>初步了解：使用恢复服务保管库保护 Azure VM
> [!div class="op_single_selector"]
> * [使用恢复服务保管库保护 VM](backup-azure-vms-first-look-arm.md)
> * [使用备份保管库保护 VM](backup-azure-vms-first-look.md)
>
>

本教程将引导你完成创建恢复服务保管库和备份 Azure 虚拟机 (VM) 的步骤。 恢复服务保管库保护：

* Azure Resource Manager 部署型 VM
* 经典 VM
* 标准存储 VM
* 高级存储 VM
* 使用 Azure 磁盘加密进行加密的 VM，以及 BEK 和 KEK

有关保护高级存储 VM 的详细信息，请参阅[备份和还原高级存储 VM](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup)

> [!NOTE]
> 本教程假设 Azure 订阅中已有 VM，且已采取措施允许备份服务访问 VM。
>
>

[!INCLUDE [learn-about-Azure-Backup-deployment-models](../../includes/backup-deployment-models.md)]

根据想要保护的虚拟机数量，可以从不同的起点着手 - 如果想要通过一项操作备份多个虚拟机，请转到恢复服务保管库，从保管库仪表板开始备份。 如果想要备份一个 VM，可以直接从 VM 管理边栏选项卡备份。

## <a name="configure-backup-from-vm-management-blade"></a>从 VM 管理边栏选项卡配置备份
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“中心”菜单中，单击“更多服务”，然后在资源列表中，键入“虚拟机”。  此时将显示虚拟机列表。 在虚拟机列表中，选择要备份的虚拟机。 此时将打开虚拟机管理边栏选项卡。
 ![VM 管理边栏选项卡](./media/backup-azure-vms-first-look-arm/vm-management-blade.png)

3. 在 VM 管理边栏选项卡中，单击“设置”左下侧显示的“备份”选项。
![VM 管理边栏选项卡中的“备份”选项](./media/backup-azure-vms-first-look-arm/backup-option-vm-management-blade.png)

4. 此时将打开“启用备份”边栏选项卡。 应在此边栏选项卡中提供两项输入：恢复服务保管库 - 用于存储 VM 备份的 Azure 备份资源；备份策略 - 备份策略指定备份计划，以及备份副本的保留期限。 此边栏选项卡中使用了默认选项。 可以根据备份要求自定义这些选项。

  ![启用备份向导](./media/backup-azure-vms-first-look-arm/vm-blade-enable-backup.png)

5. 对于恢复服务保管库，可以选择现有的保管库或创建新保管库。 如果创建新保管库，将在与虚拟机相同的资源组中创建该保管库，其位置也与虚拟机相同。 如果想要使用不同的值创建恢复服务保管库，请先[创建恢复服务保管库](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm)，然后在步骤 3 中单击“备份”选项，并在此边栏选项卡中选择该保管库。

6. 在“备份策略”边栏选项卡中选择要应用到保管库的备份策略，然后单击“确定”。
    ![选择备份策略](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)

    默认策略的详细信息将在详细信息中列出。 如果要创建策略，请从下拉菜单中选择“新建”。 下拉菜单中还提供了一个选项，用于在创建快照时切换时间。 有关定义备份策略的说明，请参阅[定义备份策略](backup-azure-vms-first-look-arm.md#defining-a-backup-policy)。 单击“确定”后，备份策略将与虚拟机相关联。

7. 单击“启用备份”在虚拟机上配置备份。 这会触发部署。
![“启用备份”按钮](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-button.png)

8. 可以通过通知跟踪配置进度。
![“启用备份”通知](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. 完成“配置备份”的部署后，在 VM 管理边栏选项卡中单击“备份”选项会转到备份的 VM 对应的“备份项”边栏选项卡。
![VM 备份项视图](./media/backup-azure-vms-first-look-arm/backup-item-view.png)

## <a name="configure-backup-from-recovery-services-vault-view"></a>从恢复服务保管库视图配置备份
概括而言，你将要完成以下这些步骤。  

1. 为 VM 创建恢复服务保管库。
2. 使用 Azure 门户选择方案、设置策略，并标识要保护的项。
3. 运行初始备份。

## <a name="create-a-recovery-services-vault-for-a-vm"></a>为 VM 创建恢复服务保管库
恢复服务保管库是存储所有按时间创建的备份和恢复点的实体。 恢复服务保管库还包含应用到受保护 VM 的备份策略。

> [!NOTE]
> 备份 VM 是在本地执行的过程。 无法将某个位置的 VM 备份到另一个位置的恢复服务保管库。 因此，每个有 VM 需要备份的 Azure 位置都必须至少存在一个恢复服务保管库。
>
>

若要创建恢复服务保管库，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“中心”菜单中，单击“**浏览**”，然后在资源列表中，键入“**恢复服务**”。 当你开始键入时，会根据你的输入筛选该列表。 单击“**恢复服务保管库**”。

    ![创建恢复服务保管库步骤 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    此时将显示恢复服务保管库列表。
3. 在“恢复服务保管库”菜单中，单击“添加”。

    ![创建恢复服务保管库步骤 2](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)

    此时将打开恢复服务保管库边栏选项卡，其中会提示你提供“名称”、“订阅”、“资源组”和“位置”。

    ![创建恢复服务保管库步骤 5](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)
4. 对于“名称”，请输入一个友好名称以标识保管库 。 名称对于 Azure 订阅需要是唯一的。 键入包含 2 到 50 个字符的名称。 名称必须以字母开头，只能包含字母、数字和连字符。
5. 单击“订阅”查看可用订阅列表。 如果不确定要使用哪个订阅，请使用默认的（或建议的）订阅。 仅当组织帐户与多个 Azure 订阅关联时，才会有多个选项。
6. 单击“资源组”查看可用资源组列表，或单击“新建”创建资源组。 有关资源组的完整信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)
7. 单击“位置”，为保管库选择地理区域  。 **必须** 与你要保护的虚拟机位于同一区域中。

   > [!IMPORTANT]
   > 如果你不确定 VM 的所在位置，请关闭保管库创建对话框，并转到门户中的虚拟机列表。 如果多个区域中有虚拟机，请在每个区域中创建恢复服务保管库。 请先在第一个位置创建保管库，然后转到下一个位置。 无需指定存储帐户即可存储备份数据 - 恢复服务保管库和 Azure 备份服务会自动处理这种情况。
   >
   >
8. 单击“创建” 。 创建恢复服务保管库可能需要一段时间。 可以在门户右上区域监视状态通知。 创建保管库后，它将显示在恢复服务保管库列表中。

    ![备份保管库列表](./media/backup-azure-vms-first-look-arm/rs-list-of-vaults.png)

现已创建保管库，接下来请了解如何设置存储复制。

### <a name="set-storage-replication"></a>设置存储复制
存储复制选项可让你在异地冗余存储与本地冗余存储之间进行选择。 默认情况下，保管库具有异地冗余存储。 如果这是你的主要备份，请将选项保持设置为异地冗余存储。 如果想要一个更便宜、但持久性不高的选项，请选择“本地冗余存储”。 请参阅 [Azure 存储空间复制概述](../storage/storage-redundancy.md)部分，深入了解[异地冗余](../storage/storage-redundancy.md#geo-redundant-storage)和[本地冗余](../storage/storage-redundancy.md#locally-redundant-storage)存储选项。

若要编辑存储复制设置，请执行以下操作：

1. 选择你的保管库以打开保管库仪表板和“设置”边栏选项卡。 如果“设置”边栏选项卡未打开，请在保管库仪表板中单击“所有设置”。
2. 在“设置”边栏选项卡中，单击“备份基础结构” > “备份配置”，打开“备份配置”边栏选项卡。 在“备份配置”边栏选项卡中，选择保管库的存储复制选项。

    ![备份保管库列表](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    选择好保管库的存储选项后，可以开始将 VM 与保管库相关联。 若要开始关联，请发现及注册 Azure 虚拟机。

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>选择备份目标、设置策略并定义要保护的项
在向保管库注册 VM 前，请先执行发现过程，以确保能够识别任何添加到订阅中的新虚拟机。 该过程将在 Azure 上查询订阅中的虚拟机列表和其他信息，例如云服务名称、区域等。 在 Azure 门户中，方案是指要放入恢复服务保管库中的项。 策略是有关恢复点创建频率和时间的计划。 策略还包含恢复点的保留范围。

1. 如果已打开恢复服务保管库，请转到步骤 2。 如果未打开恢复服务保管库，而是位于 Azure 门户中，请在“中心”菜单中单击“浏览”。

   * 在资源列表中，键入“恢复服务”。
   * 当你开始键入时，会根据你的输入筛选该列表。 出现“恢复服务保管库”时，请单击它。

     ![创建恢复服务保管库步骤 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

     此时将显示恢复服务保管库列表。
   * 在恢复服务保管库列表中选择一个保管库。

     此时将打开选定的保管库仪表板。

     ![打开保管库边栏选项卡](./media/backup-azure-vms-first-look-arm/vault-settings.png)
2. 在保管库仪表板菜单中，单击“备份”  打开“备份”边栏选项卡。

    ![打开“备份”边栏选项卡](./media/backup-azure-vms-first-look-arm/backup-button.png)

    打开该边栏选项卡后，备份服务将在订阅中搜索所有新 VM。

    ![发现 VM](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)
3. 在“备份”边栏选项卡中，单击“备份目标”打开“备份目标”边栏选项卡。

    ![打开“方案”边栏选项卡](./media/backup-azure-vms-first-look-arm/select-backup-goal-one.png)
4. 在“备份目标”边栏选项卡中，将“工作负荷的运行位置”设置为 Azure，并将“要备份的项”设置为“虚拟机”，然后单击“确定”。

    随即关闭“备份目标”边栏选项卡，然后打开“备份策略”边栏选项卡。

    ![打开“方案”边栏选项卡](./media/backup-azure-vms-first-look-arm/select-backup-goal-two.png)
5. 在“备份策略”边栏选项卡中选择要应用到保管库的备份策略，然后单击“确定”。

    ![选择备份策略](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)

    默认策略的详细信息将在详细信息中列出。 如果要创建策略，请从下拉菜单中选择“新建”。 下拉菜单还提供了一个选项，可让你将快照的创建时间切换为 7PM。 有关定义备份策略的说明，请参阅[定义备份策略](backup-azure-vms-first-look-arm.md#defining-a-backup-policy)。 单击“确定”后，备份策略将与保管库相关联。

    接下来，选择要与保管库关联的 VM。
6. 选择要与指定策略相关联的虚拟机，然后单击“选择”。

    ![选择工作负荷](./media/backup-azure-vms-first-look-arm/select-vms-to-backup-new.png)

    如果未看到所需的 VM，请检查它是否在恢复服务保管库所在的同一个 Azure 位置。
7. 现在，已定义保管库的所有设置，接下来请在“备份”边栏选项卡中，单击页面底部的“启用备份”。 随后会将策略部署到保管库和 VM。

    ![启用备份](./media/backup-azure-vms-first-look-arm/enable-backup-settings-new.png)

## <a name="initial-backup"></a>初始备份
在虚拟机上部署备份策略后，并不意味着已备份好数据。 默认情况下，第一个计划的备份（在备份策略中定义）是初始备份。 在执行初始备份之前，“**备份作业**”边栏选项卡上的“上次备份状态”显示为“**警告(等待初始备份)**”。

![等待中的备份](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

除非初始备份预计马上开始，否则建议运行“立即备份” 。

若要运行“立即备份”，请执行以下操作： 

1. 在保管库仪表板上的“备份”磁贴中，单击“Azure 虚拟机” <br/>
    ![设置图标](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    “**备份项**”边栏选项卡随即打开。
2. 在“**备份项**”边栏选项卡中，右键单击要备份的保管库，然后单击“**立即备份**”。

    ![“设置”图标](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    随即会触发备份作业。 <br/>

    ![已触发备份作业](./media/backup-azure-vms-first-look-arm/backup-triggered.png)
3. 若要查看初始备份是否已完成，在保管库仪表板上的“**备份作业**”磁贴中，单击“**Azure 虚拟机**”。

    ![“备份作业”磁贴](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    “备份作业”边栏选项卡随即打开。
4. 在“备份作业”边栏选项卡中，可以看到所有作业的状态。

    ![“备份作业”磁贴](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

   > [!NOTE]
   > 在执行备份操作的过程中，Azure 备份服务将向每个 VM 中的备份扩展发出一条命令，刷新所有写入并取得一致快照。
   >
   >

    完成备份作业后，状态将变为“*已完成*”。

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>在虚拟机中安装 VM 代理
此信息是根据需要提供的。 Azure VM 代理必须安装在 Azure 虚拟机上，备份扩展才能运行。 但是，如果 VM 创建自 Azure 资源库，则 VM 代理已存在于虚拟机上。 从本地数据中心迁移的 VM 上未安装 VM 代理。 在这种情况下，需要安装 VM 代理。 如果在备份 Azure VM 时遇到问题，请先检查是否已在虚拟机上正确安装 Azure VM 代理（请参阅下表）。 如果要创建自定义 VM，[请先确保已选中“安装 VM 代理”复选框](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)，然后再预配虚拟机。

了解 [VM 代理](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409)以及[如何安装它](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

下表提供了适用于 Windows 和 Linux VM 的 VM 代理的其他信息。

| **操作** | **Windows** | **Linux** |
| --- | --- | --- |
| 安装 VM 代理 |<li>下载并安装 [代理 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 你需要有管理员权限才能完成安装。 <li>[更新 VM 属性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) ，指明已安装代理。 |<li> 从 GitHub 安装最新的 [Linux 代理](https://github.com/Azure/WALinuxAgent) 。 你需要有管理员权限才能完成安装。 <li> [更新 VM 属性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) ，指明已安装代理。 |
| 更新 VM 代理 |更新 VM 代理与重新安装 [VM 代理二进制文件](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)一样简单。 <br>确保在更新 VM 代理时，没有任何正在运行的备份操作。 |按照[更新 Linux VM 代理](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)上的说明进行操作。 <br>确保在更新 VM 代理时，没有任何正在运行的备份操作。 |
| 验证 VM 代理安装 |<li>导航到 Azure VM 中的 *C:\WindowsAzure\Packages* 文件夹。 <li>你应会发现 WaAppAgent.exe 文件已存在。<li> 右键单击该文件，转到“**属性**”，然后选择“**详细信息**”选项卡。 “产品版本”字段应为 2.6.1198.718 或更高。 |不适用 |

### <a name="backup-extension"></a>备份扩展
在虚拟机上安装 VM 代理后，Azure 备份服务会将备份扩展安装到 VM 代理上。 Azure 备份服务会无缝地升级和修补备份扩展，不需用户进行额外的干预。

不论 VM 是否在运行，都会由备份服务安装备份扩展。 VM 运行时，很有可能会获得应用程序一致的恢复点。 但是，即使 VM 已关闭并且无法安装扩展，Azure 备份服务也会继续备份 VM。 这被称为脱机 VM。 在这种情况下，恢复点将是 *崩溃一致性*恢复点。

## <a name="troubleshooting-information"></a>故障排除信息
如果你在完成本文中的某些任务时遇到问题，请参阅 [故障排除指南](backup-azure-vms-troubleshoot.md)。

## <a name="pricing"></a>定价
将根据受保护的实例模型对 Azure VM 备份进行收费。 若要了解详细信息，请参阅[备份定价](https://azure.microsoft.com/pricing/details/backup/)

## <a name="questions"></a>有疑问？
如果你有疑问，或者希望包含某种功能，请 [给我们反馈](http://aka.ms/azurebackup_feedback)。



<!--HONumber=Dec16_HO2-->


