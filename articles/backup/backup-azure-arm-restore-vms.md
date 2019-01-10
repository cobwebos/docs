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
ms.openlocfilehash: ac0c727e41ad9b361ea9f558a97f16446c12ef0e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793370"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>使用 Azure 门户还原虚拟机
可以通过按定义的间隔创建数据快照来保护数据。 这些快照称为恢复点，存储在恢复服务保管库中。 当需要修复或重新生成虚拟机 (VM) 时，可以从保存的任何恢复点还原 VM。 还原恢复点时，可以：

* 创建新 VM：从备份 VM 的时点恢复点开始。
* 还原磁盘：使用进程随附的模板自定义已还原的 VM，或恢复单独的文件。

本文介绍如何将 VM 还原到新 VM，或者还原所有已备份的磁盘。 有关单独的文件恢复，请参阅[从 Azure VM 备份恢复文件](backup-azure-restore-files-from-vm.md)。

![从 VM 备份还原的三种方式](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)


从 VM 备份还原 VM 或所有磁盘包括两个步骤：

* 选择用于还原的还原点。
* 选择还原类型
    - 选项 1：创建新 VM
    - 选项 2：还原磁盘。

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

    - 要使用过去 30 天内的还原点进行还原，请右键单击 VM >“还原 VM”。
    - 要使用超过 30 天的还原点进行还原，请单击“还原点”下的链接。
    - 要通过自定义日期列出和筛选 VM，请单击菜单中的“还原 VM”。 使用筛选器可更改显示的还原点的时间范围。 还可筛选不同类型的数据一致性。
8. 查看还原点设置：
    - 数据一致性显示恢复点中的[一致性类型](backup-azure-vms-introduction.md#consistency-types)。
    - “恢复类型”显示点的存储位置（存储帐户、保管库或两者中）。 [详细了解](https://azure.microsoft.com/blog/large-disk-support/)即时恢复点。

  ![还原点](./media/backup-azure-arm-restore-vms/vm-blade1.png)
9. 选择还原点。

10. 选择“还原配置”。 “还原配置”边栏选项卡随即打开。

## <a name="choose-a-vm-restore-configuration"></a>选择 VM 还原配置
选择还原点后，请选择 VM 还原配置。 若要配置已还原 VM，可使用 Azure 门户或 PowerShell。

### <a name="restore-options"></a>还原选项

**选项** | **详细信息**
--- | ---
**新建 - 创建 VM** | 相当于快速创建 VM。 启动基本 VM 并在还原点运行。<br/><br/> VM 设置可以作为还原过程的一部分进行修改。
**新建 - 还原磁盘** | 创建一个可自定义为还原过程一部分的 VM。<br/><br/> 此选项将 VHD 复制到所指定的存储帐户。<br/><br/> - 存储帐户应与保管库位于同一位置。<br/><br/> 若没有合适的存储帐户，则需要创建一个帐户。<br/><br/> 存储帐户复制类型显示在括号中。 不支持区域冗余存储 (ZRS)。<br/><br/> 在存储帐户中，可将还原的磁盘附加到现有 VM，或使用 PowerShell 从还原的磁盘创建新 VM。
**替换现有** | 无需创建 VM 即可使用此选项。<br/><br/> 必须存在当前 VM。 若已删除 VM，则无法使用该选项。<br/><br/> Azure 备份会获取现有 VM 的快照，并将其存储在指定的暂存位置。 然后，使用选定的还原点替换连接到 VM 的现有磁盘。 先前创建的快照将复制到保管库中，并根据备份保留策略存储为恢复点。<br/><br/> 未加密的托管 VM 支持替换现有。 非托管磁盘、[通用 VM](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) 或[使用自定义映像创建的 VM](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) 不支持此选项。<br/><br/> 如果还原点的磁盘数多于或少于当前 VM，则还原点中的磁盘数将仅反映在 VM 中。

> [!NOTE]
> 如果要还原具有高级网络设置的 VM，例如，如果它们由内部或外部负载均衡器托管，或者具有多个 NIC 或多个保留的 IP 地址，请使用 PowerShell 进行还原。 [了解详细信息](#restore-vms-with-special-network-configurations)。
> 如果 Windows VM 使用 [HUB 许可](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，请使用“新建 - 还原磁盘”选项，然后使用 PowerShell 或还原模板创建“许可类型”为“Windows_Server”的 VM。 创建 VM 后也可以应用此设置。


指定还原设置，如下所示：

1. 在“还原”中，选择“[还原点](#select-a-restore-point-for-restore)” > “还原配置”。
2. 在“还原配置”中，根据上表中汇总的设置选择要还原 VM 的方式。

    ![还原配置向导](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)


## <a name="create-new-create-a-vm"></a>新建 - 创建 VM

1. 在“还原配置” > “新建 > 还原类型”中，选择“创建虚拟机”。
2. 在“虚拟机名称”中，指定订阅中不存在的 VM。
3. 在“资源组”中，为新 VM 选择现有资源组，或创建具有全局唯一名称的新资源组。 如果指定已存在的名称，则 Azure 将为该组分配与 VM 相同的名称。
4. 在“虚拟网络”中，选择将放置 VM 的 VNet。 将显示与订阅关联的所有 VNet。 选择子网。 默认情况下选择第一个子网。
5. 在“存储位置”中，指定用于 VM 的存储类型。

    ![还原配置向导](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. 在“还原配置”中，选择“确定”。 在“还原”中，单击“还原”以触发还原操作。



## <a name="create-new-restore-disks"></a>新建 - 还原磁盘

1. 在“还原配置” > “新建 > 还原类型”中，选择“还原磁盘”。
2. 在“资源组”中，为还原的磁盘选择现有资源组，或创建具有全局唯一名称的新资源组。
3. 在“存储帐户”中，指定要将 VHD 复制到的帐户。 确保帐户与保管库位于同一区域。

    ![已完成恢复配置](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. 在“还原配置”中，选择“确定”。 在“还原”中，单击“还原”以触发还原操作。
5. 还原磁盘后，可执行以下任一操作完成 VM 还原操作。

    - 使用作为还原操作一部分生成的模板来自定义设置，并触发 VM 部署。 可编辑默认模板设置，并提交 VM 部署模板。
    - 可[将已还原的磁盘附加](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)到现有 VM。
    - 可使用 PowerShell 从已还原的磁盘[创建新 VM](backup-azure-vms-automation.md#restore-an-azure-vm)。


## <a name="replace-existing-disks"></a>替换现有磁盘

使用此选项可通过选定的还原点替换当前 VM 中的现有磁盘。

1. 在“还原配置”中，单击“替换现有”。
2. 在“还原类型”中，选择“替换磁盘”（将替换现有 VM 磁盘的还原点）。
3. 在“暂存位置”中，指定应保存当前托管磁盘的快照的位置。

   ![还原配置向导“替换现有”](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="track-the-restore-operation"></a>跟踪还原操作
触发还原操作后，备份服务会创建一个作业用于跟踪还原操作。 备份服务还会创建并在门户的“通知”区域中临时显示通知。 如果未显示通知，请选择“通知”符号查看通知。

![已触发还原](./media/backup-azure-arm-restore-vms/restore-notification1.png)

单击通知的超链接，转到 BackupJob 列表。 BackupJob 列表中包含给定作业的操作的所有详细信息。 可通过单击保管库仪表板中的“备份作业”磁贴转到 BackupJob，选择“Azure 虚拟机”以显示与保管库关联的作业。

“备份作业”边栏选项卡随即打开并显示作业列表。

![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

现在，“还原详细信息”边栏选项卡中提供了“进度栏”。 通过单击状态为“正在进行”的任意还原作业，可以打开“还原详细信息”边栏选项卡。 “进度栏”适用于所有还原变体，例如“新建”、“还原磁盘”和“替换现有”。 “还原进度栏”执行的详细信息是“估计还原时间”、“还原百分比”和“传输的字节数”。

还原进度栏详细信息如下：

- “估计还原时间”最初提供完成还原操作所需的时间。 随着操作的进行，恢复操作完成后，所花费的时间将减少并达到 0。
- “还原百分比”提供完成还原操作的数据百分比。
- “传输的字节数”在通过“创建新 VM”进行还原时可用于子任务中。 这提供了有关要传输的总字节数中传输了多少字节数的详细信息。


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
