---
title: "克隆 StorSimple 8000 系列上的卷 | Microsoft Docs"
description: "介绍不同的克隆类型和用法，以及如何使用备份集克隆 StorSimple 8000 系列设备上的单个卷。"
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
ms.date: 07/10/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 40af4c3057d5689600b06cbcfb60fabb2674d842
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>在 Azure 门户中使用 StorSimple 设备管理器服务来克隆卷

## <a name="overview"></a>概述

本教程介绍如何通过“备份目录”边栏选项卡使用备份集来克隆单个卷。 并说明了“暂时性”克隆和“永久性”克隆的差异。 本教程中的指导适用于运行 Update 3 或更高版本的所有 StorSimple 8000 系列设备。

StorSimple 设备管理器服务的“备份目录”边栏选项卡显示手动或自动备份时创建的所有备份集。 然后，可以在某个备份集中选择要克隆的卷。

 ![备份集列表](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>有关克隆卷的注意事项

克隆卷时，请注意以下信息。

- 克隆的行为与复制普通的卷时相同。 可对卷执行的任何操作都可用于克隆。

- 克隆卷会自动禁用监视和默认备份功能。 需要针对任何备份配置克隆的卷。

- 本地固定卷作为分层卷克隆。 若需对克隆卷进行本地固定，可在成功完成克隆操作后将克隆转换为本地固定卷。 若要了解如何将分层卷转换为本地固定卷，请转到[更改卷类型](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)。

- 如果尝试在克隆后立即将克隆卷从分层卷转换为本地固定卷（此时仍为暂时性克隆），转换操作会失败，并出现以下错误消息：

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    此错误仅在克隆到不同设备时出现。 如果先将暂时性克隆转换为永久性克隆，则可成功地将卷转换为本地固定卷。 创建暂时性克隆的云快照可将它转换为永久性克隆。

## <a name="create-a-clone-of-a-volume"></a>创建卷的克隆

可以使用本地快照或云快照在相同的设备、不同的设备甚至云设备中创建克隆。

以下过程描述如何从备份目录创建克隆。  启动克隆的另一种方法是转到“卷”，选择一个卷，右键单击调用上下文菜单，选择“克隆”。

执行以下步骤从备份目录创建卷的克隆。

#### <a name="to-clone-a-volume"></a>克隆卷

1. 转到 StorSimple 设备管理器服务，单击“备份目录”。

2. 选择备份集，如下所示：
   
   1. 选择适当的设备。
   2. 在下拉列表中，选择想要选择的备份的卷或备份策略。
   3. 指定时间范围。
   4. 单击“应用”执行此查询。

    与所选的卷或备份策略相关联的备份应出现在备份集的列表中。
   
    ![备份集列表](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. 展开备份集以查看相关联的卷。 在还原这些卷之前，必须使其在主机和设备上脱机。 在设备的“卷”边栏选项卡上访问卷，遵循[使卷脱机](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)中的步骤使其脱机。
   
   > [!IMPORTANT]
   > 请确保在设备上使卷脱机之前，已在主机上使卷处于脱机状态。 如果未在主机上使卷脱机，则可能会导致数据损坏。
   
4. 导航回到“备份目录”，在备份集中选择一个卷。 单击右键，从上下文菜单中选择“克隆”。

   ![备份集列表](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. 在“克隆”边栏选项卡中执行以下步骤：
   
    1. 标识目标设备。 这是将在其中创建克隆的位置。 可以选择同一设备，也可以指定其他设备。

      > [!NOTE]
      > 确保该克隆所需的容量低于目标设备的可用容量。
       
    2. 指定克隆的唯一卷名称。 该名称必须包含 3 到 127 个字符。
      
        > [!NOTE]
        > 即使克隆的是本地固定卷，“将卷克隆为”字段也会是“分层卷”。 不能更改此设置；但是，如果也需对克隆卷进行本地固定，可在成功创建克隆后将克隆转换为本地固定卷。 若要了解如何将分层卷转换为本地固定卷，请转到[更改卷类型](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)。
          
    3. 在“连接的主机”下面，指定克隆的访问控制记录 (ACR)。 可以添加新的 ACR，或从现有列表中进行选择。 ACR 会确定哪些主机可以访问此克隆。
      
        ![备份集列表](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

    4. 单击“克隆”完成操作。

4. 此时会启动克隆作业，成功创建克隆后，系统会发出通知。 单击作业通知或转到“作业”边栏选项卡可监视克隆作业。

    ![备份集列表](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. 克隆作业完成后，请转到自己的设备，单击“卷”。 在卷列表中，应会看到刚刚创建的克隆出现在包含源卷的同一个卷容器中。

    ![备份集列表](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

通过此方式创建的克隆为暂时性克隆。 有关克隆类型的详细信息，请参阅[暂时性克隆与永久性克隆](#transient-vs.-permanent-clones)。


## <a name="transient-vs-permanent-clones"></a>暂时性克隆与永久性克隆
仅当克隆到另一个设备时，才会创建暂时性克隆。 可将特定卷从备份集克隆到 StorSimple 设备管理器所管理的其他设备。 暂时性克隆引用原始卷中的数据，并使用该数据在目标设备上进行本地读写操作。

对暂时性克隆执行云快照操作后，生成的克隆是“永久性”克隆。 在此过程中，将会在云中创建数据的副本，复制该数据的时间取决于数据大小和 Azure 延迟（此为 Azure 到 Azure 复制）。 此过程可能需要数天到数周的时间。 暂时性克隆成为永久性克隆，不再引用从其克隆的原始卷数据。

## <a name="scenarios-for-transient-and-permanent-clones"></a>进行暂时性克隆和永久性克隆的方案
以下部分描述了可以使用暂时性克隆和永久性克隆的示例方案。

### <a name="item-level-recovery-with-a-transient-clone"></a>使用暂时性克隆进行项目级恢复
你需要恢复一个一年前创建的 Microsoft PowerPoint 演示文稿文件。 IT 管理员确定从时间开始的特定备份，并筛选出相应的卷。 然后，该管理员对卷进行克隆，找到你要的文件，将其提供给你。 此方案使用暂时性克隆。

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>使用永久性克隆在生产环境中进行测试
你需要在生产环境中对测试 Bug 进行验证。 你在生产环境中创建了卷的克隆，然后对该克隆执行云快照操作，以便创建独立的克隆卷。 此方案使用永久性克隆。

## <a name="next-steps"></a>后续步骤
* 了解如何[从备份集还原 StorSimple 卷](storsimple-8000-restore-from-backup-set-u2.md)。
* 了解如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。


