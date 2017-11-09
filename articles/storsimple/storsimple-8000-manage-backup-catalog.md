---
title: "管理 StorSimple 备份目录 | Microsoft Docs"
description: "介绍如何使用 StorSimple 设备管理器服务的备份目录页列出、选择和删除备份集。"
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: ac2577c6cd350d6d437d55e61ec73d954cb24893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>使用 StorSimple 设备管理器服务管理备份目录
## <a name="overview"></a>概述
StorSimple 设备管理器服务的“备份目录”边栏选项卡显示执行手动或计划备份时创建的所有备份集。 可以使用此页面列出备份策略或卷的所有备份、选择或删除备份，或使用备份来还原或克隆卷。

本教程介绍如何列出、选择和删除备份集。 若要了解如何从备份还原设备，请转到[从备份集还原设备](storsimple-8000-restore-from-backup-set-u2.md)。 若要了解如何克隆卷，请转到[克隆 StorSimple 卷](storsimple-8000-clone-volume-u2.md)。

![备份目录](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

“备份目录”边栏选项卡提供了一个查询，可缩小备份集选择范围。 可筛选基于以下参数检索到的备份集：

* **设备** – 在其上创建备份集的设备。
* **备份策略或卷** – 与此备份集相关联的备份策略或卷。
* **开始时间和结束时间** – 创建备份集的日期和时间范围。

然后基于以下属性以表格方式列出所筛选的备份集：

* **名称** – 与备份集相关联的备份策略或卷的名称。
* **大小** – 备份集的实际大小。
* **创建日期** – 创建备份的日期和时间。 
* **类型** – 备份集可为本地快照或云快照。 本地快照是本地存储在设备上的所有卷数据的备份，而云快照是指驻留在云中的卷数据的备份。 本地快照可提供更快的访问，而云快照可提供良好的数据弹性。
* **启动方式** – 备份的启动方式分为按计划自动启动或用户手动启动。 可使用备份策略来计划备份。 也可使用“执行备份”选项来执行手动备份。

## <a name="list-backup-sets-for-a-backup-policy"></a>列出备份策略的备份集
完成以下步骤，列出备份策略的所有备份。

#### <a name="to-list-backup-sets"></a>列出备份集
1. 转到 StorSimple 设备管理器服务，单击“备份目录”。

2. 筛选选择项，如下所示：
   
   1. 指定时间范围。
   2. 选择适当的设备。
   3. 按“备份策略”筛选，以查看相应的备份。
   3. 在备份策略下拉列表中，选择“全部”查看所选设备上的所有备份。
   4. 单击“应用”执行此查询。
      
      与所选备份策略相关联的备份应出现在备份集的列表中。

      ![转到备份目录](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>选择备份集
完成以下步骤，为卷或备份策略选择备份集。

#### <a name="to-select-a-backup-set"></a>选择备份集
1. 转到 StorSimple 设备管理器服务，单击“备份目录”。
2. 筛选选择项，如下所示：
   
   1. 指定时间范围。 
   2. 选择适当的设备。 
   3. 根据想要选择的备份的卷或备份策略进行筛选。
   4. 单击“应用”执行此查询。
      
      与所选的卷或备份策略相关联的备份应出现在备份集的列表中。

      ![转到备份目录](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. 选择并展开备份集。 现在可以看到，备份集已根据它包含的卷进行划分。 可通过上下文菜单（右键菜单）对备份集使用“还原”和“删除”选项。 可以对所选备份集执行上述任一操作。

    ![转到备份目录](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>删除备份集
如果不想继续保留与备份关联的数据，请删除该备份。 可以执行以下步骤来删除备份集。

#### <a name="to-delete-a-backup-set"></a>删除备份集
 转到 StorSimple 设备管理器服务，单击“备份目录”。
2. 筛选选择项，如下所示：
   
   1. 指定时间范围。 
   2. 选择适当的设备。 
   3. 根据想要选择的备份的卷或备份策略进行筛选。
   4. 单击“应用”执行此查询。
      
      与所选的卷或备份策略相关联的备份应出现在备份集的列表中。

      ![转到备份目录](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. 选择并展开备份集。 现在可以看到，备份集已根据它包含的卷进行划分。 可通过上下文菜单（右键菜单）对备份集使用“还原”和“删除”选项。 单击右键所选的备份集，从上下文菜单中选择“删除”。

    ![转到备份目录](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

4. 当系统提示确认时，请检查显示的信息，单击“删除”。 所选的备份会永久删除。

    ![转到备份目录](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

5. 系统会在删除过程中以及删除成功完成后向用户发送通知。 删除完成后，刷新此页上的查询。 已删除的备份集不再显示在备份集列表中。

    ![转到备份目录](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>后续步骤
* 了解如何[使用备份目录从备份集还原设备](storsimple-8000-restore-from-backup-set-u2.md)。
* 了解如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。

