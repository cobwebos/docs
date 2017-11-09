---
title: "管理 StorSimple 8000 系列设备控制器 | Microsoft Docs"
description: "了解如何停止、重新启动、关闭或重置 StorSimple 设备控制器。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 75c1bdb570967b6d1902697597f0b5bf3f4ffb7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-storsimple-device-controllers"></a>管理 StorSimple 设备控制器

## <a name="overview"></a>概述

本教程介绍可在 StorSimple 设备控制器上执行的不同操作。 StorSimple 设备中的控制器是采用主动-被动配置的冗余（对等）控制器。 在任意给定时间，只能有一台控制器处于主动状态并处理所有磁盘和网络操作。 另一台控制器处于被动模式。 如果主动控制器发生故障，被动控制器会自动变成主动控制器。

本教程提供有关使用以下方式管理设备控制器的分步说明：

* StorSimple 设备管理器服务中设备的“控制器”边栏选项卡。
* Windows PowerShell for StorSimple。

我们建议通过 StorSimple 设备管理器服务管理设备控制器。 如果只能使用 Windows PowerShell for StorSimple 执行某个操作，本教程会提供相应的说明。

阅读本教程之后，能够：

* 重新启动或关闭 StorSimple 设备控制器
* 关闭 StorSimple 设备
* 将 StorSimple 设备重置为出厂默认设置

## <a name="restart-or-shut-down-a-single-controller"></a>重新启动或关闭单个控制器
在执行正常的系统操作期间，无需重新启动控制器或关闭控制器。 通常，只有需要更换有故障的设备硬件组件时，才会针对单个设备控制器执行关闭操作。 如果性能由于内存用量过大或者控制器异常而受到影响，也可能需要重新启动控制器。 在成功更换控制器后，如果想要启用和测试更换的控制器，那么，也可能需要重新启动控制器。

如果被动控制器可用，重新启动设备不会中断连接的发起程序。 如果被动控制器不可用或者已关闭，则重新启动主动控制器可能会导致服务中断和停机。

> [!IMPORTANT]
> * **千万不要以物理方式拆卸运行中的控制器，否则会导致失去冗余，增大停机时间的风险。**
> * 以下过程仅适用于 StorSimple 物理设备。 有关如何启动、停止和重启 StorSimple 云设备的信息，请参阅[使用云设备](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance)。

可以通过 StorSimple 设备管理器服务的 Azure 门户或者用于 StorSimple 的 Windows PowerShell 来重启或关闭单个设备控制器。

若要从 Azure 门户管理设备控制器，请执行以下步骤。

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>在 Azure 门户中重启或关闭控制器
1. 在 StorSimple 设备管理器服务中，转到“设备”。 从设备列表中选择自己的设备。 

    ![选择设备](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. 转到“设置”>“控制器”。
   
    ![检查 StorSimple 设备控制器是否正常](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. 在“控制器”边栏选项卡中，检查设备上的两个控制器的状态是否为“正常”。 选择一个控制器，单击右键，选择“重启”或“关闭”。

    ![选择重启或关闭 StorSimple 设备控制器](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. 此时会创建一个作业来重启或关闭控制器，同时，会显示适用的警告（如果有）。 若要监视重启或关闭状态，请转到“服务”>“活动日志”，根据服务特定的参数进行筛选。 如果控制器已关闭，则需要按下电源按钮将控制器打开。

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell for StorSimple 中重新启动或关闭控制器
执行以下步骤可以通过 用于 StorSimple 的 Windows PowerShell 关闭或重启 StorSimple 设备上的单个控制器。

1. 通过串行控制台或来自远程计算机的 Telnet 会话访问设备。 若要连接到控制器 0 或控制器 1，请遵循[使用 PuTTY 连接到设备串行控制台](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)中的步骤。
2. 在串行控制台菜单中，选择选项 1“使用完全访问权限登录”。
3. 在标题消息中，记下连接到的控制器（控制器 0 或控制器 1），以及它是主动控制器还是被动（备用）控制器。
   
   * 若要关闭单个控制器，请在提示符下键入：
     
       `Stop-HcsController`
     
       这会关闭所连接到的控制器。 如果停止主动控制器，设备会故障转移到被动控制器。

   * 若要重新启动控制器，请在提示符下键入：
     
       `Restart-HcsController`
     
       这会重启所连接到的控制器。 如果重启主动控制器，它会在重启之前故障转移到被动控制器。

## <a name="shut-down-a-storsimple-device"></a>关闭 StorSimple 设备

本部分说明如何从远程计算机关闭正在运行的或有故障的 StorSimple 设备。 在关闭两个设备控制器后，设备会关闭。 以物理方式移动设备或者不再使用设备时，才需要执行关闭操作。

> [!IMPORTANT]
> 关闭设备之前，请检查设备组件的运行状况。 导航到设备，单击“设置”>“硬件运行状况”。 在“状态和硬件运行状况”边栏选项卡中，检查所有组件的 LED 状态是否为绿色。 只有状况正常的设备才显示绿色状态。 如果正在关闭设备以便更换有故障的组件，会看到相应组件的状态为故障（红色）或降级（黄色）。


#### <a name="to-shut-down-a-storsimple-device"></a>关闭 StorSimple 设备

1. 通过[重新启动或关闭控制器](#restart-or-shut-down-a-single-controller)过程来识别和关闭设备上的被动控制器。 可以在 Azure 门户或用于 StorSimple 的 Windows PowerShell 中执行此操作。
2. 重复上述步骤关闭主动控制器。
3. 现在必须检查设备的背板。 完全关闭两个控制器之后，两个控制器上的状态 LED 应闪烁红色。 如果此时需要将设备完全关闭，请将电源和散热模块 (PCM) 上的电源开关切换到 OFF 位置。 这样即可关闭设备。

## <a name="reset-the-device-to-factory-default-settings"></a>将设备重置为出厂默认设置

> [!IMPORTANT]
> 如果需要将设备重置为出厂默认设置，请联系 Microsoft 支持。 下面所述的过程只能在 Microsoft 支持人员的协助下使用。

此过程描述如何使用 Windows PowerShell for StorSimple 将 Microsoft Azure StorSimple 设备重置为出厂默认设置。
默认情况下，重置设备会删除整个群集中的所有数据和设置。

执行以下步骤，将 Microsoft Azure StorSimple 设备重置为出厂默认设置：

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell for StorSimple 中将设备重置为默认设置
1. 通过设备的串行控制台访问设备。 检查标题消息，确保已连接到**主动**控制器。
2. 在串行控制台菜单中，选择选项 1“使用完全访问权限登录”。
3. 在提示符下，键入以下命令重置整个群集，并删除所有数据、元数据和控制器设置：
   
    `Reset-HcsFactoryDefault`
   
    若要重置单个控制器，请结合 `-scope` 参数使用 [Reset-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) cmdlet。
   
    系统将重新启动多次。 重置成功完成后，系统会发出通知。 根据系统型号，在 8100 设备上可能需要 45-60 分钟才能完成此过程，而 8600 则需要 60-90 分钟。
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>有关管理控制器的问题和解答
本部分汇总了有关管理 StorSimple 设备控制器的一些常见问题。

**问：** 如果设备上的两个控制器都运行正常并已打开，重新启动或关闭主动控制器会发生什么情况？

**答：** 如果设备上的两个控制器都运行正常并已打开，系统会提示确认。 可以选择：

* **重启主动控制器** – 系统会告知，重启主动控制器将导致设备故障转移到被动控制器。 控制器重启。
* **关闭主动控制器** – 系统会告知，关闭主动控制器会导致停机。 还需要在设备上按电源按钮打开控制器。

**问：** 如果设备上的被动控制器不可用或已关闭，重新启动或关闭主动控制器会发生什么情况？

**答：** 如果设备上的被动控制器不可用或已关闭，并且你选择：

* **重启主动控制器** – 系统会告知，继续该操作会导致服务暂时中断，并提示确认。
* **关闭主动控制器** – 系统会告知，继续操作会导致停机。 还需要按下一个或两个控制器上的电源按钮打开设备。 系统会提示进行确认。

**问：** 什么情况下无法重新启动或关闭控制器？

**答：** 如果存在以下情况，将无法重新启动或关闭控制器：

* 设备更新正在进行。
* 控制器重新启动已在进行。
* 控制器关闭已在进行。

**问：** 如何判断控制器是否已重新启动或关闭？

**答：** 可以在“控制器”边栏选项卡上查看控制器状态。 控制器状态会指示控制器是否正在重启或关闭。 此外，如果重启或关闭了控制器，“警报”边栏选项卡也会包含信息性警报。 活动日志中也会记录控制器重启和关闭操作。 有关活动日志的详细信息，请转到[查看活动日志](storsimple-8000-service-dashboard.md#view-the-activity-logs)。

**问：** 控制器故障转移是否对 I/O 造成任何影响？

**答：** 在控制器故障转移后，发起程序与主动控制器之间的 TCP 连接会重置，但是，在被动控制器接管操作后，会重新建立该连接。 在此操作期间，发起程序与设备之间的 I/O 活动可能会出现临时（小于 30 秒）的暂停。

**问：** 关闭或拆下控制器后，如何使控制器恢复工作？

**答：** 要使控制器恢复工作，必须根据[更换 StorSimple 设备上的控制器模块](storsimple-8000-controller-replacement.md)中所述将它插入机箱。

## <a name="next-steps"></a>后续步骤
* 如果使用本教程中所列的过程无法解决你遇到的 StorSimple 设备控制器相关问题，请[联系 Microsoft 支持](storsimple-8000-contact-microsoft-support.md)。
* 有关如何使用 StorSimple 设备管理器服务的详细信息，请转到[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。

