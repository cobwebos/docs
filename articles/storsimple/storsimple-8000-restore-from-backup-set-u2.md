---
title: "在 StorSimple 8000 系列上从备份还原卷 | Microsoft Docs"
description: "介绍如何使用 StorSimple 设备管理器服务备份目录从备份集还原 StorSimple 卷。"
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
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: aff0710ead4f76bb80c38e2d88fe9cd3ce6a7b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>从备份集中还原 StorSimple 卷

## <a name="overview"></a>概述

本教程介绍如何使用现有备份集在 StorSimple 8000 系列设备上执行还原操作。 使用“备份目录”边栏选项卡可从本地或云备份还原卷。 “备份目录”边栏选项卡显示执行手动或自动备份时创建的所有备份集。 在后台下载数据时，从备份集执行还原操作会立即使卷联机。

开始还原的另一种方法是转到“设备”> [你的设备] >“卷”。 在“卷”边栏选项卡中选择一个卷，单击右键调用上下文菜单，选择“还原”。

## <a name="before-you-restore"></a>还原前

在开始还原之前，请查看以下注意事项：

* **必须使卷脱机** – 启动还原操作前，请在主机和设备上使卷脱机。 虽然还原操作会自动在设备上使卷联机，但必须在主机上手动使设备联机。 在设备上使卷联机后，便可以在主机上使卷联机。 （无需等待还原操作完成即可进行此操作。）有关过程，请转到[使卷脱机](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)。

* **还原后的卷类型** – 基于快照中的类型对已删除的卷进行还原；也就是说，已在本地固定的卷会还原为本地固定卷，已分层的卷会还原为分层卷。

    对于现有卷，卷的当前使用类型会覆盖存储在快照中的类型。 例如，如果从在对卷类型进行分层时所拍摄的快照中还原卷，并且现在该卷类型为本地固定（由于执行了转换操作）时，则该卷会还原为本地固定卷。 同样，如果现有的本地固定卷已被展开，并且随后从较旧的快照（在该卷较小时所拍摄）中还原时，则还原的卷将保留当前展开后的大小。

    当卷正在还原时，不能将该卷从分层卷转换为本地固定卷，或者从本地固定卷转换为分层卷。 在还原操作完成后，便可以将卷转换为另一种类型。 有关转换卷的信息，请转到[更改卷类型](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)。 

* 卷大小反映在已还原的卷中 - 如果要还原已删除的本地固定卷，则这是需要考虑的重要因素（因为已对本地固定卷进行了完全预配）。 在尝试还原以前删除的本地固定卷之前，请确保拥有足够的空间。

* **正在进行还原时不能扩展卷** – 在还原操作完成之后，再尝试扩展卷。 有关扩展卷的信息，请转到[修改卷](storsimple-8000-manage-volumes-u2.md#modify-a-volume)。

* **还原本地卷时可执行备份** – 有关过程，请参阅[使用 StorSimple 设备管理器服务管理备份策略](storsimple-8000-manage-backup-policies-u2.md)。

* **可取消还原操作** – 如果取消还原作业，则该卷会被回滚到启动还原操作之前所处的状态。 有关过程，请转到[取消作业](storsimple-8000-manage-jobs-u2.md#cancel-a-job)。

## <a name="how-does-restore-work"></a>还原的工作原理

对于运行 Update 4 或更高版本的设备，实现基于热度地图的还原。 设备收到访问数据的主机请求时，会跟踪这些请求并创建热度地图。 高请求速率导致出现较高热度的数据区块，而较低请求速率则表现为较低热度的区块。 必须访问数据至少两次，才能将其标记为“热”。 将修改的文件也标记为“热”。 启动还原后，根据热度地图执行数据的主动水化。 对于 Update 4 之前的版本，仅根据访问情况在还原期间下载数据。

以下注意事项适用于基于热度地图的还原：

* 仅对分层卷启用热度地图跟踪，不支持本地固定卷。

* 将卷克隆到其他设备时不支持基于热度地图的还原。 

* 如果设备上存在就地还原和要还原卷的本地快照，则不解除冻结（因为数据已在本地可用）。 

* 默认情况下，会在还原时启动解除冻结作业，以根据热度地图主动解除冻结数据。 

在 Update 4 中，可使用 Windows PowerShell cmdlet 查询正在运行的解除冻结作业、取消解除冻结作业，并获取解除冻结作业的状态。

* `Get-HcsRehydrationJob` - 此 cmdlet 用于获取解除冻结作业的状态。 对一个卷触发单个解除冻结作业。

* `Set-HcsRehydrationJob` - 使用此 cmdlet 可在解除冻结过程中暂停、停止、恢复解除冻结作业。

有关解除冻结 cmdlet 的详细信息，请转到[适用于 StorSimple 的 Windows PowerShell cmdlet 参考](https://technet.microsoft.com/library/dn688168.aspx)。

使用自动解除冻结通常会产生较高的瞬时读取性能。 实际的提升程度取决于各种因素，例如访问模式、数据更改和数据类型。 

若要取消解除冻结作业，可使用 PowerShell cmdlet。 如果要对将来的所有还原永久禁用解除冻结作业，请[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)。

## <a name="how-to-use-the-backup-catalog"></a>如何使用备份目录

“备份目录”边栏选项卡提供帮助缩小备份集选择的查询。 可筛选基于以下参数检索到的备份集：

* **时间范围** – 创建备份集的日期和时间范围。
* **设备** – 在其上创建备份集的设备。
* **备份策略**或**卷** – 与此备份集相关联的备份策略或卷。

然后基于以下属性以表格方式列出所筛选的备份集：

* **名称** – 与备份集相关联的备份策略或卷的名称。
* **类型** – 备份集可为本地快照或云快照。 本地快照是本地存储在设备上的所有卷数据的备份，而云快照是指驻留在云中的卷数据的备份。 本地快照可提供更快的访问，而云快照可提供良好的数据弹性。
* **大小** – 备份集的实际大小。
* **创建日期** – 创建备份的日期和时间。 
* **卷** - 与备份集关联的卷数。
* **已启动** – 备份的启动方式分为按计划自动启动或用户手动启动。 （可使用备份策略来计划备份。 也可使用“执行备份”选项来执行交互或按需备份。）

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>如何从备份中还原 StorSimple 卷

可使用“备份目录”边栏选项卡从特定备份中还原 StorSimple 卷。 但是，请记住，还原卷会将卷还原到执行备份时其所处的状态。 在备份操作之后添加的任何数据都会丢失。

> [!WARNING]
> 从备份中还原将替换备份中的现有卷。 这可能导致备份后写入的任何数据都会丢失。


### <a name="to-restore-your-volume"></a>还原卷
1. 转到 StorSimple 设备管理器服务，单击“备份目录”。

2. 选择备份集，如下所示：
   
   1. 指定时间范围。
   2. 选择适当的设备。
   3. 在下拉列表中，选择想要选择的备份的卷或备份策略。
   4. 单击“应用”执行此查询。

    与所选的卷或备份策略相关联的备份应出现在备份集的列表中。
   
    ![备份集列表](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. 展开备份集以查看相关联的卷。 在还原这些卷之前，必须使其在主机和设备上脱机。 在设备的“卷”边栏选项卡上访问卷，遵循[使卷脱机](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)中的步骤使其脱机。
   
   > [!IMPORTANT]
   > 请确保在设备上使卷脱机之前，已在主机上使卷处于脱机状态。 如果未在主机上使卷脱机，则可能会导致数据损坏。
   
4. 导航回“备份目录”选项卡并选择备份集。 单击右键，从上下文菜单中选择“还原”。

    ![备份集列表](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. 系统会提示进行确认。 查看还原信息，并选择“确认”复选框。
   
    ![“确认”页](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7.  请单击“还原”。 这会启动可通过访问“作业”页面进行查看的还原作业。

    ![“确认”页](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. 还原完成后，可验证卷的内容是否已由备份中的卷所替换。


## <a name="if-the-restore-fails"></a>如果还原失败

如果因任何原因而导致还原操作失败，用户将收到警报。 如果发生这种情况，请刷新备份列表，以验证备份是否仍然有效。 如果备份有效，并且正在从云中进行还原，则可能是由连接问题所造成的。

要完成还原操作，需要使卷在主机上脱机，并重试还原操作。 请注意，在还原过程中之前对卷数据进行的任何修改都将丢失。

## <a name="next-steps"></a>后续步骤
* 了解如何[管理 StorSimple 卷](storsimple-8000-manage-volumes-u2.md)。
* 了解如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。

