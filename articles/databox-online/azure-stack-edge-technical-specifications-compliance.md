---
title: Microsoft Azure Stack Edge 技术规格和合规性 | Microsoft Docs
description: 了解 Azure Stack Edge 的技术规格和合规性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 5a4ac2342ca36d83d1a579851c090c4713814c8d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652040"
---
# <a name="azure-stack-edge-technical-specifications"></a>Azure Stack Edge 技术规格

Microsoft Azure Stack Edge 设备的硬件组件遵循本文中概述的技术规格和法规标准。 该技术规格介绍了电源装置 (PSU)、存储容量、机箱和环境标准。

## <a name="compute-memory-specifications"></a>计算和内存规格

Azure Stack Edge 设备的计算和内存规格如下：

| 规格           | 值                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 核 CPU                     |
| 内存              | 128 GB RAM                  |

## <a name="fpga-specifications"></a>FPGA 规格

每个启用了机器学习 (ML) 方案的 Azure Stack Edge 设备上都包含一个现场可编程门阵列 (FPGA)。

| 规格           | 值                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> 可用的深度神经网络 (DNN) 模型与[云 FPGA 实例支持的](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)模型相同。|

## <a name="power-supply-unit-specifications"></a>电源装置规格

Azure Stack Edge 设备具有两个带有高性能风扇的 100-240 V 电源装置 (PSU)。 这两个 PSU 提供了冗余电源配置。 如果 PSU 发生故障，设备将继续在另一个 PSU 上正常运行，直至发生故障的模块得到更换。 下表列出了 PSU 的技术规格。

| 规格           | 750 W PSU                  |
|-------------------------|----------------------------|
| 最大输出功率    | 750 W                     |
| 频率               | 50/60 Hz                   |
| 电压范围选择 | 自动范围：100-240 V 交流 |
| 可热插拔           | 是                        |

### <a name="azure-stack-edge-power-cord-specifications-by-region"></a>按区域划分的 Azure Stack Edge 电源线规格

Azure Stack Edge 设备需要使用的电源线因 Azure 区域而异。
有关所有支持的电源线的技术规格，请参阅[按区域划分的 Azure Stack Edge 电源线规格](azure-stack-edge-technical-specifications-power-cords-regional.md)。

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge device.-->

## <a name="network-interface-specifications"></a>网络接口规格

Azure Stack Edge 设备具有 6 个网络接口，PORT1 - PORT6。

| 规格           | 说明                 |
|-------------------------|----------------------------|
|  网络接口    | 2 个 1 GbE 接口 - 1 个管理接口，用户不可配置，用于初始设置。 其他接口可由用户配置，可以用于数据传输，默认为 DHCP。 <br>2 个 25 GbE 接口 – 这些接口也可用作 10 GbE 接口。 用户可将这些数据接口配置为 DHCP（默认）或静态接口。 <br> 2 个 25 GbE 接口 - 用户可将这些数据接口配置为 DHCP（默认）或静态接口。                  |

## <a name="storage-specifications"></a>存储器规格

Azure Stack Edge 设备具有 9 个 2.5" NVMe SSD，每个 SSD 容量均为 1.6 TB。 在这些 SSD 中，一个为操作系统磁盘，另外八个为数据磁盘。 设备的总可用容量约为 12.5 TB。 下表提供了设备存储容量的详细信息。

|     规格                          |     值             |
|--------------------------------------------|-----------------------|
|    固态硬盘 (SSD) 数量     |    8                  |
|    单个 SSD 容量                     |    1.6 TB             |
|    总容量                          |    12.8 TB            |
|    总可用容量*                  |    约 12.5 TB            |

**已保留一些空间供内部使用。*

## <a name="enclosure-dimensions-and-weight-specifications"></a>机箱尺寸和重量规格

下表列出了尺寸和重量的各种机箱规格。

### <a name="enclosure-dimensions"></a>机箱尺寸

下表列出了以毫米和英寸为单位的机箱尺寸。

|     机箱     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    44.45            |    1.75"          |
|    宽度          |    434.1           |    17.09"          |
|    长度          |    740.4           |    29.15"          |

下表列出了以毫米和英寸为单位的发货包尺寸。

|     程序包     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    311.2            |    12.25"          |
|    宽度          |    642.8          |    25.31"          |
|    长度          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>机箱重量

设备包重 61 磅， 需要两个人来操作。 设备重量取决于机箱配置。

|     机箱                                 |     重量          |
|-----------------------------------------------|---------------------|
|    总重量（包括打包）       |    61 磅。          |
|    设备重量                       |    35 磅。          |

## <a name="enclosure-environment-specifications"></a>机箱环境规格

本部分列出了与机箱环境有关的规格，如温度、湿度和海拔高度。

### <a name="temperature-and-humidity"></a>温度和湿度

|     机箱         |     环境温度范围     |     环境相对湿度     |     最大露点温度     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    可运行        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% 无冷凝。         |    29°C (84°F)            |
|    不可运行    |    -40°C 到 65°C (-40°F - 149°F)     |    5% - 95% 无冷凝。          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>气流、海拔高度、撞击、震动、方向、安全和 EMC

|     机箱                           |     操作规范                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    气流                              |    系统气流的方向是从前到后。 系统的操作环境必须是低压、后排气的安装方式。 <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    最大海拔高度，可运行        |    3,048 米（10,000 英尺），最大额定操作温度的降级由[额定操作温度降级规格](#operating-temperature-de-rating-specifications)确定。                                                                                |
|    最大海拔高度，不可运行    |    12,000 米（39,370 英尺）                                                                                                                                                                                         |
|    撞击，可运行                   |    6 G/11 毫秒（6 个方向）                                                                                                                                                                         |
|    撞击，不可运行               |    71 G/2 毫秒（6 个方向）                                                                                                                                                                           |
|    震动，可运行               |    0.26 G<sub>RMS</sub> 5 Hz 到 350 Hz 随机                                                                                                                                                                                     |
|    震动，不可运行           |    1.88 G<sub>RMS</sub> 10 Hz 到 500 Hz 测试 15 分钟（对六个面都进行测试。）                                                                                                                                                  |
|    方向和安装             |    19" 机架装载                                                                                                                                                                                        |
|    安全和审批                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A、ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014（D 类）   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    能源             |    Commission Regulation (EU) No. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>额定操作温度降级规格

|     额定操作温度降级     |     环境温度范围                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    上限为 35°C (95°F)                       |    950 米（3,117 英尺）以上，每升高 300 米，最高温度降低 1°C（每升高 547 英尺降低 1°F）。    |
|    35°C 到 40°C（95°F 到 104°F）            |    950 米（3,117 英尺）以上，每升高 175 米，最高温度降低 1°C（每升高 319 英尺降低 1°F）。    |
|    40°C 到 45°C（104°F 到 113°F）           |    950 米（3,117 英尺）以上，每升高 125 米，最高温度降低 1°C（每升高 228 英尺降低 1°F）。    |

## <a name="next-steps"></a>后续步骤

- [部署 Azure Stack Edge](azure-stack-edge-deploy-prep.md)
