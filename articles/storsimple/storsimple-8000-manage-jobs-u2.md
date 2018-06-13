---
title: 查看和管理 StorSimple 8000 系列的作业 | Microsoft Docs
description: 介绍 StorSimple 设备管理器服务的“作业”边栏选项卡，以及如何使用它来跟踪最近的、当前的和计划的备份作业。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: bf8038b7171053b75eeb9aed88bff4246e65a8a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23108185"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>使用 StorSimple 设备管理器服务查看和管理作业（Update 3 和更高版本）

## <a name="overview"></a>概述
“作业”边栏选项卡提供了一个中央门户，用于查看和管理在连接到 StorSimple 设备管理器服务的设备上启动的作业。 可以针对多个设备查看计划的、正在运行的、已完成的、已取消的和失败的作业。 结果以表格形式呈现。

![“作业”边栏选项卡](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

可以通过筛选下述字段，快速查找感兴趣的作业：

* **状态** – 作业状态可以是正在进行、成功、已取消、失败、正在取消或成功但出错。
* **时间范围** - 可以根据日期和时间范围筛选作业。 范围可以是过去 1 小时、过去 24 小时、过去 7 天、过去 30 天、过去一年或自定义日期。
* **类型** – 作业类型可以是计划的备份、手动备份、还原备份、克隆卷、故障转移卷容器、创建本地固定卷、修改卷、安装更新、收集支持日志和创建云设备。
* **设备** – 可以在连接到服务的特定设备上启动作业。
  
然后可以根据以下属性以表格方式列出所筛选的作业：
  
* **名称** – 计划的备份、手动备份、还原备份、克隆卷、故障转移卷容器、创建本地固定卷、修改卷、安装更新、收集支持日志或创建云设备。
* **状态** – 正在运行、已完成、已取消、失败、正在取消或已完成但有错误。
* **实体** – 作业可以与卷、备份策略或设备相关联。 例如，克隆作业与卷相关联，计划备份作业与备份策略相关联。 设备作业是在执行灾难恢复 (DR) 或还原操作后创建的。
* **设备** – 在其上启动作业的设备的名称。
* **启动时间** - 启动作业的时间。
* **持续时间** – 完成作业所需的时间。

作业列表每 30 秒刷新一次。

在此页面上，可以执行与作业相关的下述操作：

* 查看作业详细信息
* 取消作业

## <a name="view-job-details"></a>查看作业详细信息
执行以下步骤，查看任意作业的详细信息。

#### <a name="to-view-job-details"></a>查看作业详细信息
1. 转到 StorSimple 设备管理器服务，单击“作业”。

2. 在“作业”边栏选项卡中，通过适当的筛选器运行查询，显示所需的作业。 可以搜索已完成的、正在运行的或已取消的作业。

    ![“作业”边栏选项卡](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. 选择并单击一个作业。

    ![“作业”边栏选项卡](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. 在作业详细信息边栏选项卡中，可以查看状态、详细信息、时间统计信息和数据统计信息。
   
    ![作业详细信息](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>取消作业
执行下列步骤，取消正在运行的作业。

> [!NOTE]
> 无法取消某些作业（例如修改卷的卷类型，或者扩展卷）。


### <a name="to-cancel-a-job"></a>取消作业
1. 在“作业”页上，通过适当的筛选器运行查询，显示要取消的正在运行的作业。 选择作业。

2. 右键单击选定的作业调用上下文菜单，单击“取消”。

    ![作业详细信息](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. 出现确认提示时，单击“是”。 现在已取消此作业。

## <a name="next-steps"></a>后续步骤
* 了解如何[管理 StorSimple 备份策略](storsimple-8000-manage-backup-policies-u2.md)。
* 了解如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。

