---
title: StorSimple 虚拟阵列 Update 1.1 发行说明 | Microsoft Docs
description: 介绍了 StorSimple 虚拟阵列运行 Update 1.1 时遇到的重要待解决问题和解决方法。
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: alkohli
ms.openlocfilehash: d73f45ec5ff1ffbe207fc45a1f87dcbe4f8ff021
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347631"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>StorSimple 虚拟阵列 Update 1.1 发行说明

## <a name="overview"></a>概述

以下发行说明标识了 Microsoft Azure StorSimple 虚拟阵列更新待解决的重要问题和已解决的问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 部署 StorSimple 虚拟阵列之前，请仔细查看发行说明中包含的信息。

Update 1.1 对应于软件版本 **10.0.10307.0**。

> [!IMPORTANT]
> - 更新会造成中断，并且会使设备重新启动。 如果正在进行 I/O，则会导致设备停机。 有关如何应用此更新的详细说明，请转到[安装 Update 1.1](storsimple-virtual-array-install-update-11.md)。
>
> - 如果设备正在运行 Update 1.0，则仅可通过 Azure 门户获取 Update 1.1。

## <a name="whats-new-in-update-11"></a>Update 1.1 中的新增功能

此更新包含以下增强功能和 bug 修复：

 - **备份故障** - 通过提高对云故障和 CPU 消耗量偏高的复原能力进行了改进。
 - **日志记录** - 对设备处于支持会话时的详细模式日志记录进行了更改。


## <a name="issues-fixed-in-update-11"></a>Update 1.1 中修复的问题

下表提供了此版本中所修复问题的摘要。

| 不是。 | 功能 | 问题 |
| --- | --- | --- |
| 1 |备份| 此版本包含通过提高对云故障和 CPU 消耗量偏高的复原能力针对备份故障进行了改进的更改。|
| 2 |日志记录| 此版本包含对设备处于支持会话时的详细模式日志记录的更改。|


## <a name="known-issues-in-update-11"></a>Update 1.1 中的已知问题

下表提供了 StorSimple 虚拟阵列的已知问题的摘要，包括早期版本中发行说明的问题。

| 不是。 | 功能 | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
| **1.** |更新 |无法将预览版中创建的虚拟阵列更新为受支持的正式版本。 |必须使用灾难恢复 (DR) 工作流将这些虚拟阵列故障转移为正式版本。 |
| **2.** |预配的数据磁盘 |在预配指定大小的数据磁盘，并创建相应的 StorSimple 虚拟阵列后，不能立即扩展或收缩数据磁盘。 尝试进行这些操作将导致该设备本地层中的所有数据丢失。 | |
| **3.** |组策略 |当设备已加入域时，应用组策略反而会影响设备操作。 |对于 Active Directory，请确保虚拟数组位于其自身的组织单位 (OU) 中，并且没有对其应用任何组策略对象 (GPO)。 |
| **4.** |本地 Web UI |如果在 Internet Explorer 中启用了增强安全功能 (IE ESC)，则可能会导致“故障排除”或“维护”等某些本地 Web UI 页面无法正常工作。 这些页面上的按钮也可能无法工作。 |请在 Internet Explorer 中关闭增强安全功能。 |
| **5.** |本地 Web UI |在 Hyper-V 虚拟机中，Web UI 中的网络接口显示为 10 Gbps 接口。 |此行为是 Hyper-V 的反射。 Hyper-V 始终为虚拟网络适配器显示 10 Gbps。 |
| **6.** |分层卷或共享 |不支持对适用于 StorSimple 分层卷的应用程序进行字节范围锁定。 如果启用了字节范围锁定，则 StorSimple 分层将不起作用。 |建议的措施包括： <br></br>在应用程序逻辑中关闭字节范围锁定。<br></br>选择将此应用程序的数据放置在本地固定卷中，而不是分层卷中。<br></br>*注意事项*：当使用本地固定卷并且已启用字节范围锁定时，即使在还原完成之前，本地固定卷也可以处于联机状态。 在这种情况下，如果正在进行还原，则必须等待还原完成。 |
| **7.** |分层共享 |处理大型文件可能会导致分层速度变慢。 |处理大型文件时，建议最大的文件应小于共享大小的 3%。 |
| **8.** |共享的已用容量 |即使共享上没有任何数据时，也可能会看到共享消耗。 出现此消耗是因为共享的已用容量中包括元数据。 | |
| **9.** |灾难恢复 |只能将文件服务器灾难恢复到源设备所在的同一域中。 此版本中不支持灾难恢复到其他域中的目标设备。 |这会在更高版本中实现。 有关详细信息，请转到 [StorSimple 虚拟阵列的故障转移和灾难恢复](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |在此版本中，不可以通过 Azure PowerShell 管理 StorSimple 虚拟阵列。 |所有对虚拟设备的管理应通过 Azure 门户和本地 Web UI 进行。 |
| **11.** |密码更改 |虚拟阵列设备控制台仅接受以英语（美国）键盘格式输入。 | |
| **12.** |CHAP |CHAP 凭据一旦创建之后便无法删除。 此外，如果要修改 CHAP 凭据，首先需要使这些卷脱机，再使它们联机，以使更改生效。 |此问题会在更高版本中得以解决。 |
| **13.** |iSCSI 服务器 |在 StorSimple 设备管理器服务和 iSCSI 主机中，显示的 iSCSI 卷“已用存储”可能有所不同。 |iSCSI 主机具有“文件系统”视图。<br></br>设备能够查看当卷处于最大大小时所分配的快。 |
| **14.** |文件服务器 |如果文件夹中的文件具有与其关联的备用数据流 (ADS)，则不会通过灾难恢复、克隆和项目级恢复对该 ADS 进行备份或还原。 | |
| **15.** |文件服务器 |不支持符号链接。 | |
| **16.** |文件服务器 |将受 Windows 加密文件系统 (EFS) 保护的文件复制或存储到 StorSimple 虚拟阵列文件服务器时会导致不受支持的配置。  | |
| 17. |更新 |如果尝试通过本地 UI 安装修补程序时出现错误代码：2359302（十六进制 0x240006），则表示设备上已安装有该修补程序。   | |
| **18.** |更新 |如果使用本地 Web UI 在虚拟阵列上安装 Update 1，必须确保你运行的是 Update 0.6。 如果你运行的版本低于 Update 0.6，则必须先安装 Update 0.6，然后应用 Update 1。 如果直接从 Update 0.6 之前的版本安装 Update 1.0，则会丢失某些更新，监视图表将不会工作。   | |


## <a name="next-steps"></a>后续步骤
在 StorSimple 虚拟阵列上安装 [Update 1.1](storsimple-virtual-array-install-update-11.md)。

## <a name="references"></a>参考
查找较旧版本的发行说明？ 请转到：
* [StorSimple 虚拟阵列 Update 1.0 发行说明](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple 虚拟阵列 Update 0.6 发行说明](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple 虚拟阵列更新 0.5 发行说明](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple 虚拟阵列更新 0.4 发行说明](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple 虚拟阵列更新 0.3 发行说明](storsimple-ova-update-03-release-notes.md)
* [StorSimple 虚拟阵列更新 0.1 和 0.2 发行说明](storsimple-ova-update-01-release-notes.md)
* [StorSimple 虚拟阵列正式版发行说明](storsimple-ova-pp-release-notes.md)
