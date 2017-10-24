---
title: "StorSimple Snapshot Manager 备份作业 | Microsoft Docs"
description: "介绍如何使用 StorSimple Snapshot Manager MMC 管理单元查看和管理已计划的、当前运行的和已完成的备份作业。"
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 03e306b62250f2bb033cc14e856a59760b5406c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>使用 StorSimple Snapshot Manager查看和管理备份作业

## <a name="overview"></a>概述
“范围”窗格中的“作业”节点，显示你发起的交互式或配置策略启动的“已计划”、“最近 24 小时”和“正在运行”的备份任务。 

本教程介绍如何使用“**作业**”节点显示已计划的、最近的和当前正在运行的备份作业的相关信息。 （作业列表和相应的信息会显示在“**结果**”窗格中。）此外，可以右键单击列出的作业，并查看上下文菜单，其中列出了可用的操作。

## <a name="view-scheduled-jobs"></a>查看已计划的作业
使用以下过程查看已计划的备份作业。

#### <a name="to-view-scheduled-jobs"></a>查看已计划的作业
1. 单击桌面图标启动 StorSimple Snapshot Manager。 
2. 在“**作用域**”窗格中，展开“**作业**”节点，并单击“**已计划**”。 以下信息会显示在“**结果**”窗格中：
   
   * **名称** – 已计划快照的名称
   * **下次运行时间** – 下一个已计划快照的日期和时间
   * **上次运行时间** – 最近一个已计划快照的日期和时间
     
     > [!NOTE]
     > 对于一次性快照，“**下次运行时间**”和“**上次运行时间**”将是相同的。
     
     ![已计划的备份作业](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. 要对特定的作业执行其他操作，请在“**结果**”窗格中右键单击作业名称，并从菜单选项中进行选择。

## <a name="view-recent-jobs"></a>查看最近的作业
使用以下过程查看备份和还原过去 24 小时内完成的作业。

#### <a name="to-view-recent-jobs"></a>查看最近的作业
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，展开“**作业**”节点，并单击“**过去 24 小时**”。 “**结果**”窗格中显示过去 24 小时内的备份作业（最多显示 64 个作业）。 以下信息会显示在“**结果**”窗格中，具体将取决于指定的“**查看**”选项：
   
   * **名称** – 已计划快照的名称。
   * **开始时间** – 快照开始的日期和时间。
   * **停止时间** – 快照完成或终止的日期和时间。
   * **已用时间** –“**开始时间**”和“**停止时间**”之间的时间量。
   * **状态** – 最近已完成作业的状态。 **成功** 指示备份已成功创建。 **失败** 指示作业未成功运行。
   * **信息** – 失败的原因。
   * **处理的字节数 (MB)** – 已处理的卷组中的数据量（以 MB 为单位）。 
     
     ![过去 24 小时内运行的作业](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. 要对特定的作业执行其他操作，请在“**结果**”窗格中右键单击作业名称，并从菜单选项中进行选择。
   
    ![删除作业](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>查看当前正在运行的作业
使用以下过程查看当前正在运行的作业。

#### <a name="to-view-currently-running-jobs"></a>查看当前正在运行的作业
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，展开“**作业**”节点，并单击“**正在运行**”。 根据你指定的“**查看**”选项，以下信息会显示在“**结果**”窗格中：
   
   * **名称** – 已计划快照的名称。
   * **开始时间** – 快照开始的日期和时间。
   * **检查点** – 当前备份操作。
   * **状态** – 完成百分比。
   * **已用时间** – 自备份开始起已经过去的时间量。 
   * **平均吞吐量 (MB)** – 已处理数据的总字节数与处理所用的总时间的比率 (MB)。
   * **处理的字节数 (MB)** – 处理的数据的总字节数（以 MB 为单位）。
   * **写入的字节数 (MB)** – 写入的数据的总字节数（以 MB 为单位）。 其中包含数据以及元数据，因此通常大于处理的字节数。
     
     ![当前正在运行的作业](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. 要对特定的作业执行其他操作，请在“**结果**”窗格中右键单击作业名称，并从菜单选项中进行选择。

## <a name="next-steps"></a>后续步骤
* 了解如何[使用 StorSimple Snapshot Manager 管理 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)。
* 了解如何[使用 StorSimple Snapshot Manager 管理备份目录](storsimple-snapshot-manager-manage-backup-catalog.md)。

