---
title: "在 StorSimple 设备上安装 Update 1.2 | Microsoft Docs"
description: "介绍如何在 StorSimple 8000 系列设备上安装 StorSimple 8000 系列 Update 1.2。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 7a513923-eb77-4078-b0ab-f8e90183796a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 56af9046fff24229298997b169aaf329e1692cc2
ms.openlocfilehash: 80ff35cc47dfc38089f4c392ef4c90baf9ccc03e
ms.lasthandoff: 02/28/2017


---
# <a name="install-update-12-on-your-storsimple-8000-series-device"></a>在 StorSimple 8000 系列设备上安装 Update 1.2
## <a name="overview"></a>概述
本教程介绍如何在运行 Update 1 之前的软件版本的 StorSimple 设备上安装 Update 1.2。 本教程还介绍了将网关配置在 StorSimple 设备的非 DATA 0 网络接口上时，进行更新所需的额外步骤。

Update 1.2 包括设备软件更新、LSI 驱动程序更新以及磁盘固件更新。 软件更新和 LSI 驱动程序更新为非中断性更新，可以通过 Azure 经典门户应用。 磁盘固件更新为中断性更新，只能通过设备的 Windows PowerShell 接口应用。

可以根据设备正在运行的版本确定是否需要应用 Update 1.2。 导航到设备“仪表板”的“速览”部分即可查看设备的软件版本。

</br>

| 如果运行的是软件版本 … | 门户中会发生什么情况？ |
| --- | --- |
| 发行版 - 正式版 |如果运行的是发行版 (GA)，请勿应用此更新。 若要更新设备，请[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。 |
| Update 0.1 |门户应用 Update 1.2。 |
| Update 0.2 |门户应用 Update 1.2。 |
| Update 0.3 |门户应用 Update 1.2。 |
| Update 1 |此更新将不可用。 |
| Update 1.1 |此更新将不可用。 |

</br>

> [!IMPORTANT]
> * 用户可能不会立即看到 Update 1.2，因为更新是分阶段推出的。 请在数天内再次扫描更新，因为很快就会推出此 Update。
> * 此更新包括一组手动的和自动的预检查，用于确定设备在硬件状态和网络连接性方面的运行状况。 仅当用户从 Azure 经典门户应用更新时，才会执行这些预检查。
> * 建议通过 Azure 经典门户安装软件更新和驱动程序更新。 如果在门户中进行预更新网关检查失败，则只能转到设备的 Windows PowerShell 接口安装更新。 更新可能需要 5-10 小时进行安装（包括 Windows 更新）。 维护模式更新必须通过设备的 Windows PowerShell 接口进行安装。 由于维护模式更新为中断性更新，因此会导致设备停机。
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>通过 Azure 经典门户安装 Update 1.2
执行以下步骤，将设备更新到 [Update 1.2](storsimple-update1-release-notes.md)。 使用此过程的前提是已在设备的 DATA 0 网络接口上配置了网关。

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

1. 确保设备运行的是 **StorSimple 8000 系列 Update 1.2 (6.3.9600.17584)**。 还应修改“上次更新日期”。 此外还会显示“维护模式更新已发布”（此消息可能会在安装更新后持续显示长达 24 小时）。
   
   维护模式更新为中断性更新，会导致设备停机，只能通过设备的 Windows PowerShell 接口应用。
   
   ![维护页](./media/storsimple-install-update-1/InstallUpdate12_10M.png "维护页")
2. 下载维护模式更新时，需使用[下载修补程序](#to-download-hotfixes)中列出的步骤搜索并下载 KB3063416，以便安装磁盘固件更新（其他更新此时应已安装）。
3. 按照[安装和验证维护模式修补程序](#to-install-and-verify-maintenance-mode-hotfixes)中列出的步骤安装维护模式更新。
4. 在 Azure 经典门户中导航到“维护”页，在页面底部单击“扫描更新”查看 Windows 更新，然后单击“安装更新”。 成功安装所有更新以后，相关操作即告完成。

## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>将 Update 1.2 安装在已为非 DATA 0 网络接口配置网关的设备上
使用此过程的前提是，用户在尝试通过 Azure 经典门户安装更新时进行网关检查失败。 检查失败的原因是为非 DATA 0 网络接口分配了网关，且设备运行的是 Update 1 之前的软件版本。 如果设备的非 DATA 0 网络接口上没有网关，则可直接从 Azure 经典门户更新设备。 请参阅[通过 Azure 经典门户安装 Update 1.2](#install-update-1.2-via-the-azure-classic-portal)。

可以使用此方法升级的软件版本为 Update 0.1、Update 0.2 和 Update 0.3。

> [!IMPORTANT]
> * 如果设备运行的是发行 (GA) 版，请联系 [Microsoft 支持部门](storsimple-contact-microsoft-support.md)以获取更新方面的帮助。
> * 应用 Update 1.2 只需执行此过程一次。 可以使用 Azure 经典门户应用后续更新。
> 
> 

如果设备运行的是 pre-Update 1 软件且为非 DATA 0 网络接口设置了网关，则可通过以下两种方式应用 Update 1.2：

* **选项 1**：下载并应用更新，方法是使用设备的 Windows PowerShell 接口中的 `Start-HcsHotfix` cmdlet。 这是建议的方法。 **如果设备运行的是 Update 1.0 或 Update 1.1，请勿使用此方法应用 Update 1.2。**
* **选项 2**：删除网关配置，直接从 Azure 经典门户安装更新。

以下部分提供了每个此类选项的详细说明。

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>选项 1：使用 Windows PowerShell for StorSimple 以修补程序方式应用 Update 1.2
使用此过程的前提是：运行的是 Update 0.1、0.2、0.3 且在尝试从 Azure 经典门户安装更新时进行网关检查失败。 如果运行的是发行 (GA) 版软件，请[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)对设备进行更新。

若要以修补程序方式安装 Update 1.2，必须下载并安装以下修补程序：

| 顺序 | KB | 说明 | 更新类型 |
| --- | --- | --- | --- |
| 1 |KB3063418 |软件更新 |常规 |
| 2 |KB3043005 |LSI SAS 控制器更新 |常规 |
| 3 |KB3063416 |磁盘固件 |维护 |

使用此过程应用该更新之前，请确保：

* 两个设备控制器都处于联机状态。

执行以下步骤，应用 Update 1.2。 **完成这些更新可能需要大约 2 小时（大致为软件更新 30 分钟，驱动程序更新 30 分钟，磁盘固件更新 45 分钟）。**

[!INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]

## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>选项 2：删除网关配置后，使用 Azure 经典门户应用 Update 1.2
此过程仅适用于 StorSimple 设备，此类设备运行的是 Update 1 之前的软件版本，且已在非 DATA 0 网络接口上设置了网关。 在应用更新之前，需清除网关设置。

更新可能需要数小时才能完成。 如果主机位于其他子网中，删除 iSCSI 接口上的网关配置可能导致停机。 建议针对 iSCSI 流量配置 DATA 0，减少停机的可能性。

执行以下步骤，禁用带网关的网络接口，然后应用更新。

[!INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>后续步骤
详细了解 [Update 1.2 版](storsimple-update1-release-notes.md)。


