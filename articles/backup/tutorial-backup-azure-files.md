---
title: 使用 Azure 备份服务备份 Azure 文件存储文件共享
description: 本教程介绍如何备份 Azure 文件共享。
author: dcurwin
ms.author: dacurwin
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: a06504438340f62d5b338165cb8b159459ae35dc
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467079"
---
# <a name="back-up-azure-file-shares"></a>备份 Azure 文件共享
本文介绍如何使用 Azure 门户备份和还原 [Azure 文件共享](../storage/files/storage-files-introduction.md)。

本指南介绍如何：
> [!div class="checklist"]
> * 将恢复服务保管库配置为备份 Azure 文件
> * 运行按需备份作业，以创建还原点


## <a name="prerequisites"></a>先决条件
在备份 Azure 文件共享之前，请确保其类型为[支持的存储帐户类型](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview)。 验证这个之后，即可对文件共享进行保护。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>预览期间对 Azure 文件共享备份的限制
Azure 文件共享备份处于预览状态。 常规用途 v1 和常规用途 v2 存储帐户中的 Azure 文件共享均受支持。 Azure 文件共享不支持以下备份场景：
- 不能保护已启用虚拟网络或防火墙的存储帐户中的 Azure 文件共享。
- 无法使用 CLI 通过 Azure 备份来保护 Azure 文件。
- 每天的计划备份数上限为 1。
- 每天的按需备份数上限为 4。
- 在存储帐户上使用[资源锁定](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)，防止意外删除恢复服务保管库中的备份。
- 请勿删除由 Azure 备份创建的快照。 删除快照可能导致恢复点丢失和/或还原失败。
- 请勿删除受 Azure 备份保护的文件共享。 当前的解决方案会在文件共享删除后删除 Azure 备份创建的所有快照，因此会失去所有还原点。

使用[区域冗余存储](../storage/common/storage-redundancy-zrs.md) (ZRS) 复制对存储帐户中的 Azure 文件共享进行备份，目前只能在美国中部 (CUS)、美国东部 (EUS)、美国东部 2 (EUS2)、北欧 (NE)、东南亚 (SEA)、西欧 (WE) 和美国西部 2 (WUS2) 使用。

## <a name="configuring-backup-for-an-azure-file-share"></a>为 Azure 文件共享配置备份
本教程假定你已建立 Azure 文件共享。 若要备份 Azure 文件共享，请执行以下操作：

1. 在文件共享所在区域创建恢复服务保管库。 如果已有一个保管库，请打开保管库的“概览”页，然后单击“备份”  。

    ![在保管库的“概述”页中单击“备份”](./media/backup-file-shares/overview-backup-page.png)

2. 在“备份目标”菜单的“要备份什么?”中，   选择“Azure 文件共享”。

    ![选择“Azure 文件共享”作为“备份目标”](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. 单击“备份”  以将 Azure 文件共享配置为备份到恢复服务保管库。

   ![单击“备份”以将 Azure 文件共享与保管库相关联](./media/backup-file-shares/set-backup-goal.png)

    将保管库与 Azure 文件共享关联以后，“备份”菜单就会打开，提示你选择存储帐户。 该菜单显示保管库所在区域中尚未与恢复服务保管库关联的所有受支持的存储帐户。

   ![选择存储帐户](./media/backup-file-shares/list-of-storage-accounts.png)

4. 在存储帐户列表中选择一个帐户，然后单击“确定”。  Azure 搜索存储帐户中是否有可以备份的文件共享。 如果最近添加了文件共享，但没有在列表中看到它们，请稍等片刻，然后文件共享就会显示。

   ![正在发现文件共享](./media/backup-file-shares/discover-file-shares.png)

5. 在“文件共享”列表中选择一个或多个需要备份的文件共享，然后单击“确定”。  

6. 选择文件共享以后，“备份”菜单就会切换到“备份策略”。  从该菜单中选择现有的备份策略，或者新建一个，然后单击“启用备份”。 

   ![选择备份策略或创建一个新策略](./media/backup-file-shares/apply-backup-policy.png)

    建立备份策略以后，系统会在计划的时间拍摄文件共享的快照，并会将恢复点保留选定的一段时间。

## <a name="create-an-on-demand-backup"></a>创建按需备份
配置备份策略后，你将需要创建按需备份，以确保你的数据在下一次计划备份之前受到保护。


### <a name="to-create-an-on-demand-backup"></a>创建按需备份的步骤

1. 打开包含文件共享恢复点的恢复服务保管库，然后单击“备份项”。  此时会显示备份项类型的列表。

   ![备份项列表](./media/backup-file-shares/list-of-backup-items.png)

2. 从列表中选择“Azure 存储(Azure 文件)”。  此时会显示 Azure 文件共享的列表。

   ![Azure 文件共享列表](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. 从 Azure 文件共享的列表中，选择所需的文件共享。 此时会打开所选文件共享的“备份项”菜单。

   ![所选文件共享的“备份项”菜单](./media/backup-file-shares/backup-item-menu.png)

4. 在“备份项”菜单中单击“立即备份”  。 由于这是按需备份作业，因此没有与恢复点关联的保留策略。 此时会打开“立即备份”对话框。  指定要保留恢复点的最后一天。

   ![选择恢复点保留期的日期](./media/backup-file-shares/backup-now-menu.png)


## <a name="next-steps"></a>后续步骤

本教程使用 Azure 门户执行了以下操作：

> [!div class="checklist"]
> * 将恢复服务保管库配置为备份 Azure 文件
> * 运行按需备份作业，以创建还原点

继续阅读下一篇文章，了解如何从 Azure 文件共享的备份还原。

> [!div class="nextstepaction"]
> [从 Azure 文件共享的备份还原](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
 
