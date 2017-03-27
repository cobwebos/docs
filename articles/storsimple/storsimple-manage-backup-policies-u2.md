---
title: "管理 StorSimple 备份策略 | Microsoft Docs"
description: "介绍如何使用 StorSimple Manager 服务创建和管理手动备份、备份计划和备份保留。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 4a2db707-bbfc-425c-bfeb-bc5c97781873
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7f698f1560d99ce9ba0ed125c401f04473170c2b


---
# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>使用 StorSimple Manager 服务管理备份策略 (Update 2)
[!INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>概述
本教程介绍如何使用 StorSimple Manager 服务的“备份策略”页控制 StorSimple 卷的备份过程和备份保留。 此外还介绍如何完成手动备份。

备份卷时，可以选择创建本地快照或云快照。 若要备份本地固定卷，建议指定云快照。 如果对本地固定卷进行大量的本地快照操作，同时使用包含大量改动的数据集，则可能导致本地空间快速用尽。 如果选择进行本地快照操作，建议用户在备份最新状态时拍摄较少的每日快照，将其保留一天，然后再删除。

拍摄本地固定卷的云快照时，只将更改的数据复制到云，对其进行重复数据删除和压缩。 

## <a name="the-backup-policies-page"></a>“备份策略”页
“备份策略”页用于管理备份策略并计划本地快照和云快照。 （备份策略用于配置一组卷的备份计划和备份保留。）使用备份策略可以同时执行多个卷的快照。 这意味着，通过备份策略创建的备份属于崩溃一致性副本。 “备份策略”页将列出备份策略及其类型、关联的卷、保留的备份数以及用于启用这些策略的选项。

“备份策略”页还可通过一个或多个以下字段筛选现有备份策略：

* **策略名称** – 与策略关联的名称。 不同类型的策略包括：
  
  * 计划的策略，由用户显式创建。
  * 自动策略，在创建卷时启用此卷选项的默认备份时创建。 这些策略按 *VolumeName*_Default 格式命名，其中，*VolumeName* 是指用户在 Azure 经典门户中配置的 StorSimple 卷的名称。 自动策略会导致系统在每天 22:30（设备时间）开始拍摄云快照。
  * 导入的策略，最初在 StorSimple Snapshot Manager 中创建。 这些策略有一个标签，说明从哪些 StorSimple Snapshot Manager 主机导入策略。
* **卷** – 与策略关联的卷。 创建备份时，与备份策略关联的所有卷都集中在一起。
* **最后一个成功的备份** – 最后一个使用此策略成功进行的备份的日期和时间。
* **下一次备份** – 下一次计划通过此策略启动的备份的日期和时间。
* **计划数** – 与备份策略关联的计划数。

可以从此页执行的常用操作包括：

* 添加备份策略 
* 添加或修改计划 
* 删除备份策略 
* 创建手动备份 
* 创建包含多个卷和计划的自定义备份策略 

## <a name="add-a-backup-policy"></a>添加备份策略
添加备份策略，自动计划备份。 在 Azure 经典门户中执行以下步骤，添加用于 StorSimple 设备的备份策略。 添加策略后即可定义一个计划（请参阅[添加或修改计划](#add-or-modify-a-schedule)）。

[!INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![可用视频](./media/storsimple-manage-backup-policies-u2/Video_icon.png) **可用视频**

若要观看如何创建本地或云的备份策略的演示视频，请单击[此处](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/)。

## <a name="add-or-modify-a-schedule"></a>添加或修改计划
可以添加或修改附加到 StorSimple 设备上的现有备份策略的计划。 请在 Azure 经典门户中执行以下步骤，添加或修改计划。

[!INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>删除备份策略
在 Azure 经典门户中执行以下步骤，删除 StorSimple 设备上的备份策略。

[!INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>创建手动备份
在 Azure 经典门户中执行以下步骤，为单个卷创建按需（手动）备份。

[!INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>创建包含多个卷和计划的自定义备份策略
在 Azure 经典门户中执行以下步骤，创建有多个卷和计划的自定义备份策略。

[!INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]

## <a name="next-steps"></a>后续步骤
了解有关如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)的详细信息。




<!--HONumber=Nov16_HO3-->


