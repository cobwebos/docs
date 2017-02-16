---
title: "StorSimple 虚拟阵列灾难恢复和设备故障转移 | Microsoft 文档"
description: "了解有关如何对 StorSimple Virtual Array 进行故障转移的详细信息。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: c78eda33a64d630ba18aba6a174db372eb41dde9
ms.openlocfilehash: d31f10f660dc05290363825c089664a70ecdd037

---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>StorSimple Virtual Array 的灾难恢复和设备故障转移

## <a name="overview"></a>概述
本文介绍 Microsoft Azure StorSimple 虚拟阵列的灾难恢复，包括故障转移到其他虚拟阵列的详细步骤。 通过故障转移，可以将数据中心内*源*设备中的数据移到*目标*设备。 目标设备可以位于相同或不同的地理位置中。 设备故障转移适用于整个设备。 在故障转移期间，源设备的云数据将所有权更改为目标设备云数据。

本文仅适用于 StorSimple Virtual Arrays。 若要对 8000 系列设备进行故障转移，请转到 [StorSimple 设备的设备故障转移和灾难恢复](storsimple-device-failover-disaster-recovery.md)。

## <a name="what-is-disaster-recovery-and-device-failover"></a>什么是灾难恢复和设备故障转移？

在灾难恢复 (DR) 方案中，主要设备将停止工作。 在此方案中，可以将与故障设备关联的云数据移到另一个设备。 可以将主设备用作*源*并指定另一台设备作为*目标*。 此过程称为*故障转移*。 在故障转移期间，源设备中的所有卷或共享将改变所有权并会传输到目标设备。 不允许筛选数据。

使用基于热度地图的分层和跟踪，将 DR 建模为完整设备还原。 通过根据读取和写入模式将热度值分配给数据，定义热度地图。 然后，此热度地图将最低的热度数据区块分层到云优先，同时在本地层中保留高热度（最常用）数据区块。 在 DR 过程中，StorSimple 使用热度地图还原和解除冻结云中的数据。 设备提取最近备份中的所有卷/共享（如内部确定）并执行从该备份中还原操作。 虚拟阵列可以协调整个 DR 过程。

> [!IMPORTANT]
> 设备故障转移结束时会删除源设备，因此不支持故障回复。
> 
> 

灾难恢复通过设备故障转移功能进行协调，并从“设备”边栏选项卡启动。 此边栏选项卡以表格形式列出与 StorSimple Device Manager 服务连接的所有 StorSimple 设备。 对于每台设备，可以看到友好名称、状态、预配容量和最大容量、类型以及型号。

## <a name="prerequisites-for-device-failover"></a>设备故障转移先决条件

### <a name="prerequisites"></a>先决条件

对于设备故障转移，请确保满足以下先决条件：

* 源设备需要处于“已停用”状态。
* 目标设备需要在 Azure 门户中显示为“已准备好设置”。 预配相同或更高容量的目标虚拟阵列。 使用本地 Web UI 配置和成功注册目标虚拟阵列。
  
  > [!IMPORTANT]
  > 不要尝试通过服务配置已注册的虚拟设备。 不应通过该服务执行任何设备配置。
  > 
  > 
* 目标设备不能具有与源设备相同的名称。 故障转移完成后，始终可以重命名目标设备。
* 源和目标设备的类型必须相同。 只能将配置为文件服务器的虚拟阵列故障转移到另一个文件服务器。 这同样适用于 iSCSI 服务器。
* 对于文件服务器 DR，我们建议将目标设备加入到与源相同的域。 此配置可确保自动解决共享权限问题。 仅限故障转移到相同域中的目标设备。
* DR 的可用目标设备是所含容量等于或大于源设备的设备。 已连接到你的服务，但没有足够空间的设备不能用作目标设备。

### <a name="other-considerations"></a>其他注意事项

* 对于计划的故障转移 
  
  * 建议使所有卷或共享在源设备上脱机。
  * 建议你先进行设备备份，然后再继续执行故障转移以最大程度减少数据丢失。 
* 对于未计划的故障转移，设备使用最近备份还原数据。

### <a name="device-failover-prechecks"></a>设备故障转移预检查

在 DR 开始之前，设备将执行预检查。 这些检查可确保在开始 DR 时不会发生任何错误。 预检查包括：

* 验证存储帐户。
* 检查与 Azure 的云连接。
* 检查目标设备上的可用空间。
* 检查 iSCSI 服务器源设备卷是否具有
  
  * 有效 ACR 名称。
  * 有效 IQN（不超过 220 个字符）。
  * 有效 CHAP 密码（长并为&12;-16 个字符）。

如果上述任一预检查失败，都无法继续执行 DR。 需要解决这些问题，然后重试 DR。

成功完成 DR 后，源设备上的云数据所有权将转移到目标设备。 源设备不再可用于门户中。 对源设备上的所有卷/共享的访问权限将受阻止，目标设备会变为活动状态。

> [!IMPORTANT]
> 尽管该设备不再可用，但主机系统上预配的虚拟机仍会消耗资源。 成功完成 DR 后，可从主机系统中删除此虚拟机。
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>故障转移到 Virtual Array

我们建议你在运行此过程之前，先预配、配置另一个 StorSimple 虚拟阵列并将其注册到 StorSimple Device Manager 服务。

> [!IMPORTANT]
> 
> * 不能从 StorSimple 8000 系列设备故障转移到 1200 虚拟设备。
> * 可以从已启用联邦信息处理标准 (FIPS) 的虚拟设备故障转移到另一台已启用 FIPS 的设备或故障转移到政府门户中部署的非 FIPS 设备。


执行以下步骤可将设备还原到目标 StorSimple 虚拟设备。

1. 预配并配置符合[设备故障转移先决条件](#prerequisites)的目标设备。 通过本地 Web UI 完成设备配置并将设备注册到 StorSimple Device Manager 服务。 如果要创建文件服务器，请转到[设置为文件服务器](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device)的步骤 1。 如果要创建 iSCSI 服务器，请转到[设置为 iSCSI 服务器](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device)的步骤 1。

2. 使卷/共享在主机上脱机。 若要使卷/共享脱机，请参考主机的操作系统特定说明。 如果尚未脱机，则需要通过执行以下操作使设备上的所有卷/共享脱机。
   
    1. 转到“设备”边栏选项卡，并选择你的设备。
   
    2. 转到“设置”>“管理”>“共享”（或“设置”>“管理”>“卷”）。 
   
    3. 选择一个共享/卷，右键单击它，然后选择“脱机”。 
   
    4. 当系统提示确认时，选中“我了解使此共享脱机的影响”。 
   
    5. 单击“脱机”。

3. 在 StorSimple Device Manager 服务中，转到“管理”>“设备”。 在“设备”边栏选项卡中，选择并单击源设备。

4. 在“设备仪表板”****边栏选项卡中，单击“停用”。

5. 在“停用”边栏选项卡中，系统将提示你进行确认。 设备停用是不可撤消的*永久性*过程。 还将提醒你使主机上的共享/卷脱机。 键入设备名称以确认，然后单击“停用”。
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. 此时停用操作将启动。 停用操作成功完成后，你将收到通知。
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. 在“设备”页上，设备状态现在将更改为“已停用”。
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. 在“设备”边栏选项卡上，选择并单击要故障转移的已停用源设备。 
9. 在“设备仪表板”边栏选项卡中，单击“故障转移”。 
10. 在“故障转移设备”边栏选项卡中，执行以下操作：
    
    1. 将自动填充源设备字段。 请注意源设备的总数据大小。 此数据大小应小于目标设备上的可用容量。 查看与源设备关联的详细信息，例如设备名称、总容量和将故障转移的共享的名称。

    2. 从可用设备的下拉列表中，选择“目标设备”。 下拉列表中只显示具有足够容量的设备。

    3. 选中“我了解此操作会将数据故障转移到目标设备”。 

    4. 单击“故障转移”。
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. 故障转移作业将启动，你将收到通知。 转到“设备”>“作业”以监视故障转移。
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. 在“作业”边栏选项卡中，你将看到为源设备创建的故障转移作业。 此作业执行 DR 预检查。
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     成功完成 DR 预检查后，故障转移作业将为源设备上存在的每个共享/卷生成还原作业。
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. 故障转移完成后，转到“设备”边栏选项卡。
    
    1. 选择并单击在故障转移过程中用作目标设备的 StorSimple 设备。
    2. 转到“设置”>“管理”>“共享”或“卷”（如果是 iSCSI 服务器）。 在“共享”边栏选项卡中，可以查看旧设备上的所有共享（卷）。
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. 现在可以重命名设备（与旧的源设备相同），以便应用程序服务器可以直接连接到此设备。 如果不想重命名设备，则需要[创建 DNS 别名](https://support.microsoft.com/kb/168322)，使要尝试连接的所有应用程序重定向到新设备。

## <a name="errors-during-dr"></a>在 DR 期间出错

**在 DR 期间出现云连接中断问题**

如果云连接在 DR 启动后和设备还原完成之前中断，DR 将失败。 你会收到失败通知。 用于 DR 的目标设备将标记为“不可用” 。 不能将同一目标设备用于将来的 DR。

**没有兼容的目标设备**

如果可用目标设备的空间不足，你将看到一个错误，指示没有兼容的目标设备。

**预检查失败**

如果不满足其中一个预检查，则将看到预检查失败。

## <a name="business-continuity-disaster-recovery-bcdr"></a>业务连续性灾难恢复 (BCDR)

当整个 Azure 数据中心停止运行时，将会启动业务连续性灾难恢复 (BCDR) 方案。 这可能会影响 StorSimple Device Manager 服务和关联的 StorSimple 设备。

如果 StorSimple 设备在发生灾难的前一刻才注册，可能需要删除这些 StorSimple 设备。 你可以重新创建和配置发生灾难的这些设备。

## <a name="next-steps"></a>后续步骤

了解有关如何[使用本地 Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md) 的详细信息。




<!--HONumber=Jan17_HO4-->


