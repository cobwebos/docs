---
title: StorSimple 8000 系列 Update 4 发行说明 | Microsoft Docs
description: 介绍 StorSimple 8000 系列 Update 4 的新功能、问题和解决方法。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: f4db5a49b71b0c384da7af2b90bd5426a4f35c72
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34058142"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 系列 Update 4 发行说明

## <a name="overview"></a>概述

以下发行说明描述 StorSimple 8000 系列 Update 4 的新功能，并标识其重要的待解决问题。 其中也包含此版本中随附的 StorSimple 软件更新列表。 

Update 4 可应用于任何运行 Release (GA)、Update 0.1 到 Update 3.1 的 StorSimple 设备。 与 Update 4 相关联的设备版本是 6.3.9600.17820。

在 StorSimple 解决方案中部署更新之前，请查看发行说明中所包含的信息。

> [!IMPORTANT]
> * Update 4 包括设备软件、USM 固件、LSI 驱动程序和固件、磁盘固件、Storport 和 Spaceport、安全更新以及其他 OS 更新。 安装此更新大约需要 4 小时。 磁盘固件更新是中断性更新，并且会导致设备出现故障时间。 建议应用 Update 4，使设备保持最新状态。 
> * 对于新版本，由于我们分阶段推出更新，可能不能立即看到更新。 请等待几天，再次扫描更新，因为很快就会提供这些更新。

## <a name="whats-new-in-update-4"></a>Update 4 中的新增功能

Update 4 中进行了以下重大改进和 bug 修复。

* **更智能的自动空间回收算法** - 在 Update 4 中，自动空间回收算法已得到增强，以根据云中可用的预期回收空间调整空间回收周期。 

* **本地固定卷的性能增强** - 针对具有高数据引入的场景（数据与卷大小相当），Update 4 提升了本地固定卷的性能。

* **基于热度地图的还原** - 在早期版本中使用灾难恢复 (DR) 后，数据会基于访问模式从云中还原，这导致了性能低下。 

    Update 4 中实施了一项新功能，在进行 DR 之前使用设备时，该功能会跟踪频繁访问的数据以创建热度地图（最常用的数据区块热度高，不常用的数据区块热度低）。 在 DR 完成后，StorSimple 使用热度地图自动还原和解除冻结云中的数据。 

    所有还原现在都是基于热度地图的还原。 有关如何查询和取消基于热度地图的还原和解冻作业的详细信息，请转到 [Windows PowerShell for StorSimple cmdlet 参考](https://technet.microsoft.com/library/dn688168.aspx)。

* **StorSimple 诊断工具** - Update 4 发布了一款 StorSimple 诊断工具，以便可以轻松诊断和排查与系统、网络、性能和硬件组件运行状况相关的问题。 此工具通过 Windows PowerShell for StorSimple 运行。 有关详细信息，请转到[使用 StorSimple 诊断工具进行故障排除](storsimple-8000-diagnostics.md)。

* **基于 UI 的 StorSimple 迁移工具** - 在此版本之前，从 5000-7000 系列迁移数据要求用户使用 Azure PowerShell 接口执行一部分迁移工作流。 在此版本中，则提供了易于使用的基于 UI 的 StorSimple 迁移工具，使执行相同的迁移工作流变得更加便利。 此工具还支持合并恢复存储桶。 

* **与 FIPS 相关的更改** - 自此版本起，对于 Microsoft Azure 政府和 Azure 公有云帐户，会在所有 StorSimple 8000 系列设备上默认启用 FIPS。

* **更新更改** - 此版本修复了与更新失败相关的 bug。

* **磁盘故障警报** - 此版本中添加了一种新警报，用于警告用户即将发生的磁盘故障。 如果遇到此警报，请联系 Microsoft 支持，让其发送更换磁盘。 有关详细信息，请转到 [StorSimple 设备上的硬件警报](storsimple-8000-manage-alerts.md#hardware-alerts)。

* **控制器替换更改** - 此版本中添加了一个 cmdlet，它允许用户查询控制器替换过程的状态。 有关详细信息，请转到[可查询控制器替换状态的 cmdlet](https://technet.microsoft.com/library/dn688168.aspx)。


## <a name="issues-fixed-in-update-4"></a>在 Update 4 中修复的问题

下表提供在 Update 4 中已修复问题的摘要。    

| 否 | 功能 | 问题 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- |
| 1 |故障转移 |在早期版本中，当故障转移后，会在客户站点观察到与清理相关的问题。 在此版本中已修复了此问题。 |是 |是 |
| 2 |本地固定卷 |在之前版本中，为本地固定卷创建卷时会出现导致卷创建失败的问题。 在此版本中已找到此问题的根本原因并进行了修复。 |是 |否 |
| 3 |支持包 |之前版本中存在与支持包相关的问题，该问题会导致 System.OutOfMemory 异常，或支持包创建失败的其他错误。 在此版本中已修复这些 bug。 |是 |是 |
| 4 |监视 |在之前版本中，当监视本地固定卷的图表时会出现占用量显示在 EB 中的问题。 在此版本中已修复了此 bug。 |是 |是 |
| 5 |迁移 |在之前版本中，当从 5000-7000 系列迁移到 8000 系列设备时，会出现若干与迁移可靠性相关的问题。 此版本中已解决这些问题。 |是 |是 |
| 6 |更新 |在以前版本中，如果有更新失败，控制器会进入恢复模式，用户因此无法继续更新，需要联系 Microsoft 支持部门。 <br> 此版本更改了这一行为。 当两个控制器都运行相同版本（更新 4）后，如果用户遇到更新失败，控制器不进入恢复模式。 如果用户遇到此失败，建议稍作等待，并重试更新。 重试可能会成功。 如果重试失败，应联系 Microsoft 支持部门。 |是 |是 |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Update 4 中的已知问题（来自之前版本）

Update 4 中没有任何新的已知问题。 有关从之前版本遗留至 Update 4 的问题列表，请转到 [Update 3 发行说明](storsimple-update3-release-notes.md#known-issues-in-update-3)。

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Update 4 中的串行连接 SCSI (SAS) 控制器和固件更新

此版本包含 SAS 控制器、LSI 驱动程序和固件更新。 有关如何安装这些更新的详细信息，请参阅在 StorSimple 设备上[安装 Update 4](storsimple-install-update-4.md)。

## <a name="virtual-device-updates-in-update-4"></a>Update 4 中的虚拟设备更新

此更新不能应用于 StorSimple 云工具（也称为虚拟设备）。 将需要新建虚拟设备。 

## <a name="next-step"></a>后续步骤

了解如何在 StorSimple 设备上[安装 Update 4](storsimple-install-update-4.md)。

