---
title: "StorSimple Virtual Array Update 0.4 发行说明 | Microsoft 文档"
description: "介绍有关运行 Update 0.4 的 StorSimple Virtual Array 待解决的重要问题和解决方案。"
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
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: cc2b025b7f3e28954c7f95409ffab03e5cbcf13d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple Virtual Array Update 0.4 发行说明

## <a name="overview"></a>概述

以下发行说明标识了 Microsoft Azure StorSimple Virtual Array 更新待解决的重要问题和已解决的问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 部署 StorSimple Virtual Array 之前，请仔细查看发行说明中包含的信息。

Update 0.4 对应软件版本 **10.0.10289.0**。

> [!NOTE]
> 更新会造成中断，并且会使设备重新启动。 如果正在进行 I/O，则会导致设备停机。


## <a name="whats-new-in-the-update-04"></a>Update 0.4 中的新增内容
Update 0.4 主要是一个 bug 修复版本，外加几处改进。 在此版本中，已解决在早期版本中导致备份失败的多个 bug。 主要改进和 bug 修复如下所示：

- **备份性能改进** - 此版本进行了几处重要改进，以提高备份性能。 因此，对于涉及大量文件的备份，完整备份和增量备份的完成时间将显著减少。

- **增强还原性能** - 此版本包含在使用大量文件时，可显著改进还原性能的增强功能。 如果使用 200 万 - 400 万个文件，我们建议使用 16 GB RAM 预配虚拟阵列，以便查看这些改进。 使用小于 200 万个文件时，虚拟机的最低要求仍然是 8 GB RAM。

- **对支持包的改进** - 改进包括将磁盘、CPU、内存、网络和云的统计信息记录到支持包中，从而改进了诊断/调试设备问题的过程。

- **将本地固定 iSCSI 卷限制为 200 GB** - 对于本地固定卷，我们建议将 StorSimple Virtual Array 上的 iSCSI 卷限制为 200 GB。 分层卷的本地保留空间仍为预配的卷大小的 10%，但其上限为 200 GB。 

- **与备份相关的 bug 修复** - 在以前的软件版本中，存在着会导致备份失败的与备份相关的问题。 此版本中已解决这些 bug。


## <a name="issues-fixed-in-the-update-04"></a>Update 0.4 中修复的问题

下表提供了此版本中所修复问题的摘要。

| 否。 | 功能 | 问题 |
| --- | --- | --- |
| 1 |备份性能|在早期版本中，涉及大量文件的备份将需要很长时间才能完成（在几天之内）。 在此版本中，完整备份和增量备份可看到显著减少完成时间。 |
| 2 |支持包|现在，磁盘、CPU、内存、网络和云的统计信息将记录到支持日志中，使支持包在排查任何设备问题时非常有效。|
| 3 |备份 |在早期版本中，长时间运行的备份可能会导致设备上的空间不足，从而导致备份失败。 在此版本中通过仅允许同时不超过 5 个备份排队，解决了此 bug。|
| 4 |iSCSI | 在早期版本中，分层卷或本地固定卷的本地保留空间为预配的卷大小的 10%。 在此版本中，所有 iSCSI 卷（本地固定卷或分层卷）的本地保留空间限制为 10%，最大值可达 200 GB（适用于大于 2 TB 的分层卷），从而可释放本地磁盘上的更多空间。 我们建议，此版本中的本地固定卷限制为 200 GB。|


## <a name="known-issues-in-the-update-04"></a>Update 0.4 中的已知问题

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
| **15.** |文件服务器 |不支持符号链接。 | |
| **16.** |文件服务器 |将受 Windows 加密文件系统 (EFS) 保护的文件复制或存储到 StorSimple 虚拟阵列文件服务器时会导致不受支持的配置。  | |

## <a name="next-step"></a>后续步骤
在 StorSimple Virtual Array 上[安装 Update 0.4](storsimple-virtual-array-install-update-04.md)。

## <a name="references"></a>参考
查找较旧版本的发行说明？ 请转到： 

* [StorSimple Virtual Array Update 0.3 发行说明](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array 更新 0.1 和 0.2 发行说明](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式版发行说明](storsimple-ova-pp-release-notes.md)

