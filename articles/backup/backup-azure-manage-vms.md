---
title: 使用 Azure 备份服务管理和监视 Azure VM 备份
description: 了解如何使用 Azure 备份服务管理和监视 Azure VM 备份。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: f5c0373e2ef094a7fc5be64f4aeb8c0bb132e683
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430057"
---
# <a name="manage-azure-vm-backups"></a>管理 Azure VM 备份

本文介绍了如何管理使用 [Azure 备份服务](backup-overview.md)备份的 Azure VM，并总结了门户仪表板中可用的备份警报信息。


在 Azure 门户中，恢复服务保管库仪表板可用于访问有关保管库的信息，包括：

* 最新备份，也是最新还原点。
* 备份策略
* 所有备份快照的总大小
* 启用了备份的 VM 数

可以使用仪表板以及通过向下钻取到各个 VM 来管理备份。 对于虚拟机备份，许多管理任务都是从在仪表板中打开保管库开始。 

![带滑块的完整视图](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-in-the-dashboard"></a>在仪表板中查看 VM

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“中心”菜单中，单击“**浏览**”，并在资源列表中，键入“**恢复服务**”。 开始键入时，会根据输入筛选该列表。 单击“**恢复服务保管库**”。 
    ![创建恢复服务保管库步骤 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. 为了便于使用，请在保管库列表中右键单击保管库，然后单击“固定到仪表板”。
3. 打开保管库仪表板。 
    ![打开保管库仪表板和“设置”边栏选项卡](./media/backup-azure-manage-vms/full-view-rs-vault.png)

4. 在“备份项”磁贴中，单击“Azure 虚拟机”。

    ![打开备份项磁贴](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. 在“备份项”边栏选项卡中，可以看到每个项的最后一个备份作业。 在本示例中，有一个受此保管库保护的虚拟机：demovm-markgal。  

    ![备份项磁贴](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

 
6. 从保管库项仪表板中，可以创建或修改备份策略，查看还原点，运行按需备份，停止和恢复 VM 保护，删除恢复点，以及运行还原。

    ![带“设置”边栏选项卡的备份项仪表板](./media/backup-azure-manage-vms/item-dashboard-settings.png)



## <a name="manage-backup-policies"></a>管理备份策略
1. 在[保管库项仪表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)上，单击“所有设置”。

    ![备份策略边栏选项卡](./media/backup-azure-manage-vms/all-settings-button.png)
2. 在“设置”中，单击“备份策略”。
3. 在“**选择备份策略**”菜单中执行以下操作：

   * 要更改策略，请选择其他策略，并单击“**保存**”。 新策略将立即应用于保管库。
   * 若要创建策略，请选择“**新建**”。 [了解详细信息](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![虚拟机备份](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>运行按需备份
为 VM 配置保护后，可以对它执行按需备份。
- 如果初始备份已挂起，则按需备份会在恢复服务保管库中创建虚拟机的完整副本。
- 如果初始备份已完成，按需备份仅以前快照的更改发送到恢复服务保管库。 也就是说，后续备份始终是增量备份。
- 按需备份的保留期范围是在策略中针对“每日”备份点指定的保留期值。 如果没有选择“每日”备份点，则使用“每周”备份点。


若要触发按需备份，请执行以下操作：

1. 在“[保管库项仪表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)”中，单击“**立即备份**”。

    ![“立即备份”按钮](./media/backup-azure-manage-vms/backup-now-button.png)

 2. 单击“**是**”启动备份作业。

    ![“立即备份”按钮](./media/backup-azure-manage-vms/backup-now-check.png)

 
 备份作业将创建恢复点。 恢复点保留期范围与在虚拟机的关联策略中指定的保留期范围相同。 若要跟踪作业进度，请在保管库仪表板中，单击“**备份作业**”磁贴。  

## <a name="stop-protecting-a-vm"></a>停止保护 VM

可通过两种方法来停止保护 VM：

- 停止所有将来的备份作业并删除所有恢复点。 在这种情况下无法还原 VM。
- 停止所有将来的备份作业，但保留恢复点。 将恢复点保留在存储空间中需支付相关费用。 不过，保留恢复点的好处是可以在需要时还原 VM。 [详细](https://azure.microsoft.com/pricing/details/backup/)了解定价详细信息。

若要停止保护虚拟机，请执行以下操作：

1. 在“[保管库项仪表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)”中，单击“**停止备份**”。

    ![停止备份按钮](./media/backup-azure-manage-vms/stop-backup-button.png
2. 选择是保留还是删除备份数据，并根据需要进行确认。 按要求进行确认，并且可以提供注释。 如果不能确定项名称，将鼠标悬停在感叹号上面即可查看该名称。

    ![停止保护](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 可以通过通知消息了解到备份作业已停止。


## <a name="resume-protection-of-a-vm"></a>恢复对 VM 的保护

如果在停止 VM 时保留了备份数据，则可以恢复保护。 如果删除了备份数据，则无法恢复。

Te

1. 在“[保管库项仪表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)”中，单击“**恢复备份**”。

2. 按[管理备份策略](backup-azure-manage-vms.md#manage-backup-policies)中的步骤为虚拟机分配策略。 可以选择一个不同的策略，而不是最初用于保护虚拟机的策略。
3. 对虚拟机应用备份策略以后，会看到以下消息。

    ![成功进行保护的 VM](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>删除备份数据

可以在**停止备份**作业过程中删除与 VM 关联的备份数据，也可以在备份作业完成后随时删除。

- 甚至还可以等待数天或数周再删除恢复点。
- 与还原恢复点不同，在删除备份数据时，不能选择要删除的特定恢复点。 如果选择删除备份数据，则会删除与该项关联的所有恢复点。

此过程假设 VM 的备份作业已停止或禁用。


1. 在[保管库项仪表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)中，单击“**删除备份**”。

    ![VM 类型](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. 键入项的名称，以便确认需要删除恢复点。

    ![停止验证](./media/backup-azure-manage-vms/item-verification-box.png)

4. 要删除当前项的备份数据，请单击![“停止备份”按钮](./media/backup-azure-manage-vms/delete-button.png)

    可以通过通知消息了解到备份数据已删除。

## <a name="next-steps"></a>后续步骤
- [了解](backup-azure-vms-first-look-arm.md)如何从 VM 设置备份 Azure VM。
- [了解](backup-azure-arm-restore-vms.md)如何还原 VM。 
- [了解](backup-azure-monitor-vms.md)如何监视 Azure VM 备份。
 
