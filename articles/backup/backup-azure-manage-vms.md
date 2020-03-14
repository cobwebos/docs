---
title: 管理和监视 Azure VM 备份
description: 了解如何使用 Azure 备份服务管理和监视 Azure VM 备份。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: dd4e9dc199048b3faf3da0cadfdf60bdcb26c5bc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248146"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>通过 Azure 备份服务管理 Azure VM 备份

本文介绍如何管理使用[Azure 备份服务](backup-overview.md)备份的 azure 虚拟机（vm）。 本文还概述了可以在保管库仪表板上找到的备份信息。

在 Azure 门户中，可以使用恢复服务保管库仪表板访问保管库信息，包括：

* 最新备份，也是最新还原点。
* 备份策略。
* 所有备份快照的总大小。
* 启用了备份的 VM 数。

可以使用仪表板以及通过向下钻取到各个 VM 来管理备份。 若要开始计算机备份，请在仪表板上打开保管库。

![具有滑块的完整仪表板视图](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>在仪表板中查看 VM

若要在仪表板中查看 VM，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在中心菜单中，选择 "**浏览**"。 在资源列表中，键入“恢复服务”。 在键入时，列表会根据你的输入进行筛选。 选择“恢复服务保管库”。

    ![创建恢复服务保管库](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. 为了便于使用，右键单击保管库，然后选择 "**固定到仪表板**"。
4. 打开保管库仪表板。

    ![打开保管库仪表板和 "设置" 窗格](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. 在 "**备份项**" 磁贴中，选择 " **Azure 虚拟机**"。

    ![打开“备份项”磁贴](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. 在 "**备份项**" 窗格中，可以查看受保护的 vm 列表。 在此示例中，保管库保护着一台虚拟机：demobackup。  

    ![查看 "备份项" 窗格](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. 从保管库项的仪表板中，修改备份策略，运行按需备份，停止或恢复 VM 保护，删除备份数据，查看还原点，以及运行还原。

    !["备份项" 仪表板和 "设置" 窗格](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>管理 VM 的备份策略

若要管理备份策略，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。 打开保管库仪表板。
2. 在 "**备份项**" 磁贴中，选择 " **Azure 虚拟机**"。

    ![打开“备份项”磁贴](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. 在 "**备份项**" 窗格中，可以查看受保护的 vm 的列表，以及最近的还原点时间的备份状态。

    ![查看 "备份项" 窗格](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. 从保管库项的仪表板中，你可以选择备份策略。

   * 若要切换策略，请选择其他策略，然后选择 "**保存**"。 新策略将立即应用于保管库。

     ![选择备份策略](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>运行按需备份

为 VM 设置保护后，可以为 VM 运行按需备份。 请记住以下详细信息：

* 如果初始备份已挂起，则按需备份会在恢复服务保管库中创建 VM 的完整副本。
* 如果初始备份已完成，按需备份仅将以前快照的更改发送到恢复服务保管库。 也就是说，后续备份始终是增量备份。
* 按需备份的保留期范围是你在触发备份时指定的保留期值。

若要触发按需备份，请执行以下操作：

1. 在[保管库项仪表板](#view-vms-on-the-dashboard)上的 "**受保护项**" 下，选择 "**备份项**"。

    ![“立即备份”选项](./media/backup-azure-manage-vms/backup-now-button.png)

2. 从 "**备份管理类型**" 中选择 " **Azure 虚拟机**"。 此时将显示 "**备份项（Azure 虚拟机）** " 窗格。
3. 选择 VM，然后选择 "**立即备份**" 以创建按需备份。 此时将显示 "**立即备份**" 窗格。
4. 在 "**保留备份截止**日期" 字段中，指定要保留备份的日期。

    ![“立即备份”日历](./media/backup-azure-manage-vms/backup-now-check.png)

5. 选择 **"确定"** 运行备份作业。

若要跟踪作业的进度，请在保管库仪表板上选择 "**备份作业**" 磁贴。

## <a name="stop-protecting-a-vm"></a>停止保护 VM

可通过两种方法来停止保护 VM：

* **停止保护并保留备份数据**。 此选项将使所有将来的备份作业停止保护你的 VM；但是，Azure 备份服务将保留已备份的恢复点。  需要支付保管库中的恢复点的费用（有关详细信息，请参阅[Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)）。 如果需要，你将能够还原 VM。 如果决定恢复 VM 保护，则可以使用 "*恢复备份*" 选项。
* **停止保护并删除备份数据**。 此选项将使所有将来的备份作业停止保护你的 VM 并删除所有恢复点。 你将无法还原 VM，也无法使用*恢复备份*选项。

>[!NOTE]
>如果在不停止备份的情况下删除数据源，则新备份会失败。 旧恢复点将根据策略过期，但始终会保留一个最后的恢复点，直至你显式停止备份并删除数据。
>

### <a name="stop-protection-and-retain-backup-data"></a>停止保护并保留备份数据

若要停止保护并保留 VM 的数据，请执行以下操作：

1. 在[保管库项目的仪表板](#view-vms-on-the-dashboard)上，选择 "**停止备份**"。
2. 选择 "**保留备份数据**"，并根据需要确认选择。 如果需要，添加注释。 如果不确定项名称，将鼠标悬停在感叹号上面即可查看该名称。

    ![保留备份数据](./media/backup-azure-manage-vms/retain-backup-data.png)

一条通知将让你获悉备份作业已停止。

### <a name="stop-protection-and-delete-backup-data"></a>停止保护并删除备份数据

若要停止保护并删除 VM 的数据，请执行以下操作：

1. 在[保管库项目的仪表板](#view-vms-on-the-dashboard)上，选择 "**停止备份**"。
2. 选择 "**删除备份数据**"，并根据需要确认选择。 输入备份项的名称，并根据需要添加注释。

    ![删除备份数据](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>恢复对 VM 的保护

如果在停止 VM 保护期间选择了 "[停止保护并保留备份数据](#stop-protection-and-retain-backup-data)" 选项，则可以使用 "**恢复备份**"。 如果选择 "[停止保护并删除备份数据](#stop-protection-and-delete-backup-data)" 选项或[删除备份数据](#delete-backup-data)，则此选项不可用。

若要恢复 VM 保护，请执行以下操作：

1. 在[保管库项目的仪表板](#view-vms-on-the-dashboard)上，选择 "**恢复备份**"。

2. 按照[管理备份策略](#manage-backup-policy-for-a-vm)中的步骤为虚拟机分配策略。 不需要选择 VM 的初始保护策略。
3. 将备份策略应用于 VM 后，你会看到以下消息：

    ![指明已成功保护 VM 的消息](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>删除备份数据

有两种方法可以删除 VM 的备份数据：

* 在保管库项仪表板中，选择 "停止备份"，然后按照 "[停止保护和删除备份数据](#stop-protection-and-delete-backup-data)" 选项的说明进行操作。

  ![选择“停止备份”](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* 在保管库项仪表板中，选择“删除备份数据”。 如果已选择 "停止 VM 保护期间[停止保护并保留备份数据](#stop-protection-and-retain-backup-data)" 选项，则会启用此选项

  ![选择“删除备份”](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * 在[保管库项仪表板](#view-vms-on-the-dashboard)中，选择 "**删除备份数据**"。
  * 键入备份项的名称以确认你要删除恢复点。

    ![删除备份数据](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * 若要删除项的备份数据，请选择 "**删除**"。 一条通知消息将让你获悉备份数据已删除。

为了保护数据，Azure 备份包含软删除功能。 使用软删除，即使在删除 VM 的备份（所有恢复点）后，还会将备份数据保留14天。 有关详细信息，请参阅[软删除文档](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud)。

  > [!NOTE]
  > 删除备份数据时，将删除所有关联的恢复点。 无法选择要删除的特定恢复点。

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>主数据源不再存在的备份项

* 如果在不停止保护的情况下删除或移动了为 Azure 备份配置的 Azure Vm，则计划的备份作业和按需（即席）备份作业都将失败，并出现错误 UserErrorVmNotFoundV2。 对于失败的按需备份作业（未显示 "失败的计划作业"），备份预检查将显示为 "严重"。
* 这些备份项目在系统中保持活动状态，这与用户设置的备份和保留策略有关。 这些 Azure Vm 的备份数据将根据保留策略进行保留。 过期的恢复点（最后一个恢复点除外）根据备份策略中设置的保持期进行清理。
* 建议用户删除主数据源不再存在的备份项，以避免任何额外的成本，如果不再需要删除资源的备份项/数据，因为最后一个恢复点将被永久保留，并且根据适用的备份定价向用户收费。

## <a name="next-steps"></a>后续步骤

* 了解如何[从 VM 的设置备份 Azure vm](backup-azure-vms-first-look-arm.md)。
* 了解如何[还原 vm](backup-azure-arm-restore-vms.md)。
* 了解如何[监视 AZURE VM 备份](backup-azure-monitor-vms.md)。
