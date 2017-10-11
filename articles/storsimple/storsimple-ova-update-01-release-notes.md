---
title: "StorSimple 虚拟阵列更新发行说明 | Microsoft Docs"
description: "介绍有关运行更新 0.2 和 0.1 的 StorSimple Virtual Array 待解决的重要问题和解决方案。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: c4ccde9635b3874864baa9d4d262ff5ddcf2a425
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple Virtual Array 更新 0.2 和 0.1 发行说明
## <a name="overview"></a>概述
以下发行说明标识了 Microsoft Azure StorSimple Virtual Array 更新待解决的重要问题和已解决的问题。 （Microsoft Azure StorSimple Virtual Array 也称为 StorSimple 本地虚拟设备或 StorSimple 虚拟设备。） 

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 StorSimple 虚拟设备之前，请仔细查看发行说明中包含的信息。

更新 0.2 对应软件版本 **10.0.10280.0**；更新 0.1 对应版本 **10.0.10279.0**。 以下部分列出了对每个更新所做的更改。 

> [!NOTE]
> 更新会造成中断，并且会使设备重新启动。 如果正在进行 I/O，则会导致设备停机。
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>更新 0.2 中修复的问题
除了下表中所述的修复以外，更新 0.2 还包括更新 0.1 中的所有更新：

| 功能 | 问题 |
| --- | --- |
| 更新 |在上一版本中，在 Azure 经典门户中未自动检测到更新，因此必须使用本地 Web UI 来安装更新。 在此版本中已修复了此问题。 安装更新0.2 后，可使用 Azure 经典门户安装将来的更新。 |

## <a name="whats-new-in-the-update-01"></a>更新 0.1 中的新增内容
更新 0.1 包含以下 bug 修复和改进。 

* **针对云故障改进的复原性能**：此版本修复了在发生云连接中断时进行灾难恢复、备份、还原和分层的几个 bug。 
* **改进的还原性能**：此版本已修复了 bug，可显著减少还原作业的完成时间。
* **自动空间回收优化**：在精简预配的卷上删除数据时，需要回收未使用的存储块。 此版本改进了从云中进行空间回收的过程，同先前版本相比，可更快地使未使用的空间变为可用空间。
* **新的虚拟磁盘映像**：现在可通过 Azure 经典门户获取新的 VHD、VHDX 和 VMDK。 可下载这些映像来预配新的更新 0.1 设备。
* **提高门户中作业状态的准确性**：在软件的早期版本中，在门户中报告的作业状态并不精确。 在此版本中已修复了此问题。
* **域加入体验**：与域加入和重命名设备相关的 bug 修复。

## <a name="issues-fixed-in-the-update-01"></a>更新 0.1 中修复的问题
下表提供了此版本中所修复问题的摘要。

| 否。 | 功能 | 问题 |
| --- | --- | --- |
| 1 |VMDK |在某些 VMware 版本中，OS 磁盘偶尔会引起警报并干扰正常操作。 在此版本中已修复了此问题。 |
| 2 |iSCSI 服务器 |在上一版本中，用户需要为 StorSimple 虚拟设备每个已启用的网络接口指定网关。 此版本中已更改了此行为，使用户只需为所有已启用的网络接口至少配置一个网关即可。 |
| 3 |支持包 |在软件的早期版本中，当包大小超过 1 GB 时，支持包集合则会失败。 在此版本中已修复了此问题。 |
| 4 |云访问 |在上一版本中，如果 StorSimple Virtual Array 没有网络连接并且进行了重启，本地 UI 则会出现连接问题。 在此版本中已修复了此问题。 |
| 5 |监视图表 |在之前的版本中，在进行了设备故障转移操作后，云容量利用率图表在 Azure 经典门户中会显示不正确的值。 在当前版本中已修复了此问题。 |

## <a name="known-issues-in-the-update-01"></a>更新 0.1 中的已知问题
下表提供了 StorSimple Virtual Array 的已知问题的摘要，包括早期版本中发行说明的问题。 **此版本中发行说明的问题标有星号。此列表中几乎所有问题都是从 StorSimple Virtual Array 的正式版本中延续下来的。**

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
| **9.** |灾难恢复 |只能将文件服务器灾难恢复到源设备所在的同一域中。 此版本中不支持灾难恢复到其他域中的目标设备。 |这会在更高版本中实现。 |
| **10.** |Azure PowerShell |在此版本中，不可以通过 Azure PowerShell 管理 StorSimple 虚拟设备。 |所有对虚拟设备的管理应通过 Azure 经典门户和本地 Web UI 进行。 |
| **11.** |密码更改 |虚拟数组设备控制台仅接受 en-US 键盘格式的输入。 | |
| **12.** |CHAP |CHAP 凭据一旦创建便无法删除。 此外，如果要修改 CHAP 凭据，首先需要使这些卷脱机，再使它们联机，以使更改生效。 |这些问题会在更高版本中得以解决。 |
| **13.** |iSCSI 服务器 |在 StorSimple Manager 服务和 iSCSI 主机中，显示的 iSCSI 卷的“已用存储”可能有所不同。 |iSCSI 主机具有“文件系统”视图。<br></br>设备能够查看当卷处于最大大小时所分配的快。 |
| **14.** |文件服务器 * |如果文件夹中的文件具有与其关联的备用数据流 (ADS)，则不会通过灾难恢复、克隆和项目级恢复对该 ADS 进行备份或还原。 | |

## <a name="next-step"></a>后续步骤
在 StorSimple Virtual Array 上[安装更新](storsimple-ova-install-update-01.md)。

