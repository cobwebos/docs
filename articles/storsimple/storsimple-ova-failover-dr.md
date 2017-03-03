---
title: "StorSimple 虚拟阵列的灾难恢复和设备故障转移 | Microsoft 文档"
description: "了解有关如何对 StorSimple Virtual Array 进行故障转移的详细信息。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 94a8c3db-8e47-4e9a-917a-29b14a9263aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 78daa5a75b3414e2761333ea6ad91945596553c8
ms.openlocfilehash: 2017fcdc7a5c36c7c00d8bcef5be973ed60e3f82
ms.lasthandoff: 01/30/2017


---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>StorSimple Virtual Array 的灾难恢复和设备故障转移
## <a name="overview"></a>概述
本文介绍了 Microsoft Azure StorSimple Virtual Array（也称为 StorSimple 本地虚拟设备）的灾难恢复，包括在设备发生灾难时，对另一台虚拟设备进行故障转移所需的详细步骤。 使用故障转移可将数据中心内的*源*设备数据迁移到位于相同或不同地理位置的另一台*目标*设备。 设备故障转移适用于整个设备。 在故障转移期间，源设备的云数据将所有权更改为目标设备云数据。

设备故障转移通过灾难恢复 (DR) 功能进行协调，从“设备”页启动。 此页以表格形式列出与 StorSimple Manager 服务连接的所有 StorSimple 设备。 其中显示了每台设备的友好名称、状态、预配容量和最大容量、类型以及型号。

![](./media/storsimple-ova-failover-dr/image15.png)

本文仅适用于 StorSimple Virtual Arrays。 若要对 8000 系列设备进行故障转移，请转到 [StorSimple 设备的故障转移和灾难恢复](storsimple-device-failover-disaster-recovery.md)。

## <a name="what-is-disaster-recovery"></a>什么是灾难恢复？
在灾难恢复 (DR) 方案中，主要设备将停止工作。 在此情况下，可以使用主要设备作为*源*，指定其他设备作为*目标*，将与出现故障的设备关联的云数据移动到另一台设备。 此过程称为*故障转移*。 在故障转移期间，源设备中的所有卷或共享将改变所有权并会传输到目标设备。 不允许筛选数据。

使用基于热度地图的分层和跟踪，将 DR 建模为完整设备还原。 通过根据读取和写入模式将热度值分配给数据，定义热度地图。 然后，此热度地图将最低的热度数据区块分层到云优先，同时在本地层中保留高热度（最常用）数据区块。 在 DR 过程中，热度地图用于还原和解除冻结云中的数据。 设备提取最近备份中的所有卷/共享（如内部确定）并执行从该备份中还原操作。 整个 DR 过程通过设备进行协调。

## <a name="prerequisites-for-device-failover"></a>设备故障转移先决条件
### <a name="prerequisites"></a>先决条件
对于任何设备故障转移，应满足以下先决条件：

* 源设备需要处于“已停用”状态。
* 目标设备需要在 Azure 经典门户中显示为“活动”。 你将需要预配相同或更高容量的目标虚拟设备。 然后，应使用本地 Web UI 配置和成功注册虚拟设备。
  
  > [!IMPORTANT]
  > 不要尝试通过单击“完成设备设置”，通过该服务配置注册的虚拟设备。 不应通过该服务执行任何设备配置。
  > 
  > 
* 源和目标设备的类型必须相同。 你可以将配置为文件服务器的虚拟设备故障转移到另一个文件服务器。 这同样适用于 iSCSI 服务器。
* 对于文件服务器 DR，建议将目标设备加入与源域相同的域，以自动解析共享权限。 只有此版本才支持故障转移到相同域中的目标设备。

### <a name="other-considerations"></a>其他注意事项
* 建议使所有卷或共享在源设备上脱机。
* 如果它是计划的故障转移，建议你进行设备备份，然后继续执行故障转移以最大程度减少数据丢失。 如果它是未计划的故障转移，使用最近备份还原设备。
* DR 的可用目标设备是所含容量等于或大于源设备的设备。 虽然已连接到你的服务，但没有足够空间的设备不能用作目标设备。

### <a name="dr-prechecks"></a>DR 预检查
在 DR 开始之前，对设备执行预检查。 这些检查有助于确保在开始 DR 时不会发生任何错误。 预检查包括：

* 验证存储帐户
* 检查到 Azure 的云连接性
* 检查目标设备上的可用空间
* 检查 iSCSI 服务器源设备的与卷相关联的 ACR 名称、IQN（长度不超过 220 个字符）和 CHAP 密码（长度为 12 和 16 个字符）是否有效

如果上述任意预检查失败，都无法继续执行 DR。 需要解决这些问题，然后重试 DR。

成功完成 DR 后，源设备上的云数据所有权将转移到目标设备。 源设备不再可用于门户中。 对源设备上的所有卷/共享的访问权限将受阻止，目标设备会变为活动状态。

> [!IMPORTANT]
> 尽管该设备不再可用，但主机系统上预配的虚拟机仍会消耗资源。 成功完成 DR 后，可从主机系统中删除此虚拟机。
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>故障转移到 Virtual Array
建议你预配另一个 StorSimple Virtual Array、通过本地 Web UI 配置它、将它注册到 StorSimple Manager服务，然后再运行此过程。

> [!IMPORTANT]
> * 不允许从 StorSimple 8000 系列设备故障转移到 1200 虚拟设备。
> * 可以从在政府门户中部署的支持联邦信息处理标准 (FIPS) 的虚拟设备故障转移到 Azure 经典门户中的虚拟设备。 反之亦然。
> 
> 

执行以下步骤可将设备还原到目标 StorSimple 虚拟设备。

1. 使卷/共享在主机上脱机。 请参考有关使卷/共享在主机上脱机的特定于操作系统的说明。 如果尚未脱机，你需要通过转到“设备”>“共享”（或“设备”>“卷”）使所有卷/共享在设备上脱机。 选择共享/卷，然后单击页面底部的“实现脱机”。 出现确认提示时，单击“是”。 针对设备上的所有共享/卷重复此过程。
2. 在“设备”页上，选择源设备进行故障转移，然后单击“停用”。 
    ![](./media/storsimple-ova-failover-dr/image16.png)
3. 系统将提示进行确认。 设备停用是不可撤消的永久性过程。 还将提醒你使共享/卷在主机上脱机。
   
    ![](./media/storsimple-ova-failover-dr/image18.png)
4. 确认后，将启动停用操作。 停用操作成功完成后，你将收到通知。
   
    ![](./media/storsimple-ova-failover-dr/image19.png)
5. 在“设备”页上，设备状态现更改为“已停用”。
   
    ![](./media/storsimple-ova-failover-dr/image20.png)
6. 选择停用的设备，然后在页面底部单击“故障转移”。
7. 在打开的确认故障转移向导中，执行以下操作：
   
   1. 从可用设备的下拉列表中，选择“目标设备”。 下拉列表中只显示具有足够容量的设备。
   2. 查看与源设备相关联的详细信息，例如设备名称、总容量和将故障转移的共享的名称。
      
       ![](./media/storsimple-ova-failover-dr/image21.png)
8. 选中“我同意故障转移是一项永久性操作，且一旦故障转移成功完成，即删除源设备”。
9. 单击选中图标 ![](./media/storsimple-ova-failover-dr/image1.png)。
10. 故障转移作业将启动，你将收到通知。 单击“查看作业”，监视故障转移。
    
     ![](./media/storsimple-ova-failover-dr/image22.png)
11. 在“作业”页中，你将看到为源设备创建的故障转移作业。 此作业执行 DR 预检查。
    
    ![](./media/storsimple-ova-failover-dr/image23.png)
    
     成功完成 DR 预检查后，故障转移作业将为源设备上存在的每个共享/卷生成还原作业。
    
    ![](./media/storsimple-ova-failover-dr/image24.png)
12. 完成故障转移后，转到“设备”页。
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 为故障转移过程选择用作目标设备的 StorSimple 虚拟设备。
    
    b.保留“数据库类型”设置，即设置为“共享”。 转到“共享”页（或者，如果是 iSCSI 服务器，则转到“卷”页）。 现在应列出旧设备中的所有共享（卷）。
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png) **可用视频**

此视频演示了如何将 StorSimple 本地虚拟设备故障转移到另一台虚拟设备。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Virtual-Array-Disaster-Recovery/player]
> 
> 

## <a name="business-continuity-disaster-recovery-bcdr"></a>业务连续性灾难恢复 (BCDR)
当整个 Azure 数据中心停止运行时，将会启动业务连续性灾难恢复 (BCDR) 方案。 这可能会影响 StorSimple Manager 服务和关联的 StorSimple 设备。

如果 StorSimple 设备在发生灾难的前一刻才注册，可能需要删除这些 StorSimple 设备。 你可以重新创建和配置发生灾难的这些设备。

## <a name="errors-during-dr"></a>在 DR 期间出错
**在 DR 期间出现云连接中断问题**

如果云连接在 DR 启动后和设备还原完成之前中断，DR 将失败，你会收到通知。 然后，用于 DR 的目标设备将标记为“不可用”。 相同目标设备也无法用于将来的 DR。

**没有兼容的目标设备**

如果可用目标设备的空间不足，你将看到一个错误，指示没有兼容的目标设备。

**预检查失败**

如果不满足预检查之一，你将看到预检查失败。

## <a name="next-steps"></a>后续步骤
了解有关如何[使用本地 Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md) 的详细信息。


