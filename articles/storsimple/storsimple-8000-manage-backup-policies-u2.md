---
title: "管理 StorSimple 8000 系列的备份策略 | Microsoft Docs"
description: "介绍如何在 StorSimple 8000 系列设备上使用 StorSimple 设备管理器服务创建和管理手动备份、备份计划与备份保留。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 569dbfdeb7dcd526cb5a54b487ea1bfb59b13cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>在 Azure 门户中使用 StorSimple 设备管理器服务管理备份策略


## <a name="overview"></a>概述

本教程介绍如何使用 StorSimple 设备管理器服务的“备份策略”边栏选项卡控制 StorSimple 卷的备份过程和备份保留。 此外还介绍如何完成手动备份。

备份卷时，可以选择创建本地快照或云快照。 若要备份本地固定卷，建议指定云快照。 如果对本地固定卷进行大量的本地快照操作，同时使用包含大量改动的数据集，则可能导致本地空间快速用尽。 如果选择进行本地快照操作，建议用户在备份最新状态时拍摄较少的每日快照，将其保留一天，再删除。

拍摄本地固定卷的云快照时，只将更改的数据复制到云，对其进行重复数据删除和压缩。

## <a name="the-backup-policy-blade"></a>“备份策略”边栏选项卡

使用 StorSimple 设备的“备份策略”边栏选项卡可以管理备份策略并计划本地快照和云快照。 备份策略用于配置一组卷的备份计划和备份保留。 使用备份策略可以同时执行多个卷的快照。 这意味着，通过备份策略创建的备份属于崩溃一致性副本。

使用备份策略表格列表还可通过以下一个或多个字段筛选现有备份策略：

* **策略名称** – 与策略关联的名称。 不同类型的策略包括：

  * 计划的策略，由用户显式创建。
  * 导入的策略，最初在 StorSimple Snapshot Manager 中创建。 这些策略有一个标签，说明从哪些 StorSimple Snapshot Manager 主机导入策略。

  > [!NOTE]
  > 创建卷时不再启用自动或默认备份策略。

* **最后一个成功的备份** – 最后一个使用此策略成功进行的备份的日期和时间。

* **下一次备份** – 下一次计划通过此策略启动的备份的日期和时间。

* **卷** – 与策略关联的卷。 创建备份时，与备份策略关联的所有卷都集中在一起。

* **计划数** – 与备份策略关联的计划数。

可以针对备份策略执行的常用操作包括：

* 添加备份策略
* 添加或修改计划
* 添加或删除卷
* 删除备份策略
* 创建手动备份

## <a name="add-a-backup-policy"></a>添加备份策略

添加备份策略，自动计划备份。 如果首先创建卷，该卷不会有关联的默认备份策略。 需要添加并分配一个备份策略来保护卷数据。

在 Azure 门户中执行以下步骤，添加用于 StorSimple 设备的备份策略。 添加策略后即可定义一个计划（请参阅[添加或修改计划](#add-or-modify-a-schedule)）。

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>添加或修改计划

可以添加或修改附加到 StorSimple 设备上的现有备份策略的计划。 在 Azure 门户中执行以下步骤来添加或修改计划。

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>添加或删除卷

可以在 StorSimple 设备上添加或删除分配给备份策略的卷。 在 Azure 门户中执行以下步骤来添加或删除卷。

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>删除备份策略

在 Azure 门户中执行以下步骤，删除 StorSimple 设备上的备份策略。

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>创建手动备份

在 Azure 门户中执行以下步骤，为单个卷创建按需（手动）备份。

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>后续步骤

了解有关如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)的详细信息。

