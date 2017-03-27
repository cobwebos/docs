---
title: "从备份中还原 StorSimple 卷 | Microsoft Docs"
description: "介绍如何使用 StorSimple Manager 服务备份目录页面从备份集中还原 StorSimple 卷。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 6f289c39-96c7-4d57-b68a-4bc2e99aef9d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/26/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: fbb5a38e5ef92de7ca4752441d72d29135867a51


---
# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>从备份集中还原 StorSimple 卷（更新 2）
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>概述
**备份目录**页面显示手动或自动备份时创建的所有备份集。 可以使用此页面列出备份策略或卷的所有备份、选择或删除备份，或使用备份来还原或克隆卷。

 ![“备份目录”页面](./media/storsimple-restore-from-backup-set-u2/restore.png)

本教程介绍如何使用“备份目录”页面从备份集中还原设备。

可从本地或云备份中还原卷。 在任一情况下，当在后台下载数据时，还原操作会立即使卷联机。 

在启动还原操作前，应注意以下几点：

* **必须使卷脱机** – 启动还原操作前，请在主机和设备上使卷脱机。 虽然还原操作会自动在设备上使卷联机，但必须在主机上手动使设备联机。 在设备上使卷联机后，便可以在主机上使卷联机。 （无需等待还原操作完成即可进行此操作。）有关过程，请转到[使卷脱机](storsimple-manage-volumes-u2.md#take-a-volume-offline)。
* **还原后的卷类型** – 基于快照中的类型对已删除的卷进行还原；也就是说，已在本地固定的卷会还原为本地固定卷，已分层的卷会还原为分层卷。
  
    对于现有卷，卷的当前使用类型会覆盖存储在快照中的类型。 例如，如果从在对卷类型进行分层时所拍摄的快照中还原卷，并且现在该卷类型为本地固定（由于执行了转换操作）时，则该卷会还原为本地固定卷。 同样，如果现有的本地固定卷已被展开，并且随后从较旧的快照（在该卷较小时所拍摄）中还原时，则还原的卷将保留当前展开后的大小。
  
    当卷正在还原时，不能将该卷从分层卷转换为本地固定卷，或者从本地固定卷转换为分层卷。 在还原操作完成后，便可以将卷转换为另一种类型。 有关转换卷的信息，请转到[更改卷类型](storsimple-manage-volumes-u2.md#change-the-volume-type)。 
* **卷大小将反映在已还原的卷中** – 如果要还原已删除的本地固定卷，则这是需要考虑的重要因素（因为已对本地固定卷进行了完全预配）。 在尝试还原以前删除的本地固定卷之前，请确保拥有足够的空间。 
* **正在进行还原时不能扩展卷** – 在还原操作完成之后，再尝试扩展卷。 有关扩展卷的信息，请转到[修改卷](storsimple-manage-volumes-u2.md#modify-a-volume)。
* **还原本地卷时可执行备份** – 有关过程，请转到 [Use the StorSimple Manager service to manage backup policies](storsimple-manage-backup-policies.md)（使用 StorSimple Manager 服务管理备份策略）。
* **可取消还原操作** – 如果取消还原作业，则该卷将被回滚到启动还原操作之前所处的状态。 有关过程，请转到[取消作业](storsimple-manage-jobs-u2.md#cancel-a-job)。

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
可使用“备份目录”页从特定备份中还原 StorSimple 卷。 但是，请记住，还原卷会将卷还原到执行备份时其所处的状态。 在备份操作之后添加的任何数据都会丢失。

> [!WARNING]
> 从备份中还原将替换备份中的现有卷。 这可能导致备份后写入的任何数据都会丢失。
> 
> 

### <a name="to-restore-your-volume"></a>还原卷
1. 在“StorSimple Manager 服务”页上，单击“备份目录”选项卡。
   
    ![备份目录](./media/storsimple-restore-from-backup-set-u2/restore.png)
2. 选择备份集，如下所示：
   
   1. 选择适当的设备。
   2. 在下拉列表中，选择想要选择的备份的卷或备份策略。
   3. 指定时间范围。
   4. 单击选中图标  ![选中图标](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) 以执行此查询。
      
      与所选的卷或备份策略相关联的备份应出现在备份集的列表中。
3. 展开备份集以查看相关联的卷。 在还原这些卷之前，必须使其在主机和设备上脱机。 在“卷容器”页面上访问卷，然后按照[使卷脱机](storsimple-manage-volumes-u2.md#take-a-volume-offline)中的步骤使其脱机。
   
   > [!IMPORTANT]
   > 请确保在设备上使卷脱机之前，已在主机上使卷处于脱机状态。 如果未在主机上使卷脱机，则可能会导致数据损坏。
   > 
   > 
4. 导航回“备份目录”选项卡并选择备份集。
5. 单击页面底部的“还原”。
6. 系统将提示进行确认。 查看还原信息，然后选择“确认”复选框。
   
    ![“确认”页](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)
7. 单击选中图标![选中图标](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png)。 这将启动可通过访问“作业”页面进行查看的还原作业。 
8. 还原完成后，可验证卷的内容是否已由备份中的卷所替换。

![可用视频](./media/storsimple-restore-from-backup-set-u2/Video_icon.png)**可用视频**

若要观看演示如何在 StorSimple 中使用克隆和还原功能来恢复已删除文件的视频，请单击[此处](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)。

## <a name="if-the-restore-fails"></a>如果还原失败
如果因任何原因而导致还原操作失败，用户将收到警报。 如果发生这种情况，请刷新备份列表，以验证备份是否仍然有效。 如果备份有效，并且正在从云中进行还原，则可能是由连接问题所造成的。 

若要完成还原操作，需要使卷在主机上脱机，然后重试还原操作。 请注意，在还原过程中之前对卷数据进行的任何修改都将丢失。

## <a name="next-steps"></a>后续步骤
* 了解如何[管理 StorSimple 卷](storsimple-manage-volumes-u2.md)。
* 了解如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。




<!--HONumber=Nov16_HO3-->


