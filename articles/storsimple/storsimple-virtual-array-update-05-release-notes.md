---
title: "StorSimple 虚拟阵列更新 0.5 发行说明 | Microsoft Docs"
description: "介绍运行更新 0.5 的 StorSimple 虚拟阵列的重要待解决问题和解决方案。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4d020ff2b998da4cb52fe91e4d7d4b93544965a8
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>StorSimple 虚拟阵列更新 0.5 发行说明

## <a name="overview"></a>概述

以下发行说明标识了 Microsoft Azure StorSimple Virtual Array 更新待解决的重要问题和已解决的问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 部署 StorSimple Virtual Array 之前，请仔细查看发行说明中包含的信息。

更新 0.5 对应软件版本 10.0.10290.0。

> [!NOTE]
> 更新会造成中断，并且会使设备重新启动。 如果正在进行 I/O，则会导致设备停机。 有关如何应用此更新的详细说明，请转到[安装 Update 0.5](storsimple-virtual-array-install-update-05.md)。


## <a name="whats-new-in-the-update-05"></a>更新 0.5 中的新增内容
更新 0.5 主要是修复 Bug 的版本。 主要改进和 bug 修复如下所示：

- 备份复原能力改进 - 此版本有改进备份复原能力的修补程序。 在早期版本中，仅在出现特定异常时重试备份。 此版本将重试所有备份异常，增强备份复原能力。

- 对存储使用情况监视的更新 - 自 2017 年 6 月 30 日起，对 StorSimple 虚拟设备系列的存储使用情况监视将会停用。 这适用于运行更新 0.4 或更低版本的所有虚拟阵列上的监视图表。 此更新包含在 Azure 门户中继续使用存储使用情况监视所需的更改。 请在 2017 年 6 月 30 日前安装此重要更新，以便继续使用监视功能。


## <a name="issues-fixed-in-the-update-05"></a>更新 0.5 中修复的问题

下表提供了此版本中所修复问题的摘要。

| 否。 | 功能 | 问题 |
| --- | --- | --- |
| 1 |备份复原能力| 在早期版本中，仅在出现特定异常时重试备份。 此版本包含一个修补程序，它通过重试所有备份异常来增强备份复原能力。|
| 2 |监视| 2017 年 6 月 30 起将弃用对 StorSimple 虚拟设备系列的存储使用情况监视。 此操作会影响在 StorSimple 虚拟阵列（1200 型）上运行的 StorSimple 设备管理器服务上的监视图表。 此版本包含允许用户在 2017 年 6 月 30 日后对虚拟阵列继续使用存储使用情况监视的更新。|
| 3 |文件服务器| 在早期版本中，用户可能误将加密文件复制到虚拟阵列。 此版本包含不允许将加密文件复制到虚拟阵列的修补程序。 如果设备上已有早于此更新的加密文件，则从系统中删除加密文件前，备份将继续失败。 |


## <a name="known-issues-in-the-update-05"></a>更新 0.5 中的已知问题

下表提供了 StorSimple Virtual Array 的已知问题的摘要，包括早期版本中发行说明的问题。

| 否。 | 功能 | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
| **1.** |更新 |无法将预览版中所创建的虚拟设备更新为受支持的正式版本。 |必须使用灾难恢复 (DR) 工作流将这些虚拟设备故障转移为正式版本。 |
| **2.** |预配的数据磁盘 |在预配指定大小的数据磁盘，并创建相应的 StorSimple 虚拟设备后，不能立即扩展或收缩数据磁盘。 尝试进行这些操作将导致该设备本地层中的所有数据丢失。 | |
| **3.** |组策略 |当设备已加入域时，应用组策略反而会影响设备操作。 |对于 Active Directory，请确保虚拟数组位于其自身的组织单位 (OU) 中，并且没有对其应用任何组策略对象 (GPO)。 |
| **4.** |本地 Web UI |如果在 Internet Explorer 中启用了增强安全功能 (IE ESC)，则可能会导致“故障排除”或“维护”等某些本地 Web UI 页面无法正常工作。 这些页面上的按钮也可能无法工作。 |请在 Internet Explorer 中关闭增强安全功能。 |
| **5.** |本地 Web UI |在 Hyper-V 虚拟机中，Web UI 中的网络接口显示为 10 Gbps 接口。 |此行为是 Hyper-V 的反射。 Hyper-V 始终为虚拟网络适配器显示 10 Gbps。 |
| **6.** |分层卷或共享 |不支持对适用于 StorSimple 分层卷的应用程序进行字节范围锁定。 如果启用了字节范围锁定，则 StorSimple 分层将不起作用。 |建议的措施包括： <br></br>在应用程序逻辑中关闭字节范围锁定。<br></br>选择将此应用程序的数据放置在本地固定卷中，而不是分层卷中。<br></br>*注意事项*：当使用本地固定卷并且已启用字节范围锁定时，即使在还原完成之前，本地固定卷也可以处于联机状态。 在这种情况下，如果正在进行还原，则必须等待还原完成。 |
| **7.** |分层共享 |处理大型文件可能会导致分层速度变慢。 |处理大型文件时，建议最大的文件应小于共享大小的 3%。 |
| **8.** |共享的已用容量 |即使共享上没有任何数据时，也可能会看到共享消耗。 出现此消耗是因为共享的已用容量中包括元数据。 | |
| **9.** |灾难恢复 |只能将文件服务器灾难恢复到源设备所在的同一域中。 此版本中不支持灾难恢复到其他域中的目标设备。 |这将在更高版本中实现。 有关详细信息，请转到 [StorSimple 虚拟阵列的故障转移和灾难恢复](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |在此版本中，不可以通过 Azure PowerShell 管理 StorSimple 虚拟设备。 |所有对虚拟设备的管理应通过 Azure 门户和本地 Web UI 进行。 |
| **11.** |密码更改 |虚拟阵列设备控制台仅接受以英语（美国）键盘格式输入。 | |
| **12.** |CHAP |CHAP 凭据一旦创建之后便无法删除。 此外，如果要修改 CHAP 凭据，首先需要使这些卷脱机，然后再使它们联机，以使更改生效。 |此问题将在更高版本中得以解决。 |
| **13.** |iSCSI 服务器 |在 StorSimple 设备管理器服务和 iSCSI 主机中，显示的 iSCSI 卷“已用存储”可能有所不同。 |iSCSI 主机具有“文件系统”视图。<br></br>设备能够查看当卷处于最大大小时所分配的快。 |
| **14.** |文件服务器 |如果文件夹中的文件具有与其关联的备用数据流 (ADS)，则不会通过灾难恢复、克隆和项目级恢复对该 ADS 进行备份或还原。 | |
| **15.** |文件服务器 |不支持符号链接。 | |
| **16.** |文件服务器 |将受 Windows 加密文件系统 (EFS) 保护的文件复制或存储到 StorSimple 虚拟阵列文件服务器时将导致不受支持的配置。  | |

## <a name="next-step"></a>后续步骤
在 StorSimple 虚拟阵列上[安装更新 0.5](storsimple-virtual-array-install-update-05.md)。

## <a name="references"></a>参考
查找较旧版本的发行说明？ 请转到：

* [StorSimple 虚拟阵列更新 0.4 发行说明](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 发行说明](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array 更新 0.1 和 0.2 发行说明](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式版发行说明](storsimple-ova-pp-release-notes.md)


