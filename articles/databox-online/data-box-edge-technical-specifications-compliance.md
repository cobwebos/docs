---
title: Microsoft Azure Data Box Edge 技术规格和符合性 |Microsoft Docs
description: 了解 Azure Data Box Edge 的技术规格和符合性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/26/2019
ms.author: alkohli
ms.openlocfilehash: 9e1a7f7cd2643aae61e60d77ad74f4a08266a977
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863591"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure Data Box Edge 技术规范

Microsoft Azure Data Box Edge 设备的硬件组件遵循本文中所述的技术规格和法规标准。 技术规范介绍了电源单位（通电 psu）、存储容量、机箱和环境标准。 

## <a name="compute-memory-specifications"></a>计算，内存规格

Data Box Edge 设备具有以下计算和内存规范：

| 规格           | 值                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 核心 CPU                     |
| 内存              | 128 GB RAM                  |


## <a name="fpga-specifications"></a>FPGA 规范

支持机器学习（ML）方案的每个 Data Box Edge 设备上都包含现场可编程入口数组（FPGA）。 

| 规格           | 值                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> 可用的深层神经网络（DNN）模型与[云 FPGA 实例支持](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)的模型相同。| 


## <a name="power-supply-unit-specifications"></a>电源单位规格

Data Box Edge 设备具有两个 100-240 V 电源单元（通电 psu），具有高性能风扇。 这两个通电 psu 提供冗余电源配置。 如果某个 PSU 出现故障，则该设备将继续在另一个 PSU 上正常运行，直到发生故障的模块被更换。 下表列出了通电 psu 的技术规格。

| 规格           | 750 W PSU                  |
|-------------------------|----------------------------|
| 最大输出功率    | 750 W                     |
| 频率               | 50/60 Hz                   |
| 电压范围选择 | 自动范围： 100-240 V AC |
| 可热插拔           | 是                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>存储器规格

Data Box Edge 设备有 9 X 2.5 "NVMe Ssd，其中每个都有 1.6 TB 的容量。 在这些 Ssd 中，1是操作系统磁盘，另8是数据磁盘。 设备的总可用容量约为 12.5 TB。 下表提供了设备存储容量的详细信息。

|     规格                          |     值             |
|--------------------------------------------|-----------------------|
|    固态硬盘 (SSD) 数量     |    8                  |
|    单个 SSD 容量                     |    1.6 TB             |
|    总容量                          |    12.8 TB            |
|    总可用容量*                  |    ~ 12.5 TB            |

**保留一些空间供内部使用。*

## <a name="enclosure-dimensions-and-weight-specifications"></a>机箱尺寸和重量规格

下表列出了尺寸和重量的各种机箱规格。

### <a name="enclosure-dimensions"></a>机箱尺寸

下表列出了以毫米和英寸为单位的机箱尺寸。

|     机箱     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    44.45            |    1.75 "          |
|    宽度          |    434.1           |    17.09 "          |
|    长度          |    740.4           |    29.15 "          |

下表列出了发货包的尺寸（以毫米和英寸为单位）。

|     程序包     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    311.2            |    12.25"          |
|    宽度          |    642.8          |    25.31"          |
|    长度          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>机箱重量

设备包的重量为66磅。 和需要两个人来处理。 设备的权重取决于机箱配置。

|     机箱                                 |     重量          |
|-----------------------------------------------|---------------------|
|    总权重包括打包       |    61磅。          |
|    设备的权重                       |    35磅。          |

## <a name="enclosure-environment-specifications"></a>机箱环境规格

本部分列出了与机箱环境有关的规格，如温度、湿度和海拔高度。

### <a name="temperature-and-humidity"></a>温度和湿度

|     机箱         |     环境温度范围     |     环境相对湿度     |     最大露点温度     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    可运行        |    10° C-35 ° C （50° F-86 ° F）         |    10%-80% 非冷凝。         |    29°C (84°F)            |
|    不可运行    |    -40 ° C 到65° C （-40 ° F-149 ° F）     |    5%-95% 非冷凝。          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>气流、海拔高度、撞击、震动、方向、安全和 EMC

|     机箱                           |     操作规范                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    气流                              |    系统气流的方向是从前到后。 系统的操作环境必须是低压、后排气的安装方式。 <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    最大海拔，操作        |    3048米（10000英尺），其最高运行温度 de 已由[操作温度解除评级规范](#operating-temperature-de-rating-specifications)决定。                                                                                |
|    最大海拔，非操作    |    12000米（39370英尺）                                                                                                                                                                                         |
|    撞击，可运行                   |    6 G，6个方向为11毫秒                                                                                                                                                                         |
|    撞击，不可运行               |    6个方向的 71 G，共2毫秒                                                                                                                                                                           |
|    震动，可运行               |    0.26 G<sub>RMS</sub> 5 Hz 到 350 Hz 随机                                                                                                                                                                                     |
|    震动，不可运行           |    1.88 G<sub>RMS</sub> 10 Hz 到 500 Hz，持续15分钟（全部六个测试。）                                                                                                                                                  |
|    方向和安装             |    19 "架装                                                                                                                                                                                        |
|    安全和审批                 |    EN 60950-1:2006 + A1： 2010 + A2： 2013 + A11： 2009 + A12： 2011/IEC 60950-1:2005 ed2 + A1： 2009 + A2： 2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A、ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014（D 类）   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    能源             |    Commission Regulation (EU) No. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>操作温度解除评级规范

|     操作温度解除评级     |     环境温度范围                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    最多35个° C （95° F）                       |    最大温度降低了950米（3117英尺）以上的1° C/300 m （1° F/547 ft）。    |
|    35° C 到40° C （95° F 至104° F）            |    最大温度降低了1° C/175 m （1° F/319gb ft）以上950米（3117英尺）。    |
|    40° C 到45° C （104° F to 113 ° F）           |    最大温度降低了1° C/125 m （1° F/228 英尺）以上950米（3117英尺）。    |


## <a name="next-steps"></a>后续步骤

- [部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)
