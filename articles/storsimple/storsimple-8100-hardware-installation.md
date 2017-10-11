---
title: "安装 Microsoft Azure StorSimple 8100 设备 |Microsoft 文档"
description: "描述如何解包、 机架安装，以及在部署和配置软件之前，你的 StorSimple 8100 设备用电缆连接。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 102dffcd73f3d3b9362d7b2853faa060e9c645dd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>解压缩，机架式安装并用电缆连接 StorSimple 8100 设备
## <a name="overview"></a>概述
你的 Microsoft Azure StorSimple 8100 是一个单机箱、 架装设备。 本教程介绍如何解包，机架安装和电缆 StorSimple 8100 设备硬件之前配置和部署 StorSimple 设备。

## <a name="unpack-your-storsimple-8100-device"></a>打开 StorSimple 8100 设备的包装
以下步骤提供有关如何打开 StorSimple 8100 存储设备的包装的清除，详细说明。 此设备采用单个包装盒传送。

### <a name="prepare-to-unpack-your-device"></a>准备要打开设备的包装
打开设备的包装之前，请查看以下信息。

![警告图标](./media/storsimple-safety/IC740879.png)![举重图标](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png)**警告 ！**

1. 请确保你有两个人员在可用于管理在机箱的权重，如果你正在手动处理它。 完整配置的机箱重量可达 32 公斤 （70 磅）。
2. 框置于平坦且水平的图面上。

接下来，完成以下步骤以打开设备的包装。

#### <a name="to-unpack-your-device"></a>若要打开设备的包装
1. 检查包装盒和包装泡沫材料，看存在破碎、 刮痕、 水渍或任何其他明显的损坏。 如果包装盒或包装材料严重受损，请勿打开包装盒。 请[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)来帮助你评估是否在设备处于正常工作。
2. 打开包装盒。 下图显示的解包的视图的 StorSimple 设备。
   
     ![打开存储设备的包装](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **存储设备的解包的视图**
   
   | Label | 描述 |
   | --- | --- |
   |   1 |包装盒 |
   |   2 |底部泡沫 |
   |   3 |设备 |
   |   4 |顶部泡沫 |
   |   5 |配件盒 |
3. 打开包装盒后, 请确保你有：
   
   * 1 台机箱设备
   * 2 条电源线
   * 1 条交叉以太网电缆
   * 2 条串行控制台电缆
   * 用于串行 1 的 USB 串行转换器
   * 1 把防干扰 T10 螺丝刀
   * 4 QSFP-到-用于 10 GbE 网络接口的 SFP + 单适配器
   * 1 个机架安装套件 （2 带安装硬件侧轨）
   * 入门文档
     
     如果你没有收到上面列出的任何的项[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。

下一步是机架方式安装你的设备。

## <a name="rack-mount-your-storsimple-8100-device"></a>StorSimple 8100 设备进行机架式安装
请按照下一步的步骤，在具有前后立柱 19 英寸标准机架中安装 StorSimple 8100 存储设备。 StorSimple 8100 设备具有单个主机箱。

安装涉及多个步骤，其中每个已在以下过程。

> [!IMPORTANT]
> StorSimple 设备必须是正确操作机架安装。
> 
> 

### <a name="prepare-the-site"></a>准备站点
设备必须安装在具有前后立柱的 19 英寸标准机架中。 使用以下过程为机架安装做准备。

#### <a name="to-prepare-the-site-for-rack-installation"></a>为机架安装做准备站点
1. 请确保设备安全地停留在平面、 平稳的水平工作台面 （或类似表面） 上。
2. 验证你想要设置的站点有来自独立源或机架配电装置 (PDU) 与不间断电源 (UPS) 的标准 AC 电源。
3. 请确保你打算在其中安装设备的机架上有该一个 2U 插槽。

![警告图标](./media/storsimple-safety/IC740879.png)![举重图标](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png)**警告 ！**

请确保你有两个人员在可用于管理权重，如果你正在手动处理设备设置。 完整配置的机箱重量可达 32 公斤 （70 磅）。

### <a name="rack-prerequisites"></a>机架系统必备组件
8100 机箱被设计安装到 19 英寸标准机架机柜使用：

* 最小深度为 27.84 英寸。 机架立柱到立柱。
* 最大重量为 32 公斤设备
* 最大背压为 5 帕斯卡 （0.5 毫米水位计）。

### <a name="rack-mounting-rail-kit"></a>机架安装导轨套件
一套安装导轨提供用于 19 英寸的机架机柜中。 Rails 已经过测试，以处理最大机箱重量。 这些导轨还允许安装多个机箱不会丢失任何在机架内的空间。

#### <a name="to-install-the-device-on-the-rails"></a>若要在 rails 上安装设备
1. 仅当你的设备上未安装内部 rails，请执行此步骤。 通常情况下，内部导轨是在出厂时安装。 如果未安装 rails，然后安装左右导轨装左右导轨滑块安装到机箱的侧面。 这些附加每一侧使用六个公制螺丝连接。 为了帮助提高方向，导轨滑块标有**LH-前**和**RH – 前**，并且附在机箱背面的一端有锥形的接头。<br/>
   
    ![将导轨滑块附加到机箱](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **将内部导轨滑块附加到机箱的侧面**
   
    Label | 描述
    ----- | -----------
    1     | M 3x4 圆头螺丝
    2     | 机箱滑块

2. 将外部左的导轨和外部右栏程序集附加到机架机柜垂直部件。 支架标有**LH**， **RH**，和**此面朝上的**以指导你确定正确方向。
3. 找到导轨装配件前后面的导轨装配件销。 将导轨销插入前后机架立柱垂直部件孔中地适应夹在机架立柱之间。 为确保导轨装配件处于水平。
4. 使用两个提供公制螺丝连接来保护到机架导轨装配件垂直部件。 一个螺丝用在前面，而另背面。
5. 对其他导轨装配件重复这些步骤。<br/>
   
     ![将导轨滑块附加到机架机柜](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **附加外部导轨装配件连接到机架**
   
   | Label | 描述 |
   | --- | --- |
   |   1 |旋紧螺丝 |
   |   2 |方孔前机架立柱螺丝 |
   |   3 |左的导轨前定位销 |
   |   4 |旋紧螺丝 |
   |   5 |左的导轨前后定位销 |

### <a name="mounting-the-device-in-the-rack"></a>安装在机架中设备
使用刚安装的机架导轨，执行以下步骤来装载在机架中的设备。

#### <a name="to-mount-the-device"></a>若要安装设备
1. 协助下，抬起机箱并将其与机架导轨对齐。
2. 小心地将设备插入导轨，然后设备将完全推入机架机柜。<br/>
   
    ![将设备插到机架中](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **安装在机架中设备**
3. 删除向左和右前法兰盖拉开盖取。 法兰盖只需卡到法兰上对齐。
4. 通过安装一个提供的十字螺丝通过每个法兰，左侧和右侧固定在机架中的存储模块。
5. 安装法兰盖，方法先将其压入相应位置和卡入到位。<br/>
   
     ![安装法兰盖](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **安装法兰盖**
   
   | Label | 描述 |
   | --- | --- |
   |   1 |机箱紧固螺丝 |

下一步是对设备进行电源、 网络和串行访问布线。

## <a name="cable-your-storsimple-8100-device"></a>StorSimple 8100 设备进行布线
以下过程介绍如何对 StorSimple 8100 设备进行电源、 网络和串行连接布线。

### <a name="prerequisites"></a>必备条件
在开始你的设备的电缆连接之前，你将需要：

* 您的存储设备，完全打开包装和机架安装。
* 你的设备附带的 2 条电缆
* 对 2 个配电装置 （建议） 的访问。
* 网络电缆
* 提供的串行电缆
* USB 串行转换器 （如果需要），在 PC 上安装适当的驱动程序
* 提供 4 QSFP-到-用于 10 GbE 网络接口的 SFP + 单适配器
* [对于 10 GbE 网络接口在 StorSimple 设备上支持的硬件](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>电源布线
你的设备包括冗余电源和冷却模块 (Pcm)。 同时，必须安装 Pcm，并将其连接到不同电源，以确保高可用性。

执行以下步骤以对设备进行电源布线。

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>网络布线
你的设备是主-备配置： 在任何给定时间，一个控制器模块处于主动状态并处理所有磁盘和网络操作，而另一个控制器模块处于备用状态。 如果一个控制器出现故障，则备用控制器就会立即激活，并继续所有磁盘和网络操作。

若要支持此冗余控制器故障转移，你需要用对设备进行网络电缆连接到以下步骤中所述。

#### <a name="to-cable-for-network-connection"></a>进行网络连接布线
1. 你的设备有 6 个网络接口上每个控制器： 四个 1 Gbps 和两个 10 Gbps 以太网端口。 标识设备底板上的各种数据端口。
   
    ![8100 设备的底板](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **显示数据端口的设备的背面**
   
   | Label | 描述 |
   | --- | --- |
   |   0,1,4,5 |1 GbE 网络接口 |
   |   2,3 |10 GbE 网络接口 |
   |   6 |串行端口 |
2. 请参阅下的图以了解网络电缆。 （最低网络配置显示为蓝色实线。 所需的高可用性和性能的其他配置显示为虚线。）

    ![网络你 2U 设备用电缆连接](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **网络电缆连接到你的设备**

   |Label | 描述 |
   |----- | ----------- |
   | A    | 访问 Internet 的 LAN |
   | B    | 控制器 0 |
   | C    | PCM 0 |
   | D    | 控制器 1 |
   | E    | PCM 1 |
   | F、 G | 主机 |
   | 0-5  | 网络接口 |



在设备用电缆连接，需要的最低配置：

* 至少两个网络接口连接以及一个用于云访问，一个用于 iSCSI 的每个控制器上。 DATA 0 端口自动启用和配置通过设备串行控制台中。 除了 DATA 0，另一个数据端口也需要通过 Azure 经典门户来配置。 在这种情况下，连接 DATA 0 端口到主要 LAN （Internet 访问网络）。 与其他数据端口可以连接到网络，具体取决于预期的角色的 SAN/iSCSI LAN (VLAN) 段。
* 每个控制器上的相同接口连接到相同的网络，以确保可用性，如果发生控制器故障转移。 例如，如果你选择连接 DATA 0 和 DATA 3 的其中一个控制器，你需要连接另一个控制器上的相应 DATA 0 和 DATA 3。

请记住用于高可用性和性能：

* 如果可能，请配置一对进行云访问 (1 GbE) 网络接口和 iscsi (建议的 10 GbE) 的另一个对每个控制器上。
* 如果可能，请连接到两个不同的交换机，以确保交换机发生故障针对可用性从每个控制器的网络接口。 图阐释了两个 10 GbE 网络接口 DATA 2 和 DATA 3，从每个控制器连接到两个不同的交换机。

有关详细信息，请参阅**网络接口**下[StorSimple 设备的高可用性要求](storsimple-system-requirements.md#high-availability-requirements-for-storsimple)。

> [!NOTE]
> 如果您正在使用 10 GbE 网络接口 SFP + 收发器，使用提供的 QSFP-SFP + 单适配器。 有关详细信息，请转到[支持 10 GbE 网络接口在 StorSimple 设备上的硬件](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)。
> 
> 

### <a name="serial-port-cabling"></a>串行端口布线
执行以下步骤以串行端口布线。

#### <a name="to-cable-for-serial-connection"></a>进行串行连接布线
1. 你的设备上有一个串行端口由扳手图标标识每个控制器。 指的插图中[网络布线](#network-cabling)部分，以找到你的设备底板上的串行端口。
2. 标识设备底板上的主动控制器。 闪烁的蓝色 LED 指示控制器处于活动状态。
3. 使用提供的串行电缆 （如果需要您的笔记本电脑的 USB 串行转换器），并将控制台或计算机 （使用设备的终端模拟） 连接到主动控制器的串行端口。
4. 在你的计算机上安装的 USB 串行驱动程序 （随设备）。
5. 设置串行连接，如下所示： 115200 波特、 8 个数据位、 1 个停止位、 无奇偶校验和设置为 None 的流控制。
6. 验证通过按 Enter，在控制台上的连接正常工作。 应显示串行控制台菜单。

> [!NOTE]
> **无人值守管理**： 时设备安装在远程数据中心或提供有限的访问权限的计算机房中，确保，两个控制器的串行连接始终连接到串行控制台交换机或类似设备。 这允许带外远程控制和支持操作是否存在网络中断或意外的故障。
> 
> 

你的设备现在通过进行电源、 网络访问权限和串行连接缆线连接。 下一步是将软件配置和部署你的设备。

## <a name="next-steps"></a>后续步骤
了解如何[部署和配置本地 StorSimple 设备](storsimple-deployment-walkthrough-u2.md)。

