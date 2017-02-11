---
title: "StorSimple Virtual Array 发行说明 | Microsoft Docs"
description: "介绍 StorSimple Virtual Array 待解决的重要问题和解决方案。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 84908160-2b8b-4f4f-a674-f39aaa0bd4de
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f3ea83e32af4de2637d12766ee8c51adfbf0d3a3


---
# <a name="storsimple-virtual-array-release-notes"></a>StorSimple Virtual Array 发行说明
## <a name="overview"></a>概述
以下发行说明识别了 Microsoft Azure StorSimple Virtual Array（也称为 StorSimple 本地虚拟设备或 StorSimple 虚拟设备）2016 年 3 月正式 (GA) 版待解决的重要问题。 此版本对应软件版本 10.0.10271.0。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 StorSimple 虚拟设备之前，请仔细查看发行说明中包含的信息。 

下表提供了此版本中已知问题的摘要。

| 否。 | 功能 | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
| **1.** |更新 |无法将预览版中所创建的虚拟设备更新为受支持的正式版本。 |必须使用灾难恢复 (DR) 工作流将这些虚拟设备故障转移为正式版本。 |
| **2.** |预配的数据磁盘 |在预配指定大小的数据磁盘，并创建相应的 StorSimple 虚拟设备后，不能立即扩展或收缩数据磁盘。 尝试进行这些操作将导致该设备本地层中的所有数据丢失。 | |
| **3.** |组策略 |当设备已加入域时，应用组策略反而会影响设备操作。 |对于 Active Directory，请确保虚拟数组位于其自身的组织单位 (OU) 中，并且没有对其应用任何组策略对象 (GPO)。 |
| **4.** |本地 Web UI |如果在 Internet Explorer 中启用了增强安全功能 (IE ESC)，则可能会导致“故障排除”或“维护”等某些本地 Web UI 页面无法正常工作。 这些页面上的按钮也可能无法工作。 |请在 Internet Explorer 中关闭增强安全功能。 |
| **5.** |本地 Web UI |在 Hyper-V 虚拟机中，Web UI 中的网络接口显示为 10 Gbps 接口。 |此行为是 Hyper-V 的反射。 Hyper-V 始终为虚拟网络适配器显示 10 Gbps。 |
| **6.** |分层卷或共享 |不支持对适用于 StorSimple 分层卷的应用程序进行字节范围锁定。 如果启用了字节范围锁定，则 StorSimple 分层将不起作用。 |建议的措施包括： <br></br>在应用程序逻辑中关闭字节范围锁定。<br></br>选择将此应用程序的数据放置在本地固定卷中，而不是分层卷中。<br></br>*附加说明*：如果使用本地固定卷并且已启用字节范围锁定，则可以使本地固定卷联机，即使在还原完成之前也是如此。 在这种情况下，如果正在进行还原，则必须等待还原完成。 |
| **7.** |分层共享 |处理大型文件可能会导致分层速度变慢。 |处理大型文件时，建议最大的文件应小于共享大小的 3%。 |
| **8.** |共享的已用容量 |即使共享上没有任何数据时，也可能会看到共享消耗。 这是因为共享的已用容量中包括元数据。 | |
| **9.** |灾难恢复 |只能将文件服务器灾难恢复到源设备所在的同一域中。 此版本中不支持灾难恢复到其他域中的目标设备。 |这将在更高版本中实现。 |
| **10.** |Azure PowerShell |在此版本中，不可以通过 Azure PowerShell 管理 StorSimple 虚拟设备。 |所有对虚拟设备的管理应通过 Azure 经典门户和本地 Web UI 进行。 |
| **11.** |密码更改 |虚拟数组设备控制台仅接受 en-US 键盘格式的输入。 | |
| **12.** |CHAP |CHAP 凭据一旦创建之后便无法删除。 此外，如果要修改 CHAP 凭据，首先需要使这些卷脱机，然后再使它们联机，以使更改生效。 |这些问题将在更高版本中得以解决。 |
| **13.** |iSCSI 服务器 |在 StorSimple Manager服务和 iSCSI 主机中，为 iSCSI 卷显示的“已用存储”可能有所不同。 |iSCSI 主机具有“文件系统”视图。<br></br>设备能够查看当卷处于最大大小时所分配的快。 |




<!--HONumber=Nov16_HO3-->


