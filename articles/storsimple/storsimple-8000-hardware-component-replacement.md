---
title: "StorSimple 8000 系列硬件组件更换 | Microsoft Docs"
description: "介绍如何安全地更换 PCM、电池、控制器模块、EBOD 控制器、磁盘驱动器和 StorSimple 设备的底盘。"
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
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: 
ms.openlocfilehash: 6de50c5031db59176bdf17ecc69b934559220f6a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>更换 StorSimple 8000 系列设备上的硬件组件

## <a name="overview"></a>概述
此硬件组件更换教程介绍 Microsoft Azure StorSimple 8000 系列设备的硬件组件，以及移除和更换这些组件所需的步骤。 本文介绍安全图标、提供指向详细教程的链接，并列出可更换的组件。

> [!IMPORTANT]
> 尝试移除或更换任何 StorSimple 组件之前，请确保查看[安全图标约定](#safety-icon-conventions)以及其他[安全注意事项](storsimple-safety.md)。


### <a name="safety-icon-conventions"></a>安全图标约定
下表描述这些教程中使用的安全图标。 执行移除和替换设备组件的步骤时，请特别注意这些安全图标。

| 图标 | 文本 | 其他信息 |
|:--- |:--- |:--- |
| ![警告图标](./media/storsimple-hardware-component-replacement/Warning.png) |**危险!** |指示如果未能避免危险情况，将导致死亡或重伤。 此警示语仅限于最为极端的情况。 |
| ![警告图标](./media/storsimple-hardware-component-replacement/Warning.png) |**警告!** |指示如果未能避免危险情况，可能导致死亡或重伤。 |
| ![提醒图标](./media/storsimple-hardware-component-replacement/Caution.png) |**小心!** |指示如果未能避免危险情况，可能导致轻伤或重伤。 |
| ![注意图标](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**注意:** |指示应该给予重视但不涉及危险情况的信息。 |
| ![电击图标](./media/storsimple-hardware-component-replacement/Electric.png) |**点击危险** |指示高压电。 |
| ![重物图标](./media/storsimple-hardware-component-replacement/Weight.png) |**重物** | |
| ![无用户可维修部件图标](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**无用户可维修部件** |除非经过适当的培训，否则请勿访问。 |
| ![阅读说明图标](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**先阅读所有说明** | |
| ![倾覆风险图标](./media/storsimple-hardware-component-replacement/TipHazard.png) |**倾覆风险** | |

### <a name="before-you-begin"></a>开始之前
自行熟悉本教程中使用的设备和安全图标的安全信息。 如需完整的信息，请转到[安全地安装和运行 StorSimple 设备](storsimple-safety.md)。 在接触 StorSimple 设备之前，请务必查看[安全注意事项](storsimple-safety.md#handling-precautions)。

在尝试更换组件之前，请考虑以下信息。

![警告图标](./media/storsimple-hardware-component-replacement/Warning.png) ![电击图标](./media/storsimple-hardware-component-replacement/Electric.png) **WARNING!**

* 正确接地：在接触 StorSimple 设备的模块和组件时，使用静电放电装置或抗静电垫。
* 不要触摸任何电路。 在接触可能会泄露电路的组件时，使用提供的手柄和导杆。

![警告图标](./media/storsimple-hardware-component-replacement/Warning.png) ![注意图标](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **注意:**

替换某个模块时，**千万不要将空托架留在机箱后部**。 在移除问题部件之前，先获得更换模块或空模块。

## <a name="hardware-component-replacement-procedures"></a>硬件组件更换过程
StorSimple 8000 系列设备的主机箱和/或 EBOD 机箱包含多个插件模块。 8100 采用单个主机箱，而 8600 是配有主机箱和 EBOD 机箱的双机箱设备。

下表总结了设备中的主要硬件组件。 单击“更换过程”栏中的链接即可转到关联的教程。

| 组件 | # 存在 | 插件模块？ | 更换过程 |
|:--- |:--- |:--- |:--- |
| 底盘 |1 |否 |[更换 StorSimple 设备上的底盘](storsimple-8000-chassis-replacement.md) |
| 主控制器 |2 |是 |[更换 StorSimple 设备上的控制器模块](storsimple-8000-controller-replacement.md) |
| 764W 电源和散热模块 (PCM) |2 |是 |[更换 StorSimple 设备上的电源和散热模块](storsimple-8000-power-cooling-module-replacement.md) |
| 备用电池 |2 |是 |[更换 StorSimple 设备上的备用电池模块](storsimple-8000-battery-replacement.md) |
| 磁盘驱动器 |12 |是 |[更换 StorSimple 设备上的磁盘驱动器](storsimple-8000-disk-drive-replacement.md) |

**表 1** 主机箱中的硬件组件

主机箱和 EBOD 机箱的不同之处在于其 I/O 模块。 此外，PCM 功率也有所不同。 主机箱中的 PCM 是 764 W，而 EBOD 机箱中的是 580 W。主机箱中的 PCM 还包含备用电池模块。

| 组件 | # 存在 | 插件模块？ | 更换过程 |
|:--- |:--- |:--- |:--- |
| 底盘 |1 |否 |[更换 StorSimple 设备上的底盘](storsimple-8000-chassis-replacement.md) |
| EBOD 控制器 |2 |是 |[更换 StorSimple 设备上的 EBOD 控制器](storsimple-8000-ebod-controller-replacement.md) |
| 580W 电源和散热模块 (PCM) |2 |是 |[更换 StorSimple 设备上的电源和散热模块](storsimple-8000-power-cooling-module-replacement.md) |
| 磁盘驱动器 |12 |是 |[更换 StorSimple 设备上的磁盘驱动器](storsimple-8000-disk-drive-replacement.md) |

**表 2** EBOD 机箱中的硬件组件

以下正面图和背面图突出显示了设备上的插件模块。 在需要更换插件模块时，这些图可用于确定各种插件模块的位置。 EBOD 机箱和主机箱的正面图显示磁盘驱动器，背面图显示插件模块。

![带有磁盘驱动器的设备前面板](./media/storsimple-hardware-component-replacement/IC741028.png)

**图 1** 设备的前面

| 标签 | 说明 |
|:--- |:--- |
| 0 - 11 |磁盘驱动器（总数为 12） |

主机箱和 EBOD 机箱都有驱动器托架模块。 底盘可容纳 12 个 3.5 英寸磁盘驱动器，按 3x4 模式排列。

![设备主机箱模块底板](./media/storsimple-hardware-component-replacement/IC740994.png)

**图 2** 主机箱的背面

| 标签 | 说明 |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |控制器 0 |
| 4 |控制器 1 |

![设备 EBOD 机箱插件模块的底板](./media/storsimple-hardware-component-replacement/IC769599.png)

**图 3** EBOD 机箱的背面

| 标签 | 说明 |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD 控制器 0 |
| 4 |EBOD 控制器 1 |

## <a name="field-replaceable-units"></a>现场可更换部件
以下现场可更换部件 (FRU) 适用于 StorSimple 设备：

* 底盘（包括集成的操作面板）
* 764 W AC PCM
* 580 W AC PCM
* 硬盘驱动器和驱动器托架模块
* 控制器模块
* EBOD 控制器模块
* 备用电池模块
* 机架安装导轨套件

若要订阅任何此类更换部件，请[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)。

## <a name="next-steps"></a>后续步骤
在尝试更换 StorSimple 硬件组件之前，请查看所有[安全信息](storsimple-safety.md)。

