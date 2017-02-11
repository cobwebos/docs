---
title: "从 StorSimple Virtual Array 的备份中还原"
description: "了解有关如何还原 StorSimple Virtual Array 的备份的详细信息。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4d0deb8c-e3c7-4bc4-b89d-9881041960cb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2801bc0fc645f9ed23733d1025f1cb42a5174022


---
# <a name="restore-from-a-backup-of-your-storsimple-virtual-array"></a>从 StorSimple Virtual Array 的备份中还原
## <a name="overview"></a>概述
本文适用于运行 2016 年 3 月正式 (GA) 版或更高版本的 Microsoft Azure StorSimple Virtual Array（也称为 StorSimple 本地虚拟设备或 StorSimple 虚拟设备）。 本文分步介绍如何从 StorSimple Virtual Array 上的共享或卷的备份集中还原。 本文还详细介绍了项目级恢复在配置为文件服务器的 StorSimple Virtual Array 上的工作方式。

## <a name="restore-shares-from-a-backup-set"></a>从备份集中还原共享
**尝试还原共享之前，请确保设备上有足够空间来完成此操作。** 若要从备份还原，请在 [Azure 经典门户](https://manage.windowsazure.com/)中执行以下步骤。

#### <a name="to-restore-a-share"></a>还原共享
1. 浏览到“备份目录”。 按适当的设备和时间范围进行筛选以搜索备份。 单击选中图标 ![](./media/storsimple-ova-restore/image1.png) 以执行查询。
2. 在显示的备份集列表中，单击并选择特定的备份。 展开备份，以查看下方的各个共享。 单击并选择想要还原的共享。
3. 在页面底部，单击“还原为新”。
4. 这将启动“还原为新共享”向导。 在“指定名称和位置”页上：
   
   1. 验证源设备名称。 这应是包含想要还原的共享的设备。 设备选择为灰显。 若要选择不同的源设备，需要退出向导并再次重新选择备份集。
   2. 提供共享名。 共享名必须包含 3 至 127 个字符。
   3. 查看要尝试还原的共享大小、类型和权限。 还原完成后，能够通过 Windows 资源管理器修改共享属性。
   4. 单击选中图标 ![](./media/storsimple-ova-restore/image1.png)。
      
      ![](./media/storsimple-ova-restore/image9.png)
5. 还原作业完成后，还原将启动，并且会显示另一条通知。 若要监视还原的进度，请单击“查看作业”。 这将转到“作业”页。
6. 可以跟踪还原作业的进度。 还原 100% 完成时，向后导航到设备上的“共享”页。
7. 现在可在设备上的共享列表中查看新还原的共享。 请注意，完成还原为相同类型的共享。 分层共享还原为分层共享，本地固定共享还原为本地固定共享。

现在已完成了设备配置，并且了解了如何备份或还原共享。 

## <a name="restore-volumes-from-a-backup-set"></a>从备份集中还原卷
若要从备份还原，请在 Azure 经典门户中执行以下步骤。 还原操作将备份还原为同一虚拟设备上的新卷；不能还原到不同的设备上。

#### <a name="to-restore-a-volume"></a>还原卷
1. 浏览到“备份目录”。 按适当的设备和时间范围进行筛选以搜索备份。 单击选中图标 ![](./media/storsimple-ova-restore/image1.png) 以执行查询。
2. 在显示的备份集列表中，单击并选择特定的备份。 展开备份，以查看下方的各个卷。 选择想要还原的卷。 
3. 在页面底部，单击“还原为新”。 将启动“还原为新卷”向导。
4. 在“指定名称和位置”页上：
   
   1. 验证源设备名称。 这应是包含想要还原的卷的设备。 设备选择不可用。 若要选择不同的源设备，需要退出向导并再次重新选择备份集。
   2. 为正在还原为新卷的卷提供卷名。 卷名必须包含 3 至 127 个字符。
   3. 单击箭头图标。
      
      ![](./media/storsimple-ova-restore/image12.png)
5. 在“指定可使用此卷的主机”页上，从下拉列表中选择适当的 ACR。
   
   ![](./media/storsimple-ova-restore/image13.png)
6. 单击选中图标 ![](./media/storsimple-ova-restore/image1.png)。 这将启动还原作业，并且会显示作业正在进行的以下通知。
7. 还原作业完成后，还原将启动，并且会显示另一条通知。 若要监视还原的进度，请单击“查看作业”。 这将转到“作业”页。
8. 可以跟踪还原作业的进度。 向后导航到设备上的“卷”页。
9. 现在可在设备上的共卷列表中查看新还原的卷。 请注意，完成还原为相同类型的卷。 分层卷还原为分层卷，本地固定卷还原为本地固定卷。
10. 当卷在卷列表中显示为联机时，则表示该卷可供使用。  在 iSCSI 发起程序主机上，在 iSCSI 发起程序属性窗口中刷新目标的列表。  包含已还原的卷名的新目标在状态列下应显示为“非活动”状态。
11. 选择目标并单击“连接”。   发起程序连接到目标后，状态应更改为“已连接”。 
12. 在“ **磁盘管理** ”窗口中，已装载的卷将显示在以下所示的插图中。 右键单击“发现的卷”（单击磁盘名称），然后单击“ **联机**”。

> [!IMPORTANT]
> 尝试从备份集中还原卷或共享时，如果还原作业失败，在门户中可能仍然会创建目标卷或共享。 请务必删除门户中的此目标卷或共享，以在最大程度上减少此元素将引发的任何问题。
> 
> 

## <a name="item-level-recovery-ilr"></a>项目级恢复 (ILR)
此版本在配置为文件服务器的 StorSimple 虚拟设备上引入了项目级恢复 (ILR)。 该功能允许从 StorSimple 设备上的所有共享的云备份对文件和文件夹进行粒度恢复。 用户可使用自助服务模型从最近的备份中检索已删除的文件。

每个共享都有包含最新备份的 *.backups* 文件夹。 用户可导航到所需的备份，从备份中复制相关的文件和文件夹并进行还原。 这样无需调用管理员便可从备份中还原文件。

1. 执行 ILR 时，可通过 Windows 资源管理器查看备份。 单击想要为查看其备份的特定共享。 将在存储所有备份的共享下看到创建的 *.backups* 文件夹。 展开 *.backups* 文件夹以查看备份。 然后，该文件夹将显示整个备份层次结构的分解视图。 此视图是按需创建的，并且通常只需几秒钟的时间。
   
   最后 5 个备份以这种方式显示，并且可用于执行项目级恢复。 最近的 5 个备份包括默认计划的备份和手动备份。

    -   **计划备份**的名称为 &lt;设备名&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC。

    -   **手动备份** 的名称为 Ad-hoc-YYYYMMDD-HHMMSS-UTC。

        ![](./media/storsimple-ova-restore/image14.png)

1. 识别包含最新版本的已删除文件的备份。 尽管文件夹名称包含上述每种情况中的 UTC 时间戳，但该文件夹的创建时间是备份开始时的实际设备时间。 使用文件夹时间戳查找和标识备份。
2. 在上一步中标识的备份中查找想要还原的文件夹或文件。 请注意，用户只能查看对其具有权限的文件或文件夹。 如果不能访问某些文件或文件夹，则需要联系共享管理员，共享管理员可以使用 Windows 资源管理器来编辑共享权限并授予用户对特定文件或文件夹的访问权限。 建议的最佳做法是将共享管理员设置为用户组而非单个用户。
3. 将文件或文件夹复制到 StorSimple 文件服务器上的相应共享。

![video_icon](./media/storsimple-ova-restore/video_icon.png) **可用视频**

观看此视频以了解如何在 StorSimple Virtual Array 上创建共享、备份共享和还原数据。

> [！视频 https://channel9.msdn.com/Blogs/Windows-Azure/Use-the-StorSimple-Virtual-Array/player]
> 
> 

## <a name="next-steps"></a>后续步骤
了解有关如何[使用本地 Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md) 的详细信息。




<!--HONumber=Nov16_HO3-->


