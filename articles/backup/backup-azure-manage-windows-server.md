---
title: "管理 Azure 恢复服务保管库和服务器 | Microsoft Docs"
description: "使用本教程来了解如何管理 Azure 恢复服务保管库和服务器。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: 4eea984b-7ed6-4600-ac60-99d2e9cb6d8a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/9/2017
ms.author: markgal
translationtype: Human Translation
ms.sourcegitcommit: 767e3b2e8330b4c52576c90e8ef090e1abc69b42
ms.openlocfilehash: 20e486be706c6fc02ced8343c050379ceff99d82
ms.lasthandoff: 01/23/2017


---
# <a name="monitor-and-manage-azure-recovery-services-vaults-and-servers-for-windows-machines"></a>监视和管理适用于 Windows 计算机的 Azure 恢复服务保管库和服务器
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-manage-windows-server.md)
> * [经典](backup-azure-manage-windows-server-classic.md)
>
>

本文概述了可通过 Azure 门户和 Microsoft Azure 备份代理完成的备份监视和管理任务。 本文假定已拥有 Azure 订阅并已创建了至少一个恢复服务保管库。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>打开恢复服务保管库

恢复服务保管库仪表板显示恢复服务保管库的详细信息或属性。

1. 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“中心”菜单上，单击“更多服务”。

    ![打开恢复服务保管库列表步骤 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png) <br/>

3. 如果要打开恢复服务保管库。 在对话框中，开始键入“恢复服务”。 当你开始键入时，会根据你的输入筛选该列表。 单击“恢复服务保管库”，显示订阅中恢复服务保管库的列表。

    ![创建恢复服务保管库步骤 1](./media/backup-azure-manage-windows-server/browse-to-rs-vaults-2.png) <br/>

    此时将打开恢复服务保管库列表。

    ![创建恢复服务保管库步骤 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. 在保管库的列表中，选择需要打开的恢复服务保管库的名称。 此时会打开恢复服务保管库仪表板边栏选项卡。

    ![恢复服务保管库仪表板](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    打开恢复服务保管库后，请尝试执行任意监视或管理任务。

## <a name="monitor-backup-jobs-and-alerts"></a>监视备份作业和警报

可通过恢复服务保管库仪表板监视作业和警报，可以查看的内容包括：

* 备份警报详细信息
* 文件和文件夹，以及在云中受保护的 Azure 虚拟机
* 在 Azure 中消耗的存储空间总量
* 备份作业状态

![备份仪表板任务](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

单击各个磁贴中的信息即可打开关联的边栏选项卡，以便在其中管理相关任务。

在仪表板的顶部：

* 可通过“设置”访问可用的备份任务。
* 备份 - 用于将新文件和文件夹（或 Azure VM）备份到恢复服务保管库。
* 删除 - 如果不再使用恢复服务保管库，可将其删除，释放存储空间。 只有在从保管库中删除所有受保护的服务器后，才能进行删除。

![备份仪表板任务](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## <a name="alerts-for-backups-using-azure-backup-agent"></a>使用 Azure 备份代理进行备份的警报：
| 警报级别 | 已发送的警报 |
| --- | --- |
| 严重 |备份失败、恢复失败 |
| 警告 |备份已完成，但出现警告（由于损坏问题，有不到一百个文件尚未备份，成功备份了一百多万个文件） |
| 信息性 |无 |

## <a name="manage-backup-alerts"></a>管理备份警报
单击“**备份警报**”磁贴即可打开“**备份警报**”边栏选项卡对警报进行管理。

![备份警报](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

“备份警报”磁贴显示以下项目的数目：

* 过去 24 小时内未解决的严重警报
* 过去 24 小时内未解决的警告性警报

单击各个链接即可转到“**备份警报**”边栏选项卡，可通过其中的筛选视图查看这些警报（严重警报或警告性警报）。

可以在“备份警报”边栏选项卡执行以下操作：

* 选择要包含在警报中的相应信息。

    ![选择列](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)
* 根据严重级别、状态以及开始/结束时间筛选警报。

    ![筛选警报](./media/backup-azure-manage-windows-server/filter-alerts.png)
* 配置通知的严重级别、频率和接收者，以及打开或关闭警报。

    ![筛选警报](./media/backup-azure-manage-windows-server/configure-notifications.png)

如果选择“**每次发起警报**”作为“**通知**”频率，则不会在电子邮件中进行分组或缩减操作。 每个警报均会发送 1 个通知。 这是默认设置，此外还会立即发送解决方法电子邮件。

如果选择“**每小时摘要**”作为“**通知**”频率，则会向用户发送一封电子邮件，告知他们在过去一小时内生成了尚未解决的新警报。 在该小时结束时，将会发送解决方法电子邮件。

可以发送以下严重级别的警报：

* 严重
* 警告
* 信息

可以通过作业详细信息边栏选项卡中的“**停用**”按钮来停用警报。 单击“停用”即可提供解决方法说明。

使用“**选择列**”按钮即可选择要显示在警报中的列。

> [!NOTE]
> 在“**设置**”边栏选项卡中管理备份警报时，可先选择“**监视和报告 > 警报和事件 > 备份警报**”，然后单击“**筛选器**”或“**配置通知**”。
>
>

## <a name="manage-backup-items"></a>管理备份项
现在可以在管理门户中管理本地备份。 在仪表板的“备份”部分，“**备份项**”磁贴显示在保管库中受保护的备份项数目。

单击“备份项”磁贴中的“**文件-文件夹**”。

![备份项磁贴](./media/backup-azure-manage-windows-server/backup-items-tile.png)

此时会打开筛选器设置为“文件夹”的“备份项”边栏选项卡，其中列出了每个特定的备份项。

![备份项](./media/backup-azure-manage-windows-server/backup-item-list.png)

如果选择列表中的特定备份项，则会显示该项的重要详细信息。

> [!NOTE]
> 在“**设置**”边栏选项卡中管理文件和文件夹时，可先选择“**受保护的项 > 备份项**”，然后从下拉列表中选择“**文件-文件夹**”。
>
>

![设置中的备份项](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## <a name="manage-backup-jobs"></a>管理备份作业
本地备份（将本地服务器备份到 Azure 时）和 Azure 备份的备份作业均显示在仪表板中。

在仪表板的“备份”部分，“备份作业”磁贴显示符合以下条件的作业数：

* 正在进行
* 在过去 24 小时内失败。

若要管理备份作业，可单击“**备份作业**”磁贴以打开“备份作业”边栏选项卡。

![设置中的备份项](./media/backup-azure-manage-windows-server/backup-jobs.png)

可通过页面顶部的“**选择列**”按钮修改“备份作业”边栏选项卡中提供的信息。

使用“**筛选器**”按钮在文件和文件夹及 Azure 虚拟机备份之间进行选择。

如果看不到备份的文件和文件夹，可单击页面顶部的“**筛选器**”按钮，然后从“项类型”菜单中选择“**文件和文件夹**”。

> [!NOTE]
> 在“**设置**”边栏选项卡中管理备份作业时，可先选择“**监视和报告 > 作业 > 备份作业**”，然后从下拉菜单中选择“**文件-文件夹**”。
>
>

## <a name="monitor-backup-usage"></a>监视备份使用情况
在仪表板的“备份”部分，“备份使用情况”磁贴显示在 Azure 中耗用的存储空间。 具体提供以下存储使用情况：

* 与保管库关联的 Cloud LRS 存储使用情况
* 与保管库关联的 Cloud GRS 存储使用情况

## <a name="manage-your-production-servers"></a>管理生产服务器
若要管理生产服务器，请单击“**设置**”。

在“管理”下单击“**备份基础结构 > 生产服务器**”。

“生产服务器”边栏选项卡此时会列出所有可用的生产服务器。 单击列表中的服务器，打开服务器详细信息。

![受保护的项](./media/backup-azure-manage-windows-server/production-server-list.png)


## <a name="open-the-azure-backup-agent"></a>打开 Azure 备份代理
打开“**Microsoft Azure 备份代理**”（可以通过在计算机中搜索 *Microsoft Azure 备份*来找到它）。

![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/snap-in-search.png)

通过备份代理控制台右侧的“**操作**”，可以执行以下管理任务：

* 注册服务器
* 计划备份
* 立即备份
* 更改属性

![Microsoft Azure 备份代理控制台操作](./media/backup-azure-manage-windows-server/console-actions.png)

> [!NOTE]
> 若要**恢复数据**，请参阅 [Restore files to a Windows server or Windows client machine](backup-azure-restore-windows-server.md)（将文件还原到 Windows Server 或 Windows 客户端计算机）。
>
>

## <a name="modify-the-backup-schedule"></a>修改备份计划
1. 在 Microsoft Azure 备份代理中，单击“**计划备份**”。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/schedule-backup.png)
2. 在**计划备份向导**中，将“**更改备份项或时间**”选项保留选中状态，然后单击“**下一步**”。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
3. 如果你要添加或更改项，请在“**选择要备份的项**”屏幕中单击“**添加项**”。

    还可以在此向导页中设置“**排除设置**”。 如果你要排除文件或文件类型，请阅读有关添加[排除设置](#exclusion-settings) 的过程。
4. 选择要备份的文件和文件夹，然后单击“**确定**”。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/add-items-modify.png)
5. 指定“**备份计划**”并单击“**下一步**”。

    可以计划每日（一天最多 3 次）或每周备份。

    ![Windows Server 备份项](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > [此文](backup-azure-backup-cloud-as-tape.md)中详细介绍了如何指定备份计划。
   >

6. 选择备份副本的“**保留策略**”，然后单击“**下一步**”。

    ![Windows Server 备份项](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)
7. 在“**确认**”屏幕上复查信息，然后单击“**完成**”。
8. 向导完成创建**备份计划**后，请单击“**关闭**”。

    修改保护设置后，可以通过转到“**作业**”选项卡并确认更改已反映在备份作业中，来确认可正确触发备份。

## <a name="enable-network-throttling"></a>启用网络限制

Azure 备份代理提供的“限制”选项卡可让你控制在数据传输期间使用网络带宽的方式。 如果需要在上班时间内备份数据，但不希望备份程序干扰其他 Internet 流量，此控制机制很有帮助。 数据传输的限制适用于备份和还原活动。  

若要启用限制，请执行以下操作：

1. 在“**备份代理**”中，单击“**更改属性**”。
2. 在“限制”选项卡上，选择“为备份操作启用 Internet 带宽使用限制**”。

    ![网络限制](./media/backup-azure-manage-windows-server/throttling-dialog.png)

    启用限制后，指定在“**工作时间**”和“**非工作时间**”允许使用多少带宽进行备份数据传输。

    带宽值从每秒 512 千字节 (Kbps) 开始，最高可为每秒 1023 兆字节 (Mbps)。 你还可以指定“**工作时间**”的开始和结束时间，以及一周中有哪几天被视为工作日。 在指定的工作时间之外的时间被视为非工作时间。
3. 单击 **“确定”**。

## <a name="manage-exclusion-settings"></a>管理排除设置
1. 打开 **Microsoft Azure 备份代理**（可以通过在计算机中搜索 *Microsoft Azure 备份*来找到它）。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/snap-in-search.png)
2. 在 Microsoft Azure 备份代理中，单击“**计划备份**”。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/schedule-backup.png)
3. 在计划备份向导中，将“**更改备份项或时间**”选项保留选中状态，然后单击“**下一步**”。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
4. 单击“**排除设置**”。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/exclusion-settings.png)
5. 单击“**添加排除项**”。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/add-exclusion.png)
6. 选择位置，然后单击“**确定**”。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/exclusion-location.png)
7. 在“**文件类型**”字段中添加文件扩展名。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    添加 .mp3 扩展名

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    若要添加其他扩展名，请单击“**添加排除项**”，然后输入另一个文件类型扩展名（添加 .jpeg 扩展名）。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/exclude-jpg.png)
8. 添加所有扩展名之后，请单击“**确定**”。
9. 单击“**下一步**”继续运行计划备份向导，出现“**确认**”页时，请单击“**完成**”。

    ![计划 Windows Server 备份](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## <a name="frequently-asked-questions"></a>常见问题
**问&1;.备份作业状态在 Azure 备份代理中显示为“已完成”，但没有立即反映在门户中，为什么？**

A1. 备份作业状态反映在 Azure 备份代理中与反映在 Azure 门户中存在延迟，最大延迟为 15 分钟。

**问&2;. 备份作业失败以后，需要多长时间才会引发警报？**

答 2. Azure 备份失败以后，会在 20 分钟内引发警报。

**问&3;.是否存在配置了通知却不发送电子邮件的情况？**

A3. 以下是为了降低警报噪音而不发送通知的情况：

* 如果已将通知配置为每小时，并且在一小时内引发并解决了警报
* 取消了作业。
* 第二个备份作业失败，因为原始备份作业正在进行中。

## <a name="troubleshooting-monitoring-issues"></a>监视问题疑难解答
**问题：**Azure 备份代理的作业和/或警报未在门户中出现。

**故障排除步骤：**```OBRecoveryServicesManagementAgent``` 进程将作业和警报数据发送到 Azure 备份服务。 此进程偶尔会被阻塞或关闭。

1. 若要验证进程是否未运行，请打开“任务管理器”并检查 ```OBRecoveryServicesManagementAgent``` 进程是否正在运行。
2. 假设此进程未运行，请打开“控制面板”，并浏览服务列表。 启动或重启 **Microsoft Azure 恢复服务管理代理**。

    有关详细信息，请浏览以下位置中的日志：<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`
    例如：<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>后续步骤
* [从 Azure 还原 Windows Server 或 Windows 客户端](backup-azure-restore-windows-server.md)
* 若要了解有关 Azure 备份的详细信息，请参阅 [Azure 备份概述](backup-introduction-to-azure-backup.md)
* 访问 [Azure 备份论坛](http://go.microsoft.com/fwlink/p/?LinkId=290933)

