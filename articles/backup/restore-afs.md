---
title: 还原 Azure 文件共享
description: 了解如何使用 Azure 门户从 Azure 备份服务创建的还原点还原整个文件共享或特定文件。
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b16eb4120ff2d269135ae8ae6555ef4fdbdbda5d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294391"
---
# <a name="restore-azure-file-shares"></a>还原 Azure 文件共享

本文介绍如何使用 Azure 门户从[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)服务创建的还原点还原整个文件共享或特定文件。

在本指南中，你将了解如何执行以下操作：

* 还原完整的 Azure 文件共享
* 还原单个文件或文件夹
* 跟踪还原操作状态

## <a name="steps-to-perform-restore"></a>执行还原的步骤

### <a name="select-the-file-share-to-restore"></a>选择要还原的文件共享

1. 在[Azure 门户](https://portal.azure.com/)中，打开用于为文件共享配置备份的恢复服务保管库。

2. 单击**概述**边栏选项卡的 "**受保护的项**" 部分下的 "**备份项**"。

    ![单击 "备份项"](./media/restore-afs/backup-items.png)

3. 单击 "**备份项**" 后，将在 "**概述**" 边栏选项卡旁边显示一个新的边栏选项卡，其中列出了所有备份管理类型。

    ![备份管理类型](./media/restore-afs/backup-management.png)

4. 在 "**备份项**" 中的 "**备份管理类型**" 下，选择 " **azure 存储（azure 文件）** "。 你将看到使用此保管库备份的所有文件共享及其相应存储帐户的列表。

    ![所有文件共享的列表](./media/restore-afs/file-shares.png)

5. 从 Azure 文件共享列表中，选择要为其执行还原操作的所需文件共享。

### <a name="full-share-recovery"></a>完全共享恢复

您可以使用此还原选项在原始或备用位置还原完整的文件共享。

1. 在 "[选择要还原的文件共享](#select-the-file-share-to-restore)" 部分的步骤5中选择要还原的 "**备份项**" 边栏选项卡中，选择 "**还原共享**" 选项。

   ![选择还原共享](./media/restore-afs/restore-share.png)

2. 单击 "**还原共享**" 后，**将打开**"还原" 边栏选项卡，其中**显示了所**选文件共享可用的还原点列表。

3. 选择要用于执行还原操作的还原点，然后单击 **"确定"** 。

    ![选择还原点](./media/restore-afs/restore-point.png)

4. 单击 "确定" 后，"还原" 边栏选项卡菜单将切换到 "**还原位置**"。 在 "**还原位置**" 中，指定还原数据的位置（或方法）。 可以选择以下两个选项之一：

    * **原位置**：将完整的文件共享还原到与原始源相同的位置。
    * **备用位置**：将完整的文件共享还原到备用位置，并**按原样**保留原始文件共享。

#### <a name="restore-to-original-location"></a>还原到原始位置

1. 选择 "**原始位置**" 作为**恢复目标**，然后选择是否要在发生冲突时跳过或覆盖。 做出适当的选择后，单击 **"确定"** 。

    ![选择原始位置](./media/restore-afs/original-location.png)

2. 单击 "**还原**" 以启动还原操作。

    ![单击 "还原" 以启动](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>还原到备用位置

1. 选择**备用位置**作为恢复目标。
2. 从 "**存储帐户**" 字段的下拉菜单中选择要从中还原备份内容的目标存储帐户。
3. 根据步骤2中所选的存储帐户，"**选择文件共享**" 下拉菜单将显示在所选存储帐户中存在的文件共享的列表。 选择要在其中还原备份内容的文件共享。
4. 在 "**文件夹名称**" 字段中，指定要在目标文件共享中使用还原的内容创建的文件夹名称。
5. 选择是否要在存在冲突时跳过或覆盖。
6. 在所有字段中输入适当的值后，单击 **"确定"** 。

    ![选择备用位置](./media/restore-afs/alternate-location.png)

7. 单击 "还原" 以启动还原操作。

    ![单击 "还原" 以启动](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>项目级恢复

您可以使用此还原选项还原原始或备用位置中的单个文件或文件夹。

1. 在 "[选择要还原的文件共享](#select-the-file-share-to-restore)" 部分的步骤5中选择要还原的 "**备份项**" 边栏选项卡中，选择 "**文件恢复**" 选项。

    ![选择文件恢复](./media/restore-afs/file-recovery.png)

2. 单击 "**文件恢复**" 后，将打开 "**还原**" 边栏选项卡，其中**显示了所**选文件共享可用的还原点列表。

3. 选择要用于执行还原操作的还原点，然后单击 **"确定"** 。

    ![选择还原点](./media/restore-afs/restore-point.png)

4. 单击 "确定" 后，"还原" 边栏选项卡菜单将切换到 "**还原位置**"。 在 "**还原位置**" 中，指定还原数据的位置（或方法）。 可以选择以下两个选项之一：

    * **原位置**：将选定的文件/文件夹还原到与原始源相同的文件共享。
    * **备用位置**：将选定的文件/文件夹还原到备用位置，并**按原样**保留原始文件共享内容。

#### <a name="restore-to-original-location"></a>还原到原始位置

1. 选择 "**原始位置**" 作为**恢复目标**，然后选择是否要在发生冲突时跳过或覆盖。

    ![项目级恢复的原始位置](./media/restore-afs/original-location-item-level.png)

2. 单击 "**选择文件**" 以选择要还原的文件/文件夹。

    ![单击 "选择文件"](./media/restore-afs/select-file.png)

3. 单击 "**选择文件**" 时，将显示 "文件共享" 边栏选项卡，其中显示了为还原选择的文件共享恢复点的内容。

4. 选中对应于要还原的文件/文件夹的复选框，然后单击 "**选择**"。

    ![选择文件或文件夹](./media/restore-afs/select-file-folder.png)

5. 重复步骤2-4，选择多个文件/文件夹进行还原。
6. 选择所有要还原的项后，单击 **"确定"** 。

    ![选择所有要还原的项后，单击 "确定"](./media/restore-afs/after-selecting-items.png)

7. 单击 "还原" 以启动还原操作。

    ![单击 "还原" 以启动](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>还原到备用位置

1. 选择**备用位置**作为恢复目标。
2. 从 "**存储帐户**" 字段的下拉菜单中选择要从中还原备份内容的目标存储帐户。
3. 根据步骤2中所选的存储帐户，"**选择文件共享**" 下拉菜单将显示在所选存储帐户中存在的文件共享的列表。 选择要在其中还原备份内容的文件共享。
4. 在 "**文件夹名称**" 字段中，指定要在目标文件共享中使用还原的内容创建的文件夹名称。
5. 选择是否要在存在冲突时跳过或覆盖。
6. 单击 "**选择文件**" 以选择要还原的文件/文件夹。

    ![选择要还原到备用位置的项目](./media/restore-afs/restore-to-alternate-location.png)

7. 单击 "**选择文件**" 时，将显示 "文件共享" 边栏选项卡，其中显示了为还原选择的文件共享恢复点的内容。
8. 选中与要还原的文件/文件夹对应的复选框，然后单击 "**选择**"。

    ![选择恢复目标](./media/restore-afs/recovery-destination.png)

9. 重复步骤6-8，选择多个文件/文件夹进行还原。
10. 选择所有要还原的项后，单击 **"确定"** 。

    [选择所有文件后单击 "确定"](./media/restore-afs/after-selecting-all-items.png)

11. 单击 "**还原**" 以启动还原操作。

## <a name="track-restore-operation"></a>跟踪还原操作

触发还原操作后，备份服务会创建一个作业用于跟踪。 Azure 备份在门户中显示有关作业的通知。 若要查看针对作业的操作，请单击通知超链接。

![单击 "通知" 超链接](./media/restore-afs/notifications-link.png)

你还可以监视恢复服务保管库中的还原进度。 下面是检查还原操作状态的步骤：

1. 从触发还原操作的位置打开恢复服务保管库。
2. 单击 "**概述**" 边栏选项卡的 "**监视" 部分**下的 "**备份作业**"，查看针对不同工作负荷运行的操作的状态。

    ![单击 "备份作业"](./media/restore-afs/backup-jobs.png)

3. 若要查看有关还原操作的详细信息（例如，已传输数据、已还原文件的数量等），请单击与文件共享对应的工作负荷名称。

    ![查看还原的详细信息](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[管理 Azure 文件共享备份](manage-afs-backup.md)
