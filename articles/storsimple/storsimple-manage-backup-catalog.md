---
title: "管理 StorSimple 备份目录 | Microsoft Docs"
description: "介绍如何使用 StorSimple Manager 服务的“备份目录”页面列出、选择和删除卷的备份集。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ad81bee9-fe43-40b3-a384-b15fb274ecd9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/28/2016
ms.author: v-sharos
ms.openlocfilehash: 5ee9855e1428c7a2d871d9c215d302c5c3b7101a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>使用 StorSimple Manager 服务管理备份目录
## <a name="overview"></a>概述
StorSimple Manager 服务的“备份目录”页面显示执行手动或计划备份时创建的所有备份集。 可以使用此页面列出备份策略或卷的所有备份、选择或删除备份，或使用备份来还原或克隆卷。

本教程介绍如何列出、选择和删除备份集。 若要了解如何从备份还原设备，请转到[从备份集还原设备](storsimple-restore-from-backup-set.md)。 若要了解如何克隆卷，请转到[克隆 StorSimple 卷](storsimple-clone-volume.md)。

![备份目录](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

“备份目录”页提供了一个查询，可缩小备份集选择范围。 可筛选基于以下参数检索到的备份集：

* **设备** – 在其上创建备份集的设备。
* **备份策略或卷** – 与此备份集相关联的备份策略或卷。
* **开始时间和结束时间** – 创建备份集的日期和时间范围。

然后基于以下属性以表格方式列出所筛选的备份集：

* **名称** – 与备份集相关联的备份策略或卷的名称。
* **大小** – 备份集的实际大小。
* **创建日期** – 创建备份的日期和时间。 
* **类型** – 备份集可为本地快照或云快照。 本地快照是本地存储在设备上的所有卷数据的备份，而云快照是指驻留在云中的卷数据的备份。 本地快照可提供更快的访问，而云快照可提供良好的数据弹性。
* **启动方式** – 备份的启动方式分为按计划自动启动或用户手动启动。 可使用备份策略来计划备份。 也可使用“执行备份”选项来执行手动备份。

## <a name="list-backup-sets-for-a-volume"></a>列出卷的备份集
完成以下步骤，列出卷的所有备份。

#### <a name="to-list-backup-sets"></a>列出备份集
1. 在“StorSimple Manager 服务”页上，单击“备份目录”选项卡。
2. 筛选选择项，如下所示：
   
   1. 选择适当的设备。
   2. 在下拉列表中，选择要查看相应备份的卷。
   3. 指定时间范围。
   4. 单击选中图标  ![勾选图标](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 以执行此查询。
      
      与所选卷关联的备份应出现在备份集的列表中。

## <a name="select-a-backup-set"></a>选择备份集
完成以下步骤，为卷或备份策略选择备份集。

#### <a name="to-select-a-backup-set"></a>选择备份集
1. 在“StorSimple Manager 服务”页上，单击“备份目录”选项卡。
2. 筛选选择项，如下所示：
   
   1. 选择适当的设备。
   2. 在下拉列表中，选择想要选择的备份的卷或备份策略。
   3. 指定时间范围。
   4. 单击选中图标  ![勾选图标](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 以执行此查询。
      
      与所选的卷或备份策略相关联的备份应出现在备份集的列表中。
3. 选择并展开备份集。 “还原”和“删除”选项显示在页面底部。 可以对所选备份集执行上述任一操作。

## <a name="delete-a-backup-set"></a>删除备份集
如果不想继续保留与备份关联的数据，请删除该备份。 可以执行以下步骤来删除备份集。

#### <a name="to-delete-a-backup-set"></a>删除备份集
1. 在“StorSimple Manager 服务”页上，单击“备份目录”选项卡。
2. 筛选选择项，如下所示：
   
   1. 选择适当的设备。
   2. 在下拉列表中，选择想要选择的备份的卷或备份策略。
   3. 指定时间范围。
   4. 单击选中图标  ![勾选图标](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 以执行此查询。
      
      与所选的卷或备份策略相关联的备份应出现在备份集的列表中。
3. 选择并展开备份集。 “还原”和“删除”选项显示在页面底部。 单击“删除” 。
4. 系统会在删除过程中以及删除成功完成后向用户发送通知。 删除完成后，刷新此页上的查询。 已删除的备份集不再显示在备份集列表中。

## <a name="next-steps"></a>后续步骤
* 了解如何[使用备份目录从备份集还原设备](storsimple-restore-from-backup-set.md)。
* 了解如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。

