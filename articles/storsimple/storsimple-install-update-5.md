---
title: "安装 Update 5 StorSimple 8000 系列设备经典门户 | Microsoft Docs"
description: "介绍如何在经典门户中的 StorSimple 8000 系列设备上安装 StorSimple 8000 系列 Update 5。"
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
ms.date: 10/02/2017
ms.author: alkohli
ms.openlocfilehash: 916884e3ee95284497cded73b28b22e83751f624
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="install-update-5-on-your-storsimple-device"></a>在 StorSimple 设备上安装 Update 5

## <a name="overview"></a>概述

本教程介绍如何通过 Azure 经典门户使用修补程序方法在运行早期软件版本的 StorSimple 设备上安装 Update 5。 若要尝试在运行预发行 Update 3 版本的设备上安装 Update 5，可使用修补程序方法。 如果在 StorSimple 设备的非 DATA 0 网络接口上配置网关，若要尝试从预发行 Update 1 软件版本更新，也可以使用修补程序方法。

Update 5 包括设备软件、Storport 和 Spaceport、OS 安全更新和 OS 更新，以及磁盘固件更新。  设备软件、Spaceport、Storport、安全更新和其他 OS 更新是非中断性更新。 可通过 Azure 经典门户或修补程序方法应用非中断性更新或常规更新。 磁盘固件更新为中断性更新，在设备处于维护模式时，使用设备的 Windows PowerShell 接口通过修补程序方法进行应用。

> [!IMPORTANT]
> * 在安装之前需进行一组手动的和自动的预检查，以便确定设备在硬件状态和网络连接性方面的运行状况。 仅当用户从 Azure 门户应用更新时，才会执行这些预检查。
> * 如果要运行的版本低于 Update 3，强烈建议使用修补程序方法安装 Update 5。 若要让支持人员逐步指导如何更新，请[记录支持票证](storsimple-8000-contact-microsoft-support.md)。
> * 若要运行 Update 3 及更高版本，建议通过 Azure 经典门户安装软件和其他常规更新。 根据需从其进行更新的版本，安装更新可能需要 4 个小时（或更长时间）。 维护模式更新必须通过设备的 Windows PowerShell 接口进行安装。 由于维护模式更新为中断性更新，因此会导致设备停机。
> * 如果运行可选的 StorSimple Snapshot Manager，请确保在更新设备之前已将 Snapshot Manager 版本升级到 Update 5。


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-classic-portal"></a>通过 Azure 经典门户安装 Update 5
执行以下步骤，将设备更新到 [Update 5](storsimple-update5-release-notes.md)。

> [!NOTE]
> Microsoft 从设备中提取其他诊断信息。 因此，当运营团队确定有问题的设备以后，我们就可以更好地从设备中收集信息并诊断问题。

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

确保设备运行的是 **StorSimple 8000 系列 Update 5 (6.3.9600.17845)**。 应修改“上次更新日期”。

* 现在，会显示“维护模式更新已发布”（此消息可能会在安装更新后持续显示长达 24 小时）。 维护模式更新为中断性更新，会导致设备停机，只能通过设备的 Windows PowerShell 接口应用。

* 下载维护模式更新时，需使用[下载修补程序](#to-download-hotfixes)中列出的步骤搜索并下载 KB4011837，以便安装磁盘固件更新（其他更新此时应已安装）。 按照[安装和验证维护模式修补程序](#to-install-and-verify-maintenance-mode-hotfixes)中列出的步骤安装维护模式更新。

## <a name="install-update-5-as-a-hotfix"></a>将 Update 5 作为修补程序安装


可以使用此修补程序方法升级的软件版本为：

* Update 0.1、0.2、0.3
* Update 1、1.1、1.2
* Update 2、2.1、2.2
* Update 3、3.1
* Update 4

> [!NOTE]
> 建议通过 Azure 经典门户安装 Update 5。 不过，如果要运行的版本低于 Update 3，强烈建议使用此方法安装 Update 5。 此外，如果在尝试通过 Azure 经典门户安装更新时无法执行网关检查，也可以按照此过程操作。 当将网关分配到非 DATA 0 网络接口且设备运行的是 Update 1 之前的软件版本时，检查会失败。

修补程序方法涉及以下三个步骤：

1. 从 Microsoft 更新目录中下载修补程序。
2. 安装和验证常规模式修补程序。
3. 安装和验证维护模式修补程序。

#### <a name="download-updates-for-your-device"></a>为设备下载更新

必须按规定顺序将以下修补程序下载并安装到建议的文件夹：

| 顺序 | KB | 说明 | 更新类型 | 安装时间 |在文件夹中安装|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |软件更新<br> 同时下载 _HcsSfotwareUpdate.exe_ 和 _CisMSDAgent.exe_ |常规 <br></br>非中断性 |~ 25 分钟 |FirstOrderUpdate|

如果从运行 Update 4 的设备更新，仅需要安装 OS 累积更新作为第二顺序更新。

| 顺序 | KB | 说明 | 更新类型 | 安装时间 |在文件夹中安装|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS 累积更新包 <br> 下载 Windows Server 2012 R2 版本 |常规 <br></br>非中断性 |- |SecondOrderUpdate|

如果从运行 Update 3 或更早版本的设备安装，除了安装累积更新外，还需安装以下更新。

| 顺序 | KB | 说明 | 更新类型 | 安装时间 |在文件夹中安装|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI 驱动程序和固件更新 <br> USM 固件更新（版本 3.38） |常规 <br></br>非中断性 |~ 3 小时 <br> （包括 2A. + 2B. + 2C.）|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS 安全更新包 <br> 下载 Windows Server 2012 R2 版本 |常规 <br></br>非中断性 |- |SecondOrderUpdate|
| 2D。 |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS 更新包 <br> 下载 Windows Server 2012 R2 版本 |常规 <br></br>非中断性 |- |SecondOrderUpdate|


除了前面表中显示的所有更新，可能还需安装磁盘固件更新。 运行 `Get-HcsFirmwareVersion` cmdlet 即可验证是否需要磁盘固件更新。 如果运行的是固件版本 `XMGJ`、`XGEG`、`KZ50`、`F6C2`、`VR08`、`N003``0107`，则不需安装这些更新。

| 顺序 | KB | 说明 | 更新类型 | 安装时间 | 在文件夹中安装|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |磁盘固件 |维护 <br></br>中断性 |约 30 分钟 | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * 如果从 Update 4 进行更新，则总安装时间约为 4 小时。
> * 使用此过程应用更新之前，请确保设备控制器为联机状态，且所有硬件组件均正常运行。

执行以下步骤，下载并安装修补程序。

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>后续步骤
了解有关 [Update 5 版本](storsimple-update5-release-notes.md)的详细信息。

