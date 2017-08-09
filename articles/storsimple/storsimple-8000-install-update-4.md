---
title: "在 StorSimple 8000 系列设备上安装 Update 4 | Microsoft Docs"
description: "介绍如何在 StorSimple 8000 系列设备上安装 StorSimple 8000 系列 Update 4。"
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
ms.date: 08/02/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 57d6d63c55f8ad4da5d1905a1e209da454b0491c
ms.contentlocale: zh-cn
ms.lasthandoff: 08/03/2017

---
# <a name="install-update-4-on-your-storsimple-device"></a>在 StorSimple 设备上安装 Update 4

## <a name="overview"></a>概述

本教程介绍如何通过 Azure 门户使用修补程序方法在运行早期软件版本的 StorSimple 设备上安装 Update 4。 如果是在 StorSimple 设备的非 DATA 0 网络接口上配置网关，且用户正在尝试从 pre-Update 1 软件版本进行更新，则可使用修补程序方法。

Update 4 包括设备软件、USM 固件、LSI 驱动程序和固件、Storport 和 Spaceport、OS 安全更新以及大量其他 OS 更新。  设备软件、USM 固件、Spaceport、Storport 和其他 OS 更新是非中断性更新。 可通过 Azure 门户或修补程序方法应用非中断性更新或常规更新。 磁盘固件更新为中断性更新，且只能使用设备的 Windows PowerShell 接口通过修补程序方法进行应用。

> [!IMPORTANT]
> * 在安装之前需进行一组手动的和自动的预检查，以便确定设备在硬件状态和网络连接性方面的运行状况。 仅当用户从 Azure 门户应用更新时，才会执行这些预检查。
> * 建议通过 Azure 门户安装软件更新和其他常规更新。 如果在门户中进行预更新网关检查失败，则只能转到设备的 Windows PowerShell 接口安装更新。 根据需从其进行更新的版本，安装更新可能需要 4 个小时（或更长时间）。 还必须通过设备的 Windows PowerShell 接口安装维护模式更新。 由于维护模式更新为中断性更新，因此会导致设备停机。
> * 如果运行可选的 StorSimple Snapshot Manager，请确保在更新设备之前已将 Snapshot Manager 版本升级到 Update 4。


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>通过 Azure 门户安装 Update 4
执行以下步骤，将设备更新到 [Update 4](storsimple-update4-release-notes.md)。

> [!NOTE]
> Microsoft 从设备中提取其他诊断信息。 因此，当运营团队确定有问题的设备以后，我们就可以更好地从设备中收集信息并诊断问题。 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

验证确保设备运行的是 **StorSimple 8000 系列 Update 4 (6.3.9600.17820)**。 还应修改“上次更新日期”。

* 现在，会显示“维护模式更新已发布”（此消息可能会在安装更新后持续显示长达 24 小时）。 维护模式更新为中断性更新，会导致设备停机，只能通过设备的 Windows PowerShell 接口应用。

* 下载维护模式更新时，需使用[下载修补程序](#to-download-hotfixes)中列出的步骤搜索并下载 KB4011837，以便安装磁盘固件更新（其他更新此时应已安装）。 按照[安装和验证维护模式修补程序](#to-install-and-verify-maintenance-mode-hotfixes)中列出的步骤安装维护模式更新。

## <a name="install-update-4-as-a-hotfix"></a>将 Update 4 作为修补程序安装
建议通过 Azure 门户安装 Update 4。

如果用户在尝试通过 Azure 门户安装更新时进行网关检查失败，可使用此过程。 检查失败的原因是为非 DATA 0 网络接口分配了网关，且设备运行的是 Update 1 之前的软件版本。

可以使用此修补程序方法升级的软件版本为：

* Update 0.1、0.2、0.3
* Update 1、1.1、1.2
* Update 2、2.1、2.2
* Update 3、3.1


修补程序方法涉及以下三个步骤：

1. 从 Microsoft 更新目录中下载修补程序。
2. 安装和验证常规模式修补程序。
3. 安装和验证维护模式修补程序。

#### <a name="download-updates-for-your-device"></a>为设备下载更新

必须按规定顺序将以下修补程序下载并安装到建议的文件夹：

| 顺序 | KB | 说明 | 更新类型 | 安装时间 |在文件夹中安装|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |软件更新 |常规 <br></br>非中断性 |~ 25 分钟 |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |LSI 驱动程序和固件更新 <br> USM 固件更新（版本 3.38） |常规 <br></br>非中断性 |~ 3 小时 <br> （包括 2A. + 2B. + 2C.）|SecondOrderUpdate|
| 2B. |KB3139398、KB3108381 <br> KB3205400、KB3142030 <br> KB3197873、KB3197873 <br> KB3192392、KB3153704 <br> KB3174644、KB3139914  |OS 安全更新包 <br> 下载 Windows Server 2012 R2 |常规 <br></br>非中断性 |- |SecondOrderUpdate|
| 2C. |KB3210083、KB3103616 <br> KB3146621、KB3121261 <br> KB3123538 |OS 更新包 <br> 下载 Windows Server 2012 R2 |常规 <br></br>非中断性 |- |SecondOrderUpdate|

除了前面表中显示的所有更新，可能还需安装磁盘固件更新。 运行 `Get-HcsFirmwareVersion` cmdlet 即可验证是否需要磁盘固件更新。 如果运行的是固件版本 `XMGJ`、`XGEG`、`KZ50`、`F6C2`、`VR08`、`N002``0106`，则不需安装这些更新。

| 顺序 | KB | 说明 | 更新类型 | 安装时间 | 在文件夹中安装|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |磁盘固件 |维护 <br></br>中断性 |约 30 分钟 | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * 应用 Update 4 只需执行此过程一次。 可以使用 Azure 门户应用后续更新。
> * 如果从 Update 3 或 3.1 进行更新，则总的安装时间约为 4 小时。
> * 使用此过程应用更新之前，请确保设备控制器为联机状态，且所有硬件组件均正常运行。

执行以下步骤，下载并安装修补程序。

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>后续步骤
了解有关 [Update 4 版本](storsimple-update4-release-notes.md) 的详细信息。


