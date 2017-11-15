---
title: "管理 StorSimple 设备控制器 | Microsoft 文档"
description: "了解如何停止、重新启动、关闭或重置 StorSimple 设备控制器。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4ee989d0-956f-4c14-951e-fd4e490ea09d
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 8182d0ccf9fb57a9dba383d376d1c557845bcc8a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="manage-your-storsimple-device-controllers"></a>管理 StorSimple 设备控制器
> [!NOTE]
> 已弃用 StorSimple 经典门户。 根据弃用计划，StorSimple 设备管理器将自动迁移至新的 Azure 门户。 你将收到有关此移动的电子邮件和门户通知。 另外，此文档也将在稍后停用。 若要查看本文中新的 Azure 门户版本，请转到[管理 StorSimple 设备控制器](storsimple-8000-manage-device-controller.md)。 任何与移动相关的问题，请参阅[常见问题解答：转移到 Azure 门户](storsimple-8000-move-azure-portal-faq.md)。

## <a name="overview"></a>概述
本教程介绍可在 StorSimple 设备控制器上执行的不同操作。 StorSimple 设备中的控制器是采用主动-被动配置的冗余（对等）控制器。 在任意给定时间，只能有一台控制器处于主动状态并处理所有磁盘和网络操作。 另一台控制器处于被动模式。 如果主动控制器发生故障，被动控制器会自动变成主动控制器。

本教程提供有关使用以下方式管理设备控制器的分步说明：

* StorSimple Manager 服务中“维护”页的“控制器”部分
* Windows PowerShell for StorSimple。

我们建议通过 StorSimple Manager 服务管理设备控制器。 如果只能使用 Windows PowerShell for StorSimple 执行某个操作，本教程会提供相应的说明。

阅读本教程之后，能够：

* 重新启动或关闭 StorSimple 设备控制器
* 关闭 StorSimple 设备
* 将 StorSimple 设备重置为出厂默认设置

## <a name="restart-or-shut-down-a-single-controller"></a>重新启动或关闭单个控制器
在执行正常的系统操作期间，无需重新启动控制器或关闭控制器。 通常，只有需要更换有故障的设备硬件组件时，才会针对单个设备控制器执行关闭操作。 如果性能由于内存用量过大或者控制器异常而受到影响，也可能需要重新启动控制器。 在成功更换控制器后，如果想要启用和测试更换的控制器，那么，也可能需要重新启动控制器。

如果被动控制器可用，重新启动设备不会中断连接的发起程序。 如果被动控制器不可用或者已关闭，则重新启动主动控制器可能会导致服务中断和停机。

> [!IMPORTANT]
> * **千万不要以物理方式拆卸运行中的控制器，否则会导致失去冗余，增大停机时间的风险。**
> * 以下过程仅适用于 StorSimple 物理设备。 有关如何启动、停止和重新启动虚拟设备的信息，请参阅 [Work with the virtual device](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device)（使用虚拟设备）。
> 
> 

可以使用 StorSimple Manager 服务的 Azure 经典门户或者使用 Windows PowerShell for StorSimple 来重新启动或关闭单个设备控制器。

若要从 Azure 经典门户管理设备控制器，请执行以下步骤。

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>在经典门户中重新启动或关闭控制器
1. 导航到“设备”>“维护”。
2. 转到“硬件状态”，检查设备上的两个控制器的状态是否为“正常”。
   
    ![检查 StorSimple 设备控制器是否正常](./media/storsimple-manage-device-controller/IC766017.png)
3. 在“维护”页的底部，单击“管理控制器”。
   
    ![管理 StorSimple 设备控制器](./media/storsimple-manage-device-controller/IC766018.png)</br>
   
   > [!NOTE]
   > 如果看不到“管理控制器”，则需要安装更新。 有关详细信息，请参阅 [Update your StorSimple device](storsimple-update-device.md)（更新 StorSimple 设备）。
   > 
   > 
4. 在“更改控制器设置”对话框中执行以下操作：
   
   1. 从“选择控制器”下拉列表中，选择要管理的控制器。 选项包括“控制器 0”和“控制器 1”。 这些控制器也被标识为主动或被动。
      
      > [!NOTE]
      > 如果某个控制器不可用或者已关闭，则该控制器不可管理，并且不会显示在下拉列表中。
      > 
      > 
   2. 从“选择操作”下拉列表中，选择“重新启动控制器”或“关闭控制器”。
      
       ![重新启动 StorSimple 设备被动控制器](./media/storsimple-manage-device-controller/IC766020.png)
   3. 单击勾选图标  ![勾选图标](./media/storsimple-manage-device-controller/IC740895.png)。

这会重新启动或关闭该控制器。 下表根据“更改控制器设置”对话框中所做的选择，汇总发生的结果的详细信息。  

| 选择编号 | 如果选择... | 会发生这种结果。 |
| --- | --- | --- |
| 1. |重新启动被动控制器。 |将创建一个作业用于重新启动控制器，成功创建该作业后，系统会发出通知。 随后将重新启动控制器。 要监视重新启动过程，可以访问“服务”>“仪表板”>“查看操作日志”，并根据特定于服务的参数筛选记录。 |
| 2. |重新启动主动控制器。 |将看到以下警告：“如果重新启动主动控制器，设备将故障转移到被动控制器。 是否继续?” </br>如果选择继续此操作，后续步骤将与用于重新启动被动控制器的步骤相同（参阅选项 1）。 |
| 3. |关闭被动控制器。 |将看到以下消息：“完成关闭后，需要按控制器上的电源按钮将它打开。 确实要关闭此控制器吗?” </br>如果选择继续此操作，后续步骤将与用于重新启动被动控制器的步骤相同（参阅选项 1）。 |
| 4. |关闭主动控制器。 |将看到以下消息：“完成关闭后，需要按控制器上的电源按钮将它打开。 确实要关闭此控制器吗?” </br>如果选择继续此操作，后续步骤将与用于重新启动被动控制器的步骤相同（参阅选项 1）。 |

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell for StorSimple 中重新启动或关闭控制器
执行以下步骤可以通过 Azure 经典门户关闭或重新启动 StorSimple 设备上的单个控制器。

1. 使用串行控制台或来自远程计算机的 Telnet 会话访问设备。 按照[使用 PuTTY 连接到设备串行控制台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的步骤连接到控制器 0 或控制器 1。
2. 在串行控制台菜单中，选择选项 1“使用完全访问权限登录”。
3. 在标题消息中，记下连接到的控制器（控制器 0 或控制器 1），以及它是主动控制器还是被动（备用）控制器。
   
   * 若要关闭单个控制器，请在提示符下键入：
     
       `Stop-HcsController`
     
       这样就会关闭所连接到的控制器。 如果停止主动控制器，它在关闭之前将故障转移到被动控制器。
   * 若要重新启动控制器，请在提示符下键入：
     
       `Restart-HcsController`
     
       这样就会重新启动所连接到的控制器。 如果重新启动主动控制器，它会在重新启动之前故障转移到被动控制器。

## <a name="shut-down-a-storsimple-device"></a>关闭 StorSimple 设备
本部分说明如何从远程计算机关闭正在运行的或有故障的 StorSimple 设备。 在关闭两个设备控制器后，设备会关闭。 以物理方式移动设备或者不再使用设备时，才需要执行关闭操作。

> [!IMPORTANT]
> 关闭设备之前，请检查设备组件的运行状况。 导航到“设备”>“维护”>“硬件状态”，检查所有组件的 LED 状态是否为绿色。 只有状况正常的设备才显示绿色状态。 如果正在关闭设备以便更换有故障的组件，会看到相应组件的状态为故障（红色）或降级（黄色）。
> 
> 

#### <a name="to-shut-down-a-storsimple-device"></a>关闭 StorSimple 设备
1. 通过[重新启动或关闭控制器](#restart-or-shut-down-a-single-controller)过程来识别和关闭设备上的被动控制器。 可以在 Azure 经典门户或 Windows PowerShell for StorSimple 中执行此操作。
2. 重复上述步骤关闭主动控制器。
3. 现在需要检查设备的背板。 完全关闭两个控制器之后，两个控制器上的状态 LED 应闪烁红色。 如果此时需要将设备完全关闭，请将电源和散热模块 (PCM) 上的电源开关切换到 OFF 位置。 这样即可关闭设备。

<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>将设备重置为出厂默认设置
> [!IMPORTANT]
> 如果需要将设备重置为出厂默认设置，请联系 Microsoft 支持。 下面所述的过程只能在 Microsoft 支持人员的协助下使用。
> 
> 

此过程描述如何使用 Windows PowerShell for StorSimple 将 Microsoft Azure StorSimple 设备重置为出厂默认设置。
默认情况下，重置设备会删除整个群集中的所有数据和设置。

执行以下步骤，将 Microsoft Azure StorSimple 设备重置为出厂默认设置：

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell for StorSimple 中将设备重置为默认设置
1. 通过设备的串行控制台访问设备。 检查标题消息，确保已连接到主动控制器。
2. 在串行控制台菜单中，选择选项 1“使用完全访问权限登录”。
3. 在提示符下，键入以下命令重置整个群集，并删除所有数据、元数据和控制器设置：
   
    `Reset-HcsFactoryDefault`
   
    若要重置单个控制器，请结合 `-scope` 参数使用 [Reset-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) cmdlet。
   
    系统将重新启动多次。 重置成功完成后，系统会发出通知。 根据系统型号，在 8100 设备上可能需要 45-60 分钟才能完成此过程，而 8600 则需要 60-90 分钟。
   
   > [!TIP]
   > * 如果使用 Update 1.2 或更低版本，请使用 `–SkipFirmwareVersionCheck` 参数跳过固件版本检查（否则将看到固件不匹配错误：“固件版本不匹配，无法继续出厂重置 ）。
   > * 在政府版门户中针对运行 Update 1 或 1.1 的 StorSimple 设备执行出厂重置过程可能会失败；对于已成功执行单控制器或双控制器更换（用于更换的控制器装有 Update 1 以前的软件版本）的设备，该过程也可能会失败。 在出厂重置映像中验证控制器上是否存在 SHA1 文件（Update 1 以前的软件中不存在此文件）时，将发生这种失败。 如果发生这种出厂重置失败，请联系 Microsoft 支持，让他们提供后续步骤。 如果用于更换的控制器出厂附带了 Update 1 或更高版本的软件，则不会发生此问题。
   > 
   > 

## <a name="questions-and-answers-about-managing-device-controllers"></a>有关管理控制器的问题和解答
本部分汇总了有关管理 StorSimple 设备控制器的一些常见问题。

**问：** 如果设备上的两个控制器都运行正常并已打开，重新启动或关闭主动控制器会发生什么情况？

**答：** 如果设备上的两个控制器都运行正常并已打开，系统会提示确认。 可以选择：

* **重新启动主动控制器** – 系统会告知，重新启动主动控制器将导致设备故障转移到被动控制器。 控制器将重新启动。
* **关闭主动控制器** – 系统会告知，关闭主动控制器会导致停机。 还需要在设备上按电源按钮打开控制器。

**问：** 如果设备上的被动控制器不可用或已关闭，重新启动或关闭主动控制器会发生什么情况？

**答：** 如果设备上的被动控制器不可用或已关闭，并且你选择：

* **重新启动主动控制器** – 系统会告知，继续该操作会导致服务暂时中断，并提示确认。
* **关闭主动控制器** – 系统会告知，继续该操作会导致停机，并且需要按一个或两个控制器上的电源按钮打开设备。 系统会提示进行确认。

**问：** 什么情况下无法重新启动或关闭控制器？

**答：** 如果存在以下情况，将无法重新启动或关闭控制器：

* 设备更新正在进行。
* 控制器重新启动已在进行。
* 控制器关闭已在进行。

**问：** 如何判断控制器是否已重新启动或关闭？

**答：** 可以在“维护”页上查看控制器状态。 控制器状态将指示控制器是否已重新启动或关闭。 此外，如果重新启动或关闭了控制器，“警报”页也会包含信息性警报。 操作日志中也会记录控制器重新启动和关闭操作。 有关操作日志的详细信息，请转到[查看操作日志](storsimple-service-dashboard.md#view-the-operations-logs)。

**问：** 控制器故障转移是否对 I/O 造成任何影响？

**答：** 在控制器故障转移后，发起程序与主动控制器之间的 TCP 连接会重置，但是，在被动控制器接管操作后，会重新建立该连接。 在此操作期间，发起程序与设备之间的 I/O 活动可能会出现临时（小于 30 秒）的暂停。

**问：** 关闭或拆下控制器后，如何使控制器恢复工作？

**答：** 要使控制器恢复工作，必须根据[更换 StorSimple 设备上的控制器模块](storsimple-controller-replacement.md)中所述将它插入机箱。

## <a name="next-steps"></a>后续步骤
* 如果使用本教程中所列的过程无法解决你遇到的 StorSimple 设备控制器相关问题，请[联系 Microsoft 支持](storsimple-contact-microsoft-support.md)。
* 有关如何使用 StorSimple Manager 服务的详细信息，请转到[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。

