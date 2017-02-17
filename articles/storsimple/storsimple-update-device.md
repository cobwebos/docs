---
title: "更新 StorSimple 设备 | Microsoft Docs"
description: "介绍如何使用 StorSimple 更新功能来安装常规更新和维护模式更新和修补程序。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/18/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 9c0a113e50fdb78307b8030f9b0d95b696f3f234
ms.openlocfilehash: 8490110942741b049b6d44ac93697303cef40e8a


---
# <a name="update-your-storsimple-8000-series-device"></a>更新 StorSimple 8000 系列设备
## <a name="overview"></a>概述
通过 StorSimple 更新功能，可以轻松地让 StorSimple 设备保持最新。 根据更新类型，可以通过 Azure 经典门户或 Windows PowerShell 接口将更新应用于设备。 本教程介绍了更新类型及其安装方式。

可以应用两种类型的设备更新： 

* 常规（或普通模式）更新
* 维护模式更新

可以通过 Azure 经典门户或 Windows PowerShell 安装常规更新；但是，必须使用 Windows PowerShell 来安装维护模式更新。 

下面将逐一介绍每种更新类型。

### <a name="regular-updates"></a>常规更新
常规更新是无干扰更新，可以在设备处于普通模式下安装。 这些更新通过 Microsoft 更新网站应用于每个设备控制器。 

> [!IMPORTANT]
> 可能会在更新过程中发生控制器故障转移。 但是，这不会影响系统可用性或运行。
> 
> 

* 有关如何通过 Azure 经典门户安装常规更新的详细信息，请参阅[通过 Azure 经典门户安装常规更新](#install-regular-updates-via-the-azure-classic-portal)。
* 也可以通过 Windows PowerShell for StorSimple 安装常规更新。 关详细信息，请参阅[通过 Windows PowerShell for StorSimple 安装常规更新](#install-regular-updates-via-windows-powershell-for-storsimple)。

### <a name="maintenance-mode-updates"></a>维护模式更新
维护模式更新是干扰更新，例如磁盘固件升级。 这些更新要求将设备置于维护模式下。 有关详细信息，请参阅[步骤 2：进入维护模式](#step2)。 不能使用 Azure 经典门户安装维护模式更新。 而必须使用 Windows PowerShell for StorSimple。 

有关如何安装维护模式更新的详细信息，请参阅[通过 Windows PowerShell for StorSimple 安装维护模式更新](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)。

> [!IMPORTANT]
> 必须单独对每个控制器应用维护模式更新。 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>通过 Azure 经典门户安装常规更新
可以使用 Azure 经典门户对 StorSimple 设备应用更新。

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>通过 Windows PowerShell for StorSimple 安装常规更新
或者，也可以使用 Windows PowerShell for StorSimple 应用常规（普通模式）更新。

> [!IMPORTANT]
> 虽然用户可以使用 Windows PowerShell for StorSimple 安装常规更新，但我们强烈建议通过 Azure 经典门户安装常规更新。 从 Update 1 开始，将在从此门户安装更新之前执行预检查。 这些预检查将排除故障并确保更流畅的体验。 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>通过 Windows PowerShell for StorSimple 安装维护模式更新
使用 Windows PowerShell for StorSimple 对 StorSimple 设备应用维护模式更新。 在此模式下，所有的 I/O 请求都已暂停。 非易失性随机存取内存 (NVRAM) 或群集服务之类的服务也会停止。 在用户进入或退出此模式时，两个控制器都会重新启动。 退出此模式时，所有服务将继续运行并且应该可以正常运行。 （这可能需要几分钟的时间。）

如果需要应用维护模式更新，将通过 Azure 经典门户收到警报，通知有必须安装的更新。 此警报包括有关使用 Windows PowerShell for StorSimple 安装更新的说明。 更新设备后，请使用相同的过程将设备更改为常规模式。 有关分步说明的信息，请参阅[步骤 4：退出维护模式](#step4)。

> [!IMPORTANT]
> * 进入维护模式前，请在 Azure 经典门户中检查“**维护**”页上的“**硬件状态**”，以便验证两个设备控制器是否运行正常。 如果控制器未正常运行，请联系 Microsoft 支持部门以了解后续步骤。 有关详细信息，请转到“联系 Microsoft 支持部门”。 
> * 处于维护模式下时，需要依次对两个控制器应用更新。
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>步骤 1：连接到串行控制台 <a name="step1">
首先，使用 PuTTY 之类的应用程序访问串行控制台。 以下过程说明如何使用 PuTTY 连接到串行控制台。

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>步骤 2：进入维护模式 <a name="step2">
连接到控制台后，确定是否有要安装的更新，然后进入维护模式安装更新。

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>步骤 3：安装更新 <a name="step3">
接下来安装更新。

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>步骤 4：退出维护模式 <a name="step4">
最后，退出维护模式。

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>通过 Windows PowerShell for StorSimple 安装修补程序
与 Microsoft Azure StorSimple 的更新不同，修补程序是从共享文件夹中安装的。 和更新一样，修补程序也有两种类型： 

* 常规修补程序 
* 维护模式修补程序  

以下过程将介绍如何使用 Windows PowerShell for StorSimple 安装常规修补程序和维护模式修补程序。

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>在对设备执行恢复出厂设置时更新将会发生什么情况？
如果将设备恢复出厂设置，则所有更新都将丢失。 注册并配置恢复出厂设置的设备后，将需要通过 Azure 经典门户和/或 Windows PowerShell for StorSimple 手动安装更新。 有关恢复出厂设置的详细信息，请参阅[将设备恢复为出厂默认设置](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings)。

## <a name="next-steps"></a>后续步骤
* 详细了解如何[使用 Windows PowerShell for StorSimple 管理 StorSimple 设备](storsimple-windows-powershell-administration.md)。
* 详细了解如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。




<!--HONumber=Jan17_HO4-->


