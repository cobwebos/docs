---
title: "StorSimple Virtual Array 服务摘要边栏选项卡 |Microsoft 文档"
description: "介绍 StorSimple 设备管理器的服务摘要边栏选项卡并说明如何使用它来监视 StorSimple 虚拟阵列的运行状况。"
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
ms.openlocfilehash: 284e404c44505a98d9e0ed5abe87cd945415af56
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>使用服务摘要边栏选项卡 StorSimple 设备管理器连接到 StorSimple Virtual Array
## <a name="overview"></a>概述
服务摘要边栏选项卡为 StorSimple 设备管理器提供 StorSimple 虚拟数组 （也称为 StorSimple 本地虚拟设备或虚拟设备） 连接到你的服务，突出显示那些需要系统管理员注意的摘要的视图。 本教程介绍服务摘要边栏选项卡，说明的内容和函数，并描述可在此边栏选项卡中执行的任务。

![服务仪表板](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>管理命令和 essentials
在 StorSimple 摘要边栏选项卡，你看到了用于管理你的 StorSimple 设备管理器服务，以及注册到此服务的虚拟阵列选项。 你的边栏选项卡和左侧顶部看到管理命令。

使用这些选项以执行各种操作如 add 共享或卷，或在虚拟阵列上运行的各种作业的监视器。

Essentials 区域捕获一些重要的属性，如，资源组、 位置和在其中创建 StorSimple 设备管理器的订阅。

## <a name="storsimple-device-manager-service-summary"></a>StorSimple 设备管理器服务摘要
* **警报**磁贴提供分组的警报严重性的所有虚拟的设备上的所有活动警报的快照。 单击该磁贴将打开**警报**边栏选项卡，其中你可以单击单个警报以查看有关该警报的详细信息包括任何建议的操作。 如果问题已得到解决，你也可以清除警报。
* **容量**磁贴显示显示所有的虚拟设备的是相对于可用总存储的所有虚拟设备的预配和剩余的主存储。 **设置**准备并分配供使用的存储量是指**剩余**指可以在所有虚拟设备设置的剩余容量。 **剩余分层**容量是可以包括云，预配的可用容量时**剩余本地**是附加到虚拟数组的磁盘上剩余的容量。
* 在**用法**图表，您可以看到相关指标虚拟设备。 你可以查看所有的虚拟设备上使用的主存储，以及在过去 7 天，默认时间段内使用虚拟设备的云存储。 使用**编辑**中的选项的右上角的图表选择不同的时间范围。
* **设备**磁贴提供虚拟阵列按设备状态分组在 StorSimple 设备 Manager 数的摘要。 单击此磁贴以打开**设备**列表边栏选项卡，然后单击若要深入了解特定于设备的设备摘要的单个设备。 你还可以从给定的设备摘要边栏选项卡执行设备特定操作。 有关设备摘要边栏选项卡的详细信息，请转到[设备摘要边栏选项卡](storsimple-virtual-array-device-summary.md)。

## <a name="view-the-activity-logs"></a>查看活动日志
若要查看执行你 StorSimple 设备管理器内的各种操作，请单击**活动日志**你 StorSimple 服务摘要边栏选项卡左侧的链接。 将进入**活动日志**边栏选项卡，其中你可以看到最近执行的操作的摘要。

![活动日志](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>后续步骤
了解如何[使用的本地 web UI 来管理你的 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

