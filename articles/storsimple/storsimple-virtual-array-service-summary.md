---
title: "StorSimple Device Manager 的“服务摘要”边栏选项卡 - Microsoft Azure StorSimple 虚拟阵列 | Microsoft Docs"
description: "介绍 StorSimple Device Manager 的“服务摘要”边栏选项卡并说明如何使用它来监视 StorSimple 虚拟阵列的运行状况。"
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
translationtype: Human Translation
ms.sourcegitcommit: fd73672f97b4c16e49b2fad5e53042764f5793ca
ms.openlocfilehash: 813b4d430665e8df6f664c9c6a8db370f6ad7ac9

---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>使用已连接到 StorSimple 虚拟阵列的 StorSimple Device Manager 的“服务摘要”边栏选项卡
## <a name="overview"></a>概述
StorSimple Device Manager 的“服务摘要”边栏选项卡提供连接到用户服务的 StorSimple 虚拟阵列（也称为 StorSimple 本地虚拟设备或虚拟设备）的摘要视图，其中突出显示了需要系统管理员注意的内容。 本教程介绍“服务摘要”边栏选项卡，说明了其内容和功能，并描述了可从此边栏选项卡执行的任务。

![服务仪表板](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>管理命令和概要
在 StorSimple 摘要边栏选项卡中，可以看到各种选项，用于管理 StorSimple Device Manager 服务以及注册到该服务的虚拟阵列。 在该边栏选项卡的顶部和左侧可看到管理命令。

使用这些选项可以执行各种操作，例如添加共享或卷，或者监视运行在虚拟阵列上的各种作业。

概要区域捕获了一些重要的属性，例如资源组、位置，以及在其中创建了 StorSimple Device Manager 的订阅。

## <a name="storsimple-device-manager-service-summary"></a>StorSimple Device Manager 服务摘要
* “警报”磁贴提供对所有虚拟设备的所有活动警报的快照，并按警报的严重性进行分组。 单击该磁贴可打开“警报”边栏选项卡，在其中可单击单个警报以查看该警报的其他详细信息，包括任何建议的操作。 如果已解决此问题，还可清除警报。
* “容量”磁贴显示相对于所有虚拟设备可用的总存储，由所有虚拟设备预配的以及剩余的主存储。 “预配的”是指已准备且已分配供使用的存储量，“剩余”是指可以在所有虚拟设备上预配的剩余容量。 “剩余分层”容量是可以预配的可用容量（包括云），而“剩余本地”是附加到虚拟阵列的磁盘上剩余的容量。
* 在“使用情况”图表中，可以看到虚拟设备的相关指标。 可查看所有虚拟设备使用的主存储，以及在过去 7 天（默认时间段）虚拟设备使用的云存储。 使用图表右上角的“编辑”选项可选择其他时间范围。
* “设备”磁贴概述了 StorSimple Device Manager 中按设备状态分组的虚拟阵列的数目。 可以单击此磁贴打开“设备”列表边栏选项卡，然后再单击单个设备，查看特定于该设备的设备摘要。 也可以从给定的设备摘要边栏选项卡执行设备特定操作。 有关设备摘要边栏选项卡的详细信息，[请转到“设备摘要边栏选项卡”](storsimple-virtual-array-device-summary.md) 。

## <a name="view-the-activity-logs"></a>查看活动日志
若要查看在 StorSimple Device Manager 中执行的各种操作，请单击 StorSimple“服务摘要”边栏选项卡左侧的“活动日志”链接。 此时会转到“活动日志”边栏选项卡，其中会显示最近执行的操作的摘要。

![活动日志](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>后续步骤
了解如何[使用本地 Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。




<!--HONumber=Nov16_HO4-->


