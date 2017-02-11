---
title: "在 StorSimple 设备上安装 Update 2 | Microsoft Docs"
description: "介绍如何在 StorSimple 8000 系列设备上安装 StorSimple 8000 系列 Update 2。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 8c8981df-75d9-4d19-b137-d6c6ba39dcfb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e788439608b7122f2bca6b99b832baa5258e472d


---
# <a name="install-update-2-on-your-storsimple-device"></a>在 StorSimple 设备上安装 Update 2
## <a name="overview"></a>概述
本教程介绍如何通过 Azure 经典门户在运行早期软件版本的 StorSimple 设备上安装 Update 2。 本教程还介绍了碰到下述情况时进行更新所需的步骤：在 StorSimple 设备的非 DATA 0 网络接口上配置网关，且用户正在尝试从 pre-Update 1 软件版本进行更新。

Update 2 包括设备软件更新、LSI 驱动程序更新以及磁盘固件更新。 设备软件更新和 LSI 更新为非中断性更新，可以通过 Azure 经典门户应用。 磁盘固件更新为中断性更新，只能通过设备的 Windows PowerShell 接口应用。

> [!IMPORTANT]
> * 用户可能不会立即看到 Update 2，因为更新是分阶段推出的。 请在数天内再次扫描更新，因为很快就会推出此 Update。
> * 在安装之前需进行一组手动的和自动的预检查，以便确定设备在硬件状态和网络连接性方面的运行状况。 仅当用户从 Azure 经典门户应用更新时，才会执行这些预检查。
> * 建议通过 Azure 经典门户安装软件更新和驱动程序更新。 如果在门户中进行预更新网关检查失败，则只能转到设备的 Windows PowerShell 接口安装更新。 更新可能需要 4-7 小时进行安装（包括 Windows 更新）。 维护模式更新必须通过设备的 Windows PowerShell 接口进行安装。 由于维护模式更新为中断性更新，因此会导致设备停机。
> * 如果运行可选的 StorSimple Snapshot Manager，请确保在更新设备之前已将 Snapshot Manager 版本升级到 Update 2。
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-2-via-the-azure-classic-portal"></a>通过 Azure 经典门户安装 Update 2
执行以下步骤，将设备更新到 [Update 2](storsimple-update2-release-notes.md)。

> [!NOTE]
> Update 2 使 Microsoft 可以从设备中提取其他诊断信息。 因此，当运营团队确定有问题的设备以后，我们就可以更好地从设备中收集信息并诊断问题。 用户如果接受 Update 2，我们就能够提供此主动支持。
> 
> 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

1. 确保设备运行的是 **StorSimple 8000 系列 Update 2 (6.3.9600.17673)**。 还应修改“上次更新日期”。 此外还会显示“维护模式更新已发布”（此消息可能会在安装更新后持续显示长达 24 小时）。
   
   维护模式更新为中断性更新，会导致设备停机，只能通过设备的 Windows PowerShell 接口应用。 在某些情况下，如果运行的是 Update 1.2，则磁盘固件可能已更新，因此不需安装任何维护模式更新。
2. 下载维护模式更新时，需使用[下载修补程序](#to-download-hotfixes)中列出的步骤搜索并下载 KB3121899，以便安装磁盘固件更新（其他更新此时应已安装）。
3. 按照[安装和验证维护模式修补程序](#to-install-and-verify-maintenance-mode-hotfixes)中列出的步骤安装维护模式更新。

## <a name="install-update-2-as-a-hotfix"></a>将 Update 2 作为修补程序安装
如果用户在尝试通过 Azure 经典门户安装更新时进行网关检查失败，可使用此过程。 检查失败的原因是为非 DATA 0 网络接口分配了网关，且设备运行的是 Update 1 之前的软件版本。

可以使用此修补程序方法升级的软件版本为 Update 0.1、Update 0.2、Update 0.3、Update 1、Update 1.1、Update 1.2。 修补程序方法涉及以下三个步骤：

* 从 Microsoft 更新目录中下载修补程序。
* 安装和验证常规模式修补程序。
* 安装和验证维护模式修补程序。

若要以修补程序方式安装 Update 2，必须下载并安装以下修补程序：

| 顺序 | KB | 说明 | 更新类型 |
| --- | --- | --- | --- |
| 1 |KB3121901 |软件更新 |常规 |
| 2 |KB3121900 |LSI 驱动程序 |常规 |
| 3 |KB3080728 |Storport 修补程序 </br> Windows Server 2012 R2 |常规 |
| 4 |KB3090322 |Spaceport 修补程序 </br> Windows Server 2012 R2 |常规 |
| 5 |KB3121899 |磁盘固件 |维护 |

> [!IMPORTANT]
> * 如果设备运行的是发行 (GA) 版，请联系 [Microsoft 支持部门](storsimple-contact-microsoft-support.md)以获取更新方面的帮助。
> * 应用 Update 2 只需执行此过程一次。 可以使用 Azure 经典门户应用后续更新。
> * 每个修补程序的安装可能需要大约 20 分钟才能完成。 总的安装时间接近 2 小时。
> * 使用此过程应用更新之前，请确保设备控制器为联机状态，且所有硬件组件均正常运行。
> 
> 

执行以下步骤，以修补程序方式应用此更新。

[!INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>后续步骤
详细了解 [Update 2 版本](storsimple-update2-release-notes.md)。




<!--HONumber=Nov16_HO3-->


