---
title: 管理和使用 Azure 备份服务来监视 Azure VM 备份
description: 了解如何管理和使用 Azure 备份服务来监视 Azure VM 备份。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: aa953440f03137f3359276bc9e06cb0c73f0ab4a
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295770"
---
# <a name="manage-azure-vm-backups"></a>管理 Azure VM 备份

本文介绍如何管理 Azure 虚拟机 (Vm) 通过使用备份[Azure 备份服务](backup-overview.md)。 本文还概述了保管库仪表板上可以找到的备份信息。


在 Azure 门户中，恢复服务保管库仪表板提供访问保管库的信息，包括：

* 最新备份，也是最新还原点。
* 备份策略。
* 所有备份快照的总大小。
* 为备份启用的 Vm 数量。

通过使用仪表板和向下的单个 Vm 钻取，您可以管理备份。 若要开始机备份，请打开保管库仪表板上。

![使用滑块的完整仪表板视图](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>查看仪表板上的 Vm

若要查看保管库仪表板上的 Vm:

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在中心菜单上选择**浏览**。 在资源列表中，键入“恢复服务”。 键入时，根据输入筛选列表。 选择“恢复服务保管库”。

    ![创建恢复服务保管库](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. 为了方便使用，用鼠标右键单击该保管库，然后选择**固定到仪表板**。
4. 打开保管库仪表板。

    ![打开保管库仪表板和设置边栏选项卡](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. 上**备份项**磁贴中，选择**Azure 虚拟机**。

    ![打开备份项磁贴](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. 上 **备份项** 边栏选项卡中，您可以查看受保护的 Vm 的列表。 在此示例中，保管库保护一台虚拟机： demobackup。  

    ![查看备份项边栏选项卡](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. 从保管库项仪表板中，修改备份策略、 运行按需备份，停止或恢复虚拟机的保护，删除备份数据、 查看还原点和运行还原。

    ![备份项仪表板和设置边栏选项卡](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>管理 VM 的备份策略

若要管理备份策略：

1. 登录到 [Azure 门户](https://portal.azure.com/)。 打开保管库仪表板。
2. 上**备份项**磁贴中，选择**Azure 虚拟机**。

    ![打开备份项磁贴](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. 上 **备份项** 边栏选项卡中，您可以查看受保护的 Vm 和最新还原点时间与上次备份状态的列表。

    ![查看备份项边栏选项卡](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. 从保管库项仪表板中，可以选择备份策略。

   * 若要切换策略、 选择不同的策略，然后选择**保存**。 新策略将立即应用于保管库。

     ![选择备份策略](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>运行按需备份
设置其保护之后，可以运行按需备份虚拟机。 请记住这些详细信息：

- 如果初始备份已挂起，按需备份到恢复服务保管库中创建 VM 的完整的副本。
- 如果初始备份已完成，按需备份将从上一个快照仅发送更改，到恢复服务保管库。 也就是说，后续备份始终是递增的。
- 按需备份的保留期范围是指定何时触发备份的保持期值。

若要触发按需备份，请执行以下操作：

1. 上[保管库项仪表板](#view-vms-on-the-dashboard)下**受保护的项**，选择**备份项**。

    ![立即备份选项](./media/backup-azure-manage-vms/backup-now-button.png)

2. 从**备份管理类型**，选择**Azure 虚拟机**。 **备份项 （Azure 虚拟机）** 边栏选项卡中显示。
3. 选择 VM，然后选择**立即备份**创建按需备份。 **立即备份**边栏选项卡中显示。
4. 在中**保留备份截止**字段中，指定要保留的备份的日期。

    ![立即备份日历](./media/backup-azure-manage-vms/backup-now-check.png)

5. 选择**确定**运行备份作业。

若要跟踪作业的进度，请在保管库仪表板中，选择**备份作业**磁贴。

## <a name="stop-protecting-a-vm"></a>停止保护 VM

有两种方法来停止保护虚拟机：

- 停止所有将来的备份作业并删除所有恢复点。 在这种情况下，你将无法还原 VM。
- 停止所有将来的备份作业并保留恢复点。 尽管你将需要支付在保管库中保留恢复点，但可以根据需要还原 VM。 有关详细信息，请参阅[Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。

>[!NOTE]
>如果无需停止备份删除数据源，新备份将失败。 旧的恢复点将过期根据策略，但将始终保留一个最后一个恢复点，直到停止备份并删除数据。
>

若要停止对 VM 的保护：

1. 上[保管库项的仪表板](#view-vms-on-the-dashboard)，选择**停止备份**。
2. 选择是保留还是删除备份数据，并根据需要确认你的选择。 如果你想要，添加的注释。 如果你不确定的项的名称，悬停在感叹号上面即可查看的名称。

    ![停止保护](./media/backup-azure-manage-vms/retain-or-delete-option.png)

     通知告知你备份作业已停止。

## <a name="resume-protection-of-a-vm"></a>恢复对 VM 的保护

如果当你停止 VM 保留备份数据，可以稍后恢复保护。 如果删除备份数据，不能恢复保护。

若要恢复对 VM 的保护：

1. 上[保管库项的仪表板](#view-vms-on-the-dashboard)，选择**恢复备份**。

2. 按照中的步骤[管理备份策略](#manage-backup-policy-for-a-vm)若要将策略分配的 vm。 不需要选择 VM 的初始保护策略。
3. 备份策略应用到 VM 后，会看到以下消息：

    ![条消息，指示已成功受保护的 VM](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>删除备份数据

可以删除虚拟机的备份数据期间**停止备份**作业或备份作业完成后。 在删除备份数据之前，请记住这些详细信息：

- 它可能是一个好办法等待数天或星期的时间才能删除恢复点。
- 像过程用于还原的恢复点，在删除备份数据，不能选择要删除的特定恢复点。 如果删除备份数据，则将删除所有关联的恢复点。

停止或禁用 VM 的备份作业后，可以删除备份数据：


1. 上[保管库项仪表板](#view-vms-on-the-dashboard)，选择**删除备份数据**。

    ![选择删除备份](./media/backup-azure-manage-vms/delete-backup-buttom.png)

1. 键入备份项，以确认要删除的恢复点的名称。

    ![确认要删除的恢复点](./media/backup-azure-manage-vms/item-verification-box.png)

1. 若要删除的项的备份数据，请选择**删除**。 通知消息，告知你备份数据已被删除。

## <a name="next-steps"></a>后续步骤
- 了解如何[从 VM 的设置的 Azure Vm 备份](backup-azure-vms-first-look-arm.md)。
- 了解如何[还原 Vm](backup-azure-arm-restore-vms.md)。
- 了解如何[监视 Azure VM 备份](backup-azure-monitor-vms.md)。
