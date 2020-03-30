---
title: 在 Azure 门户中备份 Azure 文件共享
description: 了解如何使用 Azure 门户在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938255"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>在恢复服务保管库中备份 Azure 文件共享

本文介绍如何使用 Azure 门户备份[Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)。

本文将指导如何进行以下操作：

* 创建恢复服务保管库。
* 发现文件共享并配置备份。
* 运行按需备份作业以创建还原点。

## <a name="prerequisites"></a>先决条件

* 标识或与承载文件共享的存储帐户位于同一区域中的[恢复服务保管库](#create-a-recovery-services-vault)。
* 确保文件共享存在于[受支持的存储帐户类型](#limitations-for-azure-file-share-backup-during-preview)之一中。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>预览期间对 Azure 文件共享备份的限制

Azure 文件共享备份处于预览状态。 常规用途 v1 和常规用途 v2 存储帐户中的 Azure 文件共享均受支持。 以下是备份 Azure 文件共享的限制：

* 支持在具有[区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)（ZRS） 复制的存储帐户中备份 Azure 文件共享，目前仅限于[这些区域](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)。
* Azure 备份当前支持配置 Azure 文件共享的每日计划备份一次。
* 每天的计划备份数上限为 1。
* 每天的按需备份数上限为 4。
* 在存储帐户上使用[资源锁定](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)，防止意外删除恢复服务保管库中的备份。
* 不要删除 Azure 备份创建的快照。 删除快照可能会导致恢复点丢失或还原失败。
* 不要删除受 Azure 备份保护的文件共享。 当前解决方案删除 Azure 备份在删除文件共享后获取的所有快照，因此所有还原点都将丢失。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>修复存储复制

默认情况下，保管库使用[异地冗余存储 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

* 如果保管库是您的主要备份机制，我们建议您使用 GRS。
* 您可以将[本地冗余存储 （LRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)用作低成本选项。

要修改存储复制类型，

1. 在新保管库中，在 **"设置"** 部分下选择 **"属性**"。

1. 在 **"属性"** 页上，在 **"备份配置**"下，选择 **"更新**"。

1. 选择存储复制类型，然后选择 **"保存**"。

    ![更新备份配置](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 在保管库经过设置并且包含备份项之后，无法修改存储复制类型。 如果要执行此操作，则需要重新创建保管库。
>

## <a name="discover-file-shares-and-configure-backup"></a>发现文件共享并配置备份

1. 在[Azure 门户](https://portal.azure.com/)中，打开要用于备份文件共享的恢复服务保管库。

1. 在**恢复服务保管库**仪表板中，选择 **+备份**。

   ![恢复服务保管库](./media/backup-afs/recovery-services-vault.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“备份目标”中，将“工作负荷的运行位置”设置为“Azure”。************

    ![选择 Azure 文件共享作为备份目标](./media/backup-afs/backup-goal.png)

    b.保留“数据库类型”设置，即设置为“共享”。  在"**要备份什么？"** 中，从下拉列表中选择**Azure 文件共享**。

    c.  选择 **"备份"** 以在保管库中注册 Azure 文件共享扩展名。

    ![选择"备份"将 Azure 文件共享与保管库关联](./media/backup-afs/register-extension.png)

1. 选择 **"备份**"后，将打开 **"备份"** 窗格，并提示您从发现的支持存储帐户列表中选择存储帐户。 它们要么与此保管库关联，要么与保管库位于同一区域中，但尚未与任何恢复服务保管库关联。

   ![选择存储帐户](./media/backup-afs/select-storage-account.png)

1. 从已发现的存储帐户列表中，选择一个帐户，然后选择 **"确定**"。 Azure 搜索存储帐户中可以备份的文件共享。 如果您最近添加了文件共享，但列表中看不到它们，请留出一些时间来显示文件共享。

    ![发现文件共享](./media/backup-afs/discovering-file-shares.png)

1. 从 **"文件共享"** 列表中，选择要备份的一个或多个文件共享。 选择“确定”。

1. 选择文件共享后，"**备份"** 菜单将切换到 **"备份"策略**。 在此菜单中，选择现有备份策略或创建新备份策略。 然后选择 **"启用备份**"。

    ![选择“备份策略”](./media/backup-afs/select-backup-policy.png)

设置备份策略后，将在计划的时间拍摄文件共享的快照。 恢复点也会保留在所选时间段内。

## <a name="create-an-on-demand-backup"></a>创建按需备份

有时，您可能希望在备份策略中计划的时间之外生成备份快照或恢复点。 生成按需备份的常见原因是在配置备份策略后。 根据备份策略中的计划，拍摄快照可能需要数小时或数天。 若要在该备份策略发挥作用之前对数据进行保护，可以启动按需备份。 在计划更改文件共享之前，通常需要创建按需备份。

### <a name="create-a-backup-job-on-demand"></a>按需创建备份作业

1. 打开用于备份文件共享的恢复服务保管库。 在 **"概述"** 窗格中，在 **"受保护项目**"部分下选择 **"备份项目**"。

   ![选择备份项目](./media/backup-afs/backup-items.png)

1. 选择 **"备份项目**"后，"**概述"** 窗格旁边将显示一个列出所有**备份管理类型**的新窗格。

   ![备份管理类型列表](./media/backup-afs/backup-management-types.png)

1. 从 **"备份管理类型"** 列表中，选择**Azure 存储（Azure 文件）。** 您将看到所有文件共享的列表以及使用此保管库备份的相应存储帐户。

   ![Azure 存储（Azure 文件）备份项](./media/backup-afs/azure-files-backup-items.png)

1. 从 Azure 文件共享列表中，选择所需的文件共享。 将显示 **"备份项目**"详细信息。 在 **"备份项目"** 菜单上，立即选择 **"备份**"。 由于此备份作业是按需的，因此没有与恢复点关联的保留策略。

   ![立即选择备份](./media/backup-afs/backup-now.png)

1. 将打开 **"立即备份"** 窗格。 指定要保留恢复点的最后一天。 按需备份的最大保留期最长为 10 年。

   ![选择保留日期](./media/backup-afs/retention-date.png)

1. 选择 **"确定"** 以确认运行的按需备份作业。

1. 监视门户通知，以跟踪备份作业运行完成情况。 您可以在保管库仪表板中监视作业进度。 选择**正在进行的****备份作业** > 。

## <a name="next-steps"></a>后续步骤

了解如何操作：
* [还原 Azure 文件共享](restore-afs.md)
* [管理 Azure 文件共享备份](manage-afs-backup.md)
