---
title: StorSimple Snapshot Manager 备份作业 | Microsoft Docs
description: 介绍如何使用 StorSimple Snapshot Manager MMC 管理单元查看和管理已计划的、当前运行的和已完成的备份作业。
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 094b996cd3227903995c7a74ef14ed8c0561f59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933335"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>使用 StorSimple Snapshot Manager查看和管理备份作业

## <a name="overview"></a>概述
“范围”**** 窗格中的“作业”**** 节点，显示你发起的交互式或配置策略启动的“已计划”****、“最近 24 小时”**** 和“正在运行”**** 的备份任务。 

本教程介绍如何使用“**作业**”节点显示已计划的、最近的和当前正在运行的备份作业的相关信息。 （作业列表和相应信息将显示在 **"结果"** 窗格中。此外，您可以右键单击列出的作业，并查看列出可用操作的上下文菜单。

## <a name="view-scheduled-jobs"></a>查看已计划的作业
使用以下过程查看已计划的备份作业。

#### <a name="to-view-scheduled-jobs"></a>查看已计划的作业
1. 单击桌面图标启动 StorSimple Snapshot Manager。 
2. 在 **“作用域”** 窗格中，展开 **“作业”** 节点，然后单击 **“已计划”**。 此时会在 **“结果”** 窗格中显示以下信息：
   
   * **名称** – 已计划快照的名称
   * **下次运行** – 下一个已计划快照的日期和时间
   * **上次运行** – 最近一个已计划快照的日期和时间
     
     > [!NOTE]
     > 对于一次性快照，**“下次运行”** 和 **“上次运行”** 将会是相同的。
     
     ![已计划的备份作业](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. 要对特定的作业执行其他操作，请在“**结果**”窗格中右键单击作业名称，并从菜单选项中进行选择。

## <a name="view-recent-jobs"></a>查看最近的作业
使用以下过程查看备份和还原过去 24 小时内完成的作业。

#### <a name="to-view-recent-jobs"></a>查看最近的作业
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在 **“作用域”** 窗格中，展开 **“作业”** 节点，然后单击 **“过去 24 小时”**。 **“结果”** 窗格显示过去 24 小时的备份作业（最多显示 64 个作业）。 此时以下信息会出现在 **“结果”** 窗格中，具体取决于指定的 **“视图”** 选项：
   
   * **名称** – 已计划快照的名称。
   * **开始** – 快照的开始日期和时间。
   * **停止** – 快照的完成/终止日期和时间。
   * **占用** –**“开始”** 时间和 **“停止”** 时间之差。
   * **状态** – 最近完成的作业的状态。 **“成功”** 表示备份已成功创建。 **“失败”** 表示作业为成功运行。
   * **信息** – 失败原因。
   * **处理的字节数(MB)** – 卷组中已处理的数据量 (MB)。 
     
     ![过去 24 小时内运行的作业](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. 要对特定的作业执行其他操作，请在“**结果**”窗格中右键单击作业名称，并从菜单选项中进行选择。
   
    ![删除作业](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>查看当前正在运行的作业
使用以下过程查看当前正在运行的作业。

#### <a name="to-view-currently-running-jobs"></a>查看当前正在运行的作业
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在 **“作用域”** 窗格中，展开 **“作业”** 节点，然后单击 **“正在运行”**。 根据你所指定的 **“视图”** 选项，此时会在 **“结果”** 窗格中出现以下信息：
   
   * **名称** – 已计划快照的名称。
   * **开始** – 快照的开始日期和时间。
   * **检查点** – 当前的备份操作。
   * **状态** – 完成百分比。
   * **占用** – 备份开始后已过去的时间。 
   * **平均吞吐量 (MB)** – 已处理数据的总字节数与处理所用的总时间的比率 (MB)。
   * **处理的字节数 (MB)** – 处理的数据的总字节数（以 MB 为单位）。
   * **写入的字节数 (MB)** – 写入的数据的总字节数（以 MB 为单位）。 其中包含数据以及元数据，因此通常大于处理的字节数。
     
     ![当前正在运行的作业](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. 要对特定的作业执行其他操作，请在“**结果**”窗格中右键单击作业名称，并从菜单选项中进行选择。

## <a name="next-steps"></a>后续步骤
* 了解如何[使用 StorSimple Snapshot Manager 管理 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)。
* 了解如何[使用 StorSimple Snapshot Manager 管理备份目录](storsimple-snapshot-manager-manage-backup-catalog.md)。

