---
title: "StorSimple Virtual Array 灾难恢复和设备故障转移 |Microsoft 文档"
description: "你的 StorSimple Virtual Array 了解有关如何故障转移的详细信息。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12079f8dbc409afe5acc274fa08bda878c90b76e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>灾难恢复和设备故障转移通过 Azure 门户在 StorSimple 虚拟数组

## <a name="overview"></a>概述
本文介绍在 Microsoft Azure StorSimple Virtual Array 包括的详细的步骤以故障转移到另一个虚拟数组灾难恢复。 故障转移，你可以移动你的数据从*源*到数据中心内的设备*目标*设备。 目标设备可能位于相同或不同的地理位置。 设备故障转移是为整个设备。 故障转移期间，源设备的云数据与目标设备更改所有权。

本文仅适用于 StorSimple 虚拟数组。 若要故障转移 8000 系列设备，请转到[StorSimple 设备的设备故障转移和灾难恢复](storsimple-device-failover-disaster-recovery.md)。

## <a name="what-is-disaster-recovery-and-device-failover"></a>什么是灾难恢复和设备故障转移？

在灾难恢复 (DR) 方案中，主要设备停止正常工作。 在此方案中，你可以移动到另一个设备故障的设备与关联的云数据。 你可以使用作为主设备*源*并指定为另一台设备*目标*。 此过程称为*故障转移*。 在故障转移，期间的所有卷或共享来自源设备更改所有权并被传输到目标设备。 允许未筛选数据。

DR 建模为完整的设备还原使用热度基于地图的分层和跟踪。 通过将热度值分配给基于的数据定义热度地图上读取和写入模式。 此热度然后层的最低热数据区块到云第一次时映射本地层中保持高 （最常使用） 的热数据区块。 在 DR 过程 StorSimple 使用热度地图还原和 rehydrate 从云中的数据。 设备 （如内部确定） 提取中的最后一个的新备份的所有的卷/共享数据并从该备份执行还原。 虚拟的数组可以协调整个灾难恢复过程。

> [!IMPORTANT]
> 因此不支持故障回复和设备故障转移结束时删除源设备。
> 
> 

灾难恢复进行协调通过设备故障转移功能和从启动**设备**边栏选项卡。 此边栏选项卡将连接到 StorSimple 设备管理器服务的所有 StorSimple 设备。 对于每个设备，你可以看到友好名称、 状态、 预配和最大容量、 类型和模型。

## <a name="prerequisites-for-device-failover"></a>设备故障转移的先决条件

### <a name="prerequisites"></a>必备条件

设备故障转移，请确保满足以下先决条件：

* 源设备需要在**已停用**状态。
* 在目标设备需要显示为**已准备好设置**在 Azure 门户中。 设置目标虚拟组的相同或更高的容量。 使用的本地 web UI 来配置和成功注册目标虚拟数组。
  
  > [!IMPORTANT]
  > 请勿尝试来配置通过服务的已注册虚拟设备。 通过服务不应该进行任何设备配置。
  > 
  > 
* 目标设备不能具有与源设备相同的名称。
* 源和目标设备必须是同一类型。 你可以仅故障转移到另一个文件服务器的文件服务器配置的虚拟阵列。 同样适用于的 iSCSI 服务器。
* 灾难恢复的文件服务器，我们建议将目标设备加入到与源相同的域。 此配置可确保，自动解决了共享权限。 仅故障转移到同一域中的目标设备。
* DR 的可用目标设备是具有相同或更大的容量，相比源设备的设备。 连接到你的服务但不满足的条件的足够空间的设备不可用为目标设备。

### <a name="other-considerations"></a>其他注意事项

* 计划的故障转移 
  
  * 我们建议在脱机源设备上执行所有的卷或共享。
  * 我们建议您设备的备份，然后继续执行故障转移程度地减少数据丢失。 
* 对于非计划的故障转移，设备使用最新备份来还原数据。

### <a name="device-failover-prechecks"></a>设备故障转移 prechecks

DR 开始之前，设备将执行 prechecks。 这些检查有助于确保 DR 便会开始时，会发生任何错误。 Prechecks 包括：

* 正在验证存储帐户。
* 正在检查到 Azure 云连接性。
* 正在检查目标设备上的可用空间。
* 正在检查是否有的 iSCSI 服务器源设备卷
  
  * 有效的 ACR 名称。
  * （不超过 220 字符） 的有效 IQN。
  * 有效 CHAP 密码 （长 12-16 个字符）。

如果上述任一 prechecks 失败，无法继续灾难恢复。 解决这些问题，然后重试灾难恢复。

灾难恢复成功完成后，源设备上的云数据的所有权被传输到目标设备。 源设备不再然后在门户中可用。 阻止对源设备上的所有卷/共享的访问，并且目标设备变为活动状态。

> [!IMPORTANT]
> 设备不再可用，但主机系统设置虚拟机仍会消耗资源。 灾难恢复成功完成后，你可以从主机系统中删除此虚拟机。
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>故障转移到的虚拟阵列

我们建议你设置、 配置和运行此过程之前先将另一个 StorSimple Virtual Array 注册你的 StorSimple 设备管理器服务。

> [!IMPORTANT]
> 
> * 你不能从故障转移 StorSimple 8000 系列设备到 1200年虚拟设备。
> * 你可以从故障转移联邦信息处理标准 (FIPS) 启用虚拟设备到另一台启用 FIPS 设备或部署在政府门户非 FIPS 设备。


执行以下步骤以将设备还原到目标 StorSimple 虚拟设备。

1. 设置和配置符合的目标设备[设备故障转移的先决条件](#prerequisites)。 完成设备配置通过本地 web UI 并将其注册到你的 StorSimple 设备管理器服务。 如果创建的文件服务器，请转到步骤 1 的[将设置为文件服务器](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device)。 如果创建的 iSCSI 服务器，请转到步骤 1 的[将设置为 iSCSI 服务器](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device)。

2. 在主机上执行脱机卷/共享。 若要使脱机卷/共享，请参考主机的操作系统特定说明。 如果已脱机，你需要通过执行以下设备上需要的所有卷/共享脱机。
   
    1. 转到**设备**边栏选项卡，然后选择你的设备。
   
    2. 转到**设置 > 管理 > 共享**(或**设置 > 管理 > 卷**)。 
   
    3. 选择一个共享/卷，右键单击并选择**脱机**。 
   
    4. 当系统提示确认，请检查**我了解此共享脱机的影响。** 
   
    5. 单击**脱机**。

3. 在你的 StorSimple 设备管理器服务，请转至**管理 > 设备**。 在**设备**边栏选项卡，选择，然后单击你的源设备。

4. 在你**设备仪表板**边栏选项卡，单击**停用**。

5. 在**停用**边栏选项卡，提示您进行确认。 设备停用是*永久*不能撤消的过程。 你还会提示需要在主机上的共享/卷脱机。 键入要确认，然后单击的设备名称**停用**。
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. 停用操作启动。 停用操作成功完成后，你将收到通知。
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. 在设备页上，设备状态现在将变为**已停用**。
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. 在**设备**边栏选项卡，选择，然后单击已停用的源设备进行故障转移。 
9. 在**设备仪表板**边栏选项卡，单击**故障转移**。 
10. 在**故障转移设备**边栏选项卡中，执行以下操作：
    
    1. 自动填充源设备字段。 请注意源设备的总数据大小。 数据大小应小于目标设备上的可用容量。 查看与源设备，例如设备名称、 总容量和故障转移的共享的名称关联的详细信息。

    2. 从可用设备下拉列表中，选择**目标设备**。 仅具有足够的容量的设备显示在下拉列表中。

    3. 检查**我了解此操作将故障转移到目标设备的数据**。 

    4. 单击**故障转移**。
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. 故障转移作业启动，并接收通知。 转到**设备 > 作业**以监视故障转移。
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. 在**作业**边栏选项卡，你将看到为源设备创建的故障转移作业。 此作业执行 DR prechecks。
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     DR prechecks 均可成功完成后，故障转移作业将生成源设备存在的每个共享/卷的恢复作业。
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. 在故障转移完成后，请转到**设备**边栏选项卡。
    
    1. 选择并单击已用作故障转移过程目标设备的 StorSimple 设备。
    2. 转到**设置 > 管理 > 共享**(或**卷**如果 iSCSI 服务器)。 在**共享**边栏选项卡，你可以查看所有共享 （卷） 从旧设备。
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. 你将需要[创建 DNS 别名](https://support.microsoft.com/kb/168322)，以便可以获取所有的应用程序尝试连接重定向到新设备。

## <a name="errors-during-dr"></a>在 DR 过程中的错误

**在 DR 过程的云连接中断**

如果云连接中断 DR 已启动并且设备前还原完成后，灾难恢复将失败。 你收到 failore 通知。 用于灾难恢复的目标设备标记为*不可用。* 不能为将来 DRs 使用相同的目标设备。

**没有兼容的目标设备**

如果可用目标设备没有足够的空间，你将看到错误没有兼容的目标设备的效果。

**Precheck 失败**

如果不满足 prechecks 之一，然后在看到 precheck 失败。

## <a name="business-continuity-disaster-recovery-bcdr"></a>业务连续性灾难恢复 (BCDR)

当整个 Azure 数据中心停止工作时，则会发生业务连续性灾难恢复 (BCDR) 方案。 这可能会影响你的 StorSimple 设备管理器服务和关联的 StorSimple 设备。

如果存在灾难发生之前刚注册的 StorSimple 设备，然后这些 StorSimple 设备可能需要删除。 在发生灾难之后, 你可以重新创建和配置这些设备。

## <a name="next-steps"></a>后续步骤

了解有关如何[管理使用的本地 web UI 你 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

