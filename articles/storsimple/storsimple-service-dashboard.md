---
title: "StorSimple Manager 服务仪表板 | Microsoft Docs"
description: "介绍 StorSimple Manager 服务仪表板并说明如何使用它来监视 StorSimple 解决方案的运行状况。"
services: storsimple
documentationcenter: 
author: SharS
manager: carmonm
editor: 
ms.assetid: fb0f131d-d60b-45d7-ace2-56d0502e6627
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2016
ms.author: v-sharos
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 596431b7279b753ca4da838eb028cdde2022ce02
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# <a name="use-the-storsimple-manager-service-dashboard"></a>使用 StorSimple Manager 服务仪表板
## <a name="overview"></a>概述
“StorSimple Manager 服务仪表板”页提供连接到 StorSimple Manager 服务的所有设备的摘要视图，突出显示那些需要系统管理员注意的内容。 本教程对“仪表板”页进行介绍，说明了仪表板内容和功能，并描述了可从此页执行的任务。

![服务仪表板](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

StorSimple Manager 服务仪表板显示以下信息：

* 在“**图表**”区域中，可以看到设备的相关指标图。 可查看所有设备使用的主存储（本地固定和分层），以及在一段时间内设备使用的云存储。 使用图表右上角的控件可指定 1 周、1 个月、3 个月或 1 年的时间刻度。
* “**使用情况概述**”显示相对于所有设备可用的总存储而言，由所有设备预配和使用的主存储。 “**预配**”指准备和分配的供使用的存储空间量，“**已用**”指连接到设备的发起程序中现实的卷的使用情况。
* “**警报**”区提供对所有设备的所有活动警报的快照，按警报的严重性进行分组。 单击严重级别可打开“**警报**”页，确定作用域来显示这些警报。 在“警报”页上，可单击单个警报以查看该警报的其他详细信息，包括任何建议的操作。 如果已解决此问题，还可清除警报。
* “**作业**”区域提供对连接到你的服务的所有设备的最近作业的快照。 有可用于查看当前正在进行的作业、过去 24 小时内失败的作业，或计划在下一个 24 小时内运行的作业链接。
* “**速览**”区域提供服务状态、连接到服务的设备数量、服务位置，及与服务关联的订阅的详细信息等有用信息。 此外，还有操作日志的链接。 单击链接可查看所有已完成的 StorSimple Manager 服务操作的列表。

可使用“StorSimple Manager 服务仪表板”页启动以下任务：

* 查看或重新生成服务注册密钥。
* 更改服务数据加密密钥。
* 查看操作日志。

## <a name="view-or-regenerate-the-service-registration-key"></a>查看或重新生成服务注册密钥
服务注册密钥用于为 Microsoft Azure StorSimple 设备注册 StorSimple Manager 服务，以便设备出现在 Azure 经典门户中从而进行进一步管理操作。 在第一个设备上创建密钥，并将其与其他设备共享。

单击“**注册密钥**”（位于页面底部）可打开“**服务注册密钥**”对话框，在此可将当前服务注册密钥复制到剪贴板或重新生成服务注册密钥。

重新生成密钥不会影响以前注册的设备：它只会影响重新生成密钥后使用该服务注册的设备。

有关查看和生成服务注册密钥的详细信息，请转到 [获取服务注册密钥](storsimple-manage-service.md#get-the-service-registration-key)。

## <a name="change-the-service-data-encryption-key"></a>更改服务数据加密密钥
服务数据加密密钥用于加密机密客户数据，例如从 StorSimple Manager 服务发送到 StorSimple 设备的存储帐户凭据。 如果 IT 组织具有有关存储设备的密钥轮换策略，将需要定期更改这些密钥。 密钥更改过程可能会稍有不同，具体取决于 StorSimple Manager 服务管理的一个设备还是多个设备。

更改服务数据加密密钥过程有 3 个步骤：

1. 使用 Azure 经典门户，授权一个设备来更改服务数据加密密钥。
2. 使用 StorSimple 的 Windows PowerShell，启动服务数据加密密钥更改。
3. 如果有多个 StorSimple 设备，请更新其他设备上的服务数据加密密钥。

以下步骤介绍服务数据加密密钥的滚动更新过程。

[!INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]

## <a name="view-the-operations-logs"></a>查看操作日志
可通过单击仪表板的“速览”窗格中提供的操作日志链接来查看操作日志。 将转到“管理服务”页，在此可筛选和查看特定于 StorSimple Manager 服务的日志。

## <a name="next-steps"></a>后续步骤
* 了解如何 [排除 StorSimple 设备故障](storsimple-troubleshoot-operational-device.md)。
* 了解如何使用 [StorSimple Manager 服务以管理 StorSimple 设备](storsimple-manager-service-administration.md) 的详细内容。


