---
title: "监视 Resource Manager 部署型虚拟机备份 | Microsoft Docs"
description: "监视 Resource Manager 部署型虚拟机备份生成的事件和警报。 基于警报发送电子邮件。"
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: markgal;trinadhk;giridham;
ms.openlocfilehash: 1e9f6d44965e8a6cd9529ef860f0fb57fd8e572d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>监视 Azure 虚拟机备份的警报
警报是服务发出的响应，指出已达到或超过了某个事件阈值。 了解问题何时开始出现可能是控制业务成本的关键所在。 警报通常不会按计划发生，因此在警报发生后尽快知晓将很有用。 例如，当备份或还原作业失败时，在失败后的 5 分钟内发生警报。 在保管库仪表板中，“备份警报”磁贴显示了“关键”和“警告”级别的事件。 在“备份警报”设置中，可以查看所有事件。 但是，如果在处理某个单独的问题时发生警报，该怎么办呢？ 如果不知道警报何时发生，则无法确定相关的问题只是一个小小的麻烦，还是会导致数据透露。 为了确保适当的人员能够意识到发生了警报（何时发生警报），可以将服务配置为通过电子邮件发送警报通知。 有关设置电子邮件通知的详细信息，请参阅 [Configure notifications](backup-azure-monitor-vms.md#configure-notifications)（配置通知）。

## <a name="how-do-i-find-information-about-the-alerts"></a>如何查找有关警报的信息？
若要查看有关引发警报的事件的信息，必须打开“备份警报”边栏选项卡。 有两种方法可以打开“备份警报”边栏选项卡：通过保管库仪表板中的“备份警报”磁贴，或者通过“警报和事件”边栏选项卡。

若要从“备份警报”磁贴打开“备份警报”边栏选项卡，请执行以下操作：

* 在保管库仪表板上的“**备份警报**”磁贴中，单击“**关键**”或“**警告**”查看该严重级别的操作事件。

    ![“备份警报”磁贴](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

若要从“警报和事件”边栏选项卡打开“备份警报”边栏选项卡，请执行以下操作：

1. 在保管库仪表板中，单击“**所有设置**”。 ![“所有设置”按钮](./media/backup-azure-monitor-vms/all-settings-button.png)
2. 在“**设置**”边栏选项卡中，单击“**警报和事件**”。 ![“警报和事件”按钮](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. 在“**警报和事件**”边栏选项卡中，单击“**备份警报**”。 ![“备份警报”按钮](./media/backup-azure-monitor-vms/backup-alerts.png)

    “**备份警报**”边栏选项卡会打开并显示筛选的警报。

    ![“备份警报”磁贴](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. 若要查看有关特定警报的详细信息，请在事件列表中单击该警报打开其“**详细信息**”边栏选项卡。

    ![事件详细信息](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    若要自定义列表中显示的属性，请参阅 [View additional event attributes](backup-azure-monitor-vms.md#view-additional-event-attributes)（查看其他事件属性）

## <a name="configure-notifications"></a>配置通知
 可以将服务配置为针对过去一小时发生的警报发送电子邮件通知，或者在发生特定类型的事件时发送电子邮件通知。

针对警报设置电子邮件通知

1. 在“备份警报”菜单中，单击“**配置通知**”

    ![“备份警报”菜单](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    “配置通知”边栏选项卡会打开。

    ![“配置通知”边栏选项卡](./media/backup-azure-monitor-vms/configure-notifications.png)
2. 在“配置通知”边栏选项卡中，单击“电子邮件通知”旁边的“**打开**”。

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

## <a name="customize-your-view-of-events"></a>自定义事件视图
“**审核日志**”设置附带了一组预定义的筛选器和列，其中显示操作事件信息。 可以自定义视图，以便在“**事件**”边栏选项卡打开时显示你所需的信息。

1. 在[保管库仪表板](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard)中，浏览到“**审核日志**”并单击该选项，打开“**事件**”边栏选项卡。

    ![审核日志](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    “**事件**”边栏选项卡打开到仅针对当前保管库筛选的操作事件。

    ![审核日志筛选器](./media/backup-azure-monitor-vms/audit-logs-filter.png)

    该边栏选项卡显示在过去一周发生的“严重”事件、“错误”事件、“警告”事件和“信息”事件的列表。 时间跨度是在“**筛选器**”中设置的默认值。 “**事件**”边栏选项卡还显示一个跟踪事件发生时间的条形图。 如果不希望看到条形图，可在“**事件**”菜单中，单击“**隐藏图表**”将图表切换到关闭状态。 “事件”的默认视图显示“操作”、“级别”、“状态”、“资源”和“时间”信息。 有关公开其他事件属性的信息，请参阅[扩展事件信息](backup-azure-monitor-vms.md#view-additional-event-attributes)部分。
2. 有关操作事件的更多信息，请在“**操作**”列中，单击某个操作事件打开其边栏选项卡。 该边栏选项卡包含有关事件的详细信息。 事件已按其关联 ID 分组，并且会列出时间跨度内发生的事件。

    ![操作详细信息](./media/backup-azure-monitor-vms/audit-logs-details-window.png)
3. 若要查看有关特定事件的详细信息，请在事件列表中单击该事件打开其“**详细信息**”边栏选项卡。

    ![事件详细信息](./media/backup-azure-monitor-vms/audit-logs-details-window-deep.png)

    事件级信息将尽可能详细。 如果想要查看有关每个事件的这种信息量，并想要将这种详细程度添加到“**事件**”边栏选项卡，请参阅[扩展事件信息](backup-azure-monitor-vms.md#view-additional-event-attributes)部分。

## <a name="customize-the-event-filter"></a>自定义事件筛选器
使用“**筛选器**”可以调整或选择特定边栏选项卡中显示的信息。 若要筛选事件信息，请执行以下操作：

1. 在[保管库仪表板](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard)中，浏览到“**审核日志**”并单击该选项，打开“**事件**”边栏选项卡。

    ![审核日志](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    “**事件**”边栏选项卡打开到仅针对当前保管库筛选的操作事件。

    ![审核日志筛选器](./media/backup-azure-monitor-vms/audit-logs-filter.png)
2. 在“**事件**”菜单中，单击“**筛选器**”打开该边栏选项卡。

    ![打开筛选器边栏选项卡](./media/backup-azure-monitor-vms/audit-logs-filter-button.png)
3. 在“**筛选器**”边栏选项卡中，调整“**级别**”、“**时间跨度**”和“**调用方**”筛选器。 其他筛选器将不可用，因为已将它们设置为提供恢复服务保管库的当前信息。

    ![审核日志 - 查询详细信息](./media/backup-azure-monitor-vms/filter-blade.png)

    可以指定事件的“**级别**”：“关键”、“错误”、“警告”或“信息”。 可以选择任意组合的事件级别，但必须选择至少一个级别。 通过切换打开或关闭级别。 可以通过“**时间跨度**”筛选器指定捕获事件的时间长度。 如果使用自定义时间跨度，则可设置开始时间和结束时间。
4. 做好使用筛选器查询操作日志的准备以后，即可单击“**更新**”。 结果显示在“**事件**”边栏选项卡中。

    ![操作详细信息](./media/backup-azure-monitor-vms/edited-list-of-events.png)

### <a name="view-additional-event-attributes"></a>查看其他事件属性
使用“**列**”按钮可使其他事件属性显示在“**事件**”边栏选项卡上的列表中。 事件的默认列表显示“操作”、“级别”、“状态”、“资源”和“时间”信息。 若要启用其他属性，请执行以下操作：

1. 在“**事件**”边栏选项卡中，单击“**列**”。

    ![打开列](./media/backup-azure-monitor-vms/audi-logs-column-button.png)

    此时会打开“**选择列**”边栏选项卡。

    ![“列”边栏选项卡](./media/backup-azure-monitor-vms/columns-blade.png)
2. 若要选择属性，请单击相应的复选框。 属性复选框的状态将切换为打开或关闭。
3. 单击“**重置**”以重置“**事件**”边栏选项卡中的属性列表。 在列表中添加或删除属性后，使用“**重置**”查看“事件”属性的新列表。
4. 单击“**更新**”以更新“事件”属性中的数据。 下表提供有关每个属性的信息。

| 列名称 | 说明 |
| --- | --- |
| Operation |操作的名称 |
| 级别 |操作级别，值可以是：“信息”、“警告”、“错误”或“关键” |
| 状态 |操作的描述状态 |
| 资源 |用于标识资源的 URL；也称为资源 ID |
| 时间 |发生事件的时间，从当前时间算起 |
| 调用方 |事件的调用者或触发者；可以是系统或用户 |
| Timestamp |触发事件的时间 |
| 资源组 |关联的资源组 |
| 资源类型 |Resource Manager 使用的内部资源类型 |
| 订阅 ID |关联的订阅 ID |
| 类别 |事件的类别 |
| 相关性 ID |相关事件的通用 ID |

## <a name="use-powershell-to-customize-alerts"></a>使用 PowerShell 自定义警报
可以获取门户中作业的自定义警报通知。 若要获取这些作业，请针对操作日志事件定义基于 PowerShell 的警报规则。 使用 *PowerShell 1.3.0 或更高版本*。

若要定义自定义通知以便在备份失败时发出警报，可使用类似于以下脚本的命令：

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.RecoveryServices/recoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/Microsoft.RecoveryServices/vaults/trinadhVault -Actions $actionEmail
```

**ResourceId**：可以从审核日志获取 ResourceId。 ResourceId 是操作日志的“资源”列中提供的 URL。

**OperationName**：OperationName 采用“Microsoft.RecoveryServices/recoveryServicesVault/*EventName*”格式，其中的 *EventName* 可以是：<br/>

* 注册 <br/>
* 注销 <br/>
* ConfigureProtection <br/>
* 备份 <br/>
* 还原 <br/>
* StopProtection <br/>
* DeleteBackupData <br/>
* CreateProtectionPolicy <br/>
* DeleteProtectionPolicy <br/>
* UpdateProtectionPolicy <br/>

**Status**：支持的值为 Started、Succeeded 或 Failed。

**ResourceGroup**：这是资源所属的资源组。 可以将“资源组”列添加到生成的日志。 资源组是一种可用的事件信息类型。

**Name**：警报规则的名称。

**CustomEmail**：指定要向其发送警报通知的自定义电子邮件地址。

**SendToServiceOwners**：此选项会将警报通知发送给订阅的所有管理员和共同管理员。 可以在 **New-AzureRmAlertRuleEmail** cmdlet 中使用

### <a name="limitations-on-alerts"></a>对警报的限制
基于事件的警报存在以下限制：

1. 警报在恢复服务保管库的所有虚拟机上触发。 无法针对恢复服务保管库中的一部分虚拟机自定义警报。
2. 此功能以预览版提供。 [了解详细信息](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. 从“alerts-noreply@mail.windowsazure.com”发送警报。 目前无法修改电子邮件发件人。

## <a name="next-steps"></a>后续步骤
通过事件日志，可以针对备份操作进行很好的事后总结和审核。 将记录以下操作：

* 注册
* 注销
* 配置保护
* 备份（二者均可以按需备份的形式进行计划）
* 还原
* 停止保护
* 删除备份数据
* 添加策略
* 删除策略
* 更新策略
* 取消作业

有关 Azure 服务中事件、操作和审核日志的详细说明，请参阅 [View events and audit logs](../monitoring-and-diagnostics/insights-debugging-with-events.md)（查看事件和审核日志）一文。

有关如何从恢复点重新创建虚拟机的信息，请查看 [Restore Azure VMs](backup-azure-arm-restore-vms.md)（还原 Azure VM）。 如果需要有关如何保护虚拟机的信息，请参阅 [First look: Back up VMs to a Recovery Services vault](backup-azure-vms-first-look-arm.md)（初步了解：将 VM 备份到恢复服务保管库）。 有关 VM 备份管理任务的信息，请参阅 [Manage Azure virtual machine backups](backup-azure-manage-vms.md)（管理 Azure 虚拟机备份）一文。
