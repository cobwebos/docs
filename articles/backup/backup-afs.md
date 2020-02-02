---
title: 在 Azure 门户中备份 Azure 文件共享
description: 了解如何使用 Azure 门户在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938255"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>在恢复服务保管库中备份 Azure 文件共享

本文介绍如何使用 Azure 门户备份[Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)。

本文将指导如何进行以下操作：

* 创建恢复服务保管库。
* 发现文件共享并配置备份。
* 运行按需备份作业来创建还原点。

## <a name="prerequisites"></a>必备组件

* 在与托管文件共享的存储帐户相同的区域中标识或创建[恢复服务保管库](#create-a-recovery-services-vault)。
* 确保文件共享存在于[受支持的存储帐户类型](#limitations-for-azure-file-share-backup-during-preview)之一中。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>预览期间对 Azure 文件共享备份的限制

Azure 文件共享备份处于预览状态。 常规用途 v1 和常规用途 v2 存储帐户中的 Azure 文件共享均受支持。 下面是备份 Azure 文件共享的限制：

* 在具有[区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)（ZRS）复制的存储帐户中，对 Azure 文件共享的备份支持当前仅限于[这些区域](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)。
* Azure 备份当前支持配置 Azure 文件共享的计划一次日常备份。
* 每天的计划备份数上限为 1。
* 每天的按需备份数上限为 4。
* 在存储帐户上使用[资源锁定](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)，防止意外删除恢复服务保管库中的备份。
* 请勿删除由 Azure 备份创建的快照。 删除快照可能导致恢复点丢失或还原失败。
* 请勿删除受 Azure 备份保护的文件共享。 当前解决方案删除在删除文件共享后由 Azure 备份创建的所有快照，因此所有还原点都将丢失。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>修改存储复制

默认情况下，保管库使用[异地冗余存储（GRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

* 如果保管库是你的主要备份机制，则建议你使用 GRS。
* 可以使用[本地冗余存储（LRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)作为低成本选项。

若要修改存储复制类型，请执行以下操作：

1. 在新保管库中，选择 "**设置**" 部分下的 "**属性**"。

1. 在 "**属性**" 页上的 "**备份配置**" 下，选择 "**更新**"。

1. 选择存储复制类型，然后选择 "**保存**"。

    ![更新备份配置](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 在设置保管库并包含备份项后，不能修改存储复制类型。 如果要执行此操作，则需要重新创建保管库。
>

## <a name="discover-file-shares-and-configure-backup"></a>发现文件共享并配置备份

1. 在[Azure 门户](https://portal.azure.com/)中，打开要用于备份文件共享的恢复服务保管库。

1. 在**恢复服务保管库**仪表板中，选择 " **+ 备份**"。

   ![恢复服务保管库](./media/backup-afs/recovery-services-vault.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 "**备份目标**" 中，将 **"工作负荷的运行位置"** 设置为 " **Azure**"。

    ![选择 Azure 文件共享作为备份目标](./media/backup-afs/backup-goal.png)

    b.保留“数据库类型”设置，即设置为“共享”。  在 **"要备份的内容"** 中，从下拉列表中选择 " **Azure 文件共享**"。

    c.  选择 "**备份**"，将 Azure 文件共享扩展注册到保管库中。

    ![选择 "备份"，将 Azure 文件共享与保管库相关联](./media/backup-afs/register-extension.png)

1. 选择 "**备份**" 后，将打开 "**备份**" 窗格，并提示你从已发现支持的存储帐户列表中选择一个存储帐户。 它们要么与此保管库关联，要么位于与保管库相同的区域中，但尚未关联到任何恢复服务保管库。

   ![选择存储帐户](./media/backup-afs/select-storage-account.png)

1. 从已发现的存储帐户列表中，选择一个帐户，然后选择 **"确定"** 。 Azure 将在存储帐户中搜索可备份的文件共享。 如果你最近添加了文件共享，但在列表中看不到它们，则留出一些时间让文件共享出现。

    ![发现文件共享](./media/backup-afs/discovering-file-shares.png)

1. 从 "**文件共享**" 列表中，选择要备份的一个或多个文件共享。 选择“确定”。

1. 选择文件共享后，"**备份**" 菜单将切换到 "**备份策略**"。 从此菜单中，选择现有的备份策略或创建新的备份策略。 然后选择 "**启用备份**"。

    ![选择“备份策略”](./media/backup-afs/select-backup-policy.png)

设置备份策略后，将在计划的时间获取文件共享快照。 还会在所选期间保留恢复点。

## <a name="create-an-on-demand-backup"></a>创建按需备份

有时，你可能需要在备份策略中计划的时间之外生成备份快照或恢复点。 生成按需备份的常见原因是在配置备份策略之后。 根据备份策略中的计划，可能需要数小时或数天时间才能拍摄快照。 若要在该备份策略发挥作用之前对数据进行保护，可以启动按需备份。 在对文件共享进行计划的更改之前，通常需要创建按需备份。

### <a name="create-a-backup-job-on-demand"></a>按需创建备份作业

1. 打开用于备份文件共享的恢复服务保管库。 在 "**概述**" 窗格的 "**受保护的项**" 部分下，选择 "**备份项**"。

   ![选择备份项](./media/backup-afs/backup-items.png)

1. 选择**备份项**后，"**概述**" 窗格旁边会显示一个新窗格，其中列出了所有**备份管理类型**。

   ![备份管理类型列表](./media/backup-afs/backup-management-types.png)

1. 从 "**备份管理类型**" 列表中，选择 " **Azure 存储（azure 文件）** "。 你将看到使用此保管库备份的所有文件共享和相应存储帐户的列表。

   ![Azure 存储（Azure 文件）备份项](./media/backup-afs/azure-files-backup-items.png)

1. 从 Azure 文件共享列表中，选择所需的文件共享。 将显示 "**备份项**详细信息"。 在 "**备份项**" 菜单上，选择 "**立即备份**"。 由于此备份作业是按需备份作业，因此没有与恢复点关联的保留策略。

   ![选择 "立即备份"](./media/backup-afs/backup-now.png)

1. 此时将打开 "**立即备份**" 窗格。 指定要保留恢复点的最后一天。 对于按需备份，你可以最大程度地保留10年。

   ![选择保留日期](./media/backup-afs/retention-date.png)

1. 选择 **"确定"** 以确认运行的按需备份作业。

1. 监视门户通知以跟踪备份作业运行完成。 可以在保管库仪表板中监视作业进度。  > **正在进行的** **备份作业**。

## <a name="next-steps"></a>后续步骤

了解如何操作：
* [还原 Azure 文件共享](restore-afs.md)
* [管理 Azure 文件共享备份](manage-afs-backup.md)
