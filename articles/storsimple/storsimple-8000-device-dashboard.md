---
title: 使用 StorSimple 8000 系列设备摘要 | Microsoft Docs
description: 介绍 StorSimple 设备管理器服务设备摘要、如何用其查看存储指标和连接的发起程序，以及如何查找序列号和 IQN。
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 784d3ce9d8f926b00ac1c6fbf48a05c0b04f900a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23108255"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>使用 StorSimple 设备管理器服务中的设备摘要

## <a name="overview"></a>概述
StorSimple 设备摘要边栏选项卡提供特定 StorSimple 设备的概要信息，服务摘要边栏选项卡则不同，提供 Microsoft Azure StorSimple 解决方案中包括的所有设备的信息。

设备摘要边栏选项卡提供已注册到给定 StorSimple 设备管理器的 StorSimple 8000 系列设备的摘要视图，并突出显示了那些需要系统管理员注意的设备问题。 本教程介绍“设备摘要”边栏选项卡，说明了其内容和功能，并描述了可从此边栏选项卡执行的任务。

“设备摘要”边栏选项卡显示以下信息：

![设备摘要边栏选项卡](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>管理命令栏

在 StorSimple 设备边栏选项卡中，会看到用于管理 StorSimple 设备的选项。 在该边栏选项卡的顶部和左侧可看到管理命令。 使用这些选项可以添加共享或卷，也可以对设备进行更新或故障转移。

![管理命令栏](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>概要

“概要”区域捕获一些重要属性，例如状态、型号、目标 IQN 和软件版本。 

![设备概要](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>监视

* “警报”磁贴提供设备的所有活动警报的快照，这些警报按警报严重性分组。

    ![警报磁贴](./media/storsimple-8000-device-dashboard/device-summary4.png)

    单击该磁贴可打开“警报”边栏选项卡，并单击单个警报可查看有关该警报的其他详细信息，包括任何建议的操作。 如果已解决此问题，还可清除警报。

    ![单击警报磁贴](./media/storsimple-8000-device-dashboard/device-summary10.png)

* “状态和运行状况”磁贴提供设备的硬件组件运行状况洞察数据，包括设备状态。 设备状态可能是“脱机”、“联机”、“已停用”或“准备好设置”。

    ![状态和运行状况磁贴](./media/storsimple-8000-device-dashboard/device-summary5.png)

* “卷”磁贴提供设备中卷的数量的摘要，并按状态分组。

    ![卷磁贴](./media/storsimple-8000-device-dashboard/device-summary6.png)

    单击该磁贴可打开“卷”列表边栏选项卡，单击单个卷可查看或修改其属性。
    
    ![单击卷磁贴](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    有关详细信息，请参阅[如何管理卷](storsimple-8000-manage-volumes-u2.md)。

* 在“使用情况”图表中，可以查看在设备上已使用的主存储，以及在过去 7 天（默认时间段）内已使用的云存储。

     ![“使用情况”磁贴](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     若要选择其他时间范围，请使用图表右上角的“编辑”选项。

     ![编辑使用情况图表](./media/storsimple-8000-device-dashboard/device-summary12.png)

     在此图表中，用户可以查看设备在一段时间内使用的总主存储（由主机写入设备的数据量）和总云存储的指标。
  
     在此语境中，“主存储”是指由主机写入的总数据量，可以按卷类型细分：“主分层存储”包括本地存储数据以及分层到云中的数据。 “主本地固定存储”只包括本地存储数据。 另一方面，*云存储*则用于衡量在云中存储的数据的总量， 此存储包括分层数据和备份。 存储在云中的数据是删除了重复数据且经过压缩的，而存储在主存储中的数据则是未删除重复数据且未经过压缩的。 （两相比较即可确定压缩率。）不管是主存储还是云存储，显示的数据量都是基于配置的跟踪频率。 例如，如果选择的频率为一周一次，该图表会显示上周每一天的数据。

     若要查看一定时段内使用的云存储量，请选择“已使用的云存储”选项。 若要查看由主机写入的总存储，请选择“已使用的主分层存储”和“已使用的主本地固定存储”选项。 
     有关详细信息，请参阅[使用 StorSimple 设备管理器服务监视 StorSimple 设备](storsimple-monitor-device.md)。


* “容量”磁贴显示所预配的主存储，以及设备相对于可用于同一设备的总存储剩余的存储。 **已预配**是指已准备且已分配供使用的存储量，**剩余**是指可以在此设备上预配的剩余容量。 

    ![“使用情况”磁贴](./media/storsimple-8000-device-dashboard/device-summary8.png)

    单击此磁贴可查看如何跨分层卷和本地固定卷预配容量。 **剩余分层**容量是可以预配的可用容量（包括云），而**剩余本地**是附加到此设备的磁盘上剩余的容量。

    ![单击使用情况图表](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>后续步骤
* 详细了解 [StorSimple 服务摘要边栏选项卡](storsimple-8000-service-dashboard.md)。
* 了解有关如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)的详细信息。

