---
title: 还原 Azure 文件共享
description: 了解如何使用 Azure 门户从 Azure 备份创建的还原点还原整个文件共享或特定文件。
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586813"
---
# <a name="restore-azure-file-shares"></a>还原 Azure 文件共享

本文介绍如何使用 Azure 门户从[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)创建的还原点还原整个文件共享或特定文件。

本文将指导如何进行以下操作：

* 还原完整的 Azure 文件共享。
* 还原单个文件或文件夹。
* 跟踪还原操作状态。

## <a name="steps-to-perform-a-restore-operation"></a>执行还原操作的步骤

要执行还原操作，请按照以下步骤操作。

### <a name="select-the-file-share-to-restore"></a>选择要还原的文件共享

1. 在[Azure 门户](https://portal.azure.com/)中，打开用于配置文件共享备份的恢复服务保管库。

1. 在"概述"窗格中，在 **"受保护项目**"部分下选择 **"备份项目**"。

    ![选择备份项目](./media/restore-afs/backup-items.png)

1. 选择 **"备份项目**"后，将在"概览"窗格旁边打开一个列出所有备份管理类型的新窗格。

    ![备份管理类型](./media/restore-afs/backup-management.png)

1. 在 **"备份项目"** 窗格中，在 **"备份管理类型"** 下，选择**Azure 存储（Azure 文件）。** 您将看到使用此保管库备份的所有文件共享及其相应存储帐户的列表。

    ![所有文件共享的列表](./media/restore-afs/file-shares.png)

1. 从 Azure 文件共享列表中，选择要为其执行还原操作的文件共享。

### <a name="full-share-recovery"></a>完全共享恢复

您可以使用此还原选项在原始位置或备用位置还原完整的文件共享。

1. 选择 **"备份项目**"窗格中的 **"还原共享**"选项，该选项在"[选择要还原的文件共享](#select-the-file-share-to-restore)"步骤 5 中选择要还原的文件共享后显示。

   ![选择还原共享](./media/restore-afs/restore-share.png)

1. 选择 **"还原共享**"后，"**还原**"窗格将打开 **"还原点"** 菜单，该菜单显示可用于所选文件共享的还原点列表。

1. 选择要用于执行还原操作的还原点，然后选择 **"确定**"。

    ![选择还原点](./media/restore-afs/restore-point.png)

1. 选择 **"确定"** 后，"**还原**窗格"菜单将切换到 **"还原位置**"。 在 **"还原位置**"中，指定还原数据的位置或方式。 选择以下两个选项之一：

    * **原始位置**：将完整的文件共享还原到与原始源相同的位置。
    * **备用位置**：将完整的文件共享还原到备用位置，并保持原始文件共享原样。

#### <a name="restore-to-the-original-location"></a>还原到原始位置

1. 选择**原始位置**作为**恢复目标**，并选择是否跳过或覆盖是否存在冲突。 进行适当的选择后，选择 **"确定**"。

    ![选择原始位置](./media/restore-afs/original-location.png)

1. 选择 **"还原**"以启动还原操作。

    ![选择"还原"以启动](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>还原到备用位置

1. 选择**备用位置**作为**恢复目标**。
1. 选择要从 **"存储帐户**"下拉列表中还原备份内容的目标存储帐户。
1. **"选择文件共享**"下拉列表显示您在步骤 2 中选择的存储帐户中的文件共享。 选择要还原备份内容的文件共享。
1. 在"**文件夹名称"** 框中，指定要在目标文件共享中与还原的内容创建的文件夹名称。
1. 如果存在冲突，请选择是跳过还是覆盖。
1. 在所有框中输入适当的值后，选择 **"确定**"。

    ![选择备用位置](./media/restore-afs/alternate-location.png)

1. 选择 **"还原**"以启动还原操作。

    ![选择"还原"以启动](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>项目级恢复

您可以使用此还原选项还原原始位置或备用位置中的单个文件或文件夹。

1. 选择"**备份项目"** 窗格中**的文件恢复**选项，该选项在选择要还原的文件共享后显示[。"选择要还原的文件共享](#select-the-file-share-to-restore)"步骤 5 中。

    ![选择文件恢复](./media/restore-afs/file-recovery.png)

1. 选择 **"文件恢复**"后，"**还原**"窗格将打开 **"还原点"** 菜单，该菜单显示可用于所选文件共享的还原点列表。

1. 选择要用于执行还原操作的还原点，然后选择 **"确定**"。

    ![选择还原点](./media/restore-afs/restore-point.png)

1. 选择 **"确定"** 后，还原窗格菜单将切换到 **"还原位置**"。 在 **"还原位置**"中，指定还原数据的位置或方式。 选择以下两个选项之一：

    * **原始位置**：将选定的文件或文件夹还原到与原始源相同的文件共享。
    * **备用位置**：将选定的文件或文件夹还原到备用位置，并保持原始文件共享内容原样。

#### <a name="restore-to-the-original-location"></a>还原到原始位置

1. 选择**原始位置**作为**恢复目标**，并选择是否跳过或覆盖是否存在冲突。

    ![项目级恢复的原始位置](./media/restore-afs/original-location-item-level.png)

1. 选择 **"选择文件"** 以选择要还原的文件或文件夹。

    ![选择"选择文件"](./media/restore-afs/select-file.png)

1. 选择 **"选择文件"** 后，文件共享窗格将显示选择要还原的文件共享恢复点的内容。

1. 选择与要还原的文件或文件夹对应的复选框，然后**选择**。

    ![选择文件或文件夹](./media/restore-afs/select-file-folder.png)

1. 重复步骤 2 到 4 以选择要还原的多个文件或文件夹。
1. 选择要还原的所有项目后，选择 **"确定**"。

    ![选择要还原的所有项目后，选择"确定"](./media/restore-afs/after-selecting-items.png)

1. 选择 **"还原**"以启动还原操作。

    ![选择"还原"以启动](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>还原到备用位置

1. 选择**备用位置**作为**恢复目标**。
1. 选择要从 **"存储帐户**"下拉列表中还原备份内容的目标存储帐户。
1. **"选择文件共享**"下拉列表显示您在步骤 2 中选择的存储帐户中的文件共享。 选择要还原备份内容的文件共享。
1. 在"**文件夹名称"** 框中，指定要在目标文件共享中与还原的内容创建的文件夹名称。
1. 如果存在冲突，请选择是跳过还是覆盖。
1. 选择 **"选择文件"** 以选择要还原的文件或文件夹。

    ![选择要还原到备用位置的项目](./media/restore-afs/restore-to-alternate-location.png)

1. 选择 **"选择文件"** 时，文件共享窗格将显示选择要还原的文件共享恢复点的内容。
1. 选择与要还原的文件或文件夹对应的复选框，然后**选择**。

    ![选择恢复目标](./media/restore-afs/recovery-destination.png)

1. 重复步骤 6 到 8 以选择要还原的多个文件或文件夹。
1. 选择要还原的所有项目后，选择 **"确定**"。

    ![选择所有文件后选择"确定"](./media/restore-afs/after-selecting-all-items.png)

1. 选择 **"还原**"以启动还原操作。

## <a name="track-a-restore-operation"></a>跟踪还原操作

触发还原操作后，备份服务会创建一个作业用于跟踪。 Azure 备份在门户中显示有关作业的通知。 要查看作业的操作，请选择通知超链接。

![选择通知超链接](./media/restore-afs/notifications-link.png)

您还可以监视恢复服务保管库的还原进度：

1. 从触发还原操作的位置打开恢复服务保管库。
1. 在"概述"窗格中，在 **"监视**"部分下选择 **"备份作业**"以查看针对不同工作负载运行的操作状态。

    ![选择备份作业](./media/restore-afs/backup-jobs.png)

1. 选择与文件共享对应的工作负载名称，以查看有关还原操作的更多详细信息，如 **"传输的数据**"和 **"还原文件数**"。

    ![查看还原的详细信息](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>后续步骤

* 了解如何管理[Azure 文件共享备份](manage-afs-backup.md)。
