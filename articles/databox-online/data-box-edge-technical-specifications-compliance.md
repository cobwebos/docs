---
title: 微软 Azure 数据盒 边缘技术规范和合规性*微软文档
description: 了解 Azure 数据框边缘的技术规范和合规性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: b646ee9b727d5adf4ec1c8b5c769b3d8f5c0fc1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252030"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure 数据框边缘技术规范

Microsoft Azure 数据盒边缘设备的硬件组件符合本文中概述的技术规范和法规标准。 技术规范描述了电源单元 （PSUs）、存储容量、外壳和环境标准。 

## <a name="compute-memory-specifications"></a>计算、内存规格

数据框边缘设备具有以下计算和内存规范：

| 规格           | “值”                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 核心 CPU                     |
| 内存              | 128 GB 内存                  |


## <a name="fpga-specifications"></a>FPGA 规格

支持机器学习 （ML） 方案的每个数据盒边缘设备上都包含现场可编程门阵列 （FPGA）。 

| 规格           | “值”                  |
|-------------------------|----------------------------|
| Fpga   | 英特尔阿里亚 10 <br> 可用的深度神经网络 （DNN） 模型与[云 FPGA 实例支持的](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)模型相同。| 


## <a name="power-supply-unit-specifications"></a>电源单元规格

数据盒边缘设备有两台 100-240 V 电源单元 （PSUs），具有高性能风扇。 两个 PSUs 提供冗余电源配置。 如果 PSU 发生故障，设备将继续在其他 PSU 上正常运行，直到更换故障模块。 下表列出了 PSUs 的技术规格。

| 规格           | 750 W PSU                  |
|-------------------------|----------------------------|
| 最大输出功率    | 750 W                     |
| 频率               | 50/60 Hz                   |
| 电压范围选择 | 自动测距：100-240 V 交流 |
| 可热插拔           | 是                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="network-interface-specifications"></a>网络接口规范

您的数据盒边缘设备有 6 个网络接口，PORT1- PORT6。

| 规格           | 描述                 |
|-------------------------|----------------------------|
|  网络接口    | 2 个 1 GbE 接口 - 1 个管理接口，用户不可配置，用于初始设置。 其他接口可由用户配置，可以用于数据传输，默认为 DHCP。 <br>2 个 25 GbE 接口 – 这些接口也可用作 10 GbE 接口。 用户可将这些数据接口配置为 DHCP（默认）或静态接口。 <br> 2 个 25 GbE 接口 - 用户可将这些数据接口配置为 DHCP（默认）或静态接口。                  |

## <a name="storage-specifications"></a>存储器规格

数据盒边缘设备具有 9 X 2.5 英寸 NVMe SSD，每个设备容量为 1.6 TB。 在这些 SSD 中，1 是操作系统磁盘，其他 8 个是数据磁盘。 该设备的总可用容量约为 12.5 TB。 下表提供了设备的存储容量的详细信息。

|     规格                          |     “值”             |
|--------------------------------------------|-----------------------|
|    固态硬盘 (SSD) 数量     |    8                  |
|    单个 SSD 容量                     |    1.6 TB             |
|    总容量                          |    12.8 TB            |
|    总可用容量*                  |    ± 12.5 TB            |

**保留一些空间供内部使用。*

## <a name="enclosure-dimensions-and-weight-specifications"></a>机箱尺寸和重量规格

下表列出了尺寸和重量的各种机箱规格。

### <a name="enclosure-dimensions"></a>机箱尺寸

下表列出了以毫米和英寸为单位的机箱尺寸。

|     机箱     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    44.45            |    1.75"          |
|    宽度          |    434.1           |    17.09"          |
|    长度          |    740.4           |    29.15"          |

下表列出了装运包的尺寸（以毫米和英寸为单位）。

|     程序包     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    311.2            |    12.25"          |
|    宽度          |    642.8          |    25.31"          |
|    长度          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>机箱重量

设备封装重 66 磅。 并要求两个人来处理它。 设备的重量取决于机柜的配置。

|     机箱                                 |     重量          |
|-----------------------------------------------|---------------------|
|    总重量，包括包装       |    61 磅          |
|    设备重量                       |    35 磅          |

## <a name="enclosure-environment-specifications"></a>机箱环境规格

本节列出了与机柜环境相关的规范，如温度、湿度和海拔高度。

### <a name="temperature-and-humidity"></a>温度和湿度

|     机箱         |     环境温度范围     |     环境相对湿度     |     最大露点温度     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    可运行        |    10°C - 35°C （50°F - 86°F）         |    10% - 80% 非冷凝。         |    29°C (84°F)            |
|    不可运行    |    -40°C 至 65°C （-40°F - 149°F）     |    5% - 95% 非冷凝。          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>气流、海拔高度、撞击、震动、方向、安全和 EMC

|     机箱                           |     操作规范                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    气流                              |    系统气流的方向是从前到后。 系统的操作环境必须是低压、后排气的安装方式。 <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    最大海拔高度，可操作        |    3048 米（10，000 英尺），最高工作温度除位根据[工作温度去额定值规格](#operating-temperature-de-rating-specifications)确定。                                                                                |
|    最大海拔，不可操作    |    12，000公尺（39，370英尺）                                                                                                                                                                                         |
|    撞击，可运行                   |    6 G，6 个方向为 11 毫秒                                                                                                                                                                         |
|    撞击，不可运行               |    71 G，6 个方向为 2 毫秒                                                                                                                                                                           |
|    震动，可运行               |    0.26 G<sub>RMS</sub> 5 Hz 到 350 Hz 随机                                                                                                                                                                                     |
|    震动，不可运行           |    1.88 G<sub>RMS</sub> 10 Hz 至 500 Hz，15 分钟（所有六个面均经过测试）。                                                                                                                                                  |
|    方向和安装             |    19" 机架安装                                                                                                                                                                                        |
|    安全和审批                 |    EN 60950-1：2006 +A1：2010 +A2：2013 +A11：2009 +A12：2011/IEC 60950-1：2005 ed2 +A1：2009 +A2：2013 EN 62311：2008                                                                                                                                                                       |
|    EMC                                  |    FCC A、ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014（D 类）   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    能源             |    Commission Regulation (EU) No. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>工作温度去额定值规格

|     工作温度去额定值     |     环境温度范围                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    高达 35°C （95°F）                       |    最高温度降低 1°C/300 米（1°F/547 英尺），高于 950 米（3，117 英尺）。    |
|    35°C 至 40°C （95°F 至 104°F）            |    最高温度降低 1°C/175 米（1°F/319 英尺），高于 950 米（3，117 英尺）。    |
|    40°C 至 45°C （104°F 至 113°F）           |    最高温度降低 1°C/125 米（1°F/228 英尺），高于 950 米（3，117 英尺）。    |


## <a name="next-steps"></a>后续步骤

- [部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)
