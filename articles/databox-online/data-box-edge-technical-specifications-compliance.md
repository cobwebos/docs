---
title: Microsoft Azure 数据框边缘技术规格和合规性 |Microsoft Docs
description: 了解有关的技术规格和符合性在 Azure 数据框边缘
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 52fb32a8b34c62fe94ab35e2c051d996ab8bef10
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449204"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure 数据框边缘技术规范

Microsoft Azure 数据框边缘设备的硬件组件遵循的技术规格和法规标准，本文中所述。 技术规格介绍电源设备 (Psu)、 存储容量、 机箱和环境标准。 

## <a name="power-supply-unit-specifications"></a>电源单元规范

数据框边缘设备有两个 100-240v V 电源供应单元 (Psu) 与高性能风扇。 两个 Psu 提供冗余电源配置。 如果 PSU 失败，设备将继续正常运行其他 PSU 上直到更换发生故障的模块。 下表列出了 Psu 的技术规格。

| 规格           | 750 W PSU                  |
|-------------------------|----------------------------|
| 最大输出功率    | 750 W                     |
| 频率               | 50/60 Hz                   |
| 电压范围选择 | 自动范围：100 240 V 交流 |
| 可热插拔           | 是                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>存储器规格

数据框边缘设备具有 10 X 2.5"NVMe Ssd，每个都具有 1.6 TB 的容量。 这些 SSDs 的 2 个是操作系统磁盘和其他 8 个数据磁盘。 设备的总可用容量约为 12.5 TB。 下表提供了设备的存储容量的详细信息。

|     规格                          |     值             |
|--------------------------------------------|-----------------------|
|    固态硬盘 (SSD) 数量     |    8                  |
|    单个 SSD 容量                     |    1.6 TB             |
|    总容量                          |    12.8 TB            |
|    总可用容量*                  |    ~ 12.5 TB            |

**一些空间保留供内部使用。*

## <a name="enclosure-dimensions-and-weight-specifications"></a>机箱尺寸和重量规格

下表列出了尺寸和重量的各种机箱规格。

### <a name="enclosure-dimensions"></a>机箱尺寸

下表列出了以毫米和英寸为单位的机箱尺寸。

|     机箱     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    44.45            |    1.75"          |
|    宽度          |    434.1           |    17.09"          |
|    Length          |    740.4           |    29.15"          |

下表列出了以毫米和英寸寄送包裹的维度。

|     程序包     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    311.2            |    12.25"          |
|    宽度          |    642.8          |    25.31"          |
|    Length          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>机箱重量

设备包设置 66 磅。 和需要两人才来对其进行处理。 设备重量取决于存储设备的配置。

|     机箱                                 |     重量          |
|-----------------------------------------------|---------------------|
|    包括打包的总权重       |    61 磅。          |
|    设备重量                       |    35 磅。          |

## <a name="enclosure-environment-specifications"></a>机箱环境规格

本部分列出与机箱环境如温度、 湿度和海拔高度相关的规范。

### <a name="temperature-and-humidity"></a>温度和湿度

|     机箱         |     环境温度范围     |     环境相对湿度     |     最大 dew 点     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    可运行        |    10°C - 35°C (50°F - 86°F)         |    10%到 80%非冷凝。         |    29°C (84°F)            |
|    不可运行    |    -40 ° C 到 65 摄氏度 （° F-40-149 华氏度）     |    5%到 95%非冷凝。          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>气流、海拔高度、撞击、震动、方向、安全和 EMC

|     机箱                           |     操作规范                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    气流                              |    系统气流的方向是从前到后。 系统的操作环境必须是低压、后排气的安装方式。 <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    最大的海拔高度操作        |    与最大工作温度降低 3048 米 （10,000 英尺） 由[运行温度取消评级规范](#operating-temperature-de-rating-specifications)。                                                                                |
|    最大的海拔高度，不可操作状态    |    12000 米 （39370 英尺）                                                                                                                                                                                         |
|    撞击，可运行                   |    6 G 6 方向中的 11 毫秒                                                                                                                                                                         |
|    撞击，不可运行               |    71 G 的 6 方向中的 2 毫秒                                                                                                                                                                           |
|    震动，可运行               |    0.26 G<sub>RMS</sub> 5 Hz 至 350 Hz 随机                                                                                                                                                                                     |
|    震动，不可运行           |    1.88 G<sub>RMS</sub> 10 Hz 至 500 Hz，15 分钟 （所有六个边进行测试。）                                                                                                                                                  |
|    方向和安装             |    19 英寸安装机架                                                                                                                                                                                        |
|    安全和审批                 |    EN 60950-1:2006 + A1:2010 + A2:2013 + A11:2009 + A12:2011 / IEC 60950-1:2005 ed2 + A1:2009 + A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A，ICE 003 <br>EN 55032:2012 / 符合 CISPR 32:2012  <br>EN 55032:2015 / 符合 CISPR 32:2015  <br>EN 55024:2010 + A1:2015 / 符合 CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 （类 D）   <br>EN 61000-3-3:2013 / IEC 61000-3-3:2013                                                                                                                                                                                         |
|    能源             |    委员会条例 (EU) 不能。 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>温度取消评级规范

|     操作取消评级温度     |     环境温度范围                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    最长为 35 摄氏度 （95 华氏度）                       |    最大温度被减少由 1 ° C/300 m (1 ° F/547 ft) 上面 950 米 （3,117 英尺）。    |
|    35 摄氏度到 40 摄氏度 （95 华氏度到 104 华氏度）            |    最大温度被减少由 1 ° C/175 m (1 ° F/319 ft) 上面 950 米 （3,117 英尺）。    |
|    40 ° C 到 45 ° C (104 ° F 到 113 华氏度)           |    最大温度被减少由 1 ° C/125 m (1 ° F/228 ft) 上面 950 米 （3,117 英尺）。    |


## <a name="next-steps"></a>后续步骤

- [部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)
