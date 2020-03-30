---
title: 管理 Azure 文件共享备份
description: 本文介绍用于管理和监视由 Azure 备份备份的 Azure 文件共享的常见任务。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247652"
---
# <a name="manage-azure-file-share-backups"></a>管理 Azure 文件共享备份

本文介绍用于管理和监视 Azure[备份](https://docs.microsoft.com/azure/backup/backup-overview)备份的 Azure 文件共享的常见任务。 您将学习如何在恢复服务保管库中执行管理任务。

## <a name="monitor-jobs"></a>监视作业

触发备份或还原操作时，备份服务将创建用于跟踪的作业。 可以在“备份作业”页中监视所有作业的进度。****

若要打开“备份作业”**** 页，请执行以下操作：

1. 打开用于为文件共享配置备份的恢复服务保管库。 在 **"概述"** 窗格中，在 **"监视**"部分下选择 **"备份作业**"。

   ![监视部分中的备份作业](./media/manage-afs-backup/backup-jobs.png)

1. 选择 **"确定"** 后，"**备份作业"** 窗格将列出所有作业的状态。 选择与要监视的文件共享对应的工作负荷名称。

   ![工作负载名称](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>创建新策略

您可以创建一个新策略，以便从恢复服务保管库的**备份策略**部分备份 Azure 文件共享。 配置文件共享备份时创建的所有策略都将显示为**Azure 文件共享****的策略类型**。

要查看现有备份策略：

1. 打开用于配置文件共享备份的恢复服务保管库。 在"恢复服务保管库"菜单上，选择 **"管理"** 部分下的**备份策略**。 将显示保管库中配置的所有备份策略。

   ![所有备份策略](./media/manage-afs-backup/all-backup-policies.png)

1. 要查看特定于 Azure**文件共享**的策略，请从右上角的下拉列表中选择**Azure 文件共享**。

   ![选择 Azure 文件共享](./media/manage-afs-backup/azure-file-share.png)

要创建新的备份策略，请：

1. 在 **"备份策略"** 窗格中，选择 **" 添加**"。

   ![新建备份策略](./media/manage-afs-backup/new-backup-policy.png)

1. 在 **"添加**"窗格中，选择**Azure 文件共享**作为**策略类型**。 将打开 Azure**文件共享**的**备份策略**窗格。 指定恢复点的策略名称、备份频率和保留范围。 定义策略后，选择 **"确定**"。

   ![定义备份策略](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>修改策略

您可以修改备份策略以更改备份频率或保留范围。

要修改策略：

1. 打开用于配置文件共享备份的恢复服务保管库。 在"恢复服务保管库"菜单上，选择 **"管理"** 部分下的**备份策略**。 将显示保管库中配置的所有备份策略。

   ![保管库中的所有备份策略](./media/manage-afs-backup/all-backup-policies-modify.png)

1. 要查看特定于 Azure 文件共享的策略，请从右上角的下拉列表中选择**Azure 文件共享**。 选择要修改的备份策略。

   ![要修改的 Azure 文件共享](./media/manage-afs-backup/azure-file-share-modify.png)

1. 将打开 **"计划"** 窗格。 根据需要编辑**备份计划和****保留范围**，然后选择 **"保存**"。 您将在窗格中看到"正在进行的更新"消息。 策略更改成功更新后，您将看到消息"已成功更新备份策略"。

   ![保存修改后的策略](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>停止对文件共享的保护

可以通过两种方法来停止保护 Azure 文件共享：

* 停止所有将来的备份作业，并*删除所有恢复点*。
* 停止所有将来的备份作业，但*离开恢复点*。

将恢复点留在存储中可能会产生相关成本，因为 Azure 备份创建的基础快照将保留。 离开恢复点的好处是，您可以稍后还原文件共享。 有关离开恢复点的成本的信息，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/backup/)。 如果您决定删除所有恢复点，则无法还原文件共享。

若要停止对 Azure 文件共享的保护，请执行以下操作：

1. 打开包含文件共享恢复点的恢复服务保管库。 在 **"受保护项目"** 部分下选择**备份项目**。 将显示备份项类型的列表。

   ![备份项](./media/manage-afs-backup/backup-items.png)

1. 在“备份管理类型”**** 列表中，选择“Azure 存储(Azure 文件)”。**** 将显示**备份项目（Azure 存储（Azure 文件）列表**。

   ![选择 Azure 存储（Azure 文件）](./media/manage-afs-backup/azure-storage-azure-files.png)

1. 在**备份项目（Azure 存储（Azure 文件））** 列表中，选择要停止保护的备份项。

1. 选择 **"停止备份**"选项。

   ![选择“停止备份”](./media/manage-afs-backup/stop-backup.png)

1. 在 **"停止备份**"窗格中，选择 **"保留备份数据**"或 **"删除备份数据**"。 然后选择 **"停止备份**"。

    ![选择"保留备份数据"或删除备份数据](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>恢复对文件共享的保护

如果在停止文件共享保护时选择了 **"保留备份数据"** 选项，则可以恢复保护。 如果选择了 **"删除备份数据"** 选项，则无法恢复对文件共享的保护。

要恢复对 Azure 文件共享的保护，请执行以下规定：

1. 打开包含文件共享恢复点的恢复服务保管库。 在 **"受保护项目"** 部分下选择**备份项目**。 将显示备份项类型的列表。

   ![恢复的备份项目](./media/manage-afs-backup/backup-items-resume.png)

1. 在“备份管理类型”**** 列表中，选择“Azure 存储(Azure 文件)”。**** 将显示**备份项目（Azure 存储（Azure 文件）列表**。

   ![Azure 存储列表（Azure 文件）](./media/manage-afs-backup/azure-storage-azure-files.png)

1. 在**备份项目（Azure 存储（Azure 文件））** 列表中，选择要为其恢复保护的备份项。

1. 选择"**恢复备份**"选项。

   ![选择"恢复备份"](./media/manage-afs-backup/resume-backup.png)

1. 将打开 **"备份策略"** 窗格。 选择您选择的策略以恢复备份。

1. 选择备份策略后，选择 **"保存**"。 您将在门户中看到"正在进行的更新"消息。 备份成功恢复后，您将看到消息"已成功更新受保护 Azure 文件共享的备份策略"。

   ![成功更新备份策略](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>删除备份数据

您可以在 **"停止备份作业"** 期间或停止保护后的任何时间删除文件共享的备份。 在删除恢复点之前等待数天甚至数周可能是有益的。 删除备份数据时，无法选择要删除的特定恢复点。 如果您决定删除备份数据，请删除与文件共享关联的所有恢复点。

以下过程假定文件共享的保护已停止。

要删除 Azure 文件共享的备份数据，请执行以下规定：

1. 备份作业停止后，"**备份**"和 **"删除"备份数据**选项在 **"备份项目"** 仪表板中可用。 选择"**删除备份数据**"选项。

   ![删除备份数据](./media/manage-afs-backup/delete-backup-data.png)

1. 将打开 **"删除备份数据"** 窗格。 输入文件共享的名称以确认删除。 可选，在 **"原因**"或 **"注释"** 框中提供更多信息。 确定删除备份数据后，请选择 **"删除**"。

   ![确认删除数据](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>取消注册存储帐户

要使用不同的恢复服务保管库保护特定存储帐户中的文件共享，请首先停止保护该存储帐户[中的所有文件共享](#stop-protection-on-a-file-share)。 然后从用于保护的当前恢复服务保管库中取消注册帐户。

以下过程假定已停止对要取消注册的存储帐户中的所有文件共享的保护。

要取消注册存储帐户，请进行以下说明：

1. 打开存储帐户注册的恢复服务保管库。
1. 在 **"概述"** 窗格中，在 **"管理**"部分下选择 **"备份基础结构**"选项。

   ![选择“备份基础结构”。](./media/manage-afs-backup/backup-infrastructure.png)

1. 将打开 **"备份基础结构"** 窗格。 在**Azure 存储帐户**部分下选择**存储帐户**。

   ![选择存储帐户](./media/manage-afs-backup/storage-accounts.png)

1. 选择 **"存储帐户**"后，将显示在保管库注册的存储帐户列表。
1. 右键单击要取消注册的存储帐户，然后选择 **"取消注册**"。

   ![选择取消注册](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅排除[Azure 文件共享备份的疑难解答](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)。
