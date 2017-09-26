---
title: "更改 StorSimple 设备模式 | Microsoft Docs"
description: "介绍 StorSimple 设备模式，并说明如何使用 Windows PowerShell for StorSimple 更改设备模式。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: e9d7d277-8a2f-45eb-9fef-355486e14cbc
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2016
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 33c65bf2eecff3914f3227c76f7d638a4507e1f6
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>更改 StorSimple 设备上的设备模式
本文简要介绍了适合 StorSimple 设备运行的各种模式。 StorSimple 设备客户端可在下述三种模式下运行：正常、维护和恢复。 

阅读本文后，用户会了解：

* StorSimple 设备模式有哪些
* 如何推断 StorSimple 设备处于哪种模式
* 如何从正常模式变为维护模式，*反之亦然*

上面的管理任务只能通过 StorSimple 设备的 Windows PowerShell 接口执行。

## <a name="about-storsimple-device-modes"></a>关于 StorSimple 设备模式
StorSimple 设备可以按正常、维护或恢复模式运行。 下面简要介绍每种模式。

### <a name="normal-mode"></a>正常模式
这是指完全配置的 StorSimple 设备的正常操作模式。 默认情况下，设备应处于正常模式。

### <a name="maintenance-mode"></a>维护模式
有时可能需要将 StorSimple 设备置于维护模式。 此模式可以在设备上执行维护和安装中断性更新，例如与磁盘固件相关的维护和更新。

可以只通过 Windows PowerShell for StorSimple 将系统置于维护模式。 在此模式下，所有的 I/O 请求都已暂停。 非易失性随机存取内存 (NVRAM) 或群集服务之类的服务也会停止。 在用户进入或退出此模式时，两个控制器都会重新启动。 退出维护模式时，所有服务将继续运行并且应该可以正常运行。 这可能需要几分钟的时间。

> [!NOTE]
> **只能在正常工作的设备上运行维护模式。不能在一个或两个控制器无法正常工作的设备上运行维护模式。**
> </br>
> 
> 

### <a name="recovery-mode"></a>恢复模式
恢复模式可以描述为“具有网络支持的 Windows 安全模式”。 恢复模式允许 Microsoft 支持团队参与，并允许其在系统上执行诊断。 恢复模式的主要目的是检索系统日志。

如果系统进入恢复模式，应联系 Microsoft 支持部门以了解后续步骤。 有关详细信息，请转到[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。

> [!NOTE]
> **用户无法将设备置于恢复模式。如果设备处于不良工作状态，恢复模式会试图让设备进入允许 Microsoft 支持人员进行检查的状态。**
> 
> 

## <a name="determine-storsimple-device-mode"></a>确定 StorSimple 设备模式
#### <a name="to-determine-the-current-device-mode"></a>确定当前的设备模式
1. 按照[使用 PuTTY 连接到设备串行控制台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的步骤登录到设备串行控制台。
2. 查看设备的串行控制台菜单中的横幅消息。 此消息显式指示设备是处于维护模式还是恢复模式。 如果消息不包含任何属于系统模式的特定信息，则设备处于正常模式。

## <a name="change-the-storsimple-device-mode"></a>更改 StorSimple 设备模式
可以将 StorSimple 设备置于维护模式（从正常模式下），以便执行维护或安装维护模式更新。 执行以下过程，进入或退出维护模式。

> [!IMPORTANT]
> 进入维护模式前，请在 Azure 经典门户中查看“**维护**”页上的“**硬件状态**”，验证两个设备控制器是否运行正常。 如果两个控制器或其中一个控制器未正常运行，请联系 Microsoft 支持部门以了解后续步骤。 有关详细信息，请转到[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。
> 
> 

#### <a name="to-enter-maintenance-mode"></a>进入维护模式
1. 按照[使用 PuTTY 连接到设备串行控制台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的步骤登录到设备串行控制台。
2. 在串行控制台菜单中，选择选项 1“使用完全访问权限登录”。 出现提示时，请提供**设备管理员密码**。 默认密码为 `Password1`。
3. 在命令提示符处，键入 
   
    `Enter-HcsMaintenanceMode`
4. 此时会显示一条警告消息，指示维护模式将中断所有 I/O 请求并断开到 Azure 经典门户的服务器连接，并将提示用户进行确认。 键入 **Y** 进入维护模式。
5. 将重新启动两个控制器。 重新启动完成后，将显示另一条消息，指示设备处于维护模式。

#### <a name="to-exit-maintenance-mode"></a>退出维护模式
1. 登录到设备串行控制台。 从横幅消息验证设备是否处于维护模式。
2. 在命令提示符处，键入：
   
    `Exit-HcsMaintenanceMode`
3. 将显示一条警告消息和一条确认消息。 键入 **Y** 退出维护模式。
4. 将重新启动两个控制器。 重新启动完成后，将显示另一条消息，指示设备处于正常模式。

## <a name="next-steps"></a>后续步骤
了解如何在 StorSimple 设备上[应用正常模式和维护模式更新](storsimple-update-device.md)。


