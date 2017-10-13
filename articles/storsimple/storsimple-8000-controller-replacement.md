---
title: "更换 StorSimple 8000 系列设备控制器 | Microsoft Docs"
description: "说明如何拆卸和更换 StorSimple 8000 系列设备上的一个或两个控制器模块。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 849eccff114c2fd6d952e44d095d0cc89a238675
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>更换 StorSimple 设备上的控制器模块
## <a name="overview"></a>概述
本主题介绍如何拆卸和更换 StorSimple 设备中的一个或两个控制器模块。 此外，介绍单双控制器更换方案的基本逻辑。

> [!NOTE]
> 在更换控制器之前，我们建议将控制器固件更新到最新版本。
> 
> 为了防止对 StorSimple 设备造成损坏，请在 LED 显示以下状态之前不要弹出控制器：
> 
> * 所有指示灯熄灭。
> * LED 3、![绿色打勾图标](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png)和![红色打叉图标](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png)闪烁，LED 0 和 LED 7 **亮起**。


下表显示了支持的控制器更换方案。

| 案例 | 更换方案 | 适用的过程 |
|:--- |:--- |:--- |
| 1 |一个控制器处于故障状态，另一个控制器处于正常活动状态。 |[单控制器更换](#replace-a-single-controller)，其中描述了[单控制器更换背后的逻辑](#single-controller-replacement-logic)，以及[更换步骤](#single-controller-replacement-steps)。 |
| #N/A |两个控制器都发生故障，需要更换。 机箱、磁盘和磁盘盒都工作正常。 |[双控制器更换](#replace-both-controllers)，其中描述了[双控制器更换背后的逻辑](#dual-controller-replacement-logic)，以及[更换步骤](#dual-controller-replacement-steps)。 |
| 3 |切换来自同一个设备或不同设备的控制器。 机箱、磁盘和磁盘盒都工作正常。 |将显示插槽不匹配警报消息。 |
| 4 |一个控制器缺失，另一个控制器发生故障。 |[双控制器更换](#replace-both-controllers)，其中描述了[双控制器更换背后的逻辑](#dual-controller-replacement-logic)，以及[更换步骤](#dual-controller-replacement-steps)。 |
| 5 |一个或两个控制器发生故障。 无法通过串行控制台或 Windows PowerShell 远程处理功能访问设备。 |请[联系 Microsoft 支持](storsimple-8000-contact-microsoft-support.md)，了解手动更换控制器的过程。 |
| 6 |控制器有不同的内部版本，这可能是因为：<ul><li>控制器有不同的软件版本。</li><li>控制器有不同的固件版本。</li></ul> |如果控制器的软件版本不同，则更换逻辑会检测到这种情况，并更新用于更换的控制器的软件版本。<br><br>如果控制器的固件版本不同，并且旧固件版本**无法**自动升级，Azure 门户中会显示一条警报消息。 应扫描更新并安装固件更新。</br></br>如果控制器的固件版本不同，并且旧固件版本可自动升级，则控制器更换逻辑将检测到这种情况，在控制器启动后，会自动更新固件。 |

如果控制器模块发生故障，需要将其拆下。 一个或两个控制器模块可能发生故障，从而需要执行单控制器更换或双控制器更换过程。 有关更换过程及其背后的逻辑：请参阅以下主题：

* [更换单个控制器](#replace-a-single-controller)
* [更换两个控制器](#replace-both-controllers)
* [拆卸控制器](#remove-a-controller)
* [插入控制器](#insert-a-controller)
* [识别设备的活动控制器](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> 拆卸和更换控制器之前，请查看 [StorSimple 硬件组件更换](storsimple-8000-hardware-component-replacement.md)中的安全信息。
> 
> 

## <a name="replace-a-single-controller"></a>更换单个控制器
当 Microsoft Azure StorSimple 设备上的两个控制器之一发生故障、工作异常或缺失时，需要更换一个控制器。

### <a name="single-controller-replacement-logic"></a>单控制器更换逻辑
在单控制器更换过程中，首先应拆下有故障的控制器。 （设备中的另一个控制器为活动控制器。）在插入用于更换的控制器后，将执行以下操作：

1. 用于更换的控制器立即开始与 StorSimple 设备通信。
2. 在用于更换的控制器上复制活动控制器的虚拟硬盘 (VHD) 快照。
3. 修改该快照，以便在从此 VHD 启动用于更换的控制器时，将该控制器视为备用控制器。
4. 完成修改之后，用于更换的控制器将作为备用控制器启动。
5. 当两个控制器处于运行状态时，群集会联机。

### <a name="single-controller-replacement-steps"></a>单控制器更换步骤
如果 Microsoft Azure StorSimple 设备中的某个控制器发生故障，请完成以下步骤。 （另一个控制器必须处于活动状态，并且正在运行。 如果两个控制器都发生故障或功能异常，请转到[双控制器更换步骤](#dual-controller-replacement-steps)。）

> [!NOTE]
> 控制器从单控制器更换过程重新启动和完全恢复可能需要 30 到 45 分钟的时间。 整个过程的总时间（包括连接电缆）大约为 2 小时。


#### <a name="to-remove-a-single-failed-controller-module"></a>拆卸发生故障的单个控制器模块
1. 在 Azure 门户中，转到“StorSimple Device Manager 服务”，单击“设备”，并单击要监视的设备的名称。
2. 转到“监视器”>“硬件运行状况”。 控制器 0 或控制器 1 的状态应以红色显示，表明发生了故障。
   
   > [!NOTE]
   > 在单控制器更换过程中，发生故障的控制器始终为备用控制器。
   
3. 参考图 1 和下表找到发生故障的控制器模块。
   
    ![设备主机箱模块底板](./media/storsimple-controller-replacement/IC740994.png)
   
    **图 1** StorSimple 设备的背面
   
   | 标签 | 说明 |
   |:--- |:--- |
   | 1 |PCM 0 |
   | #N/A |PCM 1 |
   | 3 |控制器 0 |
   | 4 |控制器 1 |
4. 在发生故障的控制器上，从数据端口拔下所有连接的网络电缆。 如果使用的是 8600 型号，也请拔下将该控制器连接到 EBOD 控制器的 SAS 电缆。
5. 按照[拆卸控制器](#remove-a-controller)中的步骤拆卸发生故障的控制器。
6. 将厂家更换件安装在拆卸故障控制器后空出的插槽中。 此时会触发单控制器更换逻辑。 有关详细信息，请参阅[单控制器更换逻辑](#single-controller-replacement-logic)。
7. 单控制器更换逻辑在后台运行时，请重新连接电缆。 小心连接所有电缆，连接方式与更换前的连接方式完全一样。
8. 控制器重新启动后，在 Azure 门户中检查“控制器状态”和“群集状态”，确认该控制器是否已恢复到正常工作状态，并且处于备用模式。

> [!NOTE]
> 如果通过串行控制台监视设备，可以发现在控制器从更换过程恢复时会多次重新启动。 显示串行控制器菜单即表示更换过程已完成。 如果该菜单在开始更换控制器后的 2 小时内未出现，请[联系 Microsoft 支持](storsimple-8000-contact-microsoft-support.md)。
>
> 从更新 4 开始，还可以使用设备 Windows PowerShell 界面中的 cmdlet `Get-HCSControllerReplacementStatus` 监视控制器更换过程的状态。
> 

## <a name="replace-both-controllers"></a>更换两个控制器
当 Microsoft Azure StorSimple 设备上的两个控制器发生故障、工作异常或缺失时，需要更换这两个控制器。 

### <a name="dual-controller-replacement-logic"></a>双控制器更换逻辑
要更换两个控制器，首先应该拆下两个有故障的控制器，然后插入用于更换的控制器。 插入两个用于更换的控制器后，将执行以下操作：

1. 插槽 0 中用于更换的控制器会检查以下项目：
   
   1. 控制器是否使用了最新版本的固件和软件？
   2. 控制器是否已加入群集？
   3. 是否运行了对等控制器，该控制器是否已加入群集？
      
      如果上述问题的答案均为“否”，则控制器会查找最新的每日备份（位于驱动器 S 上的 **nonDOMstorage** 中）。 控制器从该备份复制 VHD 的最新快照。
2. 插槽 0 中的控制器使用该快照构建自身的映像。
3. 与此同时，插槽 1 中的控制器等待控制器 0 完成映像创建并启动。
4. 当控制器 0 启动时，控制器 1 将检测由控制器 0 创建的群集，此时会触发单控制器更换逻辑。 有关详细信息，请参阅[单控制器更换逻辑](#single-controller-replacement-logic)。
5. 然后，两个控制器将会运行，群集将会联机。

> [!IMPORTANT]
> 在执行双控制器更换并配置 StorSimple 设备后，必须对该设备进行手动备份。 每日设备配置备份直到 24 小时之后才触发。 请在 [Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)的协助下，对设备进行手动备份。


### <a name="dual-controller-replacement-steps"></a>双控制器更换步骤
当 Microsoft Azure StorSimple 设备中的控制器均发生故障时，需要执行此工作流。 这种情况可能会发生在冷却系统出现故障的数据中心。两个控制器随后也会在短时间内发生故障。 根据 StorSimple 设备是处于关闭还是打开状态，以及使用的是 8600 还是 8100 型号，需要采取的步骤也各不相同。

> [!IMPORTANT]
> 控制器从双控制器更换过程重新启动和完全恢复可能需要 45 分钟到 1 小时的时间。 整个过程的总时间（包括连接电缆）大约为 2.5 小时。

#### <a name="to-replace-both-controller-modules"></a>更换两个控制器模块
1. 如果设备已关闭，请跳过此步骤，转到下一步。 如果设备已打开，请关闭设备。
   
   1. 如果使用的是 8600 型号，请先关闭主机箱，然后关闭 EBOD 机箱。
   2. 等待设备完全关闭。 设备背面的所有 LED 都将熄灭。
2. 拔下连接到数据端口的所有网络电缆。 如果使用的是 8600 型号，也请拔下将主机箱连接到 EBOD 机箱的 SAS 电缆。
3. 从 StorSimple 设备上拆下两个控制器。 有关详细信息，请参阅[拆卸控制器](#remove-a-controller)。
4. 请先插入控制器 0 的厂家更换部件，并插入控制器 1。 有关详细信息，请参阅[插入控制器](#insert-a-controller)。 此时会触发双控制器更换逻辑。 有关详细信息，请参阅[双控制器更换逻辑](#dual-controller-replacement-logic)。
5. 控制器更换逻辑在后台运行时，请重新连接电缆。 小心连接所有电缆，连接方式与更换前的连接方式完全一样。 请参阅[安装 StorSimple 8100 设备](storsimple-8100-hardware-installation.md)或[安装 StorSimple 8600 设备](storsimple-8600-hardware-installation.md)的“设备布线”部分中与具体型号相关的详细说明。
6. 打开 StorSimple 设备。 如果使用的是 8600 型号：
   
   1. 确保先打开 EBOD 机箱。
   2. 等到 EBOD 机箱开始运行。
   3. 打开主机箱。
   4. 在第一个控制器重新启动并处于正常运行状态后，系统就会运行。
      
      > [!NOTE]
      > 如果通过串行控制台监视设备，可以发现在控制器从更换过程恢复时会多次重新启动。 显示串行控制器菜单即表示更换过程已完成。 如果该菜单在开始更换控制器后的 2.5 小时内未出现，请[联系 Microsoft 支持](storsimple-8000-contact-microsoft-support.md)。
     
## <a name="remove-a-controller"></a>拆卸控制器
请遵循以下过程从 StorSimple 设备上拆卸发生故障的控制器模块。

> [!NOTE]
> 以下示意图适用于控制器 0。 对于控制器 1，应该反过来看。


#### <a name="to-remove-a-controller-module"></a>拆卸控制器模块
1. 用拇指和食指捏住模块闩锁。
2. 轻轻地用拇指和食指将控制器闩锁拉开。
   
    ![拉开控制器闩锁](./media/storsimple-controller-replacement/IC741047.png)
   
    **图 2** 拉开控制器闩锁
3. 使用闩锁作为把手将控制器从机箱中滑出。
   
    ![将控制器从机箱中滑出](./media/storsimple-controller-replacement/IC741048.png)
   
    **图 3** 将控制器从机箱中滑出

## <a name="insert-a-controller"></a>插入控制器
从 StorSimple 设备中拆下发生故障的模块后，请遵循以下过程安装厂家提供的控制器模块。

#### <a name="to-install-a-controller-module"></a>安装控制器模块
1. 查看接口连接器是否有任何损坏。 如果连接器引脚受损或弯曲，请不要安装模块。
2. 在闩锁完全松开的情况下，将控制器模块滑入机箱中。
   
    ![将控制器滑入机箱中](./media/storsimple-controller-replacement/IC741053.png)
   
    **图 4** 将控制器滑入机箱中
3. 插入控制器模块后，一边关闭闩锁，一边继续将控制器模块推入机箱中。 闩锁会啮合，将控制器卡入位。
   
    ![关闭控制器闩锁](./media/storsimple-controller-replacement/IC741054.png)
   
    **图 5** 关闭控制器闩锁
4. 闩锁卡入位后，安装过程即告完成。 现在，**OK** LED 应会亮起。
   
   > [!NOTE]
   > 控制器和 LED 激活最长可能需要 5 分钟时间。
  
5. 若要验证更换是否成功，请在 Azure 门户中转到设备，导航到“监视器” > “硬件运行状况”，确保“控制器 0”和“控制器 1”运行正常（状态为绿色）。

## <a name="identify-the-active-controller-on-your-device"></a>识别设备的活动控制器
在很多情况下（例如，首次注册设备或更换控制器时），需要在 StorSimple 设备中找到活动控制器。 活动控制器处理所有磁盘固件和联网操作。 可以使用以下方法识别活动控制器：

* [使用 Azure 门户识别活动控制器](#use-the-azure-portal-to-identify-the-active-controller)
* [使用 Windows PowerShell for StorSimple 识别活动控制器](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [通过检查物理设备识别活动控制器](#check-the-physical-device-to-identify-the-active-controller)

下面介绍了其中的每个过程。

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>使用 Azure 门户识别活动控制器
在 Azure 门户中，依次导航到设备、“监视器” > “硬件运行状况”，并滚动到“控制器”部分。 可以在此处确认哪个控制器处于活动状态。

![在 Azure 门户中识别活动控制器](./media/storsimple-controller-replacement/IC752072.png)

**图 6** 显示活动控制器的 Azure 门户

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>使用 Windows PowerShell for StorSimple 识别活动控制器
通过串行控制台访问设备时，会显示一条标题消息。 这条标题消息包含基本的设备信息，例如型号、名称、安装的软件版本以及正在访问的控制器的状态。 下图显示了一个标题消息示例：

![串行标题消息](./media/storsimple-controller-replacement/IC741098.png)

**图 7** 显示控制器 0 处于活动状态的标题消息

可以使用标题消息来确定连接到的控制器是处于主动还是被动状态。

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>通过检查物理设备识别活动控制器
若要识别设备中的主动控制器，请找到主机箱背面 DATA 5 端口上方的蓝色 LED。

如果该 LED 一直闪烁，则此控制器处于活动状态，而另一控制器处于备用模式。 请参考下面的示意图和表格。

![带有数据端口的设备主机箱背板](./media/storsimple-controller-replacement/IC741055.png)

**图 8** 带有数据端口和监控 LED 的主机箱的背面

| 标签 | 说明 |
|:--- |:--- |
| 1-6 |DATA 0 – 5 网络端口 |
| 7 |蓝色 LED |

## <a name="next-steps"></a>后续步骤
详细了解 [StorSimple 硬件组件更换](storsimple-8000-hardware-component-replacement.md)。

