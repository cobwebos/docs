---
title: 监视 Azure 虚拟机的备份警报 | Microsoft Docs
description: 监视 Azure 虚拟机备份作业的事件和警报。 基于警报发送电子邮件。
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: ''
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: markgal;trinadhk;giridham;
ms.openlocfilehash: fbdce5c244d733a2978d473f01c8d875cbeaa65e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>监视 Azure 虚拟机备份的警报
警报是服务发出的响应，指出已达到或超过了某个事件阈值。 了解问题何时开始出现可能是控制业务成本的关键所在。 警报通常不会按计划发生，因此在警报发生后尽快知晓将很有用。 例如，当备份或还原作业失败时，在失败后的 5 分钟内发生警报。 在保管库仪表板中，“备份警报”磁贴显示了“关键”和“警告”级别的事件。 在“备份警报”设置中，可以查看所有事件。 但是，如果在处理某个单独的问题时发生警报，该怎么办呢？ 如果不知道警报何时发生，则无法确定相关的问题只是一个小小的麻烦，还是会导致数据透露。 为了确保适当的人员能够意识到发生了警报（何时发生警报），可以将服务配置为通过电子邮件发送警报通知。 有关设置电子邮件通知的详细信息，请参阅 [Configure notifications](backup-azure-monitor-vms.md#configure-notifications)（配置通知）。

## <a name="how-do-i-find-information-about-the-alerts"></a>如何查找有关警报的信息？
若要查看有关引发警报的事件的信息，必须打开“备份警报”部分。 有两种方法可以打开“备份警报”部分：通过保管库仪表板中的“备份警报”磁贴，或者通过“警报和事件”部分。

若要从“备份警报”磁贴打开“备份警报”边栏选项卡，请执行以下操作：

* 在保管库仪表板上的“**备份警报**”磁贴中，单击“**关键**”或“**警告**”查看该严重级别的操作事件。

    ![“备份警报”磁贴](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

要从“警报和事件”部分打开“备份警报”边栏选项卡，请执行以下操作：

1. 在保管库仪表板中，单击“**所有设置**”。 ![“所有设置”按钮](./media/backup-azure-monitor-vms/all-settings-button.png)
2. 在“**设置**”边栏选项卡中，单击“**警报和事件**”。 ![“警报和事件”按钮](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. 在“**警报和事件**”边栏选项卡中，单击“**备份警报**”。 ![“备份警报”按钮](./media/backup-azure-monitor-vms/backup-alerts.png)

    “备份警报”部分会打开并显示筛选的警报。

    ![“备份警报”磁贴](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. 要查看有关特定警报的详细信息，请在事件列表中单击该警报打开其“详细信息”部分。

    ![事件详细信息](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    若要自定义列表中显示的属性，请参阅 [View additional event attributes](backup-azure-monitor-vms.md#view-additional-event-attributes)（查看其他事件属性）

## <a name="configure-notifications"></a>配置通知
 可以将服务配置为针对过去一小时发生的警报发送电子邮件通知，或者在发生特定类型的事件时发送电子邮件通知。

针对警报设置电子邮件通知

1. 在“备份警报”菜单中，单击“**配置通知**”

    ![“备份警报”菜单](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    “配置通知”部分会打开。

    ![“配置通知”边栏选项卡](./media/backup-azure-monitor-vms/configure-notifications.png)
2. 在“配置通知”部分中，单击“电子邮件通知”旁边的“打开”。

    “收件人”和“严重性”对话框旁边出现了星号，因为这些信息是必填的。 至少提供一个电子邮件地址，并至少选择一个严重性。
3. 在“**收件人（电子邮件）**”对话框中，键入通知接收者的电子邮件地址。 使用以下格式：username@domainname.com。使用分号 (;) 分隔多个电子邮件地址。
4. 在“**通知**”区域中，选择“**按警报**”以便在发生指定的警报时发送通知，或者选择“**每小时摘要**”以便发送过去一小时的摘要。
5. 在“**严重性**”对话框中，选择要触发电子邮件通知的一个或多个严重级别。
6. 单击“ **保存**”。

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Azure IaaS VM 备份有哪些警报类型？
   | 警报级别 | 已发送的警报 |
   | --- | --- |
   | 严重 | 适用于备份失败、恢复失败 |
   | 警告 | 适用于备份作业已成功但出现警告（例如：创建快照时某些写入程序失败） |
   | 信息性 | 目前，信息性警报不可用于 Azure VM 备份 |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>是否存在即使配置了通知，也不发送电子邮件的情况？
有些情况下，即使通知已正确配置，也不发送警报。 在以下情况下，不会发送电子邮件通知以避免警报噪音：

* 如果已将通知配置为“每小时摘要”，并且在一小时内引发并解决了警报。
* 取消了作业。
* 某个备份作业被触发但随后失败，而另一个备份作业正在进行。
* 为启用 Resource Manager 的 VM 启动了计划的备份作业，但该 VM 不再存在。

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>使用活动日志获取成功备份的通知

如果想要在成功备份后收到通知，则可以使用基于保管库[活动日志](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)的警报。

### <a name="login-into-azure-portal"></a>登录到 Azure 门户
登录到 Azure 门户并转到相关的 Azure 恢复服务保管库，单击属性中的“活动日志”部分。

### <a name="identify-appropriate-log"></a>找出相应日志

应用下图所示的筛选器，验证是否接收到成功备份的活动日志。 相应地更改时间跨度以查看视图。

![活动日志](./media/backup-azure-monitor-vms/activity-logs-identify.png)

可以单击“JSON”段，获取更多详细信息，并将其复制粘贴到文本编辑器进行查看。 其中应该显示出保管库详细信息和触发活动日志的项（即备份项）。

然后单击“添加活动日志警报”，生成所有此类日志的警报。

### <a name="add-activity-log-alert"></a>添加活动日志警报

单击“添加活动日志警报”后会显示如下所示的屏幕

![活动日志警报](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png)
    
订阅和资源组用于存储警报。 条件将预先填充。 请确保所有的值都与要求相关。

若成功备份，“级别”会标记为“信息性”，而状态则标记为“成功”。

如果选择上述“资源”，在记录该资源或保管库的活动日志时，将生成警报。 如果想要规则适用于所有保管库，请将“资源”留空。

### <a name="define-action-on-alert-firing"></a>定义触发警报时的操作

使用“操作组”定义生成警报时的操作。 可以单击“操作类型”来了解有关可用操作（例如电子邮件/短信/与 ITSM 集成等）的详细信息。

![活动日志操作组](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)


单击“确定”后，将生成活动日志警报，并且用于记录成功备份的后续活动日志将触发操作组中定义的操作。

### <a name="limitations-on-alerts"></a>对警报的限制
基于事件的警报存在以下限制：

1. 警报在恢复服务保管库的所有虚拟机上触发。 无法针对恢复服务保管库中的一部分虚拟机自定义警报。
2. 从“alerts-noreply@mail.windowsazure.com”发送警报。 目前无法修改电子邮件发件人。

## <a name="next-steps"></a>后续步骤
有关如何从恢复点重新创建虚拟机的信息，请查看 [Restore Azure VMs](backup-azure-arm-restore-vms.md)（还原 Azure VM）。

如果需要有关如何保护虚拟机的信息，请参阅 [First look: Back up VMs to a Recovery Services vault](backup-azure-vms-first-look-arm.md)（初步了解：将 VM 备份到恢复服务保管库）。 

有关 VM 备份管理任务的详细信息，请参阅[管理 Azure 虚拟机备份](backup-azure-manage-vms.md)。
