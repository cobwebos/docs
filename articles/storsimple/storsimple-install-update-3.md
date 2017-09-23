---
title: "在 StorSimple 设备上安装 Update 3 | Microsoft Docs"
description: "介绍如何在 StorSimple 8000 系列设备上安装 StorSimple 8000 系列 Update 3。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c6c4634d-4f3a-4bc4-b307-a22bf18664e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: f48f9dff289c556aea1912ecf7b6415abb902f33
ms.openlocfilehash: 72b004a6c2604e0fc20b71b4b69217622f8f9ea0
ms.contentlocale: zh-cn
ms.lasthandoff: 02/28/2017

---
# <a name="install-update-3-on-your-storsimple-8000-series-device"></a>在 StorSimple 8000 系列设备上安装 Update 3

## <a name="overview"></a>概述

本教程介绍如何通过 Azure 经典门户使用修补程序方法在运行早期软件版本的 StorSimple 设备上安装 Update 3。 如果是在 StorSimple 设备的非 DATA 0 网络接口上配置网关，且用户正在尝试从 pre-Update 1 软件版本进行更新，则可使用修补程序方法。

Update 3 包含设备软件、LSI 驱动程序和固件、Storport 和 Spaceport 的更新。 如果从 Update 2 或更早版本进行更新，则还需应用 iSCSI 更新、WMI 更新以及在某些情况下应用磁盘固件更新。 设备软件、WMI、iSCSI、LSI 驱动程序、Spaceport 和 Storport 修补程序为非中断性更新，可以通过 Azure 经典门户应用。 磁盘固件更新为中断性更新，只能通过设备的 Windows PowerShell 接口应用。 

> [!IMPORTANT]
> * 在安装之前需进行一组手动的和自动的预检查，以便确定设备在硬件状态和网络连接性方面的运行状况。 仅当用户从 Azure 经典门户应用更新时，才会执行这些预检查。
> * 建议通过 Azure 经典门户安装软件更新和驱动程序更新。 如果在门户中进行预更新网关检查失败，则只能转到设备的 Windows PowerShell 接口安装更新。 根据需从其进行更新的版本，更新可能需要 1.5-2.5 小时进行安装。 维护模式更新必须通过设备的 Windows PowerShell 接口进行安装。 由于维护模式更新为中断性更新，因此会导致设备停机。
> * 如果运行可选的 StorSimple Snapshot Manager，请确保在更新设备之前已将 Snapshot Manager 版本升级到 Update 2。
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>通过 Azure 经典门户安装 Update 3
执行以下步骤，将设备更新到 [Update 3](storsimple-update3-release-notes.md)。

> [!NOTE]
> 如果应用 Update 2 或更高版本（包括 Update 2.1），Microsoft 可以从设备拉取其他诊断信息。 因此，当运营团队确定有问题的设备以后，我们就可以更好地从设备中收集信息并诊断问题。 用户如果接受 Update 2 或更高版本，我们就能够提供此主动支持。
> 
> 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

确保设备运行的是 **StorSimple 8000 系列 Update 3 (6.3.9600.17759)**。 还应修改“上次更新日期”。 
   - 如果用户是从 Update 2 之前的版本进行更新，则还会显示“维护模式更新已发布”（此消息可能会在安装更新后持续显示长达 24 小时）。
     维护模式更新为中断性更新，会导致设备停机，只能通过设备的 Windows PowerShell 接口应用。 在某些情况下，如果运行的是 Update 1.2，则磁盘固件可能已更新，因此不需安装任何维护模式更新。
   - 如果是从 Update 2 或更高版本更新，则设备现在应已更新。 可以跳过下一步。

下载维护模式更新时，需使用[下载修补程序](#to-download-hotfixes)中列出的步骤搜索并下载 KB3121899，以便安装磁盘固件更新（其他更新此时应已安装）。 按照[安装和验证维护模式修补程序](#to-install-and-verify-maintenance-mode-hotfixes)中列出的步骤安装维护模式更新。 

## <a name="install-update-3-as-a-hotfix"></a>将 Update 3 作为修补程序安装
如果用户在尝试通过 Azure 经典门户安装更新时进行网关检查失败，可使用此过程。 检查失败的原因是为非 DATA 0 网络接口分配了网关，且设备运行的是 Update 1 之前的软件版本。

可以使用此修补程序方法升级的软件版本为：

* Update 0.1、0.2、0.3
* Update 1、1.1、1.2
* Update 2、2.1、2.2 

> [!IMPORTANT]
> * 如果设备运行的是发行 (GA) 版，请联系 [Microsoft 支持部门](storsimple-contact-microsoft-support.md)以获取更新方面的帮助。
> 
> 

修补程序方法涉及以下三个步骤：

1. 从 Microsoft 更新目录中下载修补程序。
2. 安装和验证常规模式修补程序。
3. 安装并验证维护模式修补程序（仅当用户是从 pre-Update 2 软件更新时）。

#### <a name="download-updates-for-your-device"></a>为设备下载更新
**如果设备运行的是 Update 2.1 或 2.2**，则需按规定顺序下载并安装以下修补程序：

| 顺序 | KB | 说明 | 更新类型 | 安装时间 |
| --- | --- | --- | --- | --- |
| 1. |KB3186843 |软件更新 &#42; |常规 <br></br>非中断性 |约 45 分钟 |
| 2. |KB3186859 |LSI 驱动程序和固件 |常规 <br></br>非中断性 |约 20 分钟 |
| 3. |KB3121261 |Storport 和 Spaceport 修补程序 </br> Windows Server 2012 R2 |常规 <br></br>非中断性 |约 20 分钟 |

&#42;  *请注意，软件更新包含两个二进制文件：以 `all-hcsmdssoftwareupdate` 开头的设备软件更新，以及以 `all-cismdsagentupdatebundle` 开头的 Cis 和 Mds 代理。安装 Cis 和 Mds 代理之前，必须安装设备软件更新。此外，还必须在应用 Cis 和 Mds 代理更新后（在应用剩余的更新之前）通过 `Restart-HcsController` cmdlet 重新启动主动控制器。* 

**如果设备运行的是 Update 0.1、0.2、0.3、1.0、1.1、1.2 或 2.0**，则除了软件更新、LSI 驱动程序更新和固件更新（如上表所示），还必须按规定顺序下载和安装以下修补程序：

| 顺序 | KB | 说明 | 更新类型 | 安装时间 |
| --- | --- | --- | --- | --- |
| 4. |KB3146621 |iSCSI 包 |常规 <br></br>非中断性 |约 20 分钟 |
| 5. |KB3103616 |WMI 包 |常规 <br></br>非中断性 |约 12 分钟 |

<br></br>

**如果设备运行的是版本 0.2、0.3、1.0、1.1 和 1.2**，则除了前述表中显示的所有更新，可能还需安装磁盘固件更新。 运行 `Get-HcsFirmwareVersion` cmdlet 即可验证是否需要磁盘固件更新。 如果运行的是固件版本 `XMGG`、`XGEG`、`KZ50`、`F6C2`、`VR08`，则不需安装这些更新。

| 顺序 | KB | 说明 | 更新类型 | 安装时间 |
| --- | --- | --- | --- | --- |
| 6. |KB3121899 |磁盘固件 |维护 <br></br>中断性 |约 30 分钟 |

<br></br>

> [!IMPORTANT]
> * 应用 Update 3 只需执行此过程一次。 可以使用 Azure 经典门户应用后续更新。
> * 如果从 Update 2.2 进行更新，则总的安装时间约为 1.1 小时。
> * 使用此过程应用更新之前，请确保设备控制器为联机状态，且所有硬件组件均正常运行。
> 
> 

执行以下步骤，下载并安装修补程序。

[!INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>后续步骤
详细了解 [Update 3 版本](storsimple-update3-release-notes.md)。


