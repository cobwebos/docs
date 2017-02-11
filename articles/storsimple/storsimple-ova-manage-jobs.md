---
title: "查看和管理 StorSimple 虚拟阵列作业 | Microsoft Docs"
description: "介绍 StorSimple Manager 服务角色“作业”页，以及如何使用它跟踪 StorSimple 虚拟阵列的最近和当前作业。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 789f7e04-7b3f-456d-be69-37ea48446e9b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d6a0d156e90bfbf16712f9093284e34edd493484


---
# <a name="use-the-storsimple-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>使用 StorSimple Manager 服务查看 StorSimple 虚拟阵列的作业
## <a name="overview"></a>概述
“作业”页提供了一个中央门户，用于查看和管理在连接到 StorSimple Manager 服务的虚拟阵列（也称为本地虚拟设备）上启动的作业。 可以针对多个虚拟设备查看正在运行的、已完成的、失败的作业。 结果将以表格形式呈现。 

![“作业”页](./media/storsimple-ova-manage-jobs/ovajobs1.png)

可以通过筛选下述字段，快速查找感兴趣的作业：

* **状态** - 可搜索全部、正在运行的、已完成的或失败的作业。
* **开始时间和结束时间** - 可以根据日期和时间范围筛选作业。
* **类型** - 作业类型可以是全部、备份、还原、故障转移、下载更改或安装更新。
* **设备** - 可以在连接到服务的特定设备上启动作业。 然后可以根据以下属性以表格方式列出所筛选的作业：
  
  * **类型** - 作业类型可以是全部、备份、还原、故障转移、下载更改或安装更新。
  * **状态** - 作业可以是全部、正在运行、已完成或失败。
  * **实体** - 作业可以与卷、共享或设备相关联。 
  * **设备** - 在其上启动作业的设备的名称。
  * **启动时间** - 启动作业的时间。
  * **进度** - 正在运行的作业的完成百分比。 对于已完成的作业，此项应该始终是 100%。

作业列表每 30 秒刷新一次。

## <a name="view-job-details"></a>查看作业详细信息
执行以下步骤，查看任意作业的详细信息。

#### <a name="to-view-job-details"></a>查看作业详细信息
1. 在“作业”页上，通过适当的筛选器运行查询，显示感兴趣的作业。 可搜索已完成或正在运行的作业。
2. 从作业的表格式列表搜索某个作业。
3. 在页面底部单击“详细信息”。
4. 在“详细信息”对话框中，可查看状态、详细信息和时间统计数据。 下图显示“备份作业详细信息”对话框的示例。
   
    ![“作业详细信息”页](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>在虚拟机监控程序中暂停虚拟机时的作业失败
当作业在 StorSimple 虚拟阵列上正在进行并且设备（在虚拟机监控程序中预配的虚拟机）暂停超过 15 分钟时，作业将失败。 这是由于 StorSimple 虚拟阵列时间与 Microsoft Azure 时间不同步。 以下屏幕截图显示了还原作业失败的示例。

![还原作业失败](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

这些失败将适用于备份、还原、更新和故障转移作业。 如果在 Hyper-V 中预配了虚拟机，计算机最终将与虚拟机监控程序同步时间。 一旦同步后，可重新启动作业。 

## <a name="next-steps"></a>后续步骤
[了解如何使用本地 Web UI 管理 StorSimple 虚拟阵列](storsimple-ova-web-ui-admin.md)。




<!--HONumber=Nov16_HO3-->


