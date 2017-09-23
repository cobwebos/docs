---
title: "从备份中还原 StorSimple 卷 | Microsoft Docs"
description: "介绍如何使用 StorSimple Manager 服务备份目录页面从备份集中还原 StorSimple 卷。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b979782e-3184-4465-ad5f-e4516a5885d2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 12484338f5b4d489604d70a657ef0992b6267297
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>从备份集中还原 StorSimple 卷
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>概述
**备份目录**页面显示手动或自动备份时创建的所有备份集。 可以使用此页面列出备份策略或卷的所有备份、选择或删除备份，或使用备份来还原或克隆卷。

 ![“备份目录”页面](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

本教程介绍如何使用“备份目录”页面在设备上从备份集中还原卷。

## <a name="how-to-use-the-backup-catalog"></a>如何使用备份目录
**备份目录**页面提供帮助缩小备份集选择的查询。 可筛选基于以下参数检索到的备份集：

* **设备** – 在其上创建备份集的设备。
* **备份策略**或**卷** – 与此备份集相关联的备份策略或卷。
* **开始时间**和**结束时间** – 创建备份集的日期和时间范围。

然后基于以下属性以表格方式列出所筛选的备份集：

* **名称** – 与备份集相关联的备份策略或卷的名称。
* **大小** – 备份集的实际大小。
* **创建日期** – 创建备份的日期和时间。 
* **类型** – 备份集可为本地快照或云快照。 本地快照是本地存储在设备上的所有卷数据的备份，而云快照是指驻留在云中的卷数据的备份。 本地快照可提供更快的访问，而云快照可提供良好的数据弹性。
* **启动方式** – 备份的启动方式分为按计划自动启动或用户手动启动。 （可使用备份策略来计划备份。 也可使用“执行备份”选项来执行交互备份。）

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>如何从备份中还原 StorSimple 卷
可使用“备份目录”页从特定备份中还原 StorSimple 卷。 

> [!WARNING]
> 从备份中还原将替换备份中的现有卷。 这可能导致备份后写入的任何数据都会丢失。
> 
> 

在对卷启动还原之前，请确保卷处于脱机状态。 首先需要在主机上使卷脱机，然后在设备上使卷脱机。 请按照[使卷脱机](storsimple-manage-volumes.md#take-a-volume-offline)中的步骤操作。 执行以下步骤从备份集中还原卷。

### <a name="to-restore-from-a-backup-set"></a>从备份集中还原
1. 在“StorSimple Manager 服务”页上，单击“备份目录”选项卡。
   
    ![备份目录](./media/storsimple-restore-from-backup-set/HCS_Restore.png)
2. 选择备份集，如下所示：
   
   1. 选择适当的设备。
   2. 在下拉列表中，选择想要选择的备份的卷或备份策略。
   3. 指定时间范围。
   4. 单击选中图标  ![选中图标](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) 以执行此查询。
      
      与所选的卷或备份策略相关联的备份应出现在备份集的列表中。
3. 展开备份集以查看相关联的卷。 在还原这些卷之前，必须使其在主机和设备上脱机。 请按照[使卷脱机](storsimple-manage-volumes.md#take-a-volume-offline)中的步骤操作。
   
   > [!IMPORTANT]
   > 请确保在设备上使卷脱机之前，已在主机上使卷处于脱机状态。 如果未在主机上使卷脱机，则可能会导致数据损坏。
   > 
   > 
4. 选择一个备份集。 单击页面底部的“还原”。
5. 系统将提示进行确认。 
   
    ![“确认”页](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)
6. 查看还原信息，然后单击选中图标![选中图标](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png)。 这将启动可通过访问“作业”页面进行查看的还原作业。 
7. 还原完成后，可验证卷的内容是否已由备份中的卷所替换。

![可用视频](./media/storsimple-restore-from-backup-set/Video_icon.png)**可用视频**

若要观看演示如何在 StorSimple 中使用克隆和还原功能来恢复已删除文件的视频，请单击[此处](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)。

## <a name="next-steps"></a>后续步骤
* 了解如何[管理 StorSimple 卷](storsimple-manage-volumes.md)。
* 了解如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。


