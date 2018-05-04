---
title: Azure 备份：使用 Azure 门户还原虚拟机 | Microsoft Docs
description: 使用 Azure 门户从恢复点还原 Azure 虚拟机
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: 还原备份; 如何还原; 恢复点;
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: d3e088841bcf291363ec7c042b0fa160fc7d25ca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>使用 Azure 门户还原虚拟机
可以通过按定义的间隔创建数据快照来保护数据。 这些快照称为恢复点，存储在恢复服务保管库中。 当需要修复或重新生成虚拟机 (VM) 时，可以从保存的任何恢复点还原 VM。 还原恢复点时，可以：

* 新建 VM，表示处于特定时间点的已备份 VM。
* 还原磁盘，并使用进程随附的模板自定义已还原的 VM，或恢复单独的文件。 

本文介绍如何将 VM 还原到新 VM，或者还原所有已备份的磁盘。 有关单独的文件恢复，请参阅[从 Azure VM 备份恢复文件](backup-azure-restore-files-from-vm.md)。

![从 VM 备份还原的三种方式](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure 具有两个不同的部署模型用于创建和处理资源：[Azure 资源管理器和经典](../azure-resource-manager/resource-manager-deployment-model.md)部署模型。 本文介绍有关如何使用资源管理器模型还原已部署 VM 的信息以及相关过程。
>
>

从 VM 备份还原 VM 或所有磁盘包括两个步骤：

* 选择用于还原的还原点。
* 选择还原类型，新建 VM 或还原磁盘，并指定所需参数。 

## <a name="select-a-restore-point-for-restore"></a>选择用于还原的还原点
1. 登录到 [Azure 门户](http://portal.azure.com/)。

2. 在 Azure 菜单中，选择“浏览”。 在服务列表中，键入“恢复服务”。 服务列表会根据键入的内容调整。 出现“**恢复服务保管库**”时，请选择它。

    ![恢复服务保管库](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    随后会显示订阅中保管库的列表。

    ![恢复服务保管库列表](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. 从列表中选择与要还原的 VM 关联的保管库。 选择该保管库时，其仪表板会打开。

    ![选定的恢复服务保管库](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. 在保管库仪表板上的“备份项”磁贴中，选择“Azure 虚拟机”。

    ![保管库仪表板](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    “备份项”边栏选项卡随即打开并显示 Azure VM 列表。

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. 从列表中选择一个 VM 以打开仪表板。 VM 仪表板随即打开包含“还原点”磁贴的“监视”区域。

    ![还原点](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. 在 VM 仪表板菜单中，选择“还原”。

    ![“还原”按钮](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    “还原”边栏选项卡随即打开。

    ![“还原”边栏选项卡](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. 在“还原”边栏选项卡中，选择“还原点”。 “选择还原点”边栏选项卡随即打开。

    ![选择还原点](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    默认情况下，对话框中显示过去 30 天的所有还原点。 使用“**筛选器**”可更改显示的还原点的时间范围。 默认情况下，会显示所有一致性还原点。 修改“所有还原点”筛选器，选择特定的还原点一致性。 有关每种还原点的详细信息，请参阅[数据一致性](backup-azure-vms-introduction.md#data-consistency)。

    还原点一致性选项：

     * 崩溃一致还原点
     * 应用程序一致还原点
     * 文件系统一致还原点
     * 所有还原点

8. 选择一个还原点并选择“确定”。

    ![还原点选项](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    “还原”边栏选项卡会显示已设置还原点。

9. 如果尚未执行此操作，请转到“还原”边栏选项卡。 确保[已选择还原点](#select-restore-point-for-restore)，并选择“还原配置”。 “还原配置”边栏选项卡随即打开。

## <a name="choose-a-vm-restore-configuration"></a>选择 VM 还原配置
选择还原点后，请选择 VM 还原配置。 若要配置已还原 VM，可使用 Azure 门户或 PowerShell。

1. 如果尚未执行此操作，请转到“还原”边栏选项卡。 确保[已选择还原点](#select-restore-point-for-restore)，并选择“还原配置”。 “还原配置”边栏选项卡随即打开。

    ![还原配置向导](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. 在“还原配置”边栏选项卡上，有两个选择：

   * 创建虚拟机

   * 还原磁盘

门户为已还原的 VM 提供“快速创建”选项。 如果想要自定义在创建新 VM 过程中创建的资源的 VM 配置或名称，请使用 PowerShell 或门户还原已备份的磁盘。 使用 PowerShell 命令将其附加到所选 VM 配置。 或者可以使用附带了已还原磁盘的模板来自定义已还原的 VM。 有关如何还原具有多个 NIC 或采用负载均衡器的 VM 的详细信息，请参阅[还原采用特殊网络配置的 VM](#restore-vms-with-special-network-configurations)。 如果 Windows VM 使用 [HUB 许可](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，请还原磁盘并使用本文中所指定的 PowerShell/模板来创建 VM。 确保在创建 VM 时将“许可证类型”指定为“Windows_Server”以利用还原的 VM 上的 HUB 优势。 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>从还原点创建新的 VM
1. 如果尚未执行此操作，请[选择一个还原点](#restore-a vm-with-special-network-configurations)，然后开始从还原点创建新的 VM。 选择还原点后，请在“还原配置”边栏选项卡中，输入或选择以下每个字段的值：

    a. 还原类型。 创建虚拟机。

    b. 虚拟机名称。 为 VM 提供一个名称。 此名称对于资源组（对于Azure 资源管理器部署型 VM）或云服务（对于经典 VM）必须是唯一的。 如果 VM 已存在于订阅中，则不能替换它。

    c. 资源组。 使用现有资源组，或创建一个新的资源组。 如果要还原经典 VM，请使用此字段来指定新云服务的名称。 创建新的资源组/云服务时，该名称必须全局唯一。 通常，云服务名称与面向公众的 URL 关联：例如：[cloudservice].cloudapp.net。 如果尝试使用已用的云资源组/云服务名称，Azure 会为资源组/云服务分配与 VM 相同的名称。 Azure 会显示不与任何地缘组关联的资源组/云服务和 VM。 有关详细信息，请参阅[如何从地缘组迁移到区域虚拟网络](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。

    d. 虚拟网络。 创建 VM 时，选择虚拟网络。 该字段提供与订阅关联的所有虚拟网络。 VM 的资源组显示在括号中。

    e. 子网。 如果虚拟网络包含子网，则默认情况下选择第一个子网。 如果包含其他子网，请选择所需的子网。

    f. 存储帐户。 此菜单将列出与恢复服务保管库位于相同位置的存储帐户。 不支持区域冗余的存储帐户。 如果没有与恢复服务保管库位于相同位置的存储帐户，必须创建一个存储帐户，然后再开始还原操作。 存储帐户的复制类型显示在括号中。

    ![还原配置向导](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * 如果要还原资源管理器部署型 VM，则必须标识虚拟网络。 对于经典 VM，虚拟网络是可选的。

    > * 如果要通过托管磁盘还原 VM，请确保未在选定的存储帐户的生存期内为 Azure 存储服务加密启用该帐户。

    > * 根据所选存储帐户的存储类型（高级或标准），还原的所有磁盘都是相应的高级或标准磁盘。 还原时，我们当前不支持混合模式的磁盘。
    >
    >

2. 在“还原配置”边栏选项卡中，选择“确定”完成还原配置。 在“还原”边栏选项卡中，选择“还原”以触发还原操作。

## <a name="restore-backed-up-disks"></a>还原备份磁盘
若要自定义从“还原配置”边栏选项卡显示的磁盘之外的备份磁盘创建的 VM，请选择“还原磁盘”作为“还原类型”的值。 如果选择此选项，需要提供备份中磁盘要复制到的存储帐户。 选择存储帐户时，请选择与恢复服务保管库共享相同位置的帐户。 不支持区域冗余的存储帐户。 如果没有与恢复服务保管库位于相同位置的存储帐户，必须创建一个存储帐户，然后再开始还原操作。 存储帐户的复制类型显示在括号中。

完成还原操作后，可以：

* [使用模板自定义已还原的 VM](#use-templates-to-customize-restore-vm)
* [使用已还原的磁盘附加到现有 VM](../virtual-machines/windows/attach-managed-disk-portal.md)
* [从已还原的磁盘使用 PowerShell 新建 VM](./backup-azure-vms-automation.md#restore-an-azure-vm)

在“还原配置”边栏选项卡中，选择“确定”完成还原配置。 在“还原”边栏选项卡中，选择“还原”以触发还原操作。

![已完成恢复配置](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>跟踪还原操作
触发还原操作后，备份服务会创建一个作业用于跟踪还原操作。 备份服务还会创建并在门户的“通知”区域中临时显示通知。 如果未显示通知，请选择“通知”符号查看通知。

![已触发还原](./media/backup-azure-arm-restore-vms/restore-notification.png)

若要查看正在进行的操作或查看已完成的操作，请打开“备份作业”列表。

1. 在 Azure 菜单中，选择“浏览”，并在服务列表中键入“恢复服务”。 服务列表会根据键入的内容调整。 出现“**恢复服务保管库**”时，请选择它。

    ![打开恢复服务保管库](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    随后会显示订阅中保管库的列表。

    ![恢复服务保管库列表](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. 从列表中选择与已还原的 VM 关联的保管库。 选择该保管库时，其仪表板会打开。

3. 在“备份作业”磁贴上的保管库仪表板中，选择“Azure 虚拟机”以显示与保管库关联的作业。

    ![保管库仪表板](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    “备份作业”边栏选项卡随即打开并显示作业列表。

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>使用模板自定义还原 VM
[还原磁盘操作完成](#Track-the-restore-operation)后，使用在还原操作过程中生成的模板来创建配置与备份配置不同的新 VM。 还可使用该模板自定义从还原点创建新 VM 时创建的资源的名称。 

> [!NOTE]
> 对于 2017 年 3 月 1 日以后创建的恢复点，模板添加为还原磁盘的一部分。 这些模板适用于非托管磁盘 VM。 将来的版本可支持托管磁盘 VM。 
>
>

若要获取在执行还原磁盘操作过程中生成的模板，请执行以下操作：

1. 转到对应于还原作业的作业详细信息。

2. 在“还原作业详细信息”屏幕上，选择“部署模板”启动模板部署。 

     ![深入到还原作业](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
3. 在自定义部署的“部署模板”边栏选项卡上，请在部署之前，使用模板部署来[编辑并部署模板](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，或者通过[创作模板](../azure-resource-manager/resource-group-authoring-templates.md)来追加其他自定义项。 

   ![加载模板部署](./media/backup-azure-arm-restore-vms/loading-template.png)
   
4. 输入所需的值后，接受“条款和条件”，并选择“购买”。

   ![提交模板部署](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>还原后的步骤
* 如果使用基于 cloud-init 的 Linux 分发（如 Ubuntu），出于安全原因，还原后将阻止密码。 请在还原的 VM 上使用 VMAccess 扩展 [重置密码](../virtual-machines/linux/reset-password.md)。 建议在这些分发上使用 SSH 密钥以避免还原后重置密码。
* 会安装存在于备份配置期间的扩展，但不会启用这些扩展。 如果出现问题，请重新安装这些扩展。 
* 如果备份的 VM 具有静态 IP，则在还原后，已还原的 VM 将具有动态 IP 以避免在创建还原的 VM 时发生冲突。 详细了解如何[向还原的 VM 添加静态 IP](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)。
* 还原的 VM 不会设置可用性值。 使用已还原的磁盘从 PowerShell 或模板创建 VM 时，建议使用还原磁盘选项[添加可用性集](../virtual-machines/windows/tutorial-availability-sets.md)。 


## <a name="backup-for-restored-vms"></a>备份还原的 VM
如果将 VM 还原到与最初备份 VM 时所在的资源组同名的资源组，则还原之后，会继续备份该 VM。 如果将 VM 还原到其他资源组或为已还原的 VM 指定其他名称，则该 VM 被视为新的 VM。 需要为已还原的 VM 设置备份。

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>在发生 Azure 数据中心灾难期间还原 VM
如果运行已备份 VM 的主数据中心遇到灾难性故障，并且已将备份保管库配置为异地冗余，则 Azure 备份允许将该 VM 还原到配对的数据中心。 在这种情况下，请选择配对的数据中心中存在的存储帐户。 还原过程的其余部分保持不变。 备份使用配对地区中的计算服务来创建还原的 VM。 有关详细信息，请参阅 [Azure 数据中心复原](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)。

## <a name="restore-domain-controller-vms"></a>还原域控制器 VM
备份支持对域控制器 (DC) VM 进行备份的方案。 但是，在还原过程中，必须谨慎操作。 还原过程是否正确取决于域的结构。 最简单的情况是单个域中有单个 DC。 对于生产负载，更常见的情况是一个域中包含多个 DC，其中某些 DC 可能位于本地。 最终，可能拥有一个包含多个域的林。 

从 Active Directory 的角度来看，Azure VM 与受支持的新式虚拟机监控程序上任何其他 VM 都类似。 本地虚拟机监控程序的主要差异是 Azure 中不提供 VM 控制台。 某些方案（如使用裸机恢复 (BMR) 类型备份进行恢复）需要控制台。 但是，通过备份保管库进行 VM 还原完全取代了 BMR。 还可使用目录服务还原模式 (DSRM)，因此所有 Active Directory 恢复方案都是可行的。 有关详细信息，请参阅[虚拟化域控制器的备份和还原注意事项](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers)和[规划 Active Directory 林恢复](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx)。

### <a name="single-dc-in-a-single-domain"></a>单个域中有单个 DC
可以通过 Azure 门户或 PowerShell 还原 VM（与任何其他 VM 一样）。

### <a name="multiple-dcs-in-a-single-domain"></a>单个域中有多个 DC
可通过网络访问同一域中的其他 DC 时，可像还原任何 VM 一样还原 DC。 对于域中剩余的最后一个 DC，或者在隔离网络中执行的恢复，必须遵循林恢复过程。

### <a name="multiple-domains-in-one-forest"></a>一个林中有多个域
可通过网络访问同一域中的其他 DC 时，可像还原任何 VM 一样还原 DC。 建议在所有其他情况下恢复林。

## <a name="restore-vms-with-special-network-configurations"></a>还原采用特殊网络配置的 VM
可以备份和还原采用以下特殊网络配置的 VM。 但是，在执行还原过程时，这些配置需要经过一些特殊考虑：

* 采用负载均衡器的 VM（内部和外部）
* 具有多个保留 IP 的 VM
* 具有多个 NIC 的 VM

> [!IMPORTANT]
> 创建 VM 的特殊网络配置时，必须使用 PowerShell 从已还原的磁盘创建 VM。
>
>

若要在还原到磁盘之后完全重新创建 VM，请执行以下步骤：

1. 使用 [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) 从恢复服务保管库还原磁盘。

2. 使用 PowerShell cmdlet 创建负载均衡器/多个 NIC/多个保留 IP 所需的 VM 配置。 使用该配置来创建采用所需配置的 VM：

   a. 使用[内部负载均衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)在云服务中创建 VM。

   b. 创建 VM 以连接到[面向 Internet 的负载均衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/)。

   c. 创建具有[多个 NIC 的 VM](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)。

   d. 创建具有[多个保留 IP](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/) 的 VM。

## <a name="next-steps"></a>后续步骤
既然可以还原 VM，接下来请参阅故障排除文章中有关 VM 常见错误的信息。 另请参阅有关在 VM 中管理任务的文章。

* [排查错误](backup-azure-vms-troubleshoot.md#restore)
* [管理虚拟机](backup-azure-manage-vms.md)
