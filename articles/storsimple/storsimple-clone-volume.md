---
title: "克隆 StorSimple 卷 | Microsoft Docs"
description: "介绍不同的克隆类型和使用时机，以及如何使用备份集克隆单个卷。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b5d615f2-02a7-4222-9867-6c0385ce748c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 09b67ca9258a490338d29763710c40ea2da23ce7
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>使用 StorSimple Manager 服务克隆卷
[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>概述
StorSimple Manager 服务的“备份目录”页面显示手动或自动备份时创建的所有备份集。 可以使用此页面列出备份策略或卷的所有备份、选择或删除备份，或使用备份来还原或克隆卷。

![“备份目录”页](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

本教程介绍如何使用备份集克隆单个卷， 并说明了“暂时性”克隆和“永久性”克隆的差异。 

## <a name="create-a-clone-of-a-volume"></a>创建卷的克隆
可以使用本地快照或云快照在相同的设备、不同的设备甚至虚拟机中创建克隆。

#### <a name="to-clone-a-volume"></a>克隆卷
1. 在“StorSimple Manager 服务”页上单击“备份目录”选项卡，然后选择备份集。
2. 展开备份集以查看相关联的卷。 单击并选择备份集中的卷。
   
     ![克隆卷](./media/storsimple-clone-volume/HCS_Clone.png) 
3. 单击“克隆”开始克隆所选卷。
4. 在克隆卷向导的“指定名称和位置”下执行以下操作：
   
   1. 标识目标设备。 这是将在其中创建克隆的位置。 可以选择同一设备，也可以指定其他设备。 如果选择与其他云服务提供商（不是 Azure）关联的卷，则目标设备的下拉列表仅显示物理设备。 不能在虚拟设备上克隆与其他云服务提供商关联的卷。
      
      > [!NOTE]
      > 确保该克隆所需的容量低于目标设备的可用容量。
      > 
      > 
   2. 指定克隆的唯一卷名称。 该名称必须包含 3 到 127 个字符。
   3. 单击箭头图标  ![arrow-icon](./media/storsimple-clone-volume/HCS_ArrowIcon.png) 转到下一页。
5. 在“指定可使用此卷的主机”下，执行以下操作：
   
   1. 指定克隆的访问控制记录 (ACR)。 可以添加新的 ACR，或从现有列表中进行选择。
   2. 单击选中图标  ![check-icon](./media/storsimple-clone-volume/HCS_CheckIcon.png)完成此操作。
6. 此时会启动克隆作业，克隆成功创建后会进行通知。 单击“查看作业”，监视“作业”页上的克隆作业。
7. 克隆作业完成后：
   
   1. 转到“设备”页，选择“卷容器”选项卡。 
   2. 选择与克隆的源卷关联的卷容器。 卷列表中会显示刚创建的克隆。

> [!NOTE]
> 克隆卷会自动禁用监视和默认备份功能。
> 
> 

通过此方式创建的克隆为暂时性克隆。 有关克隆类型的详细信息，请参阅[暂时性克隆与永久性克隆](#transient-vs.-permanent-clones)。

此克隆现在为常规卷，可对其执行适用于卷的任何操作。 若要进行备份，需对此卷进行相应配置。

## <a name="transient-vs-permanent-clones"></a>暂时性克隆与永久性克隆
仅在克隆到其他设备时才创建暂时性克隆和永久性克隆。 可以将特定卷从备份集克隆到其他设备。 通过此方式创建的克隆为“暂时性”克隆。 暂时性克隆会引用原始卷，并会使用该卷进行本地读写操作。 

对暂时性克隆执行云快照操作以后，生成的克隆将是“永久性”克隆。 永久性克隆是独立的，不引用从其克隆的原始卷。  

## <a name="scenarios-for-transient-and-permanent-clones"></a>进行暂时性克隆和永久性克隆的方案
以下部分描述了可以使用暂时性克隆和永久性克隆的示例方案。

### <a name="item-level-recovery-with-a-transient-clone"></a>使用暂时性克隆进行项目级恢复
你需要恢复一个一年前创建的 Microsoft PowerPoint 演示文稿文件。 IT 管理员确定了该时间范围内的特定备份，并筛选出相应的卷。 然后，该管理员对卷进行克隆，找到你要的文件，将其提供给你。 此方案使用暂时性克隆。 

![可用视频](./media/storsimple-clone-volume/Video_icon.png) **可用视频**

若要观看演示如何在 StorSimple 中使用克隆和还原功能来恢复已删除文件的视频，请单击[此处](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)。

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>使用永久性克隆在生产环境中进行测试
你需要在生产环境中对测试 Bug 进行验证。 你在生产环境中创建卷的克隆，对该克隆执行云快照操作。 克隆的卷现在是独立的。 此方案使用永久性克隆。

## <a name="next-steps"></a>后续步骤
* 了解如何[从备份集还原 StorSimple 卷](storsimple-restore-from-backup-set.md)。
* 了解如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。


