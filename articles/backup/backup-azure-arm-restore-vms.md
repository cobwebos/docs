---
title: Azure 备份：使用 Azure 门户还原虚拟机
description: 使用 Azure 门户从恢复点还原 Azure 虚拟机
services: backup
author: geethalakshmig
manager: vijayts
keywords: 还原备份; 如何还原; 恢复点;
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: 0d78ae294cea383fbe59a1f7968d8bf18b1942d1
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422950"
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

2. 在 Azure 菜单中，选择“所有服务”。 在服务列表中，键入“恢复服务”或转到“存储”，其中将列出“恢复服务保管库”，将其选中。

    ![恢复服务保管库](./media/backup-azure-arm-restore-vms/open-recovery-services-vault1.png)

3. 随后会显示订阅中保管库的列表。

    ![恢复服务保管库列表](./media/backup-azure-arm-restore-vms/list-of-rs-vaults1.png)

4. 从恢复服务保管库列表中，选择与要还原的 VM 关联的保管库。 选择该保管库时，其仪表板会打开。

    ![选定的恢复服务保管库](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade1.png)

5. 在保管库仪表板上的“备份项”磁贴中，选择“Azure 虚拟机”。

    ![保管库仪表板](./media/backup-azure-arm-restore-vms/vault-dashboard1.png)

6. 包含 Azure VM 列表的“备份项”边栏选项卡随即打开。

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault1.png)

7. 从列表中选择一个 VM 以打开仪表板。 VM 仪表板随即打开包含“恢复点”的“监视”区域。 可在此边栏选项卡中执行所有 VM 级别的操作，如“立即备份”、“文件恢复”、“停止备份”。
在此边栏选项卡中可通过多种方式执行还原。 请注意，此边栏选项卡只列出过去 30 天的恢复点。

    a) 右键单击此边栏选项卡中的恢复点（不超过 30 天）并启动“还原 VM”。

    b) 若要还原超出 30 天的恢复点，可以使用边栏选项卡中提供的“单击此处”。

    c) 菜单标头中的“还原 VM”提供一个选项，用于根据需要按自定义日期列出和筛选 VM。

    使用“筛选器”可更改显示的还原点的时间范围。 默认情况下，会显示所有一致性还原点。 修改“所有还原点”筛选器，以选择特定的还原点一致性。 有关每种还原点的详细信息，请参阅[数据一致性](backup-azure-vms-introduction.md#data-consistency)。

    还原点一致性选项：
    - 崩溃一致还原点
    - 应用程序一致还原点
    - 文件系统一致还原点
    - 所有还原点

  ![还原点](./media/backup-azure-arm-restore-vms/vm-blade1.png)

    >  [!NOTE]
    > 恢复类型表示它是位于客户存储帐户还是位于保管库，或者同时位于两者中。 详细了解[即时恢复点](https://azure.microsoft.com/blog/large-disk-support/)。

8. 在“还原”边栏选项卡中，选择“还原点”。

    ![选择还原点](./media/backup-azure-arm-restore-vms/select-recovery-point1.png)

    单击“确定”时，“还原”边栏选项卡会显示已设置还原点。
9. 如果尚未执行此操作，请转到“还原”边栏选项卡。 确保[已选择还原点](#select-a-restore-point-for-restore)，并选择“还原配置”。 “还原配置”边栏选项卡随即打开。

## <a name="choose-a-vm-restore-configuration"></a>选择 VM 还原配置
选择还原点后，请选择 VM 还原配置。 若要配置已还原 VM，可使用 Azure 门户或 PowerShell。

1. 如果尚未执行此操作，请转到“还原”边栏选项卡。 确保[已选择还原点](#select-a-restore-point-for-restore)，并选择“还原配置”。 “还原配置”边栏选项卡随即打开。
2. 此边栏选项卡目前包含两个选项：一个是默认的“新建”选项；另一个是“替换现有”选项，用于就地还原以替换仅包含现有配置和扩展的磁盘。

> [!NOTE]
> 我们正在努力在接下来的几个月内将整个 VM 替换为磁盘、网络设置、配置和扩展。
>
>

 在用于将数据还原到新 VM 或新磁盘的“新建”选项中，有两个选项可供选择：

 ![还原配置向导](./media/backup-azure-arm-restore-vms/restore-configuration-create-new.png)

 - 创建虚拟机
 - 还原磁盘

![还原配置向导](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)

门户为已还原的 VM 提供“快速创建”选项。 如果想要自定义在创建新 VM 过程中创建的资源的 VM 配置或名称，请使用 PowerShell 或门户还原已备份的磁盘。 使用 PowerShell 命令将其附加到所选 VM 配置。 或者可以使用附带了已还原磁盘的模板来自定义已还原的 VM。 有关如何还原具有多个 NIC 或采用负载均衡器的 VM 的详细信息，请参阅[还原采用特殊网络配置的 VM](#restore-vms-with-special-network-configurations)。 如果 Windows VM 使用 [HUB 许可](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，请还原磁盘并使用本文中所指定的 PowerShell/模板来创建 VM。 确保在创建 VM 时将“许可证类型”指定为“Windows_Server”以利用还原的 VM 上的 HUB 优势。 请注意，此操作也可在创建 VM 后完成。

## <a name="create-a-new-vm-from-a-restore-point"></a>从还原点创建新的 VM
1. 在上一节中提及的“还原配置”边栏选项卡中，输入或选择以下每个字段的值：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 还原类型。 创建虚拟机。

    b. 虚拟机名称。 输入订阅中不存在的 VM 名称。

    c. 资源组。 使用现有资源组，或创建一个新的资源组。 如果要还原经典 VM，请使用此字段来指定新云服务的名称。 创建新的资源组/云服务时，该名称必须全局唯一。 通常，云服务名称与面向公众的 URL 关联：例如：[cloudservice].cloudapp.net。 如果尝试使用已用的云资源组/云服务名称，Azure 会为资源组/云服务分配与 VM 相同的名称。 Azure 会显示不与任何地缘组关联的资源组/云服务和 VM。 有关详细信息，请参阅[如何从地缘组迁移到区域虚拟网络](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。

    d. 虚拟网络。 创建 VM 时，选择虚拟网络。 该字段提供与订阅关联的所有虚拟网络。 VM 的资源组显示在括号中。

    e. 子网。 如果虚拟网络包含子网，则默认情况下选择第一个子网。 如果包含其他子网，请选择所需的子网。

    f. **存储位置**。 存储帐户是临时位置。 此菜单将列出与恢复服务保管库位于相同位置的存储帐户。 不支持区域冗余的存储帐户。 如果没有与恢复服务保管库位于相同位置的存储帐户，必须创建一个存储帐户，然后再开始还原操作。 存储帐户的复制类型显示在括号中。

    ![还原配置向导](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

    > [!NOTE]
    > * 虚拟网络对经典 VM 是可选的，但对资源管理器部署的 VM 是必需的。

    > * 临时位置的存储帐户（高级或标准）中提供的存储类型决定还原磁盘存储类型。 还原时，我们当前不支持混合模式的磁盘。
    >
    >

2. 在“还原配置”边栏选项卡中，选择“确定”完成还原配置。 在“还原”边栏选项卡中，选择“还原”以触发还原操作。

## <a name="restore-backed-up-disks"></a>还原备份磁盘
“还原配置”边栏选项卡中的还原类型值“还原磁盘”支持创建包含自定义配置的 VM。 还原磁盘时，选择的存储帐户应与恢复服务保管库位于相同位置。 如果没有与恢复服务保管库位于同一位置的存储帐户，则必须创建一个存储帐户。 不支持 ZRS 存储帐户。 存储帐户的复制类型显示在括号中。

对于还原后操作，请参阅以下资源：
* [使用模板自定义已还原的 VM](#use-templates-to-customize-restore-vm)
* [使用已还原的磁盘附加到现有 VM](../virtual-machines/windows/attach-managed-disk-portal.md)
* [从已还原的磁盘使用 PowerShell 新建 VM](./backup-azure-vms-automation.md#restore-an-azure-vm)

在“还原配置”边栏选项卡中，选择“确定”完成还原配置。 在“还原”边栏选项卡中，选择“还原”以触发还原操作。

![已完成恢复配置](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

“放置还原”通过“替换现有”选项卡完成。

## <a name="replace-existing-disks-from-a-restore-point"></a>替换还原点中的现有磁盘
“替换现有”选项可将当前 VM 中的现有磁盘替换为所选的还原点。 仅在当前 VM 存在时，才可执行此操作。 如果出于任何原因删除了 VM，此操作将无法执行；或者，建议“新建”VM 或磁盘以继续执行还原操作。 在此操作过程中，作为预防措施，请在启动替换磁盘操作之前备份数据。 此操作将在保管库中创建一个快照和一个恢复点，其保留期如在配置的备份策略中所计划。 如果还原点的磁盘数多于/少于当前 VM，则还原点中的磁盘数将仅反映在 VM 中。 目前托管磁盘和加密 VM 不支持“替换现有”选项。 [通用 VM](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) 以及使用[自定义映像](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)创建的 VM 也不支持此选项。  

 在“还原配置”边栏选项卡上，需要选择的唯一输入是“临时位置”。

   ![还原配置向导“替换现有”](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

 a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 还原类型。 表示所选还原点的替换磁盘将替换现有 VM 中的磁盘。

 b. **临时位置**。 存储帐户是托管磁盘的临时位置。 此菜单将列出与恢复服务保管库位于相同位置的存储帐户。 不支持区域冗余的存储帐户。 如果没有与恢复服务保管库位于相同位置的存储帐户，必须创建一个存储帐户，然后再开始还原操作。 存储帐户的复制类型显示在括号中。

## <a name="track-the-restore-operation"></a>跟踪还原操作
触发还原操作后，备份服务会创建一个作业用于跟踪还原操作。 备份服务还会创建并在门户的“通知”区域中临时显示通知。 如果未显示通知，请选择“通知”符号查看通知。

![已触发还原](./media/backup-azure-arm-restore-vms/restore-notification1.png)

单击通知的超链接，转到 BackupJob 列表。 BackupJob 列表中包含给定作业的操作的所有详细信息。 可通过单击保管库仪表板中的“备份作业”磁贴转到 BackupJob，选择“Azure 虚拟机”以显示与保管库关联的作业。

“备份作业”边栏选项卡随即打开并显示作业列表。

![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

## <a name="use-templates-to-customize-a-restored-vm"></a>使用模板自定义还原 VM
[还原磁盘操作完成](#Track-the-restore-operation)后，使用在还原操作过程中生成的模板来创建配置与备份配置不同的新 VM。 还可使用该模板自定义从还原点创建新 VM 时创建的资源的名称。

![深入到还原作业](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

若要获取在执行还原磁盘操作过程中生成的模板，请执行以下操作：

1. 转到作业对应的“还原作业详细信息”。

2. 在“还原作业详细信息”屏幕上，选择“部署模板”启动模板部署。

3. 在自定义部署的“部署模板”边栏选项卡上，请在部署之前，使用模板部署来[编辑并部署模板](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，或者通过[创作模板](../azure-resource-manager/resource-group-authoring-templates.md)来追加其他自定义项。

  ![加载模板部署](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. 输入所需的值后，接受“条款和条件”，并选择“购买”。

  ![提交模板部署](./media/backup-azure-arm-restore-vms/submitting-template1.png)

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

1. 使用 PowerShell cmdlet 创建负载均衡器/多个 NIC/多个保留 IP 所需的 VM 配置。 使用该配置来创建采用所需配置的 VM：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 使用[内部负载均衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)在云服务中创建 VM。

   b. 创建 VM 以连接到[面向 Internet 的负载均衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/)。

   c. 创建具有[多个 NIC 的 VM](../virtual-machines/windows/multiple-nics.md)。

   d. 创建具有[多个保留 IP](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md) 的 VM。

## <a name="next-steps"></a>后续步骤
既然可以还原 VM，接下来请参阅故障排除文章中有关 VM 常见错误的信息。 另请参阅有关在 VM 中管理任务的文章。

* [排查错误](backup-azure-vms-troubleshoot.md#restore)
* [管理虚拟机](backup-azure-manage-vms.md)
