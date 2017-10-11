---
title: "修改 StorSimple 设备上的 DATA 0 设置 | Microsoft Docs"
description: "了解如何使用 Windows PowerShell for StorSimple 重新配置 StorSimple 设备上的 DATA 0 网络接口。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 58e3d509-f425-4a7f-b650-d496a7c85193
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 3a47ff1eed220cede820e8698c3384300e94688d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-device"></a>修改 StorSimple 设备上的 DATA 0 网络接口设置
## <a name="overview"></a>概述
Microsoft Azure StorSimple 设备有六个网络接口，分别为 DATA 0 到 DATA 5。 DATA 0 接口始终是通过 Windows PowerShell 接口或串行控制台配置的，并且自动启用云。 注意，无法通过 Azure 经典门户配置 DATA 0 网络接口。 

DATA 0 接口最先是在 StorSimple 设备的初始部署期间通过设置向导配置的。 可能需要在设备处于操作模式时重新配置 DATA 0 设置。 本教程提供了修改 DATA 0 网络设置的两种方法，两种方法都使用 Windows PowerShell for StorSimple。

阅读本教程之后，能够：

* 通过设置向导修改 DATA 0 网络设置
* 通过 `Set-HcsNetInterface` cmdlet 修改 DATA 0 网络设置

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>通过设置向导修改 DATA 0 网络设置
可以通过连接到 StorSimple 设备的 Windows PowerShell 接口并启动设置向导会话来重新配置 DATA 0 网络设置。 可以执行以下步骤来修改 DATA 0 设置：

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>通过设置向导修改 DATA 0 网络设置
1. 在串行控制台菜单中，选择选项 1“使用完全访问权限登录”。 出现提示时，请提供**设备管理员密码**。 默认密码为 `Password1`。
2. 在命令提示符处，键入：
   
    `Invoke-HcsSetupWizard`
3. 设置向导随即将出现，以帮助你配置设备的 DATA 0 接口。 为 IP 地址、网关和网络掩码提供新值。

> [!NOTE]
> 固定控制器 IP 将需要在 Azure 经典门户中通过 StorSimple 设备的“配置”页进行重新配置。 有关详细信息，请转到[修改网络接口](storsimple-modify-device-config.md#modify-network-interfaces)。
> 
> 

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>通过 Set-HcsNetInterface cmdlet 修改 DATA 0 网络设置
重新配置 DATA 0 网络接口的另一种方法是使用 `Set-HcsNetInterface` cmdlet。 该 cmdlet 是从 StorSimple 设备的 Windows PowerShell 接口执行的。 当使用此过程时，还可以在此处配置控制器固定 IP。 可以执行以下步骤来修改 DATA 0 设置： 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>通过 Set-HcsNetInterface cmdlet 修改 DATA 0 网络设置
1. 在串行控制台菜单中，选择选项 1“使用完全访问权限登录”。 出现提示时，请提供设备管理员密码。 默认密码为 `Password1`。
2. 在命令提示符处，键入：
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    在尖括号中，为 DATA 0 键入以下值：
   
   * IPv4 地址
   * IPv4 网关
   * IPv4 子网掩码
   * 控制器 0 的固定 IPv4 地址
   * 控制器 1 的固定 IPv4 地址
     
     有关如何使用此 cmdlet 的详细信息，请转到 [Windows PowerShell for StorSimple cmdlet 参考](https://technet.microsoft.com/library/dn688161.aspx)。

## <a name="next-steps"></a>后续步骤
* 若要配置 DATA 0 之外的网络接口，可以使用 [Azure 经典门户中的“配置”页](storsimple-modify-device-config.md)。 
* 如果在配置网络接口时遇到任何问题，请参阅[排除部署问题](storsimple-troubleshoot-deployment.md)。

