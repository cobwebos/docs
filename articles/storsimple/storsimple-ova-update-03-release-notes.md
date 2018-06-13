---
title: StorSimple Virtual Array 更新发行说明 | Microsoft Docs
description: 介绍有关运行更新 0.3 的 StorSimple Virtual Array 待解决的重要问题和解决方案。
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: fe9d4f6b232e9abcf1fe9fc5657044b6c72fedb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23109335"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple Virtual Array 更新 0.3 发行说明
## <a name="overview"></a>概述
以下发行说明标识了 Microsoft Azure StorSimple Virtual Array 更新待解决的重要问题和已解决的问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 部署 StorSimple Virtual Array 之前，请仔细查看发行说明中包含的信息。

更新 0.3 对应软件版本 **10.0.10288.0**。

> [!NOTE]
> 更新会造成中断，并且会使设备重新启动。 如果正在进行 I/O，则会导致设备停机。
> 
> 

## <a name="whats-new-in-the-update-03"></a>更新 0.3 中的新增内容
更新 0.3 主要是修复 bug 的版本。 在此版本中，已解决在早期版本中导致备份失败的多个 bug。

## <a name="issues-fixed-in-the-update-03"></a>更新 0.3 中修复的问题
下表提供了此版本中所修复问题的摘要。

| 否。 | 功能 | 问题 |
| --- | --- | --- |
| 1 |备份 |早期版本中出现了无法完成备份以进行文件共享的问题。 如果发生此问题，备份作业会失败，并且会在 StorSimple Manager 服务中引发严重性警报以通知用户。 此问题不会对共享上的数据或对数据的访问产生影响。 在此版本中识别并修复了其根本原因。 <br></br> 修补程序不会以追溯的方式应用于已出现此问题的共享。 如果客户遇到这个问题，应先应用 Update 0.3，再联系 Microsoft 支持部门执行完整系统备份，从而解决此问题。 除了与 Microsoft 支持联系，客户还可以将受影响的共享从运行状况良好的备份还原为新的共享。 |
| #N/A |iSCSI |早期版本中出现了将数据复制到 StorSimple Virtual Array 上的卷时，卷会消失的问题。 在此版本中已修复了此问题。 <br></br> 修补程序仅对新创建的卷有效。 修补程序不会以追溯的方式应用于已出现此问题的卷。 建议客户通过 Azure 经典门户使受影响的卷联机，为这些卷执行备份，然后将这些卷还原为新卷。 |

## <a name="known-issues-in-the-update-03"></a>更新 0.3 中的已知问题
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
| **8.** |共享的已用容量 |即使共享上没有任何数据时，也可能会看到共享消耗。 这是因为共享的已用容量中包括元数据。 | |
| **9.** |灾难恢复 |只能将文件服务器灾难恢复到源设备所在的同一域中。 此版本中不支持灾难恢复到其他域中的目标设备。 |这会在更高版本中实现。 |
| **10.** |Azure PowerShell |在此版本中，不可以通过 Azure PowerShell 管理 StorSimple 虚拟设备。 |所有对虚拟设备的管理应通过 Azure 经典门户和本地 Web UI 进行。 |
| **11.** |密码更改 |虚拟数组设备控制台仅接受 en-US 键盘格式的输入。 | |
| **12.** |CHAP |CHAP 凭据一旦创建之后便无法删除。 此外，如果要修改 CHAP 凭据，首先需要使这些卷脱机，再使它们联机，以使更改生效。 |此问题会在更高版本中得以解决。 |
| **13.** |iSCSI 服务器 |在 StorSimple Manager服务和 iSCSI 主机中，为 iSCSI 卷显示的“已用存储”可能有所不同。 |iSCSI 主机具有“文件系统”视图。<br></br>设备能够查看当卷处于最大大小时所分配的快。 |
| **14.** |文件服务器 |如果文件夹中的文件具有与其关联的备用数据流 (ADS)，则不会通过灾难恢复、克隆和项目级恢复对该 ADS 进行备份或还原。 | |

## <a name="next-step"></a>后续步骤
在 StorSimple Virtual Array 上[安装更新 0.3](storsimple-ova-install-update-01.md)。

## <a name="references"></a>参考
查找较旧版本的发行说明？ 请转到： 

* [StorSimple Virtual Array 更新 0.1 和 0.2 发行说明](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式版发行说明](storsimple-ova-pp-release-notes.md)

