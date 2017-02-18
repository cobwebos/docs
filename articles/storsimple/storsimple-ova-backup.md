---
title: "StorSimple Virtual Array 备份教程 | Microsoft Docs"
description: "介绍了如何备份 StorSimple Virtual Array 共享和卷。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: a4f55053-a664-4f7c-ba9d-0cb1fb200ff4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 9db591677fb529a72029114db8ad8a8b5d8ab260


---
# <a name="back-up-your-storsimple-virtual-array"></a>备份 StorSimple Virtual Array
## <a name="overview"></a>概述
本教程适用于运行 2016 年 3 月公开发行 (GA) 版或更高版本的 Microsoft Azure StorSimple Virtual Array（也称为 StorSimple 本地虚拟设备或 StorSimple 虚拟设备）。

StorSimple Virtual Array 是一种混合云存储本地虚拟设备，可以配置为文件服务器或 iSCSI 服务器。 它可以创建备份、从备份进行还原，以及在需要进行灾难恢复时执行设备故障转移。 当配置为文件服务器时，它还允许项目级恢复。 本教程介绍了如何使用 Azure 经典门户或 StorSimple Web UI 为 StorSimple Virtual Array 创建计划的和手动的备份。

## <a name="back-up-shares-and-volumes"></a>备份共享和卷
备份可提供时间点保护，并可提高可恢复性，同时最大限度地降低共享和卷的恢复时间。 可以采用两种方式备份 StorSimple 设备上的共享或卷：**按计划**或**手动**。 以下部分将介绍上述每种方法。

> [!NOTE]
> 在此发行版中，一个默认策略会创建计划的备份，该策略每天在指定的时间运行，并且会备份设备上的所有共享或卷。 目前不能为计划的备份创建自定义策略。
> 
> 

## <a name="change-the-backup-schedule"></a>更改备份计划
StorSimple 虚拟设备具有一个默认备份策略，该策略在每天的指定时间 (22:30) 开始，每天将设备上的所有共享或卷备份一次。 你可以更改备份的开始时间，但无法更改频率和保留（这指定要保留的备份数）。 在执行这些备份期间，将备份整个虚拟设备；因此，建议你将备份安排在非高峰时间进行。

可以在 [Azure 经典门户](https://manage.windowsazure.com/)中执行以下步骤来更改默认的备份开始时间。

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>更改默认备份策略的开始时间
1. 导航到设备“配置”选项卡。
2. 在“备份”部分下，指定每日备份的开始时间。
3. 单击“保存”。

### <a name="take-a-manual-backup"></a>创建手动备份
除了计划的备份之外，还可以随时创建手动（按需）备份。

#### <a name="to-create-a-manual-on-demand-backup"></a>创建手动（按需）备份
1. 导航到“共享”选项卡或“卷”选项卡。
2. 在页面底部，单击“全部备份”。 将会提示你确认是否要立即创建备份。 单击对号图标![对号图标](./media/storsimple-ova-backup/image3.png)以继续执行备份。
   
    ![备份确认](./media/storsimple-ova-backup/image4.png)
   
    将会通知你一个备份作业正在启动。
   
    ![备份正在启动](./media/storsimple-ova-backup/image5.png)
   
    将会通知你作业已成功创建。
   
    ![备份作业已创建](./media/storsimple-ova-backup/image7.png)
3. 若要跟踪作业的进度，请单击“查看作业”。
4. 备份作业完成后，请转到“备份目录”选项卡。 你应当会看到你的已完成备份。
   
    ![已完成的备份](./media/storsimple-ova-backup/image8.png)
5. 将筛选器选择设置为相应的设备、备份策略和时间范围，然后单击对号图标 ![对号图标](./media/storsimple-ova-backup/image3.png)。
   
    备份应显示在目录中所示的备份集列表中。

## <a name="view-existing-backups"></a>查看现有备份
可以在 Azure 经典门户中执行以下步骤来查看现有备份。

#### <a name="to-view-existing-backups"></a>查看现有备份
1. 在“StorSimple Manager 服务”页上，单击“备份目录”选项卡。
2. 如下所述选择一个备份集：
   
   1. 选择设备。
   2. 在下拉列表中，选择想要选择的备份的共享或卷。
   3. 指定时间范围。
   4. 单击对号图标 ![](./media/storsimple-ova-backup/image3.png) 以执行此查询。
      
      与所选的共享或卷关联的备份应出现在备份集的列表中。

![video_icon](./media/storsimple-ova-backup/video_icon.png) **可用视频**

观看此视频以了解如何在 StorSimple Virtual Array 上创建共享、备份共享和还原数据。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Use-the-StorSimple-Virtual-Array/player]
> 
> 

## <a name="next-steps"></a>后续步骤
详细了解如何[管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。




<!--HONumber=Jan17_HO5-->


