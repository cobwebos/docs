---
title: 查看和管理 StorSimple 虚拟阵列作业 | Microsoft Docs
description: 介绍 StorSimple Device Manager 服务“作业”页，以及如何使用它跟踪 StorSimple 虚拟阵列的最近和当前作业。
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: 3fd1c262a8ce94d8e98f2b066a8028d974b15b1d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
ms.locfileid: "23109675"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>使用 StorSimple Device Manager 服务查看 StorSimple 虚拟阵列的作业
## <a name="overview"></a>概述
“作业”边栏选项卡提供了一个中央门户，用于查看和管理在连接到 StorSimple Device Manager 服务的虚拟阵列上启动的作业。 可以针对多个虚拟设备查看正在运行的、已完成的、失败的作业。 结果以表格形式呈现。

![“作业”边栏选项卡](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

可以通过筛选下述字段，快速查找感兴趣的作业：

* **时间范围** - 可以根据日期和时间范围筛选作业。
* **设备** - 可以在连接到服务的特定设备上启动作业。 然后，根据以下属性将筛选出的作业制成表格：
  
  * **名称** - 作业名称可以是“全部”、“备份”、“克隆”、“故障转移”、“下载更新”或“安装更新”。
  * **状态** - 作业可以是“全部”、“正在进行”、“成功”或“失败”或“已取消”。
  * **实体** - 作业可以与卷、共享或设备相关联。
  * **设备** - 在其上启动作业的设备的名称。
  * **启动时间** - 启动作业的时间。
  * **持续时间** - 运行作业的持续时间。
* **状态** - 可搜索全部、正在运行的、已完成的或失败的作业。
* **作业类型** - 作业类型可以是“全部”、“备份”、“还原”、“故障转移”、“下载更新”或“安装更新”。

作业列表每 30 秒刷新一次。

## <a name="view-job-details"></a>查看作业详细信息
执行以下步骤，查看任意作业的详细信息。

#### <a name="to-view-job-details"></a>查看作业详细信息
1. 在“作业”边栏选项卡上，通过适当的筛选器运行查询，显示感兴趣的作业。 可搜索已完成或正在运行的作业。
2. 从作业的表格式列表搜索某个作业。
   
    ![“作业”边栏选项卡](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. 在页面底部单击“详细信息”。
4. 在“详细信息”对话框中，可查看状态、详细信息和时间统计信息。 下图显示“备份作业详细信息”对话框的示例。
   
    ![作业详细信息](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>在虚拟机监控程序中暂停虚拟机时的作业失败
当作业正在 StorSimple 虚拟阵列上运行且设备（在虚拟机监控程序中预配的虚拟机）暂停超过 15 分钟时，作业会失败。 这是由于 StorSimple 虚拟阵列时间与 Microsoft Azure 时间不同步。 

会看到以下错误：“设备时间与 Microsoft Azure 时间不同步已超过 15 分钟。 确保虚拟机监控程序和设备时间与 NTP 服务器同步。 验证是否没有连接问题。 若要解决连接问题，请从虚拟设备的本地 Web UI 运行诊断测试。”

这些失败适用于备份、还原、更新和故障转移作业。 如果在 Hyper-V 中预配了虚拟机，则虚拟机最终将与虚拟机监控程序同步时间。 一旦同步后，可重新启动作业。

## <a name="next-steps"></a>后续步骤
[了解如何使用本地 Web UI 管理 StorSimple 虚拟阵列](storsimple-ova-web-ui-admin.md)。

