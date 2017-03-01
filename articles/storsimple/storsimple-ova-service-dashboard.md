---
title: "StorSimple Manager 服务仪表板 - Virtual Array | Microsoft Docs"
description: "介绍 StorSimple Manager 服务仪表板并说明如何使用它来监视 StorSimple Virtual Array 的运行状况。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 36ac6eb4-a616-4bb1-8163-6862ac33da63
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 58aa9508e28a00efe69d0a9cbc8adb6837d085c4


---
# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>为 StorSimple Virtual Array 使用 StorSimple Manager 服务仪表板
## <a name="overview"></a>概述
“StorSimple Manager 服务仪表板”页面提供连接到 StorSimple Manager 服务的 StorSimple Virtual Array（也称为 StorSimple 本地虚拟设备或虚拟设备）的摘要视图，其中突出显示了需要系统管理员注意的内容。 本教程中介绍了“仪表板”页面、仪表板内容和函数，并且说明了可从此页执行的任务。

![服务仪表板](./media/storsimple-ova-service-dashboard/dashboard1.png)

StorSimple Manager 服务仪表板显示以下信息：

* 在页面顶部的“图表”区域，可以看到虚拟设备的相关指标。 可查看所有虚拟设备使用的主存储，以及在一段时间内虚拟设备使用的云存储。 使用图表右上角的控件可指定相对或绝对使用情况，并可查看 1 周、1 个月、3 个月或 1 年的时间刻度。 使用刷新控件![刷新控件](./media/storsimple-ova-service-dashboard/refresh-control.png)可刷新图表。
* **使用情况概述**显示相对于所有虚拟设备可用的总存储，由所有虚拟设备预配和使用的主存储。 **已预配**指已准备并已分配以供使用的存储空间量，**已用**指由连接到虚拟设备的发起程序所查看的共享或卷的使用情况，**最大容量**显示所有虚拟设备的最大容量。
* **警报**区提供对所有虚拟设备的所有活动警报的快照，并按警报的严重性进行分组。 单击严重性级别可打开“警报”页面，确定作用域以显示这些警报。 在“警报”页上，可单击单个警报以查看该警报的其他详细信息，包括任何建议的操作。 如果问题已得到解决，还可以清除警报。
* **作业**区域提供连接到服务的所有虚拟设备的最近作业的快照。 有可用于查看当前正在进行的作业和过去 24 小时内成功或失败的作业的链接。 
* 页面右侧的“速览”区域提供了服务状态、连接到服务的虚拟设备总数、服务位置，以及与服务相关联的订阅的详细信息等有用信息。 此外，还有操作日志的链接。 单击链接可查看所有已完成的 StorSimple Manager 服务操作的列表。 

可使用“StorSimple Manager 服务仪表板”页启动以下任务：

* 获取服务注册密钥。
* 查看操作日志。

## <a name="get-the-service-registration-key"></a>获取服务注册密钥
服务注册密钥用于向 StorSimple Manager 服务注册 StorSimple 虚拟设备，使设备出现在 Azure 经典门户中便于进一步的管理操作。 在第一个虚拟设备上创建密钥，并与其他的虚拟设备共享此密钥。 

单击“注册密钥”（位于页面底部）可打开“服务注册密钥”对话框，在此可将当前服务注册密钥复制到剪贴板或重新生成服务注册密钥。

![注册密钥](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

重新生成密钥不会影响之前注册的虚拟设备：它只会影响在重新生成密钥后注册到服务的虚拟设备。

有关获取服务注册密钥的详细信息，请转到[获取服务注册密钥](storsimple-ova-manage-service.md#get-the-service-registration-key)。

## <a name="view-the-operations-logs"></a>查看操作日志
可通过单击仪表板的“速览”窗格中提供的操作日志链接来查看操作日志。 这将转到“管理服务”页面，在此可筛选和查看特定于 StorSimple Manager 服务的日志。

![操作日志](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>后续步骤
了解如何[使用本地 Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。




<!--HONumber=Nov16_HO3-->


