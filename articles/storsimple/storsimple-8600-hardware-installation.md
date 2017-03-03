---
title: "安装 Microsoft Azure StorSimple 8600 设备 | Microsoft 文档"
description: "介绍如何在部署和配置软件之前打开 StorSimple 8600 设备的包装并进行机架安装和布线。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/24/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: eb56cae77722268f42e5126c45ad2878af7db94a
ms.openlocfilehash: 309ceba2d65c0745ba1acac698acb62526ab8078
ms.lasthandoff: 01/26/2017


---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>打开 StorSimple 8600 设备的包装并进行机架安装和布线
## <a name="overview"></a>概述
Microsoft Azure StorSimple 8600 是一种双机箱设备，由主机箱和 EBOD 机箱组成。 本教程介绍如何在配置 StorSimple 软件之前打开 StorSimple 8600 设备的包装并进行机架安装和布线。

## <a name="unpack-your-storsimple-8600-device"></a>打开 StorSimple 8600 设备的包装
下述步骤详细清晰地阐述了如何打开 StorSimple 8600 存储设备的包装。 此设备采用两箱式装运，分别装载主机箱和 EBOD 机箱。 两个箱子再放入单个箱子中。

### <a name="prepare-to-unpack-your-device"></a>准备打开设备的包装
打开设备包装之前，请查看以下信息。

![警告图标](./media/storsimple-safety/IC740879.png)![重物图标](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)**警告！**

1. 若要手动装卸机箱，请确保有两名人员分担设备重量。 配置完整的机箱重达 32 kg（70 磅）。
2. 将箱子放在平坦的水平表面上。

接着，按以下步骤打开设备包装。

#### <a name="to-unpack-your-device"></a>打开设备包装
1. 检查箱子和包装泡沫是否有破损、刮痕、水渍或任何其他明显损坏。 如果箱子或包装严重损坏，请勿打开箱子。 请[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)，帮助你评估设备是否处于良好的工作状态。
2. 打开外箱，然后取出装有主机箱和 EBOD 机箱的两个箱子。 现可打开主机箱和 EBOD 机箱的包装。 下图显示了一个机箱解包后的视图。
   
    ![打开存储设备的包装](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **存储设备解包后的视图**
   
   | 标签 | 说明 |
   | --- | --- |
   |   1 |包装箱 |
   |   2 |SAS 电缆（位于配件和电缆槽中） |
   |   3 |底部泡沫 |
   |   4 |设备 |
   |   5 |顶部泡沫 |
   |   6 |配件盒 |
3. 打开两个包装箱/后，请确保内附：
   
   * 1 个主机箱（主机箱和 EBOD 机箱分装在两个箱子中）
   * 1 个 EBOD 机箱
   * 4 根电源线（每箱中 2 根）
   * 2 根 SAS 电缆（用于连接主机箱和 EBOD 机箱）
   * 1 根以太网交叉电缆
   * 2 根串行控制台电缆
   * 1 个 USB 串口转换器（用于串行访问）
   * 4 个 QSFP 到 SFP+ 适配器（用于 10 GbE 网络接口）
   * 2 个机架安装套件（每箱中 1 个），4 个安装硬件的侧导轨（主机箱和 EBOD 机箱各用 2 个）
   * 入门文档
     
     如果上述任一部件缺失，请[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。  

接下来是安装设备的机架。

## <a name="rack-mount-your-storsimple-8600-device"></a>安装 StorSimple 8600 设备的机架
请按照以下步骤，将 StorSimple 8600 存储设备安装在 19 英寸标准机架（带前后立柱）中。 此设备带有两个机箱：1 个主机箱和&1; 个 EBOD 机箱。 两个机箱皆需安装在机架上。

安装需要多个步骤，各步骤请详见以下流程。

> [!IMPORTANT]
> StorSimple 设备必须安装在机架上才能正常工作。
> 
> 

### <a name="site-preparation"></a>安装位置准备
机箱必须安装在带前后立柱的 19 英寸标准机架中。 按照以下流程，做好机架安装准备。

#### <a name="to-prepare-the-site-for-rack-installation"></a>准备好安装位置进行机架安装
1. 请确保将主机箱和 EBOD 机箱安全地放在平坦稳定的水平工作台面（或类似表面）上。
2. 确认要布置的安装位置具有来自独立源的标准交流电，或具有带不间断电源 (UPS) 的机架电源分配单元 (PDU)。
3. 确保要安装机箱的机架上有 1 个 4U (2 X 2U) 插槽。

![警告图标](./media/storsimple-safety/IC740879.png)![重物图标](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)**警告！**

 若要手动安装设备，请确保有两名人员分担设备重量。 配置完整的机箱重达 32 kg（70 磅）。

### <a name="rack-prerequisites"></a>机架必备组件
机箱应安装在 19 英寸标准机架柜中，尺寸大小为：

* 机架立柱间最小深度为 27.84 英寸
* 设备的最大重量为 32 kg
* 最大背压为 5 帕斯卡（0.5 mm 水位计）

### <a name="rack-mounting-rail-kit"></a>机架安装导轨套件
提供了 1 组用于 19 英寸机架箱的安装导轨。 导轨已经过测试，足以承担最大机箱重量。 还可在这些导轨安装多个机箱，而不会额外占用机架空间。 首先安装 EBOD 机箱。

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>将 EBOD 机箱安装在导轨上
1. 仅当设备上未安装内部导轨时，才需执行此步骤。 通常，出厂时内部导轨已装好。 若未安装，则将左侧和右侧导轨安装到机箱底架的侧面。 每侧使用&6; 个公制螺丝连接。 为帮助确定方向，导轨上标有 **LH – Front** 和 **RH – Front**，固定在机箱后部的导轨一端为锥形。
   
    ![在机箱底部安装导轨](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **在机箱侧面安装导轨**
   
   | 标签 | 说明 |
   | --- | --- |
   |  1 |M 3x4 圆头螺丝 |
   |  2 |底架滑轨 |
2. 将左右导轨组件连接到机架柜的垂直部件。 支架标有 **LH**、**RH** 和**此面朝上**，可帮助正确定向。
3. 找到导轨组件前部和后部的导轨销。 延长导轨，使其适合机架立柱间距，并将销插入前后机架立柱的垂直部件孔中。 确保导轨组件保持水平。
4. 使用提供的&2; 个公制螺丝将导轨组件固定到机架垂直部件上。 前部和后部各使用&1; 颗螺丝。
5. 其他导轨组装请重复以上步骤。
   
     ![将导轨固定到机架机箱上](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **将导轨组件固定到机架上**
   
   | 标签 | 说明 |
   | --- | --- |
   |   1 |旋紧螺丝 |
   |   2 |方孔前机架立柱螺丝 |
   |   3 |左前侧导轨固定销 |
   |   4 |旋紧螺丝 |
   |   5 |左侧导轨固定销 |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>将 EBOD 机箱安装到机架上
使用刚安装的机架导轨，执行以下步骤将 EBOD 机箱安装到机架中。

#### <a name="to-mount-the-ebod-enclosure"></a>安装 EBOD 机箱
1. 在他人的协助下，将机箱抬起并将其与机架导轨对齐。
2. 将机箱小心插入导轨，然后将其完全推入机架机箱。
   
    ![将设备插入机架](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **将机箱安装到机架上**
3. 拉松法兰帽，取下左前和右前的法兰帽。 只需将法兰帽卡到法兰上。
4. 每个法兰左右各安装&1; 个提供的十字螺丝，将机箱固定在机架中。
5. 将法兰帽压入到合适位置并将其卡入到位，完成法兰帽安装。
   
     ![安装法兰帽](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **安装法兰帽**
   
   | 标签 | 说明 |
   | --- | --- |
   |   1 |机箱紧固螺丝 |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>将主机箱安装到机架上
EBOD 机箱安装完成后，需按照相同步骤安装主机箱。

> [!NOTE]
> * 可在主机箱和 EBOD 机箱间的机架中留有几个空插槽。
> * 使用所提供的 2m SAS 电缆连接主机箱和 EBOD 机箱。
> * 主机箱单元与 EBOD 单元可随意相对放置。 因此，主机箱可放在顶部槽，EBOD 机箱可放在下方，反之亦然。
> 
> 

下一步是进行设备的电源、网络和串行访问布线。

## <a name="cable-your-storsimple-8600-device"></a>进行 StorSimple 8600 设备布线
以下过程阐释了如何针对电源、网络和串行连接进行 StorSimple 8600 设备布线。

### <a name="prerequisites"></a>先决条件
在开始设备布线之前，需要：

* 主机箱和 EBOD 机箱（完全解包）
* 设备随附的&4; 根电缆（主机箱和 EBOD 机箱各&2; 根）
* 设备随附的&2; 根 SAS 电缆（用于连接 EBOD 机箱和主机箱）
* 具有 2 个电源分配单位 (PDU)（推荐）
* 网络电缆
* 提供的串行电缆
* USB 串口转换器，且计算机上安装有相应的驱动程序（若需要）
* 提供的 4 个 QSFP 到 SFP+ 适配器（用于 10 GbE 网络接口）
* [StorSimple 设备上适用于 10 GbE 网络接口的支持硬件](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS 和电源布线
此设备配有&1; 个主机箱和&1; 个 EBOD 机箱。 需要将这两个单元连接到一起，才可获得串行 SCSI (SAS) 连接和电源。

首次布置此设备时，要先执行 SAS 布线步骤，再完成电源布线步骤。

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>网络布线
此设备采用“活动-备用”配置：在任意特定时间，一个控制器模块处于活动状态，处理所有磁盘和网络操作，而另一个控制器模块则处于备用状态。 如果该控制器出现故障，备用控制器会立即激活，继续执行所有磁盘和网络操作。

若要支持此冗余控制器故障转移，需按下述步骤进行设备网络布线。

#### <a name="to-cable-for-network-connection"></a>网络连接布线
1. 该设备上的每个控制器均具有 6 个网络接口：4 个 1 Gbps 和 2 个 10 Gbps 以太网端口。 参照以下图示，确定设备底板上的数据端口。
   
     ![8600 设备底板](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **设备背面显示数据端口**
   
   | 标签 | 说明 |
   | --- | --- |
   |   0,1,4,5 |1 GbE 网络接口 |
   |   2,3 |10 GbE 网络接口 |
   |   6 |串行端口 |
2. 有关网络布线的信息，请参阅下图。 （最低网络配置以蓝色实线显示。 高可用性和性能所需的其他配置显示为虚线。）

![进行 4U 设备的网络布线](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**设备网络布线**

| 标签 | 说明 |
| --- | --- |
| A |具有 Internet 访问的 LAN |
| B |控制器 0 |
| C |PCM 0 |
| D |控制器 1 |
| E |PCM 1 |
| F |EBOD 控制器 0 |
| G |EBOD 控制器 1 |
| H,I |主机（如文件服务器） |
| 0-5 |网络接口 |
| 6 |主机箱 |
| 7 |EBOD 机箱 |

进行设备布线时，最低配置要求为：

* 每个控制器上至少连接&2; 个网络接口，分别用于云访问和 iSCSI。 DATA 0 端口已自动启用，且通过设备串行控制台配置完毕。 除 DATA 0 外，还需要通过 Azure 经典门户再配置一个数据端口。 在此情况下，将 DATA 0 端口连接到主 LAN（具有 Internet 访问的网络）。 其他数据端口可连接到网络的 SAN/iSCSI LAN (VLAN) 分段，具体取决于预期角色。
* 将每个控制器上的相同接口连接到同一网络中，确保在发生控制器故障转移时可用。 例如，如果选择对一个控制器连接 DATA 0 和 DATA 3，则需对另一控制器连接相应的 DATA 0 和 DATA 3。

对于高可用性和性能，请记住：

* 如果可能，可在每个控制器上配置一对用于云访问的网络接口 (1 GbE) 和另一对用于 iSCSI 的网络接口（推荐&10; GbE）。
* 如果可能，可将每个控制器的网络接口连接到两台不同的交换机上，确保其中一台发生故障时设备仍可用。 上图展示了连接到两台不同交换机的每个控制器上的两个 10 GbE 网络接口（DATA 2 和 DATA 3）。 有关详细信息，请参阅 [StorSimple 设备高可用性要求](storsimple-system-requirements.md#high-availability-requirements-for-storsimple)下的**网络接口**。

> [!NOTE]
> 如果在 10 GbE 网络接口上使用 SFP+ 收发器，请使用提供的 QSFP-SFP+ 适配器。 有关详细信息，请转到 [StorSimple 设备上适用于 10 GbE 网络接口的支持硬件](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)。
> 
> 

### <a name="serial-port-cabling"></a>串行端口布线
按以下步骤执行串行端口布线。

#### <a name="to-cable-for-serial-connection"></a>串行连接布线
1. 设备每个控制器上都有一个用扳手图标标记的串行端口。 若要找到串行端口，请参考图示，其中显示有设备背面的数据端口。
2. 确定设备底板上的活动控制器。 闪烁的蓝色 LED 指示控制器处于活动状态。
3. 使用提供的串行电缆（必要时可使用笔记本电脑的 USB 串口转换器），并将控制台或计算机（使用终端模拟连接到设备）连接到活动控制器的串行端口。
4. 在计算机上安装 USB 串口驱动程序（随设备一起提供）。
5. 布置串行连接，如下所示：
   
   * 115,200 波特
   * 8 数据位
   * 1 停止位
   * 无奇偶校验位
   * 流控制设置为“无”
6. 在控制台中按 Enter 键确认连接是否正常。 将出现串行控制台菜单。

> [!NOTE]
> **无人值守管理**：当设备安装在远程数据中心或有限访问的计算机机房中时，请确保与两个控制器的串行连接始终连接到串行控制台交换机或类似设备。 如果发生网络中断或意外故障，这可实现带外远程控制和支持操作。
> 
> 

现已完成设备电源、网络访问和串行连接布线。下一步便是配置设备上的软件。

## <a name="next-steps"></a>后续步骤
现即可[部署和配置本地 StorSimple 设备](storsimple-deployment-walkthrough-u2.md)。


