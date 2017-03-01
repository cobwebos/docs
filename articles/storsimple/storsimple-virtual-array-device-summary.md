---
title: "StorSimple 虚拟阵列设备摘要边栏选项卡 | Microsoft 文档"
description: "介绍 StorSimple Device Manager 的“设备摘要”边栏选项卡并说明如何使用它来监视 StorSimple 虚拟阵列的运行状况。"
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
translationtype: Human Translation
ms.sourcegitcommit: c78eda33a64d630ba18aba6a174db372eb41dde9
ms.openlocfilehash: 35413d597c3b6b1c7600241a78572b63f982d175

---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>使用已连接到 StorSimple 虚拟阵列的 StorSimple Device Manager 的“设备摘要”边栏选项卡

## <a name="overview"></a>概述

StorSimple Device Manager 设备边栏选项卡提供已注册到给定 StorSimple Device Manager 的 StorSimple 虚拟阵列的摘要视图，并突出显示了那些需要系统管理员注意的设备问题。 本教程介绍“设备摘要”边栏选项卡，说明了其内容和功能，并描述了可从此边栏选项卡执行的任务。

“设备摘要”边栏选项卡显示以下信息：

![设备仪表板](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>管理

在 StorSimple 设备边栏选项卡中，你将看到用于管理 StorSimple 设备的选项。 在该边栏选项卡的顶部和左侧可看到管理命令。 使用这些选项可以添加共享或卷，也可以对虚拟阵列进行更新或故障转移。

“概要”区域捕获一些重要属性（如状态、型号、软件版本），并提供指向阵列的 **Web UI** 的链接。 如果在内部网络上，则可以直接启动[本地 Web UI](storsimple-ova-web-ui-admin.md) 来管理虚拟阵列。

![设备概要](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>StorSimple 设备概要

* “警报”磁贴提供虚拟阵列的所有活动警报的快照，这些警报按警报严重性分组。 单击该磁贴可打开“警报”边栏选项卡，然后单击单个警报可查看有关该警报的其他详细信息，包括任何建议的操作。 如果已解决此问题，还可清除警报。

* “容量”磁贴显示所预配的主存储，以及虚拟设备相对于可用于同一虚拟设备的总存储剩余的存储空间。 **已预配**是指已准备且已分配供使用的存储量，**剩余**是指可以在此设备上预配的剩余容量。 **剩余分层**容量是可以预配的可用容量（包括云），而**剩余本地**是附加到此虚拟阵列的磁盘上剩余的容量。

* 在“使用情况”图表中，可以查看在虚拟阵列上已使用的主存储，以及在过去 7 天（默认时间段）内已使用的云存储。 使用图表右上角的“编辑”选项可选择其他时间范围。

* “共享”或“卷”磁贴提供设备中共享或卷的数量的摘要，并按状态分组。 单击该磁贴可打开“共享”或“卷”列表边栏选项卡，然后单击单个共享或卷可查看或修改其属性。 有关详细信息，请参阅如何[管理共享](storsimple-virtual-array-manage-shares.md)或[管理卷](storsimple-virtual-array-manage-volumes.md)。

## <a name="next-steps"></a>后续步骤
了解如何：
- [管理 StorSimple 虚拟阵列上的共享](storsimple-virtual-array-manage-shares.md)
    
- [管理 StorSimple 虚拟阵列上的卷](storsimple-virtual-array-manage-volumes.md)




<!--HONumber=Jan17_HO4-->


