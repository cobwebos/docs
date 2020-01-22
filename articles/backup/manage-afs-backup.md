---
title: 管理 Azure 文件共享备份
description: 本文介绍了用于管理和监视 Azure 备份服务备份的 Azure 文件共享的常见任务。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294547"
---
# <a name="manage-azure-file-share-backups"></a>管理 Azure 文件共享备份

本文介绍了用于管理和监视[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)服务备份的 azure 文件共享的常见任务。 你将了解如何在恢复服务保管库中执行以下管理任务：

* [监视作业](#monitor-jobs)
* [创建新策略](#create-a-new-policy)
* [修改策略](#modify-policy)
* [停止对文件共享的保护](#stop-protection-on-a-file-share)
* [恢复对文件共享的保护](#resume-protection-on-a-file-share)
* [删除备份数据](#delete-backup-data)
* [取消注册存储帐户](#unregister-storage-account)

## <a name="monitor-jobs"></a>监视作业

触发备份或还原操作时，备份服务会创建一个作业用于跟踪。 可以在“备份作业”页中监视所有作业的进度。

若要打开“备份作业”页，请执行以下操作：

1. 打开用于为文件共享配置备份的恢复服务保管库。 在 "**概述**" 边栏选项卡中，单击 "**监视**" 部分下的 "**备份作业**"。

   ![监视部分中的备份作业](./media/manage-afs-backup/backup-jobs.png)

2. 单击 "确定" 后，将显示 "**备份作业**" 边栏选项卡，其中列出了所有作业的状态。 你可以单击与想要监视的文件共享对应的工作负荷名称。

   ![工作负荷名称](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>创建新策略

可以从恢复服务保管库的 "**备份策略**" 部分创建新的策略，以便备份 Azure 文件共享。 为文件共享配置备份时创建的所有策略都将显示为 "Azure 文件共享" 形式的策略类型。

若要查看现有备份策略，请执行以下操作：

1. 打开用于配置文件共享备份的恢复服务保管库，然后在 "恢复服务保管库" 菜单中，单击 "管理" 部分下的 "**备份策略**"。 将列出保管库中配置的所有备份策略。

   ![所有备份策略](./media/manage-afs-backup/all-backup-policies.png)

2. 若要查看特定于 Azure 文件共享的策略，请从右上方的下拉菜单中选择 " **Azure 文件共享**"。

   ![选择 Azure 文件共享](./media/manage-afs-backup/azure-file-share.png)

若要创建新的备份策略，请执行以下操作：

1. 在 "备份策略" 边栏选项卡中单击 " **+ 添加**"。

   ![新建备份策略](./media/manage-afs-backup/new-backup-policy.png)

2. 在 "**添加**" 边栏选项卡中选择 " **Azure 文件共享**" 作为**策略类型**。 将打开 Azure 文件共享的 "备份策略" 边栏选项卡。 为恢复点指定策略名称、备份频率和保持期。 定义策略后，单击 **"确定"** 。

   ![定义备份策略](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>修改策略

您可以修改备份策略以更改备份频率或保持期。

修改策略：

1. 打开用于为文件共享配置备份的恢复服务保管库，然后在 "恢复服务保管库" 菜单中，单击 "管理" 部分下的 "**备份策略**"。 将列出保管库中配置的所有备份策略。

   ![保管库中的所有备份策略](./media/manage-afs-backup/all-backup-policies-modify.png)

2. 若要查看特定于 Azure 文件共享的策略，请从右上方的下拉菜单中选择 " **Azure 文件共享**"。 单击要修改的备份策略。

   ![要修改的 Azure 文件共享](./media/manage-afs-backup/azure-file-share-modify.png)

3. 将打开 "**计划**" 边栏选项卡。 根据需要编辑备份计划/保留范围，并单击 "**保存**"。 你会在边栏选项卡上看到 "正在进行更新" 消息，并且策略更改成功更新后，你将看到消息 "已成功更新备份策略"。

   ![保存修改后的策略](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>停止对文件共享的保护

可以通过两种方法来停止保护 Azure 文件共享：

* 停止所有将来的备份作业并*删除所有恢复点*
* 停止所有将来的备份作业 *，但保留恢复点*

在存储中保留恢复点可能会产生费用，因为 Azure 备份创建的底层快照将会保留。 不过，保留恢复点的好处是，可以根据需要在以后还原文件共享。 有关保留恢复点的成本的信息，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/backup/)。 如果选择删除所有恢复点，则无法还原文件共享。

若要停止对 Azure 文件共享的保护，请执行以下操作：

1. 打开包含文件共享恢复点的恢复服务保管库，并单击 "受保护的项" 部分下的 "**备份项**"。 此时会显示备份项类型的列表。

   ![备份项](./media/manage-afs-backup/backup-items.png)

2. 在“备份管理类型”列表中，选择“Azure 存储(Azure 文件)”。 此时将显示 **（Azure 存储（Azure 文件））的备份项**列表。

   ![选择 Azure 存储（Azure 文件）](./media/manage-afs-backup/azure-storage-azure-files.png)

3. 在**备份项列表（Azure 存储（Azure 文件））** 中，选择要停止保护的备份项。

4. 从 "**备份项**" 边栏选项卡的菜单中选择 "**停止备份**" 选项。

   ![选择“停止备份”](./media/manage-afs-backup/stop-backup.png)

5. 在 "**停止备份**" 边栏选项卡中，选择**保留备份数据**或**删除备份数据**，然后单击 "**停止备份**"。

    ![选择保留或删除备份数据](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>恢复对文件共享的保护

如果在停止对文件共享的保护时选择了 "**保留备份数据**" 选项，则可以恢复保护。 如果选择了“删除备份数据”选项，则不能恢复对文件共享的保护。

恢复对 Azure 文件共享的保护：

1. 打开包含文件共享恢复点的恢复服务保管库，并单击 "受保护的项" 部分下的 "**备份项**"。 此时会显示备份项类型的列表。

   ![用于恢复的备份项](./media/manage-afs-backup/backup-items-resume.png)

2. 在“备份管理类型”列表中，选择“Azure 存储(Azure 文件)”。 此时将显示 **（Azure 存储（Azure 文件））的备份项**列表。

   ![Azure 存储（Azure 文件）列表](./media/manage-afs-backup/azure-storage-azure-files.png)

3. 在**备份项列表（Azure 存储（Azure 文件））** 中，选择要为其恢复保护的备份项。

4. 选择 "**备份项**" 边栏选项卡菜单中的 "**恢复备份**" 选项。

   ![选择恢复备份](./media/manage-afs-backup/resume-backup.png)

5. "**备份策略**" 边栏选项卡将打开，你可以选择要恢复备份的策略。

6. 选择所需的**备份策略**后，单击 "**保存**"。 你将在门户上看到 "正在进行更新" 消息，一旦成功恢复备份，你将看到消息 "已成功为受保护的 Azure 文件共享更新备份策略"。

   ![已成功更新备份策略](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>删除备份数据

可以在 "**停止备份**" 作业过程中删除文件共享的备份，也可以在停止保护之后的任何时间删除文件共享的备份。 甚至还可以等待数天或数周再删除恢复点。 删除备份数据时，不能选择要删除的特定恢复点。 如果选择删除备份数据，则会删除与文件共享关联的所有恢复点。

以下过程假定已停止对文件共享的保护。

删除 Azure 文件共享的备份数据：

1. 停止备份作业后，可在 "**备份项**" 仪表板中使用 "**恢复备份**" 和 "**删除备份数据**" 选项。 单击 "**备份项**" 边栏选项卡菜单中的 "**删除备份数据**" 选项。

   ![删除备份数据](./media/manage-afs-backup/delete-backup-data.png)

2. 将打开 "**删除备份数据**" 边栏选项卡。 键入文件共享的名称以确认删除。 还可以提供删除或**注释**的**原因**。 确定删除备份数据后，单击 "**删除**"。

   ![确认删除数据](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>取消注册存储帐户

如果要使用不同的恢复服务保管库保护特定存储帐户中的文件共享，请先停止对该存储帐户中[所有文件共享的保护](#stop-protection-on-a-file-share)。 然后从用于保护的当前恢复服务保管库中取消注册该帐户。

以下过程假定已停止对要注销的存储帐户中的所有文件共享的保护。

若要注销存储帐户，请执行以下操作：

1. 打开 "恢复服务" 保管库，其中注册了存储帐户。
2. 在**概述**边栏选项卡的 "**管理**" 部分下，单击 "**备份基础结构**" 选项。

   ![单击 "备份基础结构"](./media/manage-afs-backup/backup-infrastructure.png)

3. 此时将打开 "**备份基础结构**" 边栏选项卡。 单击此边栏选项卡上的 " **Azure 存储帐户**" 部分下的 "**存储帐户**"。

   ![单击 "存储帐户"](./media/manage-afs-backup/storage-accounts.png)

4. 单击 "**存储帐户**" 后，将显示注册到保管库的存储帐户的列表。
5. 右键单击要注销的存储帐户，然后选择 "**注销**"。

   ![选择注销](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅对[Azure 文件共享的备份/还原失败进行故障排除](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)
