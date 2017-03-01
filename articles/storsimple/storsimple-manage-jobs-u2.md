---
title: "查看和管理 StorSimple 作业 | Microsoft Docs"
description: "介绍 StorSimple Manager 服务的“作业”页，以及如何使用它来跟踪最近的、当前的和计划的备份作业。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: cdd3e9e8-7ccd-40a3-8c07-0ab1338c0e59
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d1d1f4d581721994fe7417ae970ad70f9a7b3368


---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs-update-2"></a>使用 StorSimple Manager 服务查看和管理 StorSimple 作业 (Update 2)
[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>概述
“作业”页提供了一个中央门户，用于查看和管理在连接到 StorSimple Manager 服务的设备上启动的作业。 可以针对多个设备查看计划的、正在运行的、已完成的、已取消的和失败的作业。 结果将以表格形式呈现。 

![“作业”页](./media/storsimple-manage-jobs-u2/jobs.png)

可以通过筛选下述字段，快速查找感兴趣的作业：

* **状态** – 作业状态可以是正在运行、已完成、已取消、失败、正在取消或已完成但有错误。
* **开始时间和结束时间**– 可以根据日期和时间范围筛选作业。
* **类型** – 作业类型可以是备份、手动备份、还原、克隆、设备故障转移、创建本地固定卷、修改卷、更新、支持包或虚拟设备预配。
* **设备** – 可以在连接到服务的特定设备上启动作业。
  然后可以根据以下属性以表格方式列出所筛选的作业：
  
  * **类型** – 备份、手动备份、还原、克隆、设备故障转移、创建本地固定卷、修改卷、更新、支持包或虚拟设备预配。
  * **状态** – 正在运行、已完成、已取消、失败、正在取消或已完成但有错误。
  * **实体** – 作业可以与卷、备份策略或设备相关联。 例如，克隆作业与卷相关联，计划备份作业与备份策略相关联。 设备作业是在执行灾难恢复 (DR) 或还原操作后创建的。
  * **设备** – 在其上启动作业的设备的名称。
  * **启动时间** – 启动作业的时间。
  * **进度** – 正在运行的作业的完成百分比。 对于已完成的作业，此项应该始终是 100%。

作业列表每 30 秒刷新一次。

在此页面上，可以执行与作业相关的下述操作：

* 查看作业详细信息
* 取消作业

## <a name="view-job-details"></a>查看作业详细信息
执行以下步骤，查看任意作业的详细信息。

#### <a name="to-view-job-details"></a>查看作业详细信息
1. 在“作业”页上，通过适当的筛选器运行查询，显示感兴趣的作业。 可以搜索已完成的、正在运行的或已取消的作业。
2. 选择作业。
3. 在页面底部单击“详细信息”。
4. 在“备份作业详细信息”对话框中，可以查看状态、详细信息、时间统计信息和数据统计信息。
   
    ![“作业详细信息”页](./media/storsimple-manage-jobs-u2/JobDetails.png)

## <a name="cancel-a-job"></a>取消作业
执行下列步骤，取消正在运行的作业。

> [!NOTE]
> 无法取消某些作业（例如修改卷的卷类型，或者扩展卷）。
> 
> 

### <a name="to-cancel-a-job"></a>取消作业
1. 在“作业”页上，通过适当的筛选器运行查询，显示要取消的正在运行的作业。
2. 选择作业。
3. 在页面底部，单击“取消”。
4. 出现确认提示时，单击“是”。

现在已取消此作业。

## <a name="next-steps"></a>后续步骤
* 了解如何[管理 StorSimple 备份策略](storsimple-manage-backup-policies.md)。
* 了解如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。




<!--HONumber=Nov16_HO3-->


