---
title: 在 Azure 门户中备份 Azure 文件共享
description: 了解如何使用 Azure 门户在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b2e2053857147513a95b3ae72b82d55450ebcffa
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294508"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>在恢复服务保管库中备份 Azure 文件共享

本文介绍如何使用 Azure 门户备份[Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)。

在本指南中，你将学习如何：

* 创建恢复服务保管库
* 发现文件共享并配置备份
* 运行按需备份作业，以创建还原点

## <a name="prerequisites"></a>必备组件

* 在与托管文件共享的存储帐户相同的区域中标识或创建[恢复服务保管库](#create-a-recovery-services-vault)。

* 确保文件共享存在于[受支持的存储帐户类型](#limitations-for-azure-file-share-backup-during-preview)之一中。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>预览版期间 Azure 文件共享备份的限制

Azure 文件共享备份为预览版。 常规用途 v1 和常规用途 v2 存储帐户中的 Azure 文件共享均受支持。 下面是备份 Azure 文件共享的限制：

* 支持在具有[区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)（ZRS）复制的存储帐户中备份 Azure 文件共享当前限制为[这些区域](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)。
* Azure 备份当前支持配置 Azure 文件共享的计划一次日常备份。
* 每天的计划备份数上限为 1。
* 每天的按需备份数上限为 4。
* 在存储帐户上使用[资源锁定](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)，防止意外删除恢复服务保管库中的备份。
* 请勿删除由 Azure 备份创建的快照。 删除快照可能导致恢复点丢失和/或还原失败。
* 请勿删除受 Azure 备份保护的文件共享。 删除文件共享后，当前解决方案将删除 Azure 备份执行的所有快照，因此所有还原点都将丢失。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>修改存储复制

默认情况下，保管库使用[异地冗余存储（GRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

* 如果保管库是你的主要备份机制，则建议你使用 GRS。

* 可以使用[本地冗余存储（LRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)作为低成本选项。

按如下所示修改存储复制类型：

1. 在新保管库中，单击 "**设置**" 部分中的 "**属性**"。

2. 在 "**属性**" 中的 "**备份配置**" 下，单击 "**更新**"。

3. 选择存储复制类型，然后单击 "**保存**"。

    ![更新备份配置](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 在设置保管库并包含备份项后，不能修改存储复制类型。 如果要执行此操作，则需要重新创建保管库。
>

## <a name="discover-file-shares-and-configure-backup"></a>发现文件共享并配置备份

1. 在[Azure 门户](https://portal.azure.com/)中，打开要用于备份文件共享的恢复服务保管库。

2. 在**恢复服务保管库**仪表板中，选择 " **+ 备份**"。

   ![恢复服务保管库](./media/backup-afs/recovery-services-vault.png)

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 "**备份目标**" 中，将 **"工作负荷的运行位置"** 设置为 " **Azure**"。

    ![选择 Azure 文件共享作为备份目标](./media/backup-afs/backup-goal.png)

    b.保留“数据库类型”设置，即设置为“共享”。    在**要备份的内容**中，从下拉菜单中选择 " **Azure 文件共享**"。

    c.    单击 "**备份**" 以在保管库中注册 Azure 文件共享扩展。

      ![单击 "备份"，将 Azure 文件共享与保管库相关联](./media/backup-afs/register-extension.png)

3. 单击 "**备份**" 后，"备份" 边栏选项卡将打开，并提示你从已发现支持的存储帐户列表中选择一个存储帐户。 它们要么与此保管库关联，要么位于与保管库相同的区域中，但尚未关联到任何恢复服务保管库。

   ![选择存储帐户](./media/backup-afs/select-storage-account.png)

4. 从已发现的存储帐户列表中，选择一个帐户，然后单击 **"确定"** 。 Azure 搜索存储帐户中是否有可以备份的文件共享。 如果你最近添加了文件共享，但在列表中看不到它们，则请提供一些时间让文件共享出现。

    ![发现文件共享](./media/backup-afs/discovering-file-shares.png)

5. 在“文件共享”列表中选择一个或多个需要备份的文件共享，然后单击“确定”。

6. 选择文件共享后，"**备份**" 菜单将切换到 "**备份策略**"。 从此菜单中选择现有的备份策略，或创建新的备份策略，然后单击 "**启用备份**"。

    ![选择“备份策略”](./media/backup-afs/select-backup-policy.png)

建立备份策略以后，系统会在计划的时间拍摄文件共享的快照，并会将恢复点保留选定的一段时间。

## <a name="create-an-on-demand-backup"></a>创建按需备份

有时，你可能需要在备份策略中计划的时间之外生成备份快照或恢复点。 生成按需备份的常见原因是在配置备份策略之后。 可能需要等待数小时或数天才会拍摄快照，具体取决于备份策略中的计划。 若要在该备份策略发挥作用之前对数据进行保护，可以启动按需备份。 在对文件共享进行计划的更改之前，通常需要创建按需备份。

### <a name="to-create-an-on-demand-backup"></a>创建按需备份的步骤

1. 打开备份文件共享所用的恢复服务保管库，并单击 "**概述**" 边栏选项卡中 "**受保护的项**" 部分下的 "**备份项**"。

   ![单击 "备份项"](./media/backup-afs/backup-items.png)

2. 单击 "**备份项**" 后 **，将在**"**概述**" 边栏选项卡旁边显示一个新的边栏选项卡，如下所示：

   ![备份管理类型列表](./media/backup-afs/backup-management-types.png)

3. 从 "**备份管理类型**" 列表中，选择 " **Azure 存储（azure 文件）** "。 你将看到使用此保管库备份的所有文件共享和相应存储帐户的列表。

   ![Azure 存储（Azure 文件）备份项](./media/backup-afs/azure-files-backup-items.png)

4. 从 Azure 文件共享的列表中，选择所需的文件共享。 将显示 "**备份项**详细信息"。 在“备份项”菜单中单击“立即备份”。 由于这是按需备份作业，因此没有与恢复点关联的保留策略。

   ![单击 "立即备份"](./media/backup-afs/backup-now.png)

5. 此时会打开 "**立即备份**" 边栏选项卡。 指定要保留恢复点的最后一天。 对于按需备份，你可以最大程度地保留10年。

   ![选择保留日期](./media/backup-afs/retention-date.png)

6. 单击 **"确定"** 以确认按需备份作业运行。

7. 监视门户通知以跟踪备份作业运行完成。 可以在保管库仪表板 >“备份作业” > “进行中”监视作业进度。

## <a name="next-steps"></a>后续步骤

* 了解如何[还原 Azure 文件共享](restore-afs.md)

* 了解如何[管理 Azure 文件共享备份](manage-afs-backup.md)
