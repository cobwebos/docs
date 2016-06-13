
<properties
	pageTitle="使用 Azure 门户从备份中还原虚拟机 | Azure"
	description="使用 Azure 门户从恢复点还原 Azure 虚拟机"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="还原备份; 如何还原; 恢复点;"/>

<tags
	ms.service="backup"
	ms.date="05/10/2016"
	wacn.date=""/>


# 使用 Azure 门户还原虚拟机

> [AZURE.SELECTOR]
- [在经典门户中还原 VM](backup-azure-restore-vms.md)
- [在 Azure 门户中还原 VM](backup-azure-arm-restore-vms.md)


可以通过使用备份服务在定义的间隔创建数据快照来保护数据。这些快照称为恢复点，存储在恢复服务保管库中。当你需要修复或重建 VM 时，可以从保存的任何恢复点还原 VM。还原恢复点时，可将 VM 返回或恢复到创建恢复点时的状态。本文介绍如何还原 VM。

> [AZURE.NOTE] Azure 有两种用于创建和使用资源的部署模型：[Resource Manager 和经典部署模型](../resource-manager-deployment-model.md)。本文提供有关还原使用 Resource Manager 模型部署的 VM 的信息和过程。



## 还原恢复点

1. 登录到 [Azure 门户](http://ms.portal.azure.com/)

2. 在 Azure 菜单中，单击“浏览”，然后在服务列表中键入“恢复服务”。服务列表会根据键入的内容调整。出现“恢复服务保管库”时，请选择它。

    ![打开恢复服务保管库](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    随后将显示订阅中保管库的列表。

    ![恢复服务保管库列表](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

3. 从列表中选择与要还原的 VM 关联的保管库。当你单击该保管库时，其仪表板将会打开。

    ![恢复服务保管库列表](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)

4. 现在你已进入保管库仪表板。在“备份项”磁贴上，单击“Azure 虚拟机”以显示与保管库关联的 VM。

    ![保管库仪表板](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    “备份项”边栏选项卡随即打开并显示 Azure 虚拟机列表。

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)

5. 从列表中选择一个 VM 以打开仪表板。在 VM 仪表板中，默认会打开包含“还原点”磁贴的“监视”区域。

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/vm-blade.png)

6. 在 VM 仪表板菜单中，单击“还原”

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    此时将打开“还原”边栏选项卡。

    ![还原边栏选项卡](./media/backup-azure-arm-restore-vms/restore-blade.png)

7. 在“还原”边栏选项卡中，单击“还原点”打开“选择还原点”边栏选项卡。

    ![还原边栏选项卡](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    默认情况下，对话框会显示过去 30 天的所有还原点。使用边栏选项卡顶部的“筛选器”可更改显示的还原点的时间范围。默认情况下，将显示所有一致性还原点。修改“所有还原点”筛选器，以选择特定的还原点一致性。有关每种类型的还原点的详细信息，请参阅 [Data consistency（数据一致性）](./backup-azure-vms-introduction.md#data-consistency)中的说明。
    - 从“还原点一致性”列表中选择：
        - 崩溃一致还原点，
        - 应用程序一致还原点，
        - 文件系统一致还原点
        - 所有还原点。  

8. 选择一个还原点并单击“确定”。

    ![选择还原点](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    “还原”边栏选项卡将显示已设置还原点。

    ![已设置还原点](./media/backup-azure-arm-restore-vms/recovery-point-set.png)

9. 设置还原点后，“还原”边栏选项卡中会自动打开“还原配置”。

    ![已设置还原配置向导](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

## 选择 VM 还原配置

选择还原点点，请选择用于还原 VM 的配置。用于配置已还原 VM 的选项包括使用 Azure 门户或 PowerShell。

> [AZURE.NOTE] 门户针对已还原的 VM 提供“快速创建”选项。如果你想自定义要还原的 VM 的 VM 配置，请使用 PowerShell 来还原已备份的磁盘，然后将它们附加到所选的 VM 配置。请参阅[还原采用特殊网络配置的 VM](#restoring-vms-with-special-network-configurations)。

1. 如果尚未到达这一步，请转到“还原”边栏选项卡。确保已选择“还原点”，然后单击“还原配置”打开“恢复配置”边栏选项卡。

    ![已设置恢复配置向导](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

2. 在“还原配置”边栏选项卡中，输入或选择以下每个字段的值：
    - **虚拟机名称** - 提供 VM 的名称。此名称对于资源组（适用于 ARM VM）或云服务（适用于经典 VM）必须是唯一的。当虚拟机在订阅中时，不支持替换虚拟机。
    - **资源组** - 使用现有资源组，或创建新组。如果要还原经典 VM，请使用此字段来指定新云服务的名称。创建新资源组/云服务时，该名称必须全局唯一。通常，云服务名称与面向公众的 URL 相关联 - 例如：[cloudservice].cloudapp.net。如果你尝试使用已用的云资源组/云服务名称，Azure 将为资源组/云服务分配与 VM 相同的名称。Azure 将显示不与任何地缘组关联的资源组/云服务和 VM。有关详细信息，请参阅 [How to migrate from Affinity Groups to a Regional Virtual Network (VNet)（如何从地缘组迁移到区域虚拟网络 (VNet)）](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。
    - **虚拟网络** - 创建 VM 时，请选择虚拟网络 (VNET)。该字段提供与订阅关联的所有 VNET。VM 的资源组显示在括号中。 
    - **子网** - 如果 VNET 包含子网，则默认情况下会选择第一个子网。如果有其他子网，请选择所需的子网。
    - **存储帐户** - 打开与恢复服务保管库位于相同位置的存储帐户的列表。选择存储帐户时，必须选择与恢复服务保管库共享相同位置的帐户。不支持区域冗余的存储帐户。如果没有与恢复服务保管库位于同一位置的存储帐户，必须在开始还原操作之前创建一个存储帐户。存储帐户的复制类型在括号中注明。 
    
    > [AZURE.NOTE] 还原基于 ARM 的 VM 时，必须选择 VNET。对于 VM，VNET 是可选的。

3. 在“还原配置”边栏选项卡中，单击“确定”完成还原配置。

4. 在“还原”边栏选项卡中，单击“还原”以触发还原操作。

    ![已完成恢复配置](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## 跟踪还原操作

触发还原操作后，备份服务将创建一个作业用于跟踪还原操作。备份服务还会创建并在门户的“通知”区域中临时显示通知。如果你看不到通知，随时可以单击“通知”图标来查看通知。

![已触发还原](./media/backup-azure-arm-restore-vms/restore-notification.png)

若要查看正在进行的操作或查看已完成的操作，请打开“备份作业”列表。

1. 在 Azure 菜单中，单击“浏览”，然后在服务列表中键入“恢复服务”。服务列表会根据键入的内容调整。出现“恢复服务保管库”时，请选择它。

    ![打开恢复服务保管库](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    随后将显示订阅中保管库的列表。

    ![恢复服务保管库列表](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

2. 从列表中选择与已还原的 VM 关联的保管库。当你单击该保管库时，其仪表板将会打开。

3. 在保管库仪表板中的“备份作业”磁贴上，单击“Azure 虚拟机”以显示与保管库关联的作业。

    ![保管库仪表板](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    “备份作业”边栏选项卡将打开并显示作业列表。

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)

## 还原采用特殊网络配置的 VM
可以备份和还原采用以下特殊网络配置的 VM。但是，在执行还原过程时，这些配置需要经过一些特殊的考虑。

- 采用负载平衡器的 VM（内部和外部）
- 具有多个保留 IP 的 VM
- 具有多个 NIC 的 VM

>[AZURE.IMPORTANT] 创建 VM 的特殊网络配置时，必须使用 PowerShell 从还原的磁盘创建 VM。

若要在还原到磁盘之后完全重新创建虚拟机，请执行以下步骤：

1. 使用 [PowerShell](backup-azure-vms-automation.md/#restore-an-azure-vm) 从恢复服务保管库还原磁盘

2. 使用 PowerShell cmdlet 创建负载平衡器/多个 NIC/多个保留 IP 所需的 VM 配置，并使用该配置创建具有所需配置的 VM。
	- 使用[内部负载平衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)在云服务中创建 VM
	- 创建 VM 以连接到[面向 Internet 的负载平衡器](https://azure.microsoft.com/zh-cn/documentation/articles/load-balancer-internet-getstarted/)
	- 创建具有[多个 NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/) 的 VM
	- 具有[多个保留 IP](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/) 的 VM

## 后续步骤
既然你可以还原 VM，接下来请参阅故障排除文章中有关 VM 常见错误的信息。另请参阅有关在 VM 中管理任务的文章。

- [排查错误](backup-azure-vms-troubleshoot.md#restore)
- [管理虚拟机](backup-azure-manage-vms.md)

<!---HONumber=Mooncake_0606_2016-->