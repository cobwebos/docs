---
title: "管理 StorSimple 备份策略 | Microsoft Docs"
description: "介绍如何使用 StorSimple Manager 服务创建和管理手动备份、备份计划和备份保留。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: d1834bc8-d520-4463-82ae-3b32fabd021c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2016
ms.author: v-sharos
ms.openlocfilehash: c1e9d5d0450bab5d371aafb40fd7c5920d39dfdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-backup-policies"></a>使用 StorSimple Manager 服务管理备份策略
[!INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>概述
本教程介绍如何使用 StorSimple Manager 服务的“备份策略”页控制 StorSimple 卷的备份过程和备份保留。 此外还介绍如何完成手动备份。

“备份策略”页用于管理备份策略并计划本地快照和云快照。 （备份策略用于配置一组卷的备份计划和备份保留。）使用备份策略可以同时执行多个卷的快照。 这意味着，通过备份策略创建的备份属于崩溃一致性副本。 此页将列出备份策略及其类型、关联的卷、保留的备份数以及用于启用这些策略的选项。

“备份策略”页还可通过一个或多个以下字段筛选现有备份策略：

* **策略名称** – 与策略关联的名称。 不同类型的策略包括：
  
  * 计划的策略，由用户显式创建。
  * 自动策略，在创建卷时启用此卷选项的默认备份时创建。 这些策略按 VolumeName_Default 格式命名，其中，VolumeName 是指用户在 Azure 经典门户中配置的 StorSimple 卷的名称。 自动策略会导致系统在每天 22:30（设备时间）开始拍摄云快照。
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

[!INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]

![可用视频](./media/storsimple-manage-backup-policies/Video_icon.png) **可用视频**

若要观看如何创建本地或云的备份策略的演示视频，请单击[此处](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/)。

## <a name="add-or-modify-a-schedule"></a>添加或修改计划
可以添加或修改附加到 StorSimple 设备上的现有备份策略的计划。 请在 Azure 经典门户中执行以下步骤，添加或修改计划。

[!INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## <a name="delete-a-backup-policy"></a>删除备份策略
在 Azure 经典门户中执行以下步骤，删除 StorSimple 设备上的备份策略。

[!INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>创建手动备份
在 Azure 经典门户中执行以下步骤，为单个卷创建按需（手动）备份。

[!INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>创建包含多个卷和计划的自定义备份策略
在 Azure 经典门户中执行以下步骤，创建有多个卷和计划的自定义备份策略。

[!INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]

## <a name="next-steps"></a>后续步骤
了解有关如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)的详细信息。

