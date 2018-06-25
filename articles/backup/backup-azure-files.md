---
title: 备份 Azure 文件共享
description: 本文详述了如何备份和还原 Azure 文件共享，并对管理任务进行了说明。
services: backup
author: markgalioto
ms.author: markgal
ms.date: 3/23/2018
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 9697bd5a55a5cfcdcd6958f8baff85e55c880c87
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287654"
---
# <a name="back-up-azure-file-shares"></a>备份 Azure 文件共享
本文介绍如何使用 Azure 门户备份和还原 [Azure 文件共享](../storage/files/storage-files-introduction.md)。

本指南介绍如何：
> [!div class="checklist"]
> * 将恢复服务保管库配置为备份 Azure 文件
> * 运行按需备份作业，以创建还原点
> * 从还原点还原一个或多个文件
> * 管理备份作业
> * 停止对 Azure 文件的保护
> * 删除备份数据

## <a name="prerequisites"></a>先决条件
在备份 Azure 文件共享之前，请确保其类型为[支持的存储帐户类型](backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview)。 验证这个之后，即可对文件共享进行保护。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>预览版期间 Azure 文件共享备份的限制
Azure 文件共享备份处于预览状态。 Azure 文件共享不支持以下备份场景：
- 不能保护具有[读取访问异地冗余存储](../storage/common/storage-redundancy-grs.md) (RA-GRS) 复制功能的存储帐户中的 Azure 文件共享。
- 不能保护已启用虚拟网络或防火墙的存储帐户中的 Azure 文件共享。
- 无法使用 PowerShell 或 CLI 通过 Azure 备份来保护 Azure 文件。
- 每天的计划备份数上限为 1。
- 每天的按需备份数上限为 4。
- 在存储帐户上使用[资源锁定](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)，防止意外删除恢复服务保管库中的备份。
- 请勿删除由 Azure 备份创建的快照。 删除快照可能导致恢复点丢失和/或还原失败。

\*具有[读取访问异地冗余存储](../storage/common/storage-redundancy-grs.md) (RA-GRS) 复制功能的存储帐户中的 Azure 文件共享将作为 GRS 发挥作用并按 GRS 价格计费

使用[区域冗余存储空间](../storage/common/storage-redundancy-zrs.md) (ZRS) 复制对存储帐户中的 Azure 文件共享进行备份，目前只能在美国中部 (CUS) 和美国东部 2 (EUS2) 中使用

## <a name="configuring-backup-for-an-azure-file-share"></a>为 Azure 文件共享配置备份
所有备份数据都存储在恢复服务保管库中。 本教程假定你已建立 Azure 文件共享。 若要备份 Azure 文件共享，请执行以下操作：

1. 在文件共享所在区域创建恢复服务保管库。 如果已有一个保管库，请打开保管库的“概览”页，然后单击“备份”。

    ![选择“Azure 文件共享”作为“备份目标”](./media/backup-file-shares/overview-backup-page.png)

2. 在“备份目标”菜单的“要备份什么?”中，选择“Azure 文件共享”。

    ![选择“Azure 文件共享”作为“备份目标”](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. 单击“备份”以将 Azure 文件共享配置为备份到恢复服务保管库。 

   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/set-backup-goal.png)

    将保管库与 Azure 文件共享关联以后，“备份”菜单就会打开，提示你选择存储帐户。 该菜单显示保管库所在区域中所有支持的存储帐户，这些帐户尚未与恢复服务保管库关联。

   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/list-of-storage-accounts.png)

4. 在存储帐户列表中选择一个帐户，然后单击“确定”。 Azure 搜索存储帐户中是否有可以备份的文件共享。 如果最近添加了文件共享，但没有在列表中看到它们，请稍等片刻，然后文件共享就会显示。

   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/discover-file-shares.png)

5. 在“文件共享”列表中选择一个或多个需要备份的文件共享，然后单击“确定”。

6. 选择文件共享以后，“备份”菜单就会切换到“备份策略”。 从该菜单中选择现有的备份策略，或者新建一个，然后单击“启用备份”。 

   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/apply-backup-policy.png)

    建立备份策略以后，系统会在计划的时间拍摄文件共享的快照，并会将恢复点保留选定的一段时间。

## <a name="create-an-on-demand-backup"></a>创建按需备份
有时候，可能需要在备份策略计划的时间之外生成备份快照或恢复点。 通常是在配置备份策略之后立即生成按需备份。 可能需要等待数小时或数天才会拍摄快照，具体取决于备份策略中的计划。 若要在该备份策略发挥作用之前对数据进行保护，可以启动按需备份。 在对文件共享进行计划内更改之前，通常需要创建按需备份。 

### <a name="to-create-an-on-demand-backup"></a>创建按需备份的步骤

1. 打开包含文件共享恢复点的恢复服务保管库，然后单击“备份项”。 此时会显示备份项类型的列表。

   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/list-of-backup-items.png)

2. 从列表中选择“Azure 存储(Azure 文件)”。 此时会显示 Azure 文件共享的列表。

   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. 从 Azure 文件共享的列表中，选择所需的文件共享。 此时会打开所选文件共享的“备份项”菜单。

   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/backup-item-menu.png)

4. 在“备份项”菜单中单击“立即备份”。 由于这是按需备份作业，因此没有与恢复点关联的保留策略。 此时会打开“立即备份”对话框。 指定要保留恢复点的最后一天。 
  
   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/backup-now-menu.png)

## <a name="restore-from-backup-of-azure-file-share"></a>从 Azure 文件共享的备份还原
如需从某个还原点还原整个文件共享或单个文件或文件夹，请转到上一部分详细介绍的“备份项”。 选择“还原共享”，从所需的时间点还原整个文件共享。 从显示的还原点列表中，选择一个能够覆盖当前文件共享或者能够将该共享还原到同一区域中的备用文件共享的还原点。

   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>从 Azure 文件共享的备份还原单个文件或文件夹
Azure 备份提供在 Azure 门户中浏览还原点的功能。 若要还原所选文件或文件夹，请单击“备份项”页中的“文件恢复”，然后从还原点列表中进行选择。 选择“恢复目标”，然后单击“选择文件”，对还原点进行浏览。 选择所需文件或文件夹，然后选择“还原”。

   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>管理 Azure 文件共享备份

可以在“备份作业”页上执行多个针对文件共享备份的管理任务，其中包括：
- [监视作业](backup-azure-files.md#monitor-jobs)
- [创建新策略](backup-azure-files.md#create-a-new-policy)
- [停止对文件共享的保护](backup-azure-files.md#stop-protecting-an-azure-file-share)
- [恢复对文件共享的保护](backup-azure-files.md#resume-protection-for-azure-file-share)
- [删除备份数据](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>监视作业

可以在“备份作业”页中监视所有作业的进度。

若要打开“备份作业”页，请执行以下操作：

- 打开要监视的恢复服务保管库，在“恢复服务保管库”菜单中单击“作业”，然后单击“备份作业”。
   ![选择要监视的作业](./media/backup-file-shares/open-backup-jobs.png)

    此时会显示备份作业的列表和这些作业的状态。
   ![选择要监视的作业](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>创建新策略

可以创建新的策略，以便通过恢复服务保管库的“备份策略”备份 Azure 文件共享。 在为文件共享配置备份时创建的所有策略都会显示，其“策略类型”为“Azure 文件共享”。

若要查看现有备份策略，请执行以下操作：

- 打开所需的恢复服务保管库，然后在“恢复服务保管库”菜单中单击“备份策略”。 此时会列出所有备份策略。

   ![选择要监视的作业](./media/backup-file-shares/list-of-backup-policies.png)

若要创建新的备份策略，请执行以下操作：

1. 在“恢复服务保管库”菜单中，单击“备份策略”。
2. 在备份策略列表中，单击“添加”。

   ![选择要监视的作业](./media/backup-file-shares/new-backup-policy.png)

3. 在“添加”菜单中，选择“Azure 文件共享”。 此时会打开 Azure 文件共享的“备份策略”菜单。 提供策略名称、备份频率以及恢复点的保留期。 定义策略后，单击“确定”。

   ![选择要监视的作业](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>停止保护 Azure 文件共享

如果选择停止保护 Azure 文件共享，系统会询问你是否需要保留恢复点。 可以通过两种方法来停止保护 Azure 文件共享：

- 停止所有将来的备份作业并删除所有恢复点，或
- 停止所有将来的备份作业，但保留恢复点

在存储中保留恢复点可能需要付出相关成本，因为需保留 Azure 备份创建的基础快照。 不过，保留恢复点的好处是，可以根据需要在以后还原文件共享。 如需了解保留恢复点的成本，请参阅定价详细信息。 如果选择删除所有恢复点，则无法还原文件共享。

若要停止对 Azure 文件共享的保护，请执行以下操作：

1. 打开包含文件共享恢复点的恢复服务保管库，然后单击“备份项”。 此时会显示备份项类型的列表。

   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/list-of-backup-items.png) 

2. 在“备份管理类型”列表中，选择“Azure 存储(Azure 文件)”。 此时会显示“备份项(Azure 存储(Azure 文件))”列表。

   ![单击某个项，打开额外的菜单](./media/backup-file-shares/azure-file-share-backup-items.png) 

3. 在“备份项(Azure 存储(Azure 文件))”列表中，选择要停止的备份项。

4. 在 Azure 文件共享项中，单击“更多”菜单，然后选择“停止备份”。 

   ![单击某个项，打开额外的菜单](./media/backup-file-shares/stop-backup.png)

5. 从“停止备份”菜单中选择“保留备份数据”或“删除备份数据”，然后单击“停止备份”。

   ![单击某个项，打开额外的菜单](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>恢复对 Azure 文件共享的保护

如果在停止对文件共享的保护时选择了“保留备份数据”选项，则可恢复对文件共享的保护。 如果选择了“删除备份数据”选项，则不能恢复对文件共享的保护。

若要恢复对文件共享的保护，请转到“备份项”并单击“恢复备份”。 “备份策略”打开后，即可选择恢复备份所需的策略。

   ![选择要监视的作业](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>删除备份数据 

可以在执行“停止备份”作业期间删除文件共享的备份，也可以在停止保护之后的任何时间这样做。 甚至还可以等待数天或数周再删除恢复点。 与还原恢复点不同，在删除备份数据时，不能选择要删除的特定恢复点。 如果选择删除备份数据，则会删除与该项关联的所有恢复点。

以下过程假设虚拟机的备份作业已停止。 停止备份作业后，即可在“备份项”仪表板中使用“恢复备份”和“删除备份数据”选项。 请单击“删除备份数据”并键入文件共享的名称来确认删除。 也可提供删除原因或注释。

## <a name="see-also"></a>另请参阅
有关 Azure 文件共享的其他信息，请参阅
- [Azure 文件共享备份常见问题解答](backup-azure-files-faq.md)
- [排查 Azure 文件共享备份问题](troubleshoot-azure-files.md)
