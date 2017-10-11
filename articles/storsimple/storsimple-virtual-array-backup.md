---
title: "Microsoft Azure StorSimple 虚拟阵列备份教程 | Microsoft 文档"
description: "介绍了如何备份 StorSimple Virtual Array 共享和卷。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c926f0c80ce56cac3106ad97ec3ec2e18a8e2cc6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>备份 StorSimple 虚拟阵列上的共享或卷

## <a name="overview"></a>概述

StorSimple Virtual Array 是一种混合云存储本地虚拟设备，可以配置为文件服务器或 iSCSI 服务器。 虚拟阵列允许用户为设备上的所有共享或卷创建计划备份和手动备份。 当配置为文件服务器时，它还允许项目级恢复。 本教程介绍如何创建计划备份和手动备份，以及如何执行项目级恢复以在虚拟阵列上还原删除的文件。

本教程仅适用于 StorSimple 虚拟阵列。 有关 8000 系列的信息，请转到[创建 8000 系列设备的备份](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>备份共享和卷

备份可提供时间点保护，并可提高可恢复性，同时最大限度地降低共享和卷的恢复时间。 可以采用两种方式备份 StorSimple 设备上的共享或卷：**按计划**或**手动**。 以下部分介绍上述每种方法。

## <a name="change-the-backup-start-time"></a>更改备份开始时间

> [!NOTE]
> 在此发行版中，一个默认策略会创建计划的备份，该策略每天在指定的时间运行，并且会备份设备上的所有共享或卷。 目前不能为计划的备份创建自定义策略。


StorSimple 虚拟阵列具有一个默认备份策略，该策略从每天的指定时间 (22:30) 开始，每天将设备上的所有共享或卷备份一次。 可以更改备份的开始时间，但无法更改频率和保留（这指定要保留的备份数）。 执行这些备份期间，整个虚拟设备将进行备份。 这可能会影响设备的性能，并会影响部署在设备上的工作负荷。 因此，我们建议计划在非高峰时间执行这些备份。

 若要更改默认备份开始时间，请在 [Azure 门户](https://portal.azure.com/)中执行以下步骤。

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>更改默认备份策略的开始时间

1. 转到“设备”。 将显示已向 StorSimple Device Manager 服务注册的设备列表。 
   
    ![导航到“设备”](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. 选择并单击设备。 将显示“设置”边栏选项卡。 转到“管理”>“备份策略”。
   
    ![选择设备](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. 在“备份策略”边栏选项卡中，默认开始时间为 22:30。 可以在设备时区中为每日计划指定新的开始时间。
   
    ![导航到“备份策略”](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. 单击“保存” 。

### <a name="take-a-manual-backup"></a>创建手动备份

除了计划的备份之外，还可以随时为设备数据创建手动（按需）备份。

#### <a name="to-create-a-manual-backup"></a>创建手动备份

1. 转到“设备”。 选择设备，并右键单击所选行最右端的“...”。 从上下文菜单中，选择“执行备份”。
   
    ![导航“执行备份”](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. 在“执行备份”边栏选项卡中，单击“执行备份”。 这会备份文件服务器上的所有共享或 iSCSI 服务器上的所有卷。 
   
    ![备份正在启动](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    按需备份开始，会看到备份作业已启动。
   
    ![备份正在启动](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    已成功完成作业后，会通知您再次。 然后，启动备份过程。
   
    ![备份作业已创建](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. 若要跟踪备份进度和查看作业详细信息，请单击“通知”。 这会你将带到“作业详细信息”。
   
     ![备份作业详细信息](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. 在备份完成后，请转到“管理”>“备份目录”。 会看到设备上所有共享（或卷）的云快照。
   
    ![已完成的备份](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>查看现有备份
若要查看现有备份，请在 Azure 门户中执行以下步骤。

#### <a name="to-view-existing-backups"></a>查看现有备份

1. 转到“设备”边栏选项卡。 选择并单击设备。 在“设置”边栏选项卡中，转到“管理”>“备份目录”。
   
    ![导航到“备份目录”](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. 指定要用于筛选的以下条件：
   
    - **时间范围** - 可以是“过去 1 小时”、“过去 24 小时”、“过去 7 天”、“过去 30 天”、“过去一年”和“自定义日期”。
    
    - **设备** - 从已向 StorSimple Device Manager 服务注册的文件服务器或 iSCSI 服务器的列表中选择。
   
    - **启动** - 可以是自动**计划**启动（由备份策略启动）或**手动**启动（由用户启动）。
   
    ![筛选备份](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. 单击“应用” 。 已筛选的备份列表会显示在“备份目录”边栏选项卡中。 请注意，在给定的时间只能显示 100 个备份元素。
   
    ![已更新的备份目录](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>后续步骤

详细了解如何[管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

