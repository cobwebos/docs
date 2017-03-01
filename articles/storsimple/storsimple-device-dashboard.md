---
title: "使用 StorSimple Manager 设备仪表板 | Microsoft Docs"
description: "介绍 StorSimple Manager 服务设备仪表板、如何用其查看存储指标和连接的发起程序，以及如何查找序列号和 IQN。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 6c213969-a385-461f-b698-78ef5b8a79cc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 29a9477c740acca2bd0e220c554fda9a6f515998


---
# <a name="use-the-storsimple-manager-device-dashboard"></a>使用 StorSimple Manager 设备仪表板
## <a name="overview"></a>概述
StorSimple Manager 设备仪表板提供特定 StorSimple 设备的概要信息，服务仪表板则不同，提供 Microsoft Azure StorSimple 解决方案中包括的所有设备的信息。

![“设备仪表板”页](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

仪表板包含以下信息：

* **图表区域** – 可以在仪表板顶部的图表区域查看相关的存储指标。 在此图表中，用户可以查看设备在一段时间内使用的总主存储（由主机写入设备的数据量）和总云存储的指标。
  
     在这种情况下，“主存储”是指由主机写入的总数据量，可以按卷类型细分为“主分层存储”（包含本地存储数据以及通过分层扩展到云的数据）和“主本地固定存储”（只包含本地存储数据）。 另一方面，*云存储*则用于衡量在云中存储的数据的总量， 其中包括分层数据和备份。 请注意，存储在云中的数据是删除了重复数据且经过压缩的，而存储在主存储中的数据则是未删除重复数据且未经过压缩的。 （两相比较即可确定压缩率。）不管是主存储还是云存储，显示的数据量都是基于配置的跟踪频率。 例如，如果选择的频率为一周一次，该图表会显示上周每一天的数据。
  
     可以将图表配置如下：
  
  * 若要查看一定时段内使用的云存储量，请选择“已使用的云存储”选项。 若要查看由主机写入的总存储，请选择“已使用的主分层存储”和“已使用的主本地固定存储”选项。 在图中，两个选项均已选中。因此，该图表会同时显示云存储和主存储的存储量。 请注意，在安装 Update 2 之前使用的主存储由“已使用的主分层存储”表示。
  * 使用图表右上角的下拉菜单可将时间段指定为 1 周、1 月、3 月或 1 年。 请注意，顶级图表每天仅刷新一次，因此反映的是上一天的总量。
    
    有关详细信息，请参阅[使用 StorSimple Manager 服务监视 StorSimple 设备](storsimple-monitor-device.md)。
* **使用概览** – 在“使用概览”区域，用户可以看到已使用的主存储量、预配的存储量，以及设备的最大存储容量。 将这些使用数据与最大可用存储量进行比较，即可大致得出是否需要获取额外的存储。 请注意，此概览每 15 分钟更新一次，由于更新频率的不同，其所显示的数据可能不同于上述图表区域中显示的数据（每日更新）。 有关详细信息，请参阅[使用 StorSimple Manager 服务监视 StorSimple 设备](storsimple-monitor-device.md)。
* **警报** –“警报”区域包含设备警报的概要情况。 将按严重性对警报分组，并在每个严重性级别对警报计数。 单击警报严重性会打开“警报”选项卡的受限视图，仅显示此设备在该严重性级别的警报。
* **作业** –“作业”区域显示最新作业活动的结果。 这样可确保用户了解系统是在正常运行，还是需要纠正。 若要查看最近完成的作业的详细信息，请单击“过去 24 小时内成功完成的作业”。
* 仪表板右侧的“速览”区域提供了各种有用的信息，例如设备型号、序列号、状态、说明、卷数。

也可从设备仪表板配置故障转移并查看已连接的发起程序。

可在此页执行以下常见任务：

* 查看已连接的发起程序
* 查找设备序列号
* 查找设备目标 IQN

## <a name="view-connected-initiators"></a>查看已连接的发起程序
单击设备仪表板“速览”区域提供的“查看已连接的发起程序”链接即可查看连接到设备的 iSCSI 发起程序。 此页以表格形式列出了已成功连接到设备的发起程序。 对于每个发起程序，可以看到以下内容：

* 已连接发起程序的 iSCSI 限定名称 (IQN)。
* 允许这个已连接发起程序的访问控制记录 (ACR) 的名称。
* 已连接发起程序的 IP 地址。
* 发起程序在存储设备上连接到的网络接口。 此类接口的范围为 DATA 0 到 DATA 5。
* 允许已连接的发起程序根据当前 ACR 配置访问的所有卷。

如果此列表中出现意外的发起程序，或者没有出现预期的发起程序，请查看 ACR 配置。 最多允许 512 个发起程序连接到设备。

## <a name="find-the-device-serial-number"></a>查找设备序列号
在设备上配置 Microsoft 多路径 I/O (MPIO) 时，可能需要设备序列号。 若要查找设备序列号，请执行以下步骤。

#### <a name="to-find-the-device-serial-number"></a>查找设备序列号
1. 导航到“设备” > “仪表板”。
2. 在仪表板的右窗格中，找到“速览”区域。
3. 向下滚动并找到序列号。

## <a name="find-the-device-target-iqn"></a>查找设备目标 IQN
在 StorSimple 设备上配置质询握手身份验证协议 (CHAP) 时，可能需要设备目标 IQN。 若要查找设备目标 IQN，请执行以下步骤。

### <a name="to-find-the-device-target-iqn"></a>查找设备目标 IQN
1. 导航到“设备” > “仪表板”。
2. 在仪表板的右窗格中，找到“速览”区域。
3. 向下滚动并找到目标 IQN。

## <a name="next-steps"></a>后续步骤
* 详细了解 [StorSimple Manager 服务仪表板](storsimple-service-dashboard.md)。
* 了解有关如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)的详细信息。




<!--HONumber=Nov16_HO3-->


