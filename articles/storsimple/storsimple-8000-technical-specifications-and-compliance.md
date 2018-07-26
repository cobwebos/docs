---
title: StorSimple 技术规格 | Microsoft Docs
description: 介绍 StorSimple 硬件组件的技术规格和法规标准符合性信息。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 855ea6c34082b859bb5b5b6e69b3e3f2fa54eb4a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056457"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>StorSimple 设备的技术规格和符合性

## <a name="overview"></a>概述

Microsoft Azure StorSimple 设备的硬件组件遵循本文中概述的技术规格和法规标准。 此技术规格介绍电源和散热模块 (PCM)、磁盘驱动器、存储容量和机箱。 符合性信息涉及国际标准、安全与发射以及缆线等内容。

## <a name="power-and-cooling-module-specifications"></a>电源和散热模块规格

StorSimple 设备有两个 100-240V 双风扇且符合 SBB 标准的电源散热模块 (PCM)。 这提供了冗余电源配置。 如果 PCM 发生故障，设备将继续在另一个 PCM 上正常运行，直至发生故障的模块被更换。

EBOD 机箱使用 580 W PCM，而主机箱使用 764 W PCM。 下表列出了与 PCM 相关的技术规格。

| 规格 | 580 W PCM（EBOD 机箱） | 764 W PCM（主机箱） |
| --- | --- | --- |
| 最大输出功率 |580 W |764 |
| 频率 |50/60 Hz |50/60 Hz |
| 电压范围选择 |自动范围：90 – 264 V 交流，47/63 Hz |自动范围：90 – 264 V 交流，47/63 Hz |
| 最大浪涌电流 |20 A |20 A |
| 功率因数校正 |>95% 标称输入电压 |>95% 标称输入电压 |
| 谐波 |符合 EN61000-3-2 |符合 EN61000-3-2 |
| 输出 |5V 备用电压 \@ 2.0 A |5V 备用电压 \@ 2.7 A |
| + 5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| 可热插拔 |是 |是 |
| 开关和 LED |交流 ON/OFF 开关和四个状态指示器 LED |交流 ON/OFF 开关和六个状态指示器 LED |
| 机箱散热 |具有可变风扇转速控制的轴向冷却风扇 |具有可变风扇转速控制的轴向冷却风扇 |

## <a name="power-consumption-statistics"></a>功耗统计信息

下表列出了各种型号的 StorSimple 设备的典型功耗数据（实际值可能与已发布的数值不同）。

| 条件 | 240 V 交流 | 240 V 交流 | 240 V 交流 | 110 V 交流 | 110 V 交流 | 110 V 交流 |
| --- | --- | --- | --- | --- | --- | --- |
|  风扇转速缓慢，驱动器处于闲置状态 |1.45 A |0.31 kW |1057.76 BTU/小时 |3.19 A |0.34 kW |1160.13 BTU/小时 |
|  风扇转速缓慢，驱动器访问中 |1.54 A |0.33 kW |1126.01 BTU/小时 |3.27 A |0.36 kW |1228.37 BTU/小时 |
|  风扇转速快，驱动器处于闲置状态，两个 PSU 供电 |2.14 A |0.49 kW |1671.95 BTU/小时 |4.99 A |0.54 kW |1842.56 BTU/小时 |
|  风扇转速快，驱动器处于闲置状态，一个 PSU 供电，另一个 PSU 处于闲置状态 |2.05 A |0.48 kW |1637.83 BTU/小时 |4.58 A |0.50 kW |1706.07 BTU/小时 |
|  风扇转速快，驱动器访问中，两个 PSU 供电 |2.26 A |0.51 kW |1740.19 BTU/小时 |4.95 A |0.54 kW |1842.56 BTU/小时 |
|  风扇转速快，驱动器访问中，一个 PSU 供电，另一个 PSU 处于闲置状态 |2.14 A |0.49 kW |1671.95 BTU/小时 |4.81 A |0.53 kW |1808.44 BTU/小时 |

## <a name="disk-drive-specifications"></a>磁盘驱动器规格

StorSimple 设备最多可支持 12 个 3.5 英寸外形规格串行连接 SCSI (SAS) 磁盘驱动器。 实际驱动器可能是固态硬盘 (SSD) 或硬盘驱动器 (HDD) 的混合，具体取决于产品配置。 这 12 个磁盘驱动器插槽以 3x4 配置分布于机箱前面。 EBOD 机箱允许放置额外的 12 个磁盘驱动器。 这些磁盘驱动器始终是 HDD。

## <a name="storage-specifications"></a>存储器规格

StorSimple 设备 8100 和 8600 都是硬盘驱动器和固态硬盘的混合。 8100 和 8600 的总可用容量分别约为 15 TB 和 38 TB。 下表列出了 StorSimple 解决方案容量背景下的 SSD、HDD 和云容量的详细信息。

| 设备型号/容量 | 8100 | 8600 |
| --- | --- | --- |
| 硬盘驱动器 (HDD) 数量 |8 |19 |
| 固态硬盘 (SSD) 数量 |4 |5 |
| 单个 HDD 容量 |4 TB |4 TB |
| 单个 SSD 容量 |400 GB |800 GB |
| 备用容量 |4 TB |4 TB |
| 可用 HDD 容量 |14 TB |36 TB |
| 可用 SSD 容量 |800 GB |2 TB |
| 总可用容量* |~ 15 TB |~ 38 TB |
| 最大解决方案容量（包括云） |200 TB |500 TB |

<sup>* </sup>- *总可用容量包括数据、元数据和缓冲区的可用容量。可以在 8100 设备上预配高达 8.5 TB 的本地固定卷，或者在更大的 8600 设备上预配高达 22.5 TB 的本地固定卷。有关详细信息，请转到 [StorSimple 本地固定卷](storsimple-8000-local-volume-faq.md)。*

## <a name="enclosure-dimensions-and-weight-specifications"></a>机箱尺寸和重量规格

下表列出了尺寸和重量的各种机箱规格。

### <a name="enclosure-dimensions"></a>机箱尺寸

下表列出了以毫米和英寸为单位的机箱尺寸。

| 机箱 | 毫米 | 英寸 |
| --- | --- | --- |
| 高度 |87.9 |3.46 |
| 安装法兰的宽度 |483 |19.02 |
| 机箱主体的宽度 |443 |17.44 |
| 从安装法兰前端到机箱主体远端的深度 |577 |22.72 |
| 从操作面板到机箱最远端的深度 |630.5 |24.82 |
| 从安装法兰到机箱最远端的深度 |603 |23.74 |

### <a name="enclosure-weight"></a>机箱重量

根据配置，满载主机箱的重量可能在 21 到 33 kg 之间，需要两人才能搬动。

| 机箱 | 重量 |
| --- | --- |
| 最大重量（取决于配置） |30 kg – 33 kg |
| 空机箱（未安装驱动器） |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>机箱环境规格

本部分列出与机箱环境有关的规格。 此类别中包含温度、湿度、海拔高度、撞击、震动、方向、安全和电磁兼容性 (EMC)。

### <a name="temperature-and-humidity"></a>温度和湿度

| 机箱 | 环境温度范围 | 环境相对湿度 | 最大湿球温度 |
| --- | --- | --- | --- |
| 可运行 |5°C - 35°C(41°F - 95°F) |20% - 80% 非冷凝- |28°C (82°F) |
| 不可运行 |-40°C - 70°C(40°F - 158°F) |5% - 100% 非冷凝 |29°C (84°F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>气流、海拔高度、撞击、震动、方向、安全和 EMC

| 机箱 | 操作规范 |
| --- | --- |
| 气流 |系统气流的方向是从前到后。 系统的操作环境必须是低压、后排气的安装方式。 机架门和障碍物产生的背压不应超过 5 帕斯卡（0.5 毫米水柱）。 |
| 海拔高度，可运行 |-30 米至 3045 米（-100 英尺至 10,000 英尺），7000 英尺以上的最大工作温度降低 5°C。 |
| 海拔高度，不可运行 |-305 米至 12,192 米（-1,000 英尺至 40,000 英尺） |
| 撞击，可运行 |5g 10 ms ½ sine |
| 撞击，不可运行 |30g 10 ms ½ sine |
| 震动，可运行 |0.21g RMS 5-500 Hz 随机 |
| 震动，不可运行 |1.04g RMS 2-200 Hz 随机 |
| 震动，重定位 |3g 2-200 Hz sine |
| 方向和安装 |19 英寸安装机架（2 个 EIA 单元） |
| 机架导轨 |安装符合 IEC 297 标准的最小 700 毫米（31.50 英寸）深度的机架 |
| 安全和审批 |CE 和 UL EN 61000-3、IEC 61000-3、UL 61000-3 |
| EMC |EN55022 (CISPR - A)、FCC A |

## <a name="international-standards-compliance"></a>国际标准符合性

Microsoft Azure StorSimple 设备符合以下国际标准：  

* CE - EN 60950 - 1
* CB 报告符合 IEC 60950 - 1
* UL 和 cUL 符合 UL 60950-1

## <a name="safety-compliance"></a>安全符合性

Microsoft Azure StorSimple 设备满足以下安全评级要求：

* 系统产品类型审批：UL、cUL、CE
* 安全符合性：UL 60950、IEC 60950、EN 60950

## <a name="emc-compliance"></a>EMC 符合性

Microsoft Azure StorSimple 设备满足以下 EMC 评级要求。

### <a name="emissions"></a>发射量

该设备符合 EMC 标准的传导和辐射发射水平。

* 传导发射限值：CFR 47 第 15B 部分 A 类 EN55022 A 类 CISPR A类
* 辐射发射限值：CFR 47 第 15B 部分 A 类 EN55022 A 类 CISPR A类

### <a name="harmonics-and-flicker"></a>谐波和闪烁

该设备符合 EN61000-3-2/3 标准。

### <a name="immunity-limit-levels"></a>抗扰度限值

该设备符合 EN55024 标准。

## <a name="ac-power-cord-compliance"></a>交流电源线符合性

插头和整个电源线组件必须符合适用于设备所在国家/地区的标准，并且必须具有该国家/地区可接受的安全许可。 下表列出了美国和欧洲的标准。

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>交流电源线 - 美国（必须在 NRTL 登记）

| 组件 | 规格 |
| --- | --- |
| 电源线类型 |SV 或 SVT，最小 18 AWG，3 芯，最长 2.0 米 |
| 插头 |NEMA 5-15P 接地型连接插头，额定电压电流为 120 V，10 A；或者是 IEC 320 C14，额定电压电流为 250 V，10 A |
| 插座 |IEC 320 C-13，250 V，10 A |

### <a name="ac-power-cords---europe"></a>交流电源线 - 欧洲

| 组件 | 规格 |
| --- | --- |
| 电源线类型 |谐振线，H05-VVF-3G1.0 |
| 插座 |IEC 320 C-13，250 V，10 A |

## <a name="supported-network-cables"></a>支持的网络电缆

对于 10 GbE 网络接口、DATA 2 和 DATA 3，请参阅[支持的网络电缆和模块列表](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)。

## <a name="next-steps"></a>后续步骤

现在可以在数据中心部署 StorSimple 设备。 有关详细信息，请参阅[部署本地设备](storsimple-8000-deployment-walkthrough-u2.md)。

