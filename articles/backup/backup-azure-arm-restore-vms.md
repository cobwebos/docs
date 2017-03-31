---
title: "Azure 备份：使用 Azure 门户还原虚拟机 | Microsoft 文档"
description: "使用 Azure 门户从恢复点还原 Azure 虚拟机"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "还原备份; 如何还原; 恢复点;"
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/12/2017
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1dc2883056eab9764cda674b42fa40c517550ccd
ms.lasthandoff: 03/27/2017


---
# <a name="use-azure-portal-to-restore-virtual-machines"></a>使用 Azure 门户还原虚拟机
> [!div class="op_single_selector"]
> * [在经典门户中还原 VM](backup-azure-restore-vms.md)
> * [在 Azure 门户中还原 VM](backup-azure-arm-restore-vms.md)
>
>

可以通过按定义的间隔创建数据快照来保护数据。 这些快照称为恢复点，存储在恢复服务保管库中。 当你需要修复或重建 VM 时，可以从保存的任何恢复点还原 VM。 还原某个恢复点时，可以创建一个新 VM 来充当已备份的 VM 的时间点表示形式，或者还原磁盘并使用其中随附的模板来自定义已还原的 VM，或者执行单独的文件恢复。 本文介绍如何将 VM 还原到新 VM，或者还原所有已备份的磁盘。 有关单独的文件恢复，请参阅[从 Azure VM 备份恢复文件](backup-azure-restore-files-from-vm.md)

![3-ways-restore-from-vm-backup](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure 有两种用于创建和使用资源的部署模型：[Resource Manager 部署模型和经典部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文提供有关还原使用 Resource Manager 模型部署的 VM 的信息和过程。
>
>

从 VM 备份还原 VM 或所有磁盘包括两个步骤：

1. 选择用于还原的还原点
2. 选择还原类型 - 创建新 VM，或者还原磁盘并指定所需的参数。 

## <a name="select-restore-point-for-restore"></a>选择用于还原的还原点
1. 登录到 [Azure 门户](http://portal.azure.com/)
2. 在 Azure 菜单中，单击“**浏览**”，然后在服务列表中键入“**恢复服务**”。 服务列表会根据键入的内容调整。 出现“**恢复服务保管库**”时，请选择它。

    ![打开恢复服务保管库](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    随后将显示订阅中保管库的列表。

    ![恢复服务保管库列表](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. 从列表中选择与要还原的 VM 关联的保管库。 当你单击该保管库时，其仪表板将会打开。

    ![恢复服务保管库列表](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. 现在你已进入保管库仪表板。 在“**备份项**”磁贴上，单击“**Azure 虚拟机**”，以显示与保管库关联的 VM。

    ![保管库仪表板](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    “**备份项**”边栏选项卡随即打开并显示 Azure 虚拟机列表。

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. 从列表中选择一个 VM 以打开仪表板。 在 VM 仪表板中，默认会打开包含“还原点”磁贴的“监视”区域。

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. 在 VM 仪表板菜单中，单击“**还原**”

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    此时将打开“还原”边栏选项卡。

    ![还原边栏选项卡](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. 在“**还原**”边栏选项卡中，单击“**还原点**”，打开“**选择还原点**”边栏选项卡。

    ![还原边栏选项卡](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    默认情况下，对话框会显示过去 30 天的所有还原点。 使用“**筛选器**”可更改显示的还原点的时间范围。 默认情况下，将显示所有一致性还原点。 修改“**所有还原点**”筛选器，以选择特定的还原点一致性。 有关每种类型还原点的详细信息，请参阅[数据一致性](backup-azure-vms-introduction.md#data-consistency)的说明。  

   * 从“**还原点一致性**”列表中选择：
     * 崩溃一致还原点，
     * 应用程序一致还原点，
     * 文件系统一致还原点
     * 所有还原点。  
8. 选择一个还原点并单击“**确定**”。

    ![选择还原点](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    “**还原**”边栏选项卡将显示已设置还原点。

    ![已设置还原点](./media/backup-azure-arm-restore-vms/recovery-point-set.png)
9. 设置还原点后，“**还原**”边栏选项卡中会自动打开“**还原配置**”。

## <a name="choosing-a-vm-restore-configuration"></a>选择 VM 还原配置
选择还原点点，请选择用于还原 VM 的配置。 用于配置已还原 VM 的选项包括使用 Azure 门户或 PowerShell。

1. 如果尚未到达这一步，请转到“**还原**”边栏选项卡。 确保已选择“还原点”，然后单击“还原配置”，打开“恢复配置”边栏选项卡。[](#select-restore-point-for-restore)

    ![已设置恢复配置向导](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. 在“还原配置”边栏选项卡上，有两个选择：
   * 还原完整虚拟机
   * 还原备份磁盘

门户针对已还原的 VM 提供“快速创建”选项。 如果想要自定义在创建新 VM 过程中创建的资源的 VM 配置或名称，请使用 PowerShell 或门户还原已备份的磁盘，然后使用 PowerShell 命令将它们附加到所选的 VM 配置，或者使用还原磁盘随附的模板来自定义已还原的 VM。 有关如何还原使用多个 NIC 或负载均衡器的 VM 的详细信息，请参阅[还原采用特殊网络配置的 VM](#restoring-vms-with-special-network-configurations)。 
 
## <a name="create-a-new-vm-from-restore-point"></a>从还原点创建新的 VM
如果尚未执行此操作，请[选择一个还原点](#restoring-vms-with-special-network-configurations)，然后继续从还原点创建新的 VM。 选择还原点后，请在“还原配置”边栏选项卡中，输入或选择以下每个字段的值：

* **还原类型** - 创建虚拟机。
* **虚拟机名称** - 提供 VM 的名称。 此名称对于资源组（适用于 Resource Manager 部署型 VM）或云服务（适用于经典 VM）必须是唯一的。 如果虚拟机已存在于订阅中，则不能替换该虚拟机。
* **资源组** - 使用现有资源组，或创建新组。 如果要还原经典 VM，请使用此字段来指定新云服务的名称。 创建新资源组/云服务时，该名称必须全局唯一。 通常，云服务名称与面向公众的 URL 相关联 - 例如：[cloudservice].cloudapp.net。 如果你尝试使用已用的云资源组/云服务名称，Azure 将为资源组/云服务分配与 VM 相同的名称。 Azure 将显示不与任何地缘组关联的资源组/云服务和 VM。 有关详细信息，请参阅[如何从地缘组迁移到区域虚拟网络 (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。
* **虚拟网络** - 创建 VM 时，请选择虚拟网络 (VNET)。 该字段提供与订阅关联的所有 VNET。 VM 的资源组显示在括号中。
* **子网** - 如果 VNET 包含子网，则默认情况下会选择第一个子网。 如果有其他子网，请选择所需的子网。
* **存储帐户** - 此菜单列出与恢复服务保管库位于相同位置的存储帐户。 不支持区域冗余的存储帐户。 如果没有与恢复服务保管库位于同一位置的存储帐户，必须在开始还原操作之前创建一个存储帐户。 存储帐户的复制类型在括号中注明。

![已设置还原配置向导](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

> [!NOTE]
> 若要还原 Resource Manager 部署型 VM，则必须标识虚拟网络 (VNET)。 对于经典 VM，虚拟网络 (VNET) 是可选的。
>
>

在“**还原配置**”边栏选项卡中，单击“**确定**”完成还原配置。 在“**还原**”边栏选项卡中，单击“**还原**”以触发还原操作。

## <a name="restore-backed-up-disks"></a>还原备份磁盘
如果要自定义从备份磁盘而不是还原配置边栏选项卡显示的磁盘中创建的虚拟机，请选择“还原磁盘”作为“还原类型”的值。 如果选择此选项，需要提供备份中磁盘要复制到的存储帐户。 选择存储帐户时，可选择与恢复服务保管库共享相同位置的帐户。 不支持区域冗余的存储帐户。 如果没有与恢复服务保管库位于同一位置的存储帐户，必须在开始还原操作之前创建一个存储帐户。 存储帐户的复制类型在括号中注明。

完成还原操作后，可以：
* [使用模板来自定义已还原的 VM](#use-templates-to-customize-restore-vm)
* [使用已还原的磁盘附加到现有虚拟机](../virtual-machines/virtual-machines-windows-attach-disk-portal.md)
* [使用 PowerShell 从已还原的磁盘创建新虚拟机。](./backup-azure-vms-automation.md#restore-an-azure-vm)

在“**还原配置**”边栏选项卡中，单击“**确定**”完成还原配置。 在“**还原**”边栏选项卡中，单击“**还原**”以触发还原操作。

![已完成恢复配置](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>跟踪还原操作
触发还原操作后，备份服务将创建一个作业用于跟踪还原操作。 备份服务还会创建并在门户的“通知”区域中临时显示通知。 如果你看不到通知，随时可以单击“通知”图标来查看通知。

![已触发还原](./media/backup-azure-arm-restore-vms/restore-notification.png)

若要查看正在进行的操作或查看已完成的操作，请打开“备份作业”列表。

1. 在 Azure 菜单中，单击“**浏览**”，然后在服务列表中键入“**恢复服务**”。 服务列表会根据键入的内容调整。 出现“**恢复服务保管库**”时，请选择它。

    ![打开恢复服务保管库](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    随后将显示订阅中保管库的列表。

    ![恢复服务保管库列表](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. 从列表中选择与已还原的 VM 关联的保管库。 当你单击该保管库时，其仪表板将会打开。
3. 在保管库仪表板中的“**备份作业**”磁贴上，单击“**Azure 虚拟机**”以显示与保管库关联的作业。

    ![保管库仪表板](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    “**备份作业**”边栏选项卡将打开并显示作业列表。

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-restore-vm"></a>使用模板来自定义还原 VM
[完成磁盘还原操作](#Track-the-restore-operation)后，可以使用还原操作过程中生成的模板来创建一个配置与备份配置不同的新 VM，或者自定义在从还原点创建新 VM 时创建的资源的名称。 

> [!NOTE]
> 对于 2017 年 3 月 1 日以后创建的恢复点，模板将添加为还原磁盘的一部分。 这些模板适用于未加密的 VM 和非托管磁盘 VM。 将来的版本可支持加密的 VM 和托管磁盘 VM。 
>
>

若要获取在执行还原磁盘操作过程中生成的模板，请执行以下操作：

1. 转到对应于还原作业的作业详细信息。 
2. 此时将列出可从中下载模板的模板 URI。 请记下值中的容器名称。 

     ![深入到还原作业](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
     
3. 记下值中的目标存储帐户名称、容器名称和模板 Blob URI。 转到目标存储帐户，选择“Blob”>“容器”，然后转到“文件”并下载名称以 *azuredeploy* 开头的文件。

    ![download-template-storage-account](./media/backup-azure-arm-restore-vms/download-template.png)
    
   或者，也可以使用[Azure 存储资源管理器](http://storageexplorer.com/)转到相应的订阅 > 目标存储帐户，选择“Blob 容器”，然后选择上一步骤中记下的容器名称。 在右窗格（其中显示了容器内的文件）中，下载名称以 *azuredeploy* 开头的文件。 
   
   ![download-template-storage-explorer](./media/backup-azure-arm-restore-vms/template-storage-explorer-download.png)
     
下载模板后，请在部署之前，使用模板部署来[编辑并部署模板](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，或者通过[创作模板](../azure-resource-manager/resource-group-authoring-templates.md)来追加其他自定义项。 可以使用“加载文件”选项来部署前面下载的模板。 

   ![加载模板部署](./media/backup-azure-arm-restore-vms/loading-template.png)
   
输入所需的值后，接受“条款和条件”，然后单击“购买”。

   ![提交模板部署](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>还原后的步骤
* 如果使用基于 cloud-init 的 Linux 分发（如 Ubuntu），出于安全原因，还原后将阻止密码。 请在还原的 VM 上使用 VMAccess 扩展 [重置密码](../virtual-machines/linux/classic/reset-access.md)。 建议在这些分发上使用 SSH 密钥以避免还原后重置密码。
* 将会安装存在于备份配置期间的扩展，但不会启用这些扩展。 如果发现任何问题，请重新安装扩展。 
* 如果备份的 VM 具有静态 IP，则还原的 VM 将具有动态 IP 以避免在创建还原的 VM 时发生冲突。 详细了解如何[向还原的 VM 添加静态 IP](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
* 还原的 VM 将不会设置可用性值。 使用已还原的磁盘从 PowerShell 或模板创建 VM 时，建议使用还原磁盘选项和[添加可用性集](../virtual-machines/virtual-machines-windows-create-availability-set.md#use-powershell-to-create-an-availability-set)。 

## <a name="backup-for-restored-vms"></a>备份还原的 VM
如果将 VM 还原到的资源组与最初备份 VM 时所在的资源组同名，则还原之后，会继续备份该 VM。 如果将 VM 还原到了不同的资源组或者为还原的 VM 指定了不同的名称，则系统会将此 VM 视为新 VM，因此需要为还原的 VM 设置备份。

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>在发生 Azure 数据中心灾难期间还原 VM
如果运行已备份 VM 的主数据中心遇到灾难性故障，并且你已将备份保管库配置为异地冗余，则 Azure 备份允许将该 VM 还原到配对的数据中心。 在这种情况下，需要选择一个在配对数据中心内存在的存储帐户，而余下的还原过程将保持不变。 Azure 备份使用配对地区中的计算服务来创建还原的虚拟机。 详细了解 [Azure 数据中心复原](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-vms-with-special-network-configurations"></a>还原采用特殊网络配置的 VM
可以备份和还原采用以下特殊网络配置的 VM。 但是，在执行还原过程时，这些配置需要经过一些特殊的考虑。

* 采用负载均衡器的 VM（内部和外部）
* 具有多个保留 IP 的 VM
* 具有多个 NIC 的 VM

> [!IMPORTANT]
> 创建 VM 的特殊网络配置时，必须使用 PowerShell 从还原的磁盘创建 VM。
>
>

若要在还原到磁盘之后完全重新创建虚拟机，请执行以下步骤：

1. 使用 [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) 从恢复服务保管库还原磁盘
2. 使用 PowerShell cmdlet 创建负载均衡器/多个 NIC/多个保留 IP 所需的 VM 配置，并使用该配置创建具有所需配置的 VM。

   * 使用[内部负载均衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)在云服务中创建 VM
   * 创建 VM 以连接到[面向 Internet 的负载均衡器](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * 创建具有[多个 NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/) 的 VM
   * 创建具有[多个保留 IP](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/) 的 VM

## <a name="next-steps"></a>后续步骤
既然你可以还原 VM，接下来请参阅故障排除文章中有关 VM 常见错误的信息。 另请参阅有关在 VM 中管理任务的文章。

* [排查错误](backup-azure-vms-troubleshoot.md#restore)
* [管理虚拟机](backup-azure-manage-vms.md)

