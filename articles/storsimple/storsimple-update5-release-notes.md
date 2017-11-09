---
title: "StorSimple 8000 系列 Update 5 发行说明 | Microsoft Docs"
description: "介绍 StorSimple 8000 系列 Update 5 的新功能、问题和解决方法。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/28/2017
ms.author: alkohli
ms.openlocfilehash: fa7360a44c48d4f15233b937f09285233533a0e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 系列 Update 5 发行说明

## <a name="overview"></a>概述

以下发行说明描述 StorSimple 8000 系列 Update 5 的新功能，并标识其重要的待解决问题。 其中也包含此版本中随附的 StorSimple 软件更新列表。

Update 5 可应用于任何运行 Update 0.1 到 Update 4 的 StorSimple 设备。 与 Update 5 相关联的设备版本是 6.3.9600.17845。

在 StorSimple 解决方案中部署更新之前，请查看发行说明中所包含的信息。

> [!IMPORTANT]
> * Update 5 包含设备软件、磁盘固件、OS 安全性和其他 OS 更新。 安装此更新大约需要 4 小时。 磁盘固件更新是中断性更新，并且会导致设备出现故障时间。 建议应用 Update 5，使设备保持最新状态。
> * 对于新版本，由于我们分阶段推出更新，可能不能立即看到更新。 请等待几天，再次扫描更新，因为很快就会提供这些更新。

## <a name="whats-new-in-update-5"></a>Update 5 中的新增功能

Update 5 中进行了以下重大改进和 bug 修复。

* **使用 Azure Active Directory (AAD) 对 StorSimple 设备管理器服务进行身份验证** – 从 Update 5 开始，使用 Azure Active Directory 对 StorSimple 设备管理器服务进行身份验证。 旧身份验证机制将在 2017 年 12 月弃用。 所有用户必须在其防火墙规则中包含新的身份验证 URL。 有关详细信息，请转到 [StorSimple 设备网络要求中所列的身份验证 URL](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal)。

    如果未将身份验证 URL 包含在防火墙规则中，用户会看到一条关键警报，指出其 StorSimple 设备无法对服务进行身份验证。 如果用户看到此警报，则需要包含新的身份验证 URL。 有关详细信息，请转到 [StorSimple 网络警报](storsimple-8000-manage-alerts.md#networking-alerts)。

* StorSimple Snapshot Manager 的新版本 - 包含更新 5 的新版 StorSimple Snapshot Manager 已发布，它与运行更新 4 或更高版本的所有 StorSimple 设备保持兼容。 我们建议更新到此版本。 早期版本的 StorSimple Snapshot Manager 适用于运行更新 3 或更早版本的 StorSimple 设备。 [下载 StorSimple Snapshot Manager 的相应版本](https://www.microsoft.com/en-us/download/details.aspx?id=44220)并参考[部署 StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md)。


## <a name="issues-fixed-in-update-5"></a>在 Update 5 中修复的问题

下表提供在 Update 5 中已修复问题的摘要。

| 否 | 功能 | 问题 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell 远程功能 |在以前的版本中，用户尝试通过 Windows PowerShell 与 StorSimple 云设备建立远程连接时会收到错误。 在此版本中已找到此问题的根本原因并进行了修复。 |否 |是 |
| #N/A |带宽模板 |在以前的版本中，带宽模板中的某个问题会导致带宽低于配置给设备的带宽。 在此版本中已修复了此问题。 |是 |是 |
| 3 |故障转移 |在以前的版本中，如果将包含大量卷的设备故障转移到运行 Update 4 的另一台设备，则在尝试应用访问控制记录时，故障转移过程会失败。 在此版本中已修复了此问题。 |是 |是 |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Update 5 中的已知问题（来自以前的版本）

Update 5 中没有任何新的已知问题。 有关从之前版本遗留至 Update 5 的问题列表，请转到 [Update 3 发行说明](storsimple-update3-release-notes.md#known-issues-in-update-3)。

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Update 5 中的串行连接 SCSI (SAS) 控制器和固件更新

此版本包含 SAS 控制器、LSI 驱动程序和固件更新。 有关如何安装这些更新的详细信息，请参阅在 StorSimple 设备上[安装 Update 5](storsimple-8000-install-update-5.md)。

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Update 5 中的 StorSimple 云设备更新

此更新不能应用于 StorSimple 云工具（也称为虚拟设备）。 需要使用 Update 5 映像创建新的云设备。 有关如何创建 StorSimple 云设备的信息，请转到[部署和管理 StorSimple 云设备](storsimple-8000-cloud-appliance-u2.md)。

## <a name="next-step"></a>后续步骤

了解如何在 StorSimple 设备上[安装 Update 5](storsimple-8000-install-update-5.md)。

